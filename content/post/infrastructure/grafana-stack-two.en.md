+++
title = "Monitoring notes with Grafana, Loki and Promtail - Configuration Examples"
author = "Fcch"
date = "2026-09-21"
description = "Log-based monitoring with the Grafana stack on on-premise servers"
featured = true
tags = [
    "grafana",
    "loki",
    "promtail",
    "monitoring",
    "linux"
]
categories = [
    "Infrastructure",
]
series = ["Servers"]
thumbnail = "images/grafana-stack/grafana-stack-logo-n2.png"
+++

In the first part, we talked about monitoring certain services; the article got quite long, and adding the example configurations would have made it much longer, so in this second part we leave several configuration examples for each service, with a short description.

<!--more-->

![](/images/grafana-stack/grafana-stack-logo-n2.png)

Before diving into each service, it is worth recalling the general flow. **Promtail** reads the log files and the systemd journal, assigns labels and sends everything to **Loki**, which stores and indexes them. **Grafana** queries Loki with **LogQL** to build dashboards. The examples in this part focus on two things:

- What each service must emit so that its logs are useful (log format, logging driver, paths).
- How Promtail collects and labels those logs so they can be filtered later in Grafana.

**Important note:** On Debian 13 (Trixie) `rsyslog` no longer exists by default, there is no `/var/log/syslog` or `/var/log/messages`. Everything from the system goes through `systemd-journald`, which is why several services are read with the `journal` type scraper.

## List of services to monitor

### 1. [SSH](https://www.openssh.org/)

SSH writes its events to the systemd journal through the `ssh.service` unit (on Debian the binary is `sshd`). There is no need to configure anything special in SSH to monitor it; it is enough to read the journal filtering by the corresponding unit.

To make sure that login attempts and source IPs are recorded with enough detail, it is worth reviewing the log level in `/etc/ssh/sshd_config`:

```bash
# /etc/ssh/sshd_config
LogLevel VERBOSE
```

With `VERBOSE`, the fingerprints of the keys used and more detail of each attempt are recorded. Apply the change:

```bash
sudo systemctl restart ssh
```

To check what SSH is emitting to the journal:

```bash
# Latest events from the ssh unit
journalctl -u ssh -n 50 --no-pager

# Follow failed attempts live
journalctl -u ssh -f | grep -i "failed\|invalid\|accepted"
```

In Grafana you can run LogQL queries like these:

```logql
# Failed SSH login attempts
{unit="ssh.service"} |= "Failed password"

# Successful logins
{unit="ssh.service"} |= "Accepted"

# Extract the source IP from each failed attempt
{unit="ssh.service"} |= "Failed password"
  | regexp "from (?P<ip>\\d+\\.\\d+\\.\\d+\\.\\d+)"
```

### 2. [Nginx](https://nginx.org/en/docs/index.html)

For Nginx the ideal is to have a predictable log format and to split the logs per domain, so Promtail can label each file with its `domain` and tell `access` from `error`.

Define a clear `log_format` in `/etc/nginx/nginx.conf` inside the `http` block:

```nginx
# /etc/nginx/nginx.conf (http block)
log_format monitor '$remote_addr - $remote_user [$time_local] '
                   '"$request" $status $body_bytes_sent '
                   '"$http_referer" "$http_user_agent" '
                   'rt=$request_time';
```

Then, in each server block, point the logs to per-domain paths so the `domain` label comes out clean:

```nginx
# /etc/nginx/sites-available/example.com
server {
    listen 80;
    server_name example.com;

    access_log /var/log/nginx/example.com-access.log monitor;
    error_log  /var/log/nginx/example.com-error.log warn;

    location / {
        proxy_pass http://127.0.0.1:8080;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

Validate and reload Nginx:

```bash
sudo nginx -t
sudo systemctl reload nginx
```

Example LogQL queries for the dashboard:

```logql
# Requests per second
sum(rate({job="nginx", log_type="access"}[1m]))

# HTTP status code count
sum by (status) (count_over_time({job="nginx", log_type="access"}[5m]))

# Only 5xx errors
{job="nginx", log_type="access"} | status =~ "5.."

