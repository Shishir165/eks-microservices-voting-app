🚀 EKS Microservices Voting App (Gateway API Edition)
📌 Project Overview

            HEAD
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

This project demonstrates a cloud-native microservices application deployed on Amazon EKS, using:

🐳 Docker

☸️ Kubernetes

🌐 Gateway API (NGINX Gateway Fabric)

⚡ Redis

🐘 PostgreSQL

🔁 CI/CD with GitHub Actions

🌍 Route 53 DNS

The application allows users to vote between two options and view live results.

🏗 Architecture
User
  |
  v
AWS Network Load Balancer
  |
  v
Kubernetes Gateway API
  |
  +--> /vote  ---> Vote Service (Python/Flask)
  |                 |
  |                 v
  |              Redis
  |                 |
  |                 v
  |             Worker (.NET)
  |                 |
  |                 v
  +--> /result --> PostgreSQL --> Result Service (Node.js)
🧱 Services
🗳 Vote Service

Built with Flask

Writes votes to Redis

Exposed via /vote

⚙ Worker Service

Consumes votes from Redis

Writes results to PostgreSQL

📊 Result Service

Reads aggregated results from PostgreSQL

Exposed via /result

🗄 Redis

Temporary vote storage

🐘 PostgreSQL

Persistent vote storage

☸ Kubernetes Setup
Deployments

vote

result

worker

redis

db

Services

ClusterIP for internal communication

Gateway API for external traffic

🌐 Traffic Management (Gateway API)

Instead of traditional Ingress, this project uses Kubernetes Gateway API.

Gateway

Single HTTP listener (port 80)

Backed by AWS Network Load Balancer

HTTPRoutes

/vote → vote service

/result → result service

This provides:

Clear separation of traffic rules

Modern Kubernetes networking model

Scalable and cloud-native routing

🌍 DNS Configuration

Route 53 is configured to point:

vote.diogohack.shop
result.diogohack.shop

➡ to the AWS Network Load Balancer created by the Gateway.

🔁 CI/CD Pipeline (GitHub Actions)

On every push to main:

Build Docker images

Push images to Docker Hub

Configure AWS credentials

Update kubeconfig

Apply Kubernetes manifests

This ensures automatic deployment to EKS.

🔐 Secrets Management

Database credentials are stored securely using Kubernetes Secrets instead of hardcoding them in manifests.

Example:

env:
  - name: POSTGRES_USER
    valueFrom:
      secretKeyRef:
        name: db-credentials
        key: POSTGRES_USER

This keeps sensitive information outside version control.

🔒 HTTPS / TLS (Design Decision)

HTTPS was evaluated using cert-manager and Let's Encrypt.

However, HTTP-01 validation with Gateway API is not fully stable with the selected controller.

To maintain a stable production-ready setup, the application currently runs over HTTP.

TLS can be added in the future using:

Ingress + cert-manager

AWS ACM

CloudFront

DNS-01 validation

🧪 How to Access
http://vote.diogohack.shop
http://vote.diogohack.shop/result
📦 Local Development (Optional)

Run with Docker Compose:

docker-compose up --build
🧠 Key Learnings

Deploying microservices on EKS

Using Gateway API instead of Ingress

Implementing CI/CD to Kubernetes

Configuring AWS Load Balancer Controller

Managing DNS with Route 53

Handling real-world TLS challenges

Production decision-making in cloud environments

🚀 Future Improvements

Add HTTPS via ACM or Ingress

Add monitoring (Prometheus + Grafana)

Add Horizontal Pod Autoscaling

Add Blue/Green deployment strategy

Add centralized logging

🏁 Project Status

✅ Fully functional
✅ Deployed on EKS
✅ Automated CI/CD
✅ DNS configured
✅ Production-stable routing
42373ef (Update README and clean Gateway (remove HTTPS))
