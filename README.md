# Voting App on Amazon EKS (Gateway API + CI/CD)

## Overview
A microservices-based voting application deployed on Amazon EKS using Kubernetes.
Traffic is exposed via the Kubernetes Gateway API backed by an AWS Network Load Balancer.
CI/CD is implemented with GitHub Actions.

## Architecture
- Vote frontend: Python (Flask + Gunicorn)
- Worker: .NET
- Result frontend: Node.js
- Data stores: Redis, PostgreSQL
- Platform: Amazon EKS
- Traffic: Kubernetes Gateway API (NGINX Gateway Fabric)
- CI/CD: GitHub Actions

## Traffic Flow
Internet → AWS NLB → Gateway → HTTPRoute → Service → Pod

## Endpoints
- `/` → Vote app
- `/vote` → Vote app
- `/result` → Result app

## CI/CD
On every push to `main`:
- GitHub Actions authenticates to AWS
- Updates kubeconfig for EKS
- Applies all Kubernetes manifests in `k8s/`

## Deployment
```bash
kubectl apply -f k8s/