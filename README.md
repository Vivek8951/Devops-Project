# DevOps Observability Project

## Overview

This project demonstrates a **production-ready CI/CD pipeline** combined with a **complete observability stack**. It showcases containerization, automated deployments via GitHub Actions, and real-time monitoring using Prometheus, Grafana, and Jaeger for full system visibility.

**Repository:** [https://github.com/Vivek8951/Devops-Project](https://github.com/Vivek8951/Devops-Project)

---

## Project Components

### 1. CI/CD Pipeline (`ci-cd-pipeline`)
- **GitHub Actions Workflow** (`ci-cd-pipeline.yml`)
  - Automated code checkout and Docker build
  - Multi-stage Docker image building
  - Secure credentials management using GitHub Secrets
  - Direct push to Docker Hub with authentication
  - Built-in health checks and testing

- **Docker Integration**
  - Optimized Dockerfile with Alpine Linux base image
  - Minimal image footprint for faster deployment
  - Production-ready container setup

### 2. Observability System (`observability-system`)
- **Prometheus:** Time-series database for metrics collection and storage
- **Grafana:** Interactive dashboards for real-time visualization
- **Jaeger:** Distributed tracing system for request tracking
- **Loki:** (Optional) Centralized log aggregation
- **Sample Application:** Node.js app emitting logs, metrics, and traces

---

## Quick Start

### Prerequisites
- Docker and Docker Compose installed
- Git installed
- GitHub account (for CI/CD)

### Running the Project Locally

#### 1. Clone the Repository
```bash
git clone https://github.com/Vivek8951/Devops-Project.git
cd Devops-Project
```

#### 2. Start the Observability Stack
```bash
cd observability-system
docker-compose up
```

This command will start all services:
- App container on port 4000
- Prometheus on port 9090
- Grafana on port 3001
- Jaeger on port 16686
- Loki on port 3100

#### 3. Access the Services

**Grafana Dashboard:**
- URL: [http://localhost:3001](http://localhost:3001)
- Default Username: `admin`
- Default Password: `admin`
- Once logged in, add Prometheus as data source using `http://prometheus:9090`

**Prometheus UI:**
- URL: [http://localhost:9090](http://localhost:9090)
- Query metrics directly (e.g., `up`, `scrape_duration_seconds`)

**Jaeger Tracing UI:**
- URL: [http://localhost:16686](http://localhost:16686)
- No login required
- Search for traces by service and operation name

**Application Metrics Endpoint:**
- URL: [http://localhost:4000/metrics](http://localhost:4000/metrics)
- Exposes Prometheus-compatible metrics

---

## Architecture

```
┌─────────────────────────────────────────────────────┐
│             GitHub Actions CI/CD                     │
│  (Automated Build, Test, Push to Docker Hub)        │
└────────────────┬────────────────────────────────────┘
                 │
                 ▼
┌─────────────────────────────────────────────────────┐
│          Docker Compose Orchestration               │
├─────────────────────────────────────────────────────┤
│  ┌──────────────┐  ┌──────────────┐                │
│  │   App (4000) │  │ Prometheus   │                │
│  │  Node.js     │  │   (9090)     │                │
│  └──────┬───────┘  └──────┬───────┘                │
│         │                 │                         │
│         ├──────metrics───→│                         │
│         │                 │                         │
│  ┌──────▼────────────────▼────────┐                │
│  │      Grafana Dashboard (3001)  │                │
│  │   (Visualizes Metrics/Logs)    │                │
│  └─────────────────────────────────┘                │
│                                                      │
│  ┌──────────────────┐  ┌──────────────┐            │
│  │  Jaeger (16686)  │  │  Loki(3100)  │            │
│  │   Tracing UI     │  │  Log Storage │            │
│  └──────────────────┘  └──────────────┘            │
└─────────────────────────────────────────────────────┘
```

---

## File Structure

```
Devops-Project/
├── ci-cd-pipeline/
│   ├── app.js                          # Node.js application
│   ├── package.json                    # Dependencies
│   ├── Dockerfile                      # Container build configuration
│   ├── docker-compose.yml              # Local compose for CI/CD testing
│   └── .github/workflows/
│       └── ci-cd-pipeline.yml          # GitHub Actions workflow
│
├── observability-system/
│   ├── app.js                          # Sample app with metrics/traces
│   ├── package.json                    # App dependencies
│   ├── Dockerfile                      # App container image
│   ├── docker-compose.yml              # Full observability stack
│   ├── prometheus.yml                  # Prometheus scrape config
│   └── dashboards/
│       └── grafana-dashboard.json      # Exported Grafana dashboard
│
├── screenshots/
│   ├── grafana-dashboard.png           # Grafana metrics visualization
│   ├── jaeger-traces.png               # Jaeger trace UI
│   └── prometheus-metrics.png          # Prometheus query results
│
├── README.md                            # This file
├── PROJECT_REPORT.pdf                  # Detailed 1–2 page report
└── .gitignore
```

---

## Key Features

### CI/CD Pipeline
- **Automated Builds:** Every push triggers a Docker build
- **Secrets Management:** Safe credential handling via GitHub Secrets
- **Docker Hub Integration:** Images pushed automatically
- **Health Checks:** Containers tested before deployment
- **Reproducible Deployments:** Consistent environments

### Observability Stack
- **Real-time Metrics:** Prometheus scrapes every 15 seconds
- **Interactive Dashboards:** Grafana visualizes trends and alerts
- **Distributed Tracing:** Jaeger tracks requests across services
- **Scalable Logging:** Loki aggregates logs from all containers
- **Production-Ready:** All services run in isolated containers

---

## Configuration Details

### Prometheus Configuration (`prometheus.yml`)
```yaml
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: 'app'
    static_configs:
      - targets: ['app:4000']
```

### Docker Compose Services
- **app:** Exposes metrics on `/metrics` endpoint
- **prometheus:** Scrapes metrics from app
- **grafana:** Connects to Prometheus via service discovery
- **jaeger:** All-in-one deployment for tracing
- **loki:** Receives logs from Docker containers

---

## Observability Queries & Dashboards

### Sample Prometheus Queries
- `up` – Check if services are running
- `scrape_duration_seconds` – Metric scrape duration
- `rate(http_requests_total[5m])` – Request rate
- Custom app metrics (if instrumented)

### Grafana Dashboard Panels
- Service uptime monitoring
- Request latency graphs
- Error rate tracking
- Resource utilization charts

### Jaeger Trace Information
- Request latency breakdown
- Service dependencies
- Error traces and stack traces
- Span duration analysis

---

## Troubleshooting

### Prometheus Connection Error in Grafana
**Error:** `connection refused`
**Solution:**
- Use `http://prometheus:9090` as data source URL (not localhost)
- Ensure all containers are running: `docker-compose ps`
- Restart services: `docker-compose restart`

### Docker Compose Build Fails
**Error:** `failed to build: Dockerfile not found`
**Solution:**
- Ensure `Dockerfile` exists in `observability-system/` directory
- Check file permissions: `ls -la Dockerfile`

### Port Already in Use
**Solution:**
- Change port mappings in `docker-compose.yml`
- Or kill existing process: `lsof -i :3001` and `kill -9 <PID>`

### Loki Container Exiting
**Error:** `Exit 1`
**Solution:**
- Check logs: `docker-compose logs loki`
- Verify `loki-config.yml` if custom config is used
- Use default image without custom config for simplicity

---

## Best Practices Implemented

✅ **Infrastructure as Code:** Docker Compose manages entire stack
✅ **CI/CD Automation:** GitHub Actions handles deployment
✅ **Secrets Management:** Credentials stored securely
✅ **Container Optimization:** Minimal Alpine-based images
✅ **Service Discovery:** Containers communicate via service names
✅ **Health Monitoring:** Prometheus + Grafana for visibility
✅ **Distributed Tracing:** End-to-end request tracking with Jaeger
✅ **Scalability:** Easy to extend with additional services

---

## Interview Preparation

This project covers:
- **DevOps:** CI/CD, containerization, orchestration
- **Monitoring:** Metrics collection, dashboarding, alerting
- **System Design:** Service communication, scalability
- **Automation:** GitHub Actions, infrastructure automation
- **Cloud-Native Concepts:** Microservices, observability

---

## Deployment to Production

To deploy to a production environment:

1. **Update credentials** in GitHub Secrets
2. **Configure persistent storage** for Prometheus/Grafana
3. **Set resource limits** in `docker-compose.yml`
4. **Enable TLS/SSL** for secure connections
5. **Configure alerting** rules in Prometheus
6. **Set up log retention** policies for Loki

---

## References & Documentation

- [Docker Compose Docs](https://docs.docker.com/compose/)
- [Prometheus Documentation](https://prometheus.io/docs/)
- [Grafana Documentation](https://grafana.com/docs/)
- [Jaeger Tracing](https://www.jaegertracing.io/docs/)
- [GitHub Actions Docs](https://docs.github.com/actions)

---

## License

This project is open-source and available for educational purposes.

---

## Contact & Support

For questions or improvements, please refer to the repository or open an issue on GitHub.

**Repository:** [https://github.com/Vivek8951/Devops-Project](https://github.com/Vivek8951/Devops-Project)

---

**Last Updated:** November 23, 2025