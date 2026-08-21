# GitOps Deployment Repository (`GITOPS`)

This repository contains the declarative Kubernetes deployment manifests for managing the **Project Management Web Application** deployment state across Kubernetes environments (e.g., AWS EKS, Minikube, ArgoCD, Flux CD).

---

## 📁 Repository Structure

```text
GITOPS/
├── deployment.yaml   # Kubernetes Deployment (Replicas, Container Spec, Health Probes)
├── service.yaml      # Kubernetes LoadBalancer Service
├── ingress.yaml      # AWS ALB / Ingress Controller Routing Configuration
└── README.md         # GitOps documentation
```

---

## 🔄 GitOps Workflow Integration

1. **Application Repository**: [`https://github.com/someshtarra/project-management.git`](https://github.com/someshtarra/project-management.git)
   - Contains Java/Spring Boot application source code, unit tests, and `Dockerfile`.
   - CI Pipeline builds and pushes Docker image `someshtarra/project-management:latest`.

2. **GitOps Repository**: [`https://github.com/someshtarra/GITOPS.git`](https://github.com/someshtarra/GITOPS.git)
   - Contains Kubernetes target state (`deployment.yaml`, `service.yaml`, `ingress.yaml`).
   - CD Operator (e.g., **ArgoCD** or **Flux**) continuously syncs cluster state with this repository.

---

## 🚀 Manual Deployment Commands

```bash
# Clone GitOps repository
git clone https://github.com/someshtarra/GITOPS.git
cd GITOPS

# Apply all manifests to Kubernetes cluster
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
kubectl apply -f ingress.yaml

# Verify deployment status
kubectl get pods -l app=project-management
kubectl get svc project-management-service
```
