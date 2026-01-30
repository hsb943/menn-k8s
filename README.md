# 1. Kubernetes Microservices Deployment

---

## 1.1 Overview

This repository contains **Kubernetes manifests only** for deploying a simple **microservices-based application**. The focus is on **infrastructure, orchestration, and deployment design**, not application source code.

### 1.1.1 Tags

* Kubernetes (K8s)
* Docker (container images)
* GitHub Actions
* GitOps using ArgoCD
* Kubernetes Deployments
* Kubernetes Services (ClusterIP)
* Kubernetes Ingress
* Microservices Architecture
* Infrastructure as Code (IaC)
* Cloud-Native
* CI/CD

The system includes:

1. Frontend service
2. Backend service
3. MongoDB database
4. Ingress for external access
5. CI workflow for manifest updates

---

## 1.2 Motivation

1. Demonstrate real-world Kubernetes usage beyond toy examples
2. Practice clean separation of **application code vs infrastructure**
3. Build GitHub-ready infra repos aligned with **GitOps workflows**

Why this approach:

* Production teams rarely mix app code and infra
* Kubernetes skills are judged on **manifests and design**, not YAML quantity

---

## 2. System Architecture

### 2.1 High-Level Flow

```
User → Ingress → Frontend → Backend → MongoDB
```

### 2.2 Component Responsibilities

1. **Ingress**

   * Entry point for external traffic
   * Routes requests to frontend service

2. **Frontend**

   * Handles client-facing logic
   * Communicates with backend via ClusterIP service

3. **Backend**

   * Contains business logic
   * Connects to MongoDB using internal DNS

4. **MongoDB**

   * Persistent data store
   * Deployed inside the cluster for simplicity

---

## 3. Repository Structure

### 3.1 Directory Layout

```
.
├── .github/workflows/
│   └── update-manifests.yaml
├── authentication-ingress.yaml
├── frontend.yaml
├── backend.yaml
├── mongodb-deployment.yaml
└── README.md
```

### 3.2 Why This Structure

1. Flat structure keeps learning-focused repos readable
2. One manifest file per logical component
3. CI kept separate under `.github/`

---

## 4. Kubernetes Resources Used

### 4.1 Core Resources

1. **Deployment**

   * Used for frontend, backend, and MongoDB
   * Chosen for simplicity and stateless focus

2. **Service (ClusterIP)**

   * Enables internal service discovery
   * Avoids unnecessary external exposure

3. **Ingress**

   * Centralized traffic routing
   * Cleaner than NodePort-based access

---

## 5. Deployment & Execution

### 5.1 Apply Manifests

```bash
kubectl apply -f mongodb-deployment.yaml
kubectl apply -f backend.yaml
kubectl apply -f frontend.yaml
kubectl apply -f authentication-ingress.yaml
```

### 5.2 Verification

```bash
kubectl get pods
kubectl get svc
kubectl get ingress
```

Why this order:

1. Database first
2. Backend depends on database
3. Frontend depends on backend
4. Ingress comes last

---

## 6. CI / GitOps Readiness

### 6.1 GitHub Actions Workflow

1. Tracks manifest changes in Git
2. Acts as the trigger point for GitOps flow
3. Keeps Git as the single source of truth

### 6.2 Argo CD Integration (Active)

1. Argo CD continuously watches this repository
2. Any YAML change is automatically detected
3. Drift between desired state (Git) and live cluster is reconciled
4. No manual `kubectl apply` required after initial bootstrap

Why Argo CD was chosen:

* Pull-based deployment model (more secure)
* Automatic drift detection
* Clear separation between CI (build/update) and CD (sync/apply)
* Flow:
1. Developer updates Kubernetes manifests
2. Changes are committed and pushed to Git
3. Git acts as the authoritative desired state
4. Argo CD continuously monitors the Git repository
5. Argo CD compares desired state with live cluster state
6. Drift is detected automatically
7. Argo CD applies changes to the Kubernetes cluster
8. Reconciliation continues until desired state is enforced

---

## 7. Design Decisions & Constraints

### 7.1 Key Decisions

1. **Manifest-only repo**

   * Mirrors real production setups

2. **MongoDB as Deployment**

   * Easier to reason about for learning
   * Avoids StatefulSet complexity initially

3. **No Secrets Included**

   * Prevents bad security practices
   * Keeps repo public-safe

> This section separates juniors from seniors.

---

## 8. Screenshots & Proof of Execution

### 8.1 Screenshots Location

```
screenshots/
```

<img width="1164" height="622" alt="user-interface-browser" src="https://github.com/user-attachments/assets/f031496f-ec78-4302-a621-428ddda2af0b" />

<img width="953" height="524" alt="updated-manifest-action" src="https://github.com/user-attachments/assets/87a8fbd3-f6e7-4280-9233-1fac2ff64f26" />


---

## 9. Observability & Monitoring

### 9.1 Monitoring Stack (Installed on Cluster)

1. Prometheus

   * Metrics collection from Kubernetes components
   * Resource-level visibility (CPU, memory)

2. Grafana

   * Dashboards for cluster and workload monitoring
   * Visualization of Prometheus metrics

Why this stack:

* De-facto standard for Kubernetes observability
* Cloud-agnostic and production-proven
* Works seamlessly with Kubernetes

---

## 10. License

MIT
