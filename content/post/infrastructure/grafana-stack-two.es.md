+++
title = "Apuntes monitoreo con Grafana, Loki y Promtail - Ejemplos de Configuración"
author = "Fcch"
date = "2026-09-21"
description = "Monitoreo basado en logs con el stack de Grafana en servidores on-premise"
featured = true
tags = [
    "grafana",
    "loki",
    "promtail",
    "monitoreo",
    "linux"
]
categories = [
    "Infraestructura",
]
series = ["Servidores"]
thumbnail = "images/grafana-stack/grafana-stack-logo-n2.png"
+++

En la primera parte, se hablo sobre monitoreo de ciertos servicios, el artículo se alargó mucho, agregar las configuraciones de ejemplo hubiera alargado mucho más el atículo, en esta segunda parte dejaremos varios ejemplos de configuración para cada servicio, con una breve descripción.

<!--more-->

![](/images/grafana-stack/grafana-stack-logo-n2.png)

Antes de entrar a cada servicio, conviene recordar el flujo general. **Promtail** lee los archivos de log y el journal de systemd, asigna labels y envía todo a **Loki**, que los almacena e indexa. **Grafana** consulta a Loki con **LogQL** para armar dashboards. Los ejemplos de esta parte se enfocan en dos cosas:

- Qué debe emitir cada servicio para que sus registros sean útiles (formato de logs, driver de logging, rutas).
- Cómo Promtail recolecta y etiqueta esos registros para poder filtrarlos después en Grafana.

**Nota importante:** En Debian 13 (Trixie) ya no existe `rsyslog` por defecto, no hay `/var/log/syslog` ni `/var/log/messages`. Todo lo del sistema pasa por `systemd-journald`, por eso varios servicios se leen con el scraper de tipo `journal`.

## Lista de servicios a monitorear

### 1. [SSH](https://www.openssh.org/)

SSH escribe sus eventos en el journal de systemd a través de la unidad `ssh.service` (en Debian el binario es `sshd`). No hace falta configurar nada especial en SSH para monitorearlo, basta con leer el journal filtrando por la unidad correspondiente.

Para asegurar que los intentos de login y las IPs de origen queden registrados con suficiente detalle, conviene revisar el nivel de log en `/etc/ssh/sshd_config`:

```bash
# /etc/ssh/sshd_config
LogLevel VERBOSE
```

Con `VERBOSE` se registran los fingerprints de las llaves usadas y más detalle de cada intento. Aplicar el cambio:

```bash
sudo systemctl restart ssh
```

Para verificar qué está emitiendo SSH al journal:

```bash
# Últimos eventos de la unidad ssh
journalctl -u ssh -n 50 --no-pager

# Seguir intentos fallidos en vivo
journalctl -u ssh -f | grep -i "failed\|invalid\|accepted"
```

En Grafana se pueden hacer consultas LogQL como estas:

```logql
# Intentos de login fallidos por SSH
{unit="ssh.service"} |= "Failed password"

# Logins exitosos
{unit="ssh.service"} |= "Accepted"

# Extraer la IP de origen de cada intento fallido
{unit="ssh.service"} |= "Failed password"
  | regexp "from (?P<ip>\\d+\\.\\d+\\.\\d+\\.\\d+)"
```

### 2. [Nginx](https://nginx.org/en/docs/index.html)

Para Nginx lo ideal es tener un formato de log predecible y separar los logs por dominio, así Promtail puede etiquetar cada archivo con su `domain` y distinguir `access` de `error`.

Definir un `log_format` claro en `/etc/nginx/nginx.conf` dentro del bloque `http`:

```nginx
# /etc/nginx/nginx.conf (bloque http)
log_format monitor '$remote_addr - $remote_user [$time_local] '
                   '"$request" $status $body_bytes_sent '
                   '"$http_referer" "$http_user_agent" '
                   'rt=$request_time';
```

Luego, en cada server block, apuntar los logs a rutas por dominio para que el label `domain` salga limpio:

```nginx
# /etc/nginx/sites-available/ejemplo.com
server {
    listen 80;
    server_name ejemplo.com;

    access_log /var/log/nginx/ejemplo.com-access.log monitor;
    error_log  /var/log/nginx/ejemplo.com-error.log warn;

    location / {
        proxy_pass http://127.0.0.1:8080;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

Validar y recargar Nginx:

```bash
sudo nginx -t
sudo systemctl reload nginx
```

Ejemplos de consultas LogQL para el dashboard:

```logql
# Requests por segundo
sum(rate({job="nginx", log_type="access"}[1m]))

# Conteo de códigos de estado HTTP
sum by (status) (count_over_time({job="nginx", log_type="access"}[5m]))

# Solo errores 5xx
{job="nginx", log_type="access"} | status =~ "5.."

