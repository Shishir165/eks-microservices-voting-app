# Voting App on Amazon EKS (Gateway API + CI/CD)

## Overview
This project deploys a microservices-based Voting Application on **Amazon EKS** using modern Kubernetes best practices.  
Traffic is exposed using the **Kubernetes Gateway API** backed by an **AWS Network Load Balancer**, with automated deployments via **GitHub Actions CI/CD**.

---

## Architecture

**Microservices**
- **Vote** – Python (Flask + Gunicorn)
- **Worker** – .NET background service
- **Result** – Node.js
- **Redis** – In-memory queue
- **PostgreSQL** – Persistent data store

**Platform & Tooling**
- Amazon EKS
- Kubernetes Gateway API (NGINX Gateway Fabric)
- AWS Network Load Balancer
- GitHub Actions (CI/CD)
- Docker Hub (prebuilt images)
- Kubernetes Secrets
- Liveness & Readiness Probes

---

## Traffic Flow
Internet
↓
AWS Network Load Balancer
↓
Kubernetes Gateway
↓
HTTPRoutes
↓
Services
↓
Pods (Vote / Worker / Result)
↓
Redis + PostgreSQL

---

## Endpoints

| Path | Description |
|-----|-------------|
| `/` | Vote application |
| `/vote` | Vote application |
| `/result` | Live vote results |

---

## Kubernetes Features Implemented

### Gateway API
- Replaced legacy Ingress
- Path-based routing using `HTTPRoute`
- AWS-managed Load Balancer

### Health Checks
- **Vote / Result**: HTTP liveness & readiness probes
- **Worker**: Exec-based probes
- **PostgreSQL**: `pg_isready`
- **Redis**: `redis-cli ping`

### Secrets
Database credentials stored securely using Kubernetes Secrets:
- `POSTGRES_USER`
- `POSTGRES_PASSWORD`
- `POSTGRES_DB`

Injected into Postgres and Worker deployments.

---

## CI/CD Pipeline (GitHub Actions)

On every push to `main`:
1. Authenticate to AWS
2. Update kubeconfig for EKS
3. Deploy Kubernetes manifests:
   - `infra`
   - `backend`
   - `frontend`
   - `gateway`

No manual kubectl actions required.

---

## Repository Structure
.github/
└── workflows/
└── ci-cd.yml

k8s/
├── infra/
│ ├── redis.yaml
│ └── db-deployment.yaml
├── backend/
│ └── worker-deployment.yaml
├── frontend/
│ ├── vote-deployment.yaml
│ ├── vote-service.yaml
│ ├── result-deployment.yaml
│ └── result-service.yaml
└── gateway/
├── gateway.yaml
├── root-route.yaml
├── vote-httproute.yaml
└── result-httproute.yaml

---

Deployed a microservices voting application on Amazon EKS using Kubernetes Gateway API, secured credentials with Kubernetes Secrets, added health checks, and automated deployments using GitHub Action
