+++
title = "Monitoreo de Servidores con Grafana, Loki y Promtail"
author = "Fcch"
date = "2026-09-14"
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
thumbnail = "images/grafana-stack/grafana-stack-logo.png"
+++

Tener un servidor funcionando sin monitoreo es como manejar un auto sin tablero de instrumentos: todo parece bien hasta que deja de funcionar. No importa si administras un servidor personal, un VPS o una infraestructura pequeña, contar con un sistema de monitoreo básico es fundamental para saber qué está pasando, detectar problemas a tiempo y tomar decisiones informadas.

<!--more-->

En este artículo comparto la implementación de un sistema de monitoreo basado en el stack de **Grafana** para servidores on-premise, utilizando exclusivamente logs del sistema operativo y de servicios críticos como SSH, Nginx y Fail2ban.

## ¿Por qué es importante monitorear?

Muchos administradores de servidores, especialmente los que manejan infraestructura pequeña, postergan el monitoreo porque lo consideran complejo o innecesario. Sin embargo, un monitoreo básico permite:

- **Detectar ataques en curso**: intentos de fuerza bruta por SSH, escaneos de puertos, IPs maliciosas.
- **Identificar problemas antes de que escalen**: errores 5xx en Nginx, certificados SSL a punto de vencer, servicios caídos.
- **Entender el comportamiento del servidor**: picos de tráfico, patrones de uso, dominios más consultados.
- **Tener evidencia para tomar decisiones**: datos reales sobre qué está pasando, no suposiciones.
- **Responder más rápido ante incidentes**: con dashboards centralizados la información está a un vistazo.

No se trata de armar un sistema de monitoreo empresarial con cientos de métricas. Se trata de tener lo mínimo necesario para no estar a ciegas.

## El stack: Grafana + Loki + Promtail

