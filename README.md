# ☸️ GitOps Configuration Repository

**GitOps config repository for AKS deployments managed via ArgoCD. Acts as the source of truth for Kubernetes manifests across dev and prod environments.**

---

## 🔄 How It Connects to the Main Repository

This repository implements the **split-repo GitOps pattern**. 
Instead of CI tools directly applying changes to the Kubernetes cluster (`kubectl apply`), the architecture is decoupled:

1. **Continuous Integration (Main Repo)**: Developers push code to the application repository. The CI pipeline runs quality gates, builds the Docker image, runs vulnerability scans, signs it, and pushes it to the container registry.
2. **Automated Commits**: As the final step in the CI pipeline, an automated commit is pushed to *this* GitOps repository, updating the image tag.
3. **Continuous Deployment (ArgoCD)**: ArgoCD constantly monitors this repository. When it detects the new commit with the updated image tag, it automatically syncs the changes to the AKS cluster.

This separation of concerns ensures that the Kubernetes cluster only needs read access to this repository, rather than granting administrative cluster credentials to the CI pipeline.

---

## 🔁 GitOps Flow Diagram

```text
👩‍💻 Code Push (Main Repo) 
      │
      ▼
⚙️ GitHub Actions CI Pipeline (Build, Scan, Push, Sign)
      │
      ▼
🤖 Automated Commit: Updates image tags in THIS repo
      │
      ▼
🦑 ArgoCD detects diff in Git state vs Cluster state
      │
      ▼
⛵ Syncs manifests to Azure Kubernetes Service (AKS) automatically
```

---

## 📁 Folder Structure

```
gitops-deployments/
├── dev/                  # Development environment manifests
├── prod/                 # Production environment manifests
├── app.yaml              # ArgoCD Application configuration for Dev
└── app-prod.yaml         # ArgoCD Application configuration for Prod
```

---

## 📸 Deployment Gallery

### 1. Continuous Deployment (ArgoCD)
*(ArgoCD UI showing the applications in a Synced & Healthy state across environments)*
![ArgoCD Synced](docs/screenshots/argocd-all-environments.png)

### 2. CI/CD Pipeline Success (Main Repo)
*(Full pipeline run showing all green jobs)*
![Pipeline Overview](docs/screenshots/pipeline-overview.png)

### 3. Automated Sync History
*(ArgoCD sync history showing automated deployments triggered by CI updates)*
![ArgoCD Sync History](docs/screenshots/argocd-sync-history.png)
