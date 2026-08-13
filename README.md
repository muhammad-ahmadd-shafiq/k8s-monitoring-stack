# k8s-monitoring-stack

Production-ready Kubernetes observability stack built on **Prometheus** and **Grafana**, deployed via Helm. Spin up cluster-wide metrics collection, alerting, and pre-built dashboards in minutes.

![Kubernetes](https://img.shields.io/badge/Kubernetes-326CE5?style=flat&logo=kubernetes&logoColor=white)
![Helm](https://img.shields.io/badge/Helm-0F1689?style=flat&logo=helm&logoColor=white)
![Prometheus](https://img.shields.io/badge/Prometheus-E6522C?style=flat&logo=prometheus&logoColor=white)
![Grafana](https://img.shields.io/badge/Grafana-F46800?style=flat&logo=grafana&logoColor=white)
![License](https://img.shields.io/badge/license-MIT-blue)

---

## Overview

This repository provides a ready-to-deploy monitoring stack for Kubernetes clusters using the [`kube-prometheus-stack`](https://github.com/prometheus-community/helm-charts/tree/main/charts/kube-prometheus-stack) Helm chart. It bundles:

- **Prometheus** — metrics collection and storage
- **Grafana** — dashboards and visualization
- **Alertmanager** — alert routing (Slack / email / webhook)
- **Node Exporter** — node-level system metrics
- **kube-state-metrics** — Kubernetes object state metrics

Custom Helm values tuned for lightweight clusters running on k3s, giving you cluster health, resource usage, and pod-level metrics out of the box, with dashboards and alerting wired up from a single install.

## Features

- Cluster-wide monitoring
- Resource utilization dashboards
- Prometheus metrics collection
- Grafana visualization
- Alertmanager integration
- Helm-based deployment
- Persistent storage support
- Production-ready architecture

## Architecture

```
                ┌─────────────────┐
                │     Grafana      │  ← dashboards / visualization
                └────────┬─────────┘
                         │
                ┌────────▼─────────┐
                │    Prometheus     │  ← scrapes & stores metrics
                └───┬─────────┬────┘
                    │         │
        ┌───────────▼─┐   ┌───▼─────────────┐
        │ Node Exporter│   │ kube-state-metrics│
        │ (per node)   │   │ (cluster objects) │
        └──────────────┘   └────────────────────┘
                    │
             ┌──────▼──────┐
             │ Alertmanager │  ← Slack / email / webhook alerts
             └─────────────┘
```

## Repository Structure

```text
k8s-monitoring-stack/
├── values.yaml
├── screenshots/
├── LICENSE
└── README.md
```

## Prerequisites

- A running Kubernetes cluster — tested on **k3s**
- [Helm 3](https://helm.sh/docs/intro/install/)
- `kubectl` configured with cluster access
- (Optional) [Ingress controller](https://kubernetes.github.io/ingress-nginx/) if exposing dashboards externally

## Quick Start

```bash
# Clone the repo
git clone https://github.com/muhammad-ahmadd-shafiq/k8s-monitoring-stack.git
cd k8s-monitoring-stack

# Add the Prometheus community Helm repo
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update

# Create a dedicated namespace
kubectl create namespace monitoring

# Install the stack with custom values
helm install monitoring prometheus-community/kube-prometheus-stack \
  -n monitoring \
  -f values.yaml
```

## Verify Installation

```bash
kubectl get pods -n monitoring
kubectl get svc -n monitoring
helm list -n monitoring
```

## Accessing the Dashboards

Port-forward Grafana locally:

```bash
kubectl port-forward -n monitoring svc/monitoring-grafana 3000:80
```

Then open [http://localhost:3000](http://localhost:3000).

| Service      | Default Credentials         |
|--------------|------------------------------|
| Grafana      | `admin` / `prom-operator` *(change via values.yaml)* |
| Prometheus   | No auth by default — restrict via NetworkPolicy/Ingress |

## Configuration

Key values you'll likely want to override in `values.yaml`:

```yaml
grafana:
  adminPassword: "<set-a-real-password>"
  persistence:
    enabled: true
    size: 5Gi

prometheus:
  prometheusSpec:
    retention: 15d
    resources:
      requests:
        cpu: 250m
        memory: 512Mi

alertmanager:
  config:
    receivers:
      - name: "slack-notifications"
        slack_configs:
          - api_url: "<slack-webhook-url>"
            channel: "#alerts"
```

## Screenshots

![Grafana Dashboard](screenshots/grafana-dashboard.png)
![Cluster Metrics](screenshots/cluster-metrics.png)

## Technologies Used

- Kubernetes
- Helm
- Prometheus
- Grafana
- Alertmanager
- kube-state-metrics
- Node Exporter
- YAML
- Git

## Roadmap

- [ ] Add pre-built custom Grafana dashboards (JSON)
- [ ] Add PrometheusRule CRDs for custom alerting thresholds
- [ ] Ingress + TLS setup for external dashboard access
- [ ] Loki integration for log aggregation
- [ ] Terraform module for cluster provisioning

## Uninstall

```bash
helm uninstall monitoring -n monitoring
kubectl delete namespace monitoring
```

## Contributing

Issues and PRs are welcome. Please open an issue first for major changes.

## License

This project is licensed under the [MIT License](LICENSE).

## Author

**Muhammad Ahmad Shafiq**

Aspiring DevOps Engineer focused on Kubernetes, Cloud Infrastructure, CI/CD, Monitoring, and Automation.

GitHub: [https://github.com/muhammad-ahmadd-shafiq](https://github.com/muhammad-ahmadd-shafiq)
LinkedIn: [https://www.linkedin.com/in/muhammad-ahmad-11b220428](https://www.linkedin.com/in/muhammad-ahmad-11b220428)
