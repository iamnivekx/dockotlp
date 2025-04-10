# DockOTLP

A comprehensive monitoring and OpenTelemetry solution for Docker environments, integrating Prometheus, Grafana, Loki, Promtail, Tempo, and AlertManager for complete observability.

> This project is based on [dockprom](https://github.com/stefanprodan/dockprom) by Stefan Prodan, with additional OpenTelemetry integration and enhanced observability features.

## Overview

DockOTLP provides a complete observability stack for Docker hosts and containers, featuring:

- **Grafana**: Visualization and analytics platform
- **Prometheus**: Time-series database for metrics
- **AlertManager**: Alert management and notification system
- **Loki**: Log aggregation system
- **Tempo**: Distributed tracing backend
- **Alloy**: Modern observability pipeline
- **Cadvisor**: Container resource usage and performance monitoring

## Prerequisites

- Docker Engine >= 1.13
- Docker Compose >= 1.11

## Quick Start

1. Create necessary data directories:
```bash
mkdir -p data/{alertmanager,alloy,grafana,loki,prometheus,tempo}
```

2. Clone the repository and set up the environment:
```bash
git clone https://github.com/iamnivekx/dockotlp.git
cd dockotlp
cp .env.sample .env
```

3. Configure your environment variables in `.env`:
```bash
GF_SECURITY_ADMIN_USER=admin
GF_SECURITY_ADMIN_PASSWORD=changeme
GF_USERS_ALLOW_SIGN_UP=false
GF_SERVER_ROOT_URL=https://grafana.example.com
```

4. Start the services:
```bash
docker-compose up -d
```

## Service Endpoints

- OpenTelemetry Collector: `http://<host-ip>:4317`
- Prometheus: `http://<host-ip>:9090`
- AlertManager: `http://<host-ip>:9093`
- Grafana: `http://<host-ip>:3000`
- Loki: `http://<host-ip>:3100`
- Promtail: `http://<host-ip>:9080`
- Tempo: `http://<host-ip>:3200`

## Configuration

### Grafana Setup

1. Access Grafana at `http://<host-ip>:3000`
2. Default credentials:
   - Username: admin
   - Password: changeme

You can customize Grafana settings in the docker-compose file:

```yaml
grafana:
  image: grafana/grafana:10.2.2
  restart: unless-stopped
  environment:
    - GF_SERVER_ROOT_URL=${GF_SERVER_ROOT_URL}
    - GF_SECURITY_ADMIN_USER=${GF_SECURITY_ADMIN_USER:-admin}
    - GF_SECURITY_ADMIN_PASSWORD=${GF_SECURITY_ADMIN_PASSWORD:-changepass}
    - GF_USERS_ALLOW_SIGN_UP=${GF_USERS_ALLOW_SIGN_UP:-false}
```

### Loki Configuration

Loki settings can be configured in `loki/loki.yaml`. For detailed configuration options, refer to the [official documentation](https://grafana.com/docs/loki/latest/configure/examples/).

### Tempo Configuration

Tempo settings can be configured in `tempo/tempo.yaml`. For detailed configuration options, refer to the [official documentation](https://grafana.com/docs/tempo/latest/configuration/manifest/).

### AlertManager Setup

AlertManager handles alert routing and notifications. Configure notification receivers in `alertmanager/config.yaml`.

#### Slack Integration Example

To set up Slack notifications:

1. Create a custom integration in your Slack workspace using "incoming webhooks"
2. Update the AlertManager configuration:

```yaml
route:
  receiver: "slack"

receivers:
  - name: "slack"
    slack_configs:
      - send_resolved: true
        text: "{{ .CommonAnnotations.description }}"
        username: "Prometheus"
        channel: "#<channel>"
        api_url: "https://hooks.slack.com/services/<webhook-id>"
```

## Related Projects

- [dockprom](https://github.com/stefanprodan/dockprom) - Original Docker monitoring solution by Stefan Prodan
- [OpenTelemetry](https://github.com/open-telemetry) - Cloud-native observability framework

## License

[MIT](LICENSE)