La solución se compone de tres herramientas del ecosistema de [Grafana](https://grafana.com/), todas de código abierto:

| Componente   | Función                                                  | Puerto |
| ------------ | -------------------------------------------------------- | ------ |
| **Promtail** | Agente que recolecta logs del sistema y los envía a Loki | 9080   |
| **Loki**     | Motor de almacenamiento e indexación de logs             | 3100   |
| **Grafana**  | Interfaz web para visualización y dashboards             | 3011   |

El flujo es simple: **Promtail** lee los archivos de log y el journal de systemd, los envía a **Loki** que los almacena e indexa, y **Grafana** consulta a Loki para mostrar la información en dashboards.

```
┌──────────────────────────────────────────────────────┐
│                   Servidor Debian 13                 │
│                                                      │
│  ┌──────────┐    ┌──────────┐    ┌────────────────┐  │
│  │ Promtail │───▶│   Loki   │◀───│    Grafana     │  │
│  │  :9080   │    │  :3100   │    │    :3011       │  │
│  └──────────┘    └──────────┘    └────────────────┘  │
│       │                                              │
│       ▼                                              │
│  ┌────────────────────────────────────────────────┐  │
│  │           Fuentes de Logs                      │  │
│  │                                                │  │
│  │  • /var/log/journal      (systemd journal)     │  │
│  │  • /var/log/nginx/*.log  (access + error)      │  │
│  │  • /var/log/fail2ban.log (bloqueos)            │  │
│  │  • /var/log/letsencrypt/ (certificados)        │  │
│  └────────────────────────────────────────────────┘  │
└──────────────────────────────────────────────────────┘
```

### ¿Por qué este stack y no otro?

- **No requiere agentes de métricas adicionales**: toda la información proviene de logs que ya existen en el servidor.
- **Bajo consumo de recursos**: Loki no indexa el contenido completo de los logs, solo los labels, lo que lo hace mucho más ligero que Elasticsearch.
- **Instalación simple**: los tres componentes se instalan desde el repositorio oficial de Grafana con `apt`.
- **LogQL**: el lenguaje de consultas de Loki es potente y permite extraer métricas directamente de los logs.

## Datos mínimos para un monitoreo útil

No es necesario monitorear todo. Con las siguientes fuentes de datos ya se tiene una visión clara del estado del servidor:

### 1. SSH: seguridad de acceso

El monitoreo de SSH es probablemente el más importante. Permite detectar:

- **Intentos de login fallidos** (fuerza bruta)
- **Logins exitosos** (verificar que solo acceden usuarios autorizados)
- **IPs de origen** de cada intento
- **Usuarios probados** por atacantes

Promtail lee directamente del journal de systemd y extrae campos estructurados mediante pipeline stages:

```yaml
- job_name: journal
  journal:
    path: /var/log/journal
    max_age: 12h
  pipeline_stages:
    - match:
        selector: '{unit="ssh.service"}'
        stages:
          - regex:
              expression: 'Accepted publickey for (?P<user>\S+) from (?P<ip>[0-9.]+) port (?P<port>[0-9]+)'
          - labels:
              user:
              ip:
              port:
          - static_labels:
              result: accepted
              method: publickey
```

Con esta configuración se pueden construir consultas como:

```logql
# Intentos de SSH fallidos
{unit="ssh.service"} |= "Failed password"

# Logins exitosos con llave pública
{unit="ssh.service"} |= "Accepted publickey"

# Top 10 IPs atacantes en los últimos 15 minutos
topk(10, sum by (ip)(
  count_over_time({unit="ssh.service"}
    | regexp "from (?P<ip>[0-9.]+)"
    [15m]
  )
))
```

### 2. Nginx: tráfico web

Para los servidores web, lo mínimo que conviene monitorear:

- **Requests por segundo/minuto**: detectar picos de tráfico.
- **Códigos de estado HTTP**: cuántos errores 4xx y 5xx se están produciendo.
- **Top IPs**: identificar quién está generando más tráfico.
- **Top URLs**: qué recursos son los más solicitados.
- **Error logs**: visualizar errores del servidor en tiempo real.

Promtail se configura con static_configs apuntando a los archivos de log de cada dominio:

```yaml
- job_name: nginx-pad-access
  static_configs:
    - targets:
        - localhost
      labels:
        job: nginx
        domain: pad.xnibble.com
        service: nginx
        log_type: access
        host: fcch-xn
        __path__: /var/log/nginx/pad-xn-access.log
```

Consultas útiles:

```logql
# Requests por segundo por dominio
sum by (domain)(rate({job="nginx", log_type="access"}[1m]))

# Errores 4xx en los últimos 5 minutos
sum(count_over_time(
  {job="nginx", log_type="access"}
  | regexp "^[^ ]+ .*\" [^ ]+ (?P<status>[0-9]{3}) "
  | status=~"4.."
  [5m]
))

# Error logs en tiempo real
{job="nginx", log_type="error"}
```

### 3. Fail2ban: protección activa

Fail2ban complementa el monitoreo mostrando las acciones de defensa automática:

- **IPs baneadas**: quién fue bloqueado y cuándo.
- **IPs detectadas**: intentos sospechosos antes del baneo.
- **Tendencias**: si los ataques están aumentando o disminuyendo.

```logql
# Top 10 IPs baneadas en la última hora
topk(10, sum by (ip)(
  count_over_time({job="fail2ban"}
    | regexp "Ban (?P<ip>[0-9.]+)"
    [1h]
  )
))
```

### 4. Let's Encrypt: certificados SSL

Un monitoreo simple pero importante: verificar que las renovaciones de certificados se ejecutan correctamente.

```logql
# Renovaciones de certificados
{service="certbot"} |= "renewal"

# Errores en renovación
{service="certbot"} |= "error"
```

## Instalación del stack

Los tres componentes se instalan desde el repositorio oficial de Grafana mediante `apt` en Debian. Los paquetes `.deb` ya incluyen los archivos de unidad para systemd.

### Agregar el repositorio de Grafana

```bash
sudo apt install -y apt-transport-https software-properties-common wget
sudo mkdir -p /etc/apt/keyrings
wget -qO - https://apt.grafana.com/gpg.key | sudo tee /etc/apt/keyrings/grafana.asc > /dev/null
```

Crear el archivo `/etc/apt/sources.list.d/grafana.sources`:

```
X-Repolib-Name: grafana
Types: deb
Components: main
Suites: stable
URIs: https://apt.grafana.com
Signed-By: /etc/apt/keyrings/grafana.asc
Enabled: yes
```

```bash
sudo apt update
```

### Instalar los tres servicios

```bash
sudo apt install grafana loki promtail
```

Esto instala:

- **Grafana** → servicio `grafana-server`, config en `/etc/grafana/grafana.ini`
- **Loki** → servicio `loki`, config en `/etc/loki/config.yml`
- **Promtail** → servicio `promtail`, config en `/etc/promtail/config.yml`

### Crear directorios de datos para Loki

```bash
sudo mkdir -p /var/lib/loki/{chunks,rules,compactor}
sudo chown -R loki:loki /var/lib/loki
```

### Habilitar e iniciar servicios

```bash
sudo systemctl enable grafana-server loki promtail
sudo systemctl start grafana-server loki promtail
```

## Configuración de Loki

La configuración de Loki define cómo almacena e indexa los logs. Los puntos clave:

```yaml
auth_enabled: false

server:
  http_listen_port: 3100
  grpc_listen_port: 9096

common:
  instance_addr: 127.0.0.1
  path_prefix: /var/lib/loki
  storage:
    filesystem:
      chunks_directory: /var/lib/loki/chunks
      rules_directory: /var/lib/loki/rules
  replication_factor: 1
  ring:
    kvstore:
      store: inmemory

schema_config:
  configs:
    - from: 2026-05-20
      store: tsdb
      object_store: filesystem
      schema: v13
      index:
        prefix: index_
        period: 24h

limits_config:
  retention_period: 168h
  ingestion_rate_mb: 8
  ingestion_burst_size_mb: 16
  max_streams_per_user: 10000
  max_query_lookback: 168h

compactor:
  working_directory: /var/lib/loki/compactor
  compaction_interval: 10m
  retention_enabled: true
  delete_request_store: filesystem

analytics:
  reporting_enabled: false
```

Puntos importantes:

- **Retención de 7 días** (`168h`): suficiente para un servidor personal y no consume demasiado disco.
- **Esquema v13 con TSDB**: el formato más reciente y eficiente para indexación.
- **Almacenamiento en filesystem**: ideal para un solo servidor, sin necesidad de almacenamiento distribuido.
- **Telemetría deshabilitada**: no envía datos de uso a Grafana Labs.

## Configuración de Promtail

Promtail es el agente que recolecta los logs. Su configuración define qué archivos leer y cómo procesarlos:

```yaml
server:
  http_listen_port: 9080
  grpc_listen_port: 0

positions:
  filename: /var/lib/promtail/positions.yaml

clients:
  - url: http://localhost:3100/loki/api/v1/push
```

La sección `scrape_configs` define los jobs de recolección. Cada job apunta a una fuente de logs y asigna labels que permiten filtrar en Grafana:

| Job             | Fuente                                 | Labels clave                |
| --------------- | -------------------------------------- | --------------------------- |
| journal         | `/var/log/journal`                     | `unit`, `host`, `level`     |
| nginx-\*-access | `/var/log/nginx/*-access.log`          | `domain`, `log_type=access` |
| nginx-\*-error  | `/var/log/nginx/*-error.log`           | `domain`, `log_type=error`  |
| fail2ban        | `/var/log/fail2ban.log`                | `service=fail2ban`          |
| letsencrypt     | `/var/log/letsencrypt/letsencrypt.log` | `service=certbot`           |

> **Nota sobre Debian 13:** Trixie ya no incluye `rsyslog` por defecto. No existe `/var/log/syslog` ni `/var/log/messages`. Todos los logs del sistema se gestionan a través de systemd-journald, por eso se usa el scraper de tipo `journal` en Promtail para leer logs de SSH y otras unidades systemd.

## Verificación del stack

Una vez instalado y configurado, se puede verificar que todo funciona correctamente:

```bash
# Verificar que los servicios están activos
sudo systemctl status grafana-server loki promtail

# Verificar que Loki está listo
curl -s http://localhost:3100/ready

# Verificar labels disponibles en Loki
curl -s http://localhost:3100/loki/api/v1/labels

# Verificar targets activos de Promtail
curl -s http://localhost:9080/targets
```

Luego acceder a Grafana en `http://<IP_SERVIDOR>:3011` (credenciales por defecto: `admin` / `admin`), agregar Loki como datasource apuntando a `http://localhost:3100` y comenzar a crear dashboards o importar los que ya existen en el repositorio del proyecto.

## Dashboards del proyecto

El proyecto incluye dashboards listos para importar:

- **Login Users**: seguridad SSH con paneles de logins exitosos, fallidos, top IPs atacantes y usuarios probados.
- **Web Server**: monitoreo completo de Nginx y Fail2ban con requests por segundo, códigos de estado HTTP, top URLs, top IPs y error logs.
- **Registry Health**: disponibilidad de providers del Terraform Registry (usando datasource Infinity).

## Escalando a múltiples servidores

Esta misma solución se puede escalar para monitorear múltiples servidores instalando solo Promtail en cada servidor remoto y apuntándolo al Loki central:

```yaml
# En cada servidor remoto, cambiar la URL del cliente
clients:
  - url: http://<IP_SERVIDOR_CENTRAL>:3100/loki/api/v1/push
```

Solo hay que cambiar `instance_addr` de `127.0.0.1` a `0.0.0.0` en Loki y proteger el acceso con firewall:

```bash
sudo ufw allow from 192.168.1.10 to any port 3100
```

## Conclusión

No hace falta una infraestructura compleja para tener visibilidad sobre lo que pasa en un servidor. Con Grafana, Loki y Promtail se puede implementar un sistema de monitoreo basado en logs que cubre lo esencial: seguridad de acceso, tráfico web, protección contra ataques y estado de certificados.

Lo importante es empezar con lo mínimo. Un dashboard con intentos de SSH fallidos y errores de Nginx ya es infinitamente mejor que no tener nada.

## Referencias

- [Grafana](https://grafana.com/)
- [Loki - Documentación Oficial](https://grafana.com/docs/loki/latest/)
- [Promtail - Documentación Oficial](https://grafana.com/docs/loki/latest/send-data/promtail/)
- [LogQL - Lenguaje de Consultas de Loki](https://grafana.com/docs/loki/latest/query/)
