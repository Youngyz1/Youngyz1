# Registration App — AWS EKS

> Production-grade full-stack app (React 18 + FastAPI + PostgreSQL) deployed on AWS EKS with a complete DevSecOps pipeline, GitOps delivery via ArgoCD, Istio service mesh, and full observability.

![AWS](https://img.shields.io/badge/AWS-EKS-orange?logo=amazonaws)
![Terraform](https://img.shields.io/badge/IaC-Terraform-purple?logo=terraform)
![ArgoCD](https://img.shields.io/badge/GitOps-ArgoCD-teal)
![Security](https://img.shields.io/badge/DevSecOps-Trivy%20%7C%20SonarQube%20%7C%20OWASP-red)

---

## Architecture

```
GitHub push
    │
    ▼
GitHub Actions (CI)
SonarQube (SAST) → Trivy (image scan) → OWASP ZAP (DAST) → Docker Hub
    │
    ▼ image tag updated in manifests
ArgoCD (GitOps CD) detects change → syncs to EKS
    │
    ▼
AWS EKS Cluster
├── WAF v2                    — bot/DDoS protection
├── Istio mTLS                — encrypted service mesh
├── React 18 (frontend)
├── FastAPI (backend)
├── PostgreSQL RDS Multi-AZ   — managed, high-availability DB
├── HPA (3–10 replicas)       — auto-scaling
├── External Secrets Operator — zero secrets in Git
└── Velero                    — automated backups
    │
    ▼
Observability: Prometheus → Grafana → Loki → Alertmanager → Slack
```

> All infrastructure provisioned with Terraform (~72 resources).
> IAM IRSA, private subnets, Secrets Manager, encrypted at rest.

---

## Stack

| Layer | Technology |
|---|---|
| Frontend | React 18 + Nginx |
| Backend | FastAPI (Python 3.9) |
| Database | PostgreSQL 15 (RDS Multi-AZ) |
| Orchestration | Kubernetes (EKS) + Helm |
| GitOps CD | ArgoCD |
| CI Pipeline | GitHub Actions (OIDC, self-hosted runners) |
| IaC | Terraform (~72 resources) |
| Security | WAF v2 · Istio mTLS · ESO · Trivy · SonarQube · OWASP ZAP |
| Observability | Prometheus · Grafana · Loki · Alertmanager |
| Backup | Velero |

---

## Repo structure

```
registration-app-EKS/
├── .github/workflows/    # GitHub Actions CI pipeline
├── frontend/             # React 18 app
├── backend/              # FastAPI app
├── k8s/                  # Kubernetes manifests
│   ├── namespace/        # Namespace, Secrets, ConfigMap
│   ├── postgres/         # PostgreSQL Deployment + PVC
│   ├── backend/          # Backend Deployment + Service
│   └── frontend/         # Frontend Deployment + Service
├── argocd/               # ArgoCD Application manifest
├── helm/                 # Helm chart
├── kind-config.yaml      # Local kind cluster config
└── sonar-project.properties
```

---

## Quick start (local — kind)

```bash
kind create cluster --config kind-config.yaml
kubectl apply -f k8s/namespace/
kubectl apply -f k8s/postgres/
kubectl apply -f k8s/backend/
kubectl apply -f k8s/frontend/
# Frontend available at http://localhost:30080
```

---

## GitHub Actions secrets required

| Secret | Purpose |
|---|---|
| `DOCKER_USERNAME` | Docker Hub login |
| `DOCKER_PASSWORD` | Docker Hub password |
| `SONAR_TOKEN` | SonarQube auth |
| `SONAR_HOST_URL` | SonarQube server |
| `SNYK_TOKEN` | Snyk dependency scan |
| `SLACK_WEBHOOK_URL` | Pipeline notifications |
| `APP_URL` | Target URL for OWASP ZAP DAST |
| `REACT_APP_API_URL` | Backend API URL for frontend build |

---

## Author

[Ohia Uche Godwill](https://github.com/Youngyz1) — Cloud & DevOps Engineer
[LinkedIn](https://www.linkedin.com/in/ohia-uche-10ba3b223) · [Portfolio](https://github.com/Youngyz1)
