# 🚀 Spring Boot + MySQL on Kubernetes

A production-grade Kubernetes deployment for a Spring Boot application backed by MySQL, built with security, scalability, and automation in mind.

---

## 📆 Stack Overview

| Layer                  | Tech/Tool |
|------------------------|-----------|
| Application            | Spring Boot (Java 17) |
| Database               | MySQL (StatefulSet + PVC) |
| Secrets Management     | AWS Secrets Manager + External Secrets Operator |
| Kubernetes Templating  | Helm |
| Env-specific Overrides | Kustomize |
| CI/CD Pipeline         | GitHub Actions |
| Observability          | Prometheus-ready integration hooks |

---

## ✨ Key Features

| Feature                         | Description |
|----------------------------------|-------------|
| ✅ Dockerized Spring Boot        | With Flyway DB migrations |
| ✅ MySQL via StatefulSet         | With persistent volumes |
| ✅ AWS Secrets Manager           | Centralized secret storage |
| ✅ External Secrets Operator     | Automatic sync of AWS secrets to K8s |
| ✅ Helm Charts                   | Clean, reusable templating |
| ✅ Kustomize Overlays            | Per-environment configurations (dev, staging, prod) |
| ✅ TLS via cert-manager + Let's Encrypt | Secure HTTPS via Ingress |
| ✅ Ingress (NGINX)               | With routing, HTTPS, and rewrite support |
| ✅ Horizontal Pod Autoscaler     | Auto-scales app based on CPU |
| ✅ CI/CD Pipeline                | GitHub Actions with multi-stage flow |
| ✅ Secure Image Handling         | Versioned image tags, no `latest` used |
| ✅ Health Probes & Limits        | Readiness/liveness probes and resource limits |

---

## 📂 Folder Structure

```bash
k8s/
├── base/                          # Common base manifests for Kustomize
│   ├── deployment.yaml
│   ├── mysql-service.yaml
│   ├── mysql-statefulset.yaml
│   ├── service.yaml
│   └── kustomization.yaml

├── overlays/                      # Environment-specific overlays (Kustomize)
│   ├── dev/
│   │   ├── configmap.yaml
│   │   ├── kustomization.yaml
│   │   ├── mysql-external-secret.yaml
│   │   ├── namespace.yaml
│   │   ├── secret-store.yaml
│   │   └── cluster-issuer.yaml    # cert-manager ClusterIssuer (staging/prod)
│   └── staging/
│   └── prod/

├── helm/                          # Helm chart for full app deployment
│   ├── Chart.yaml
│   ├── values.yaml                # Default config
│   └── templates/
│       ├── _helpers.tpl
│       ├── configmap-mysql.yaml
│       ├── secret-external-mysql.yaml
│       ├── secretstore-aws.yaml
│       ├── serviceaccount.yaml
│       ├── mysql/
│       │   ├── service-mysql-headless.yaml
│       │   └── statefulset-mysql.yaml
│       ├── springboot/
│       │   ├── deployment-springboot.yaml
│       │   ├── hpa-springboot.yaml
│       │   ├── ingress-springboot.yaml
│       │   └── service-springboot.yaml
│       └── tests/
│           └── test-connection.yaml

├── values/                        # Helm values per environment
│   ├── dev-values.yaml
│   ├── staging-values.yaml
│   └── prod-values.yaml

.github/
└── workflows/
    └── crewmeister-ci.yaml       # CI/CD pipeline definition
```

---

## 🛠️ Usage Guide

### ✅ Apply Kustomize Overlay (Dev)

```bash
kubectl apply -k k8s/overlays/dev
```

### ❌ Delete Dev Resources

```bash
kubectl delete -k k8s/overlays/dev
```

---

### 🥤 Helm Usage

```bash
# Install
helm install my-app ./k8s/helm -f ./k8s/values/dev-values.yaml

# Upgrade
helm upgrade my-app ./k8s/helm -f ./k8s/values/dev-values.yaml

# Uninstall
helm uninstall my-app
```

---

### 🌐 Port Forward for Local Access

```bash
kubectl port-forward svc/my-app-service -n dev 8080:80
```

Then access: [http://localhost:8080](http://localhost:8080)

---

## 🔐 AWS Secrets Setup

Before deploying, manually create the AWS credentials secret in your cluster:

```bash
kubectl create secret generic aws-credentials \
  --from-literal=access-key-id=<AWS_ACCESS_KEY_ID> \
  --from-literal=secret-access-key=<AWS_SECRET_ACCESS_KEY>
```

> **Note**: Never commit your AWS credentials to Git or store them in `values.yaml`.

---

## ⚙️ GitHub Actions CI/CD (`.github/workflows/crewmeister-ci.yaml`)

| Stage         | Description |
|---------------|-------------|
| `build`       | Compiles the app with Maven |
| `code-quality`| Runs linting/tests |
| `docker`      | Builds versioned image, pushes to Docker Hub |
| `updatek8s`   | Deploys Helm chart to EKS using OIDC-authenticated GitHub Action |

---

## 🌟 Observability & Reliability

- ✅ `readinessProbe` and `livenessProbe` configured
- ✅ `resources.requests` and `limits` defined for CPU/memory
- ✅ Horizontal Pod Autoscaler enabled for autoscaling
- ✅ Ingress TLS via cert-manager (staging & prod)
- ✅ Prepped for Prometheus metrics

---

## ✅ Best Practices Followed

- No use of `:latest` tag in Docker builds
- All secrets pulled dynamically via AWS Secret Manager
- Values managed via Helm `values.yaml`
- GitOps-compatible structure via Kustomize overlays
- Externalized environment handling
- Linted and validated YAML
- Modular and extensible folder layout