# Nginx errors in real time
{job="nginx", log_type="error"}
```

### 3. [Fail2ban](https://fail2ban.readthedocs.io/en/latest/)

Fail2ban writes its actions to `/var/log/fail2ban.log`, that is where bans and detections come from. It is worth making sure of the log level in its configuration.

In `/etc/fail2ban/fail2ban.local` (so as not to touch the `.conf` that gets overwritten on updates):

```ini
# /etc/fail2ban/fail2ban.local
[Definition]
loglevel = INFO
logtarget = /var/log/fail2ban.log
```

A minimal jail example for SSH in `/etc/fail2ban/jail.local`:

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

Restart the service:

```bash
sudo systemctl restart fail2ban
sudo fail2ban-client status sshd
```

LogQL queries for the defense dashboard:

```logql
# Banned IPs
{job="fail2ban"} |= "Ban" != "Unban"

# Detected IPs (before the ban)
{job="fail2ban"} |= "Found"

# Ban trend over time
sum(count_over_time({job="fail2ban"} |= "Ban" != "Unban" [1h]))
```

### 4. [Docker](https://www.docker.com/)

As seen in the first part, to monitor containers we ask Docker to write its logs to the systemd journal by changing the **Logging Driver** from `json-file` to `journald`.

Reminder of the `/etc/docker/daemon.json` file:

```bash
sudo tee /etc/docker/daemon.json > /dev/null <<'EOF'
{
 "log-driver": "journald"
}
EOF
sudo systemctl restart docker
docker info --format '{{.LoggingDriver}}'   # Output: journald
```

With `journald`, Docker tags each line with metadata such as the container name and its image. Promtail exposes them as labels with `relabel_configs` on the same `journal` type scraper:

```yaml
# Promtail scrape_configs (fragment) - Docker via journal
- job_name: docker
  journal:
    path: /var/log/journal
    max_age: 12h
    labels:
      job: docker
      host: vps-01
  relabel_configs:
    # Only lines coming from Docker's journald driver
    - source_labels: ['__journal_container_name']
      target_label: container
    - source_labels: ['__journal_image_name']
      target_label: image
    # Drop entries that are not from containers
    - source_labels: ['__journal_container_name']
      regex: '^$'
      action: drop
```

LogQL queries for the containers dashboard:

```logql
# Logs from a specific container
{job="docker", container="my-app"}

# Log volume per container
sum by (container) (rate({job="docker"}[5m]))

# Search for errors across all containers
{job="docker"} |~ "(?i)error|fatal|panic"
```

## Applying the Promtail configuration

Every time the `scrape_configs` are edited, you have to validate and restart the agent:

```bash
# Restart Promtail
sudo systemctl restart promtail

# Check that there are no startup errors
sudo journalctl -u promtail -n 50 --no-pager

# Verify active targets from the Promtail API
curl -s http://localhost:9080/targets | head
```

If Loki is receiving data, in Grafana → Explore the labels (`job`, `unit`, `domain`, `container`) should already appear for filtering.

## Label summary per service

| Service  | Source                        | Promtail job    | Key labels                       |
| :------- | :---------------------------- | :-------------- | :------------------------------- |
| SSH      | journal (`ssh.service`)       | journal         | `unit`, `level`, `host`          |
| Nginx    | `/var/log/nginx/*-access.log` | nginx-access    | `domain`, `status`, `log_type`   |
| Nginx    | `/var/log/nginx/*-error.log`  | nginx-error     | `domain`, `log_type=error`       |
| Fail2ban | `/var/log/fail2ban.log`       | fail2ban        | `service=fail2ban`               |
| Docker   | journal (`journald` driver)   | docker          | `container`, `image`, `host`     |

## Conclusion

The key to good log-based monitoring is not only in the stack, but in each service emitting logs with a predictable format and in Promtail labeling them consistently. With well-defined labels (`unit`, `domain`, `status`, `container`) the LogQL queries become simple and the dashboards come together quickly.

In this part we covered the configuration examples for each monitored service. The configurations of the Grafana stack itself (Loki, `grafana.ini` and the full Promtail `config.yml`) I will leave on my side to complement these notes.

## References

- [Grafana](https://grafana.com/)
- [Loki - Official Documentation](https://grafana.com/docs/loki/latest/)
- [Promtail - scrape_configs Configuration](https://grafana.com/docs/loki/latest/send-data/promtail/configuration/)
- [LogQL - Loki Query Language](https://grafana.com/docs/loki/latest/query/)
- [Nginx - log module](https://nginx.org/en/docs/http/ngx_http_log_module.html)
- [Fail2ban - Official Documentation](https://fail2ban.readthedocs.io/en/latest/)
- [Docker - Logging Drivers](https://docs.docker.com/engine/logging/drivers/journald/)