# Errores de Nginx en tiempo real
{job="nginx", log_type="error"}
```

### 3. [Fail2ban](https://fail2ban.readthedocs.io/en/latest/)

Fail2ban escribe sus acciones en `/var/log/fail2ban.log`, de ahí salen los baneos y las detecciones. Conviene asegurar el nivel de log en su configuración.

En `/etc/fail2ban/fail2ban.local` (para no tocar el `.conf` que se sobreescribe en actualizaciones):

```ini
# /etc/fail2ban/fail2ban.local
[Definition]
loglevel = INFO
logtarget = /var/log/fail2ban.log
```

Un ejemplo mínimo de jail para SSH en `/etc/fail2ban/jail.local`:

```ini
# /etc/fail2ban/jail.local
[DEFAULT]
bantime  = 1h
findtime = 10m
maxretry = 5

[sshd]
enabled  = true
port     = ssh
logpath  = %(sshd_log)s
backend  = systemd
```

Reiniciar el servicio:

```bash
sudo systemctl restart fail2ban
sudo fail2ban-client status sshd
```

Consultas LogQL para el dashboard de defensa:

```logql
# IPs baneadas
{job="fail2ban"} |= "Ban" != "Unban"

# IPs detectadas (antes del baneo)
{job="fail2ban"} |= "Found"

# Tendencia de baneos en el tiempo
sum(count_over_time({job="fail2ban"} |= "Ban" != "Unban" [1h]))
```

### 4. [Docker](https://www.docker.com/)

Como se vio en la primera parte, para monitorear contenedores le pedimos a Docker que escriba sus registros en el journal de systemd cambiando el **Logging Driver** de `json-file` a `journald`.

Recordatorio del archivo `/etc/docker/daemon.json`:

```bash
sudo tee /etc/docker/daemon.json > /dev/null <<'EOF'
{
 "log-driver": "journald"
}
EOF
sudo systemctl restart docker
docker info --format '{{.LoggingDriver}}'   # Salida: journald
```

Con `journald`, Docker etiqueta cada línea con metadatos como el nombre del contenedor y su imagen. Promtail los expone como labels con `relabel_configs` sobre el mismo scraper de tipo `journal`:

```yaml
# scrape_configs de Promtail (fragmento) - Docker vía journal
- job_name: docker
  journal:
    path: /var/log/journal
    max_age: 12h
    labels:
      job: docker
      host: vps-01
  relabel_configs:
    # Solo líneas que provienen del driver journald de Docker
    - source_labels: ['__journal_container_name']
      target_label: container
    - source_labels: ['__journal_image_name']
      target_label: image
    # Descarta entradas que no sean de contenedores
    - source_labels: ['__journal_container_name']
      regex: '^$'
      action: drop
```

Consultas LogQL para el dashboard de contenedores:

```logql
# Logs de un contenedor específico
{job="docker", container="mi-app"}

# Volumen de logs por contenedor
sum by (container) (rate({job="docker"}[5m]))

# Buscar errores en todos los contenedores
{job="docker"} |~ "(?i)error|fatal|panic"
```

## Aplicar la configuración de Promtail

Cada vez que se editan los `scrape_configs`, hay que validar y reiniciar el agente:

```bash
# Reiniciar Promtail
sudo systemctl restart promtail

# Revisar que no haya errores de arranque
sudo journalctl -u promtail -n 50 --no-pager

# Verificar los targets activos desde la API de Promtail
curl -s http://localhost:9080/targets | head
```

Si Loki está recibiendo datos, en Grafana → Explore ya deberían aparecer los labels (`job`, `unit`, `domain`, `container`) para filtrar.

## Resumen de labels por servicio

| Servicio | Fuente                        | Job en Promtail | Labels clave                     |
| :------- | :---------------------------- | :-------------- | :------------------------------- |
| SSH      | journal (`ssh.service`)       | journal         | `unit`, `level`, `host`          |
| Nginx    | `/var/log/nginx/*-access.log` | nginx-access    | `domain`, `status`, `log_type`   |
| Nginx    | `/var/log/nginx/*-error.log`  | nginx-error     | `domain`, `log_type=error`       |
| Fail2ban | `/var/log/fail2ban.log`       | fail2ban        | `service=fail2ban`               |
| Docker   | journal (`journald` driver)   | docker          | `container`, `image`, `host`     |

## Conclusión

La clave de un buen monitoreo basado en logs no está solo en el stack, sino en que cada servicio emita registros con formato predecible y en que Promtail los etiquete de forma consistente. Con labels bien definidos (`unit`, `domain`, `status`, `container`) las consultas LogQL se vuelven simples y los dashboards se arman rápido.

En esta parte quedaron los ejemplos de configuración de cada servicio monitoreado. Las configuraciones propias del stack de Grafana (Loki, `grafana.ini` y el `config.yml` completo de Promtail) las dejaré por mi lado para complementar estos apuntes.

## Referencias

- [Grafana](https://grafana.com/)
- [Loki - Documentación Oficial](https://grafana.com/docs/loki/latest/)
- [Promtail - Configuración de scrape_configs](https://grafana.com/docs/loki/latest/send-data/promtail/configuration/)
- [LogQL - Lenguaje de Consultas de Loki](https://grafana.com/docs/loki/latest/query/)
- [Nginx - Módulo log](https://nginx.org/en/docs/http/ngx_http_log_module.html)
- [Fail2ban - Documentación Oficial](https://fail2ban.readthedocs.io/en/latest/)
- [Docker - Logging Drivers](https://docs.docker.com/engine/logging/drivers/journald/)
