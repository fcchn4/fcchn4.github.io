+++
title = "Apuntes monitoreo con Grafana, Loki y Promtail"
author = "Fcch"
date = "2026-09-17"
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

A lo largo de la vida profesional es normal ver muchos tipos de implementaciones en cuanto a desarrollo e infraestructura, en pocas palabras para infraestructura es normal que no exista monitoreo de servicios en servidores de alguna organización, esto es más común en servidores on-premise, por este motivo me hice algunas preguntas.

<!--more-->

![](/images/grafana-stack/grafana-stack-logo.png)

- ¿Cuánto tiempo toma armar un sistema de monitoreo?
- ¿Qué tan complicado puede ser implementar un sistema de monitoreo?
- ¿Qué capacidades de cómputo necesito para tener un sistema de monitoreo?
- ¿Cuántos sistemas de monitoreo existen y puedo utilizar?

Para este artículo hice una prueba de concepto aprovechando una rebaja en servidores VPS de [**CONTABO**](https://contabo.com/en/), armamos infraestructura simple, instalamos algunos honeypots para obtener datos, generamos tráfico para obtener métricas y crear gráficas.

| Servidor | Característica   |
| :------- | :--------------- |
| CPU      | AMD 4 Cores      |
| RAM      | 8GB              |
| SSD      | 150 GB           |
| OS       | Debian Trixie 13 |

## ¿Por qué tenemos que monitorear?

Tenemos que monitorear porque necesitamos enterarnos de lo que sucede en nuestra infraestructura antes de que los problemas aparezcan, los reclamos lleguen y existan incidentes de seguridad, monitorear nos permite:

- **Detectar ataques en curso**, desde ataques de fuerza bruta, escaneos de puertos, IPs maliciosas.
- **Identificar problemas antes de que escalen**, errores 5xx en servidores web, certificados SSL a punto de vencer, servicios caídos.
- **Entender el comportamiento del servidor**, tráfico y demanda de red, patrones de uso, dominios más consultados.
- **Tener evidencia para tomar decisiones**, datos reales sobre qué está pasando, no suposiciones.
- **Responder más rápido ante incidentes**, con dashboards centralizados la información está a un vistazo.

Muchas veces se piensa que armar un sistema de monitoreo trata de un sistema complejo que necesita de hardware especializado, licencias de uso para el software y conocimientos avanzados de seguridad, pero la idea es tener lo mínimo necesario para no estar a ciegas (para infraestructuras pequeñas).

## Sistemas de monitoreo

Existen diferentes tipos de sistemas de monitoreo, algunos de propósito general, otros con propósito específico:

- [**Zabbix**](https://www.zabbix.com/), plataforma de monitoreo de infraestructura que permite supervisar servidores, redes, aplicaciones, bases de datos y servicios. Utiliza agentes, SNMP y otros protocolos para recopilar métricas y generar alertas.
- [**Nagios**](https://www.nagios.org/) **\- [Icinga](https://icinga.com/)**, soluciones de monitorización de infraestructura y servicios. Permiten verificar disponibilidad, estado y rendimiento mediante plugins. Icinga nació como un fork de Nagios y mantiene compatibilidad con gran parte de su ecosistema.
- [**PandoraFMS**](https://pandorafms.com/), plataforma de monitorización integral para infraestructura, redes, servidores, aplicaciones y servicios. Permite recopilar métricas, generar alertas, visualizar estados y realizar monitorización tanto local como remota.
- [**Grafana**](https://grafana.com/docs/grafana/latest/) **\- [Loki](https://grafana.com/docs/loki/latest/) \- [Promtail](https://grafana.com/docs/loki/latest/send-data/promtail/)**, stack orientado principalmente a observabilidad y gestión de logs. **Grafana** proporciona dashboards con visualización, **Loki** almacena y consulta logs, **Promtail** recopila y envía los logs hacia **Loki**.
- [**Wazuh**](https://wazuh.com/), permite monitorizar endpoints, analizar logs, detectar amenazas, realizar análisis de vulnerabilidades, file integrity monitoring (FIM) y generar alertas de seguridad.
- [**ELK Stack**](https://www.elastic.co/elastic-stack) **(Elasticsearch, Kibana, Beats y Logstash)**, plataforma de gestión y análisis de logs y datos. Elasticsearch almacena e indexa datos, Logstash realiza ingesta y transformación, Beats recopila información desde sistemas y servicios, y Kibana proporciona visualización, dashboards y análisis.

No queremos alargar este artículo, el monitoreo simple que haremos será con [Grafana](https://grafana.com/docs/grafana/latest/) **\-** [Loki](https://grafana.com/docs/loki/latest/) **\-** [Promtail](https://grafana.com/docs/loki/latest/send-data/promtail/), con datos de los registros de sistema, utilizando agentes con acceso a los datos de los servicios que queremos monitorear.

**Nota importante:** Actualmente, **Promtail** está en proceso de reemplazo dentro del ecosistema Grafana por [Grafana Alloy](https://grafana.com/docs/alloy/latest/).

## Grafana - Loki - Promtail

La solución se compone de tres herramientas del ecosistema de [Grafana](https://grafana.com/), todas de código abierto:

| Componente   | Función                                                  | Puerto |
| :----------- | :------------------------------------------------------- | :----- |
| **Promtail** | Agente que recolecta logs del sistema y los envía a Loki | 9080   |
| **Loki**     | Motor de almacenamiento e indexación de logs             | 3100   |
| **Grafana**  | Interfaz web para visualización y dashboards             | 3000   |

El flujo es simple: **Promtail** lee los archivos de log y el journal de systemd, los envía a **Loki** que los almacena e indexa, y **Grafana** consulta a Loki para mostrar la información en dashboards.

![](/images/grafana-stack/grafana-loki-promtail-workflow.png)

### ¿Por qué solo Grafana - Loki - Promtail?

- **No requiere agentes de métricas adicionales**, toda la información proviene de logs que ya existen en el servidor.
- **Bajo consumo de recursos**, Loki no indexa el contenido completo de los logs, solo los labels, lo que lo hace mucho más ligero que Elasticsearch.
- **Instalación simple**, los tres componentes se instalan desde el repositorio oficial de Grafana con `apt`.
- **LogQL**, el lenguaje de consultas de Loki es potente y permite extraer métricas directamente de los logs.

### ¿Qué vamos a monitorear?

Nuestra prueba solo va a monitorear servicios como SSH, Nginx, Fail2ban y Docker.

### 1. [SSH](https://www.openssh.org/)

Muchos conocemos SSH, nos permite conectar de forma remota a un servidor que tiene instalado este servicio, el puerto por defecto es el 22. Monitorear este servicio es muy importante porque permite detectar:

- **Intentos de login fallidos**, fuerza bruta.
- **Logins exitosos**, verificar que solo acceden usuarios autorizados.
- **IPs de origen**, de cada intento.
- **Usuarios probados**, por atacantes.

![](/images/grafana-stack/grafana-ssh-dashboard.png)

### 2. [Nginx](https://nginx.org/en/docs/index.html)

Para servidores web lo mínimo que conviene monitorear es el tráfico:

- **Requests por segundo/minuto**, detectar alta o baja demanda de tráfico.
- **Códigos de estado HTTP**, cuántos errores 4xx y 5xx se están produciendo.
- **Top IPs**, identificar quién está generando más tráfico.
- **Top URLs**, qué recursos son los más solicitados.
- **Error logs**, visualizar errores del servidor en tiempo real.

![](/images/grafana-stack/grafana-nginx-dashboard-v2.png)

### 3. [Fail2ban](https://fail2ban.readthedocs.io/en/latest/)

Protección contra ataques de fuerza bruta, complementa el monitoreo mostrando las acciones de defensa automática:

- **IPs baneadas**, quién fue bloqueado y cuándo.
- **IPs detectadas**, intentos sospechosos antes del baneo.
- **Tendencias**, si los ataques están aumentando o disminuyendo.

![](/images/grafana-stack/grafana-fail2ban-dashboard-v1.png)

### 4. [Docker](https://www.docker.com/)

Para un monitoreo de contenedores simple, podemos pedirle a docker que escriba sus registros de sistema en journal de systemd, con una configuración no muy compleja podremos monitorear los contenedores, códigos de estado, volúmenes.

Por defecto el **Logging Driver** de Docker está configurado para "**json-file**", esta configuración debe estar con "**journald**", se puede verificar con algunos comandos.

```bash
# Ver el driver de logs activo
docker info --format '{{.LoggingDriver}}'

# Ver si ya existe el archivo de config del daemon
sudo cat /etc/docker/daemon.json 2>/dev/null || echo "No existe daemon.json todavía"
```

Comúnmente el archivo "**daemon.json**", si es necesario se debe crear.

```bash
sudo tee /etc/docker/daemon.json > /dev/null <<'EOF'
{
 "log-driver": "journald"
}
EOF
```

Si el archivo ya existe con otros datos, no se debe sobrescribir, se debe editar el contenido en base al comando anterior. Ruta del archivo a editar **/etc/docker/daemon.json**

Recomendable validar si el JSON es correcto.

```bash
sudo python3 -c "import json; json.load(open('/etc/docker/daemon.json')); print('JSON válido')"
```

Aplicando cambios y verificando los datos:

```bash
sudo systemctl restart docker
docker info --format '{{.LoggingDriver}}' # Salida: "journald"
```

**Nota importante:** El driver de registro de sistema se asigna al crear contenedores, es posible que se deba recrear contenedores existentes para que se creen con el nuevo driver.

```bash
# Para un contenedor suelto
docker stop <nombre> && docker rm <nombre>
# y volver a lanzarlo

# Si usas docker compose
docker compose up -d --force-recreate
```

Por último se deben verificar los cambios:

```bash
docker inspect --format '{{.HostConfig.LogConfig.Type}}' <nombre_o_id>
```

Con estos detalles podemos crear un dashboard para contenedores también.

![](/images/grafana-stack/grafana-docker-dashboard-v1.png)

## Instalación de Grafana y sus componentes

Los tres componentes se instalan desde el repositorio oficial de Grafana mediante `apt` en Debian. Los paquetes `.deb` ya incluyen los archivos de unidad para systemd.

### Repositorio de Grafana

```bash
sudo apt install -y apt-transport-https wget gnupg
sudo mkdir -p /etc/apt/keyrings
sudo wget -O /etc/apt/keyrings/grafana.asc https://apt.grafana.com/gpg-full.key
sudo chmod 644 /etc/apt/keyrings/grafana.asc
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

Actualizamos e instalamos los paquetes:

```bash
sudo apt update
sudo apt install -y grafana loki promtail
```

### Archivos de configuración

- **Grafana**, archivo de configuración en `/etc/grafana/grafana.ini`
- **Loki**, archivo de configuración en `/etc/loki/config.yml`
- **Promtail**, archivo de configuración en `/etc/promtail/config.yml`

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

## Configuración adicional para Loki

La configuración de Loki define cómo se almacenan e indexan los registros de sistema.

Puntos importantes:

- **Retención de 7 días** (`168h`): suficiente para un servidor personal y no consume demasiado disco.
- **Esquema v13 con TSDB**, el formato más reciente y eficiente para indexación.
- **Almacenamiento en filesystem**: ideal para un solo servidor, sin necesidad de almacenamiento distribuido.
- **Telemetría deshabilitada**, no envía datos de uso a Grafana Labs.

## Configuración de Promtail

Promtail es el agente que recolecta los registros de sistema, su configuración define qué archivos leer y cómo procesarlos.  
La sección `scrape_configs` define los jobs de recolección, cada job apunta a una fuente de registro de sistema y asigna labels que permiten filtrar en Grafana:

| Job             | Fuente                        | Labels clave                |
| :-------------- | :---------------------------- | :-------------------------- |
| journal         | `/var/log/journal`            | `unit`, `host`, `level`     |
| nginx-\*-access | `/var/log/nginx/*-access.log` | `domain`, `log_type=access` |
| nginx-\*-error  | `/var/log/nginx/*-error.log`  | `domain`, `log_type=error`  |
| fail2ban        | `/var/log/fail2ban.log`       | `service=fail2ban`          |

**Nota importante:** Debian 13 (Trixie) ya no incluye `rsyslog` por defecto. No existe `/var/log/syslog` ni `/var/log/messages`. Todos los logs del sistema se gestionan a través de systemd-journald, por eso se usa el scraper de tipo `journal` en Promtail para leer logs de SSH y otras unidades systemd.

## Verificación del stack

Una vez instalado y configurado, se puede verificar que todo funciona correctamente:

```bash
curl http://localhost:3000/login
```

## Dashboards del proyecto

El proyecto incluye dashboards listos para importar:

- **Login Users**: seguridad SSH con paneles de logins exitosos, fallidos, top IPs atacantes y usuarios probados.
- **Web Server**: monitoreo completo de Nginx y Fail2ban con requests por segundo, códigos de estado HTTP, top URLs, top IPs y error logs.
- **Registry Health**: disponibilidad de providers del Terraform Registry (usando datasource Infinity).

![](/images/grafana-stack/grafana-status-services-dashboard.png)

## Escalando a múltiples servidores

Esta misma solución se puede escalar para monitorear múltiples servidores instalando solo Promtail en cada servidor remoto y apuntándolo al Loki central.

Solo hay que cambiar `instance_addr` de `127.0.0.1` a `0.0.0.0` en Loki y proteger el acceso con firewall.

## Conclusión

No hace falta una infraestructura compleja para tener visibilidad sobre lo que pasa en un servidor. Con Grafana, Loki y Promtail se puede implementar un sistema de monitoreo basado en logs que cubre lo esencial: seguridad de acceso, tráfico web, protección contra ataques y estado de certificados.

Lo importante es empezar con lo mínimo. Un dashboard con intentos de SSH fallidos y errores de Nginx ya es infinitamente mejor que no tener nada.

¿Cuánto tiempo nos tomó? Puedo decir que me tomó más tiempo crear el artículo que desplegar todo lo necesario:

Instalación y configuración del servidor, con Ansible 20 minutos, ya tenía un proyecto con todo lo necesario, el sistema de monitoreo fueron como 5 horas y un poco más, ya que no conocíamos la herramienta, tocó leer, probar y preguntar a la IA también.

**Nota importante:** Este artículo se hizo largo, en otro dejaré las configuraciones de cada servicio incluyendo la configuración del reverse proxy de Nginx para tener más detalle de cada configuración.

## Referencias

- [Grafana](https://grafana.com/)
- [Loki - Documentación Oficial](https://grafana.com/docs/loki/latest/)
- [Promtail - Documentación Oficial](https://grafana.com/docs/loki/latest/send-data/promtail/)
- [LogQL - Lenguaje de Consultas de Loki](https://grafana.com/docs/loki/latest/query/)
