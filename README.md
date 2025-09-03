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
- **Alloy**: Modern observability pipeline for logs and metrics collection
- **Cadvisor**: Container resource usage and performance monitoring
- **Node Exporter**: Host system metrics collection
- **PushGateway**: Metrics gateway for short-lived jobs
- **OpenTelemetry Collector**: Unified telemetry data collection and processing

## Prerequisites

- Docker Engine >= 1.13
- Docker Compose >= 1.11

## Quick Start

1. Create necessary data directories:
```bash
mkdir -p data/{alertmanager,alloy,grafana,loki,prometheus,tempo}
```

Maybe you need to change the owner of the data directories:
```bash
chown -R 1000:1000 data
```

2. Clone the repository and set up the environment:
```bash
git clone https://github.com/iamnivekx/dockotlp.git
cd dockotlp
cp grafana/grafana.ini.sample grafana/grafana.ini
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

- **Grafana**: `http://<host-ip>:3000`
- **Prometheus**: `http://<host-ip>:9090`
- **AlertManager**: `http://<host-ip>:9093`
- **Loki**: `http://<host-ip>:3100`
- **Tempo**: `http://<host-ip>:3200`
- **OpenTelemetry Collector**:
  - OTLP gRPC: `http://<host-ip>:4317`
  - OTLP HTTP: `http://<host-ip>:4318`
  - Health Check: `http://<host-ip>:13133`
  - Metrics: `http://<host-ip>:8888`
- **Alloy**: `http://<host-ip>:12345`
- **Node Exporter**: `http://<host-ip>:9100`
- **PushGateway**: `http://<host-ip>:9091`
- **cAdvisor**: `http://<host-ip>:8080`

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

### OpenTelemetry Collector Configuration

OpenTelemetry Collector is the central component for receiving, processing, and exporting telemetry data. It supports multiple protocols and formats.

**Key features:**
- **OTLP Protocol**: Native OpenTelemetry protocol support
- **Multiple Receivers**: gRPC, HTTP, Jaeger, Zipkin
- **Data Processing**: Filtering, transformation, and routing
- **Multiple Exporters**: Prometheus, Loki, Tempo, and more

Configuration is managed in `otel/otel-collector.yaml`. The collector is configured to:
- Receive OTLP data on ports 4317 (gRPC) and 4318 (HTTP)
- Export metrics to Prometheus
- Export logs to Loki
- Export traces to Tempo

### Alloy Configuration

Alloy is a modern observability pipeline that replaces Promtail for log collection. It provides:
- **Docker Log Collection**: Automatic container log discovery
- **Log Processing**: Parsing, filtering, and transformation
- **Multiple Outputs**: Loki, Prometheus, and other backends

Configuration is managed in `alloy/config.alloy`. Key features:
- Automatic Docker container log discovery
- Log parsing and enrichment
- Metrics extraction from logs
- Flexible routing and filtering

### Node Exporter

Node Exporter collects host system metrics including:
- CPU, memory, and disk usage
- Network statistics
- File system metrics
- System load and processes

The exporter runs with host network access to collect system-level metrics and is automatically discovered by Prometheus.

### PushGateway

PushGateway is used for metrics from short-lived jobs that cannot be scraped by Prometheus. It:
- Accepts metrics via HTTP POST requests
- Stores metrics until they are scraped by Prometheus
- Supports batch job monitoring
- Provides a bridge for ephemeral services

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
