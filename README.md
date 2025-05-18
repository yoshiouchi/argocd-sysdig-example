# How to set up Sysdig Shield with ArgoCD App of Apps

This guide shows you how to deploy Sysdig Shield (Cluster Shield and Host Shield) in a Kubernetes cluster using ArgoCD’s App of Apps pattern. Sysdig Shield is typically deployed alongside other applications in a cluster, so this example provides an easy and repeatable way to deploy Sysdig Shield together with your other workloads.

---

## What is the App of Apps Pattern?

The **App of Apps** pattern in ArgoCD is a strategy for managing complex deployments by creating a "parent" ArgoCD application that manages multiple "child" applications. Each child application is defined in its own manifest and can represent a separate microservice, infrastructure component, or any deployable unit. This pattern enables GitOps best practices, modular architecture, and simplified management at scale.

---

## Prerequisites

- A Kubernetes cluster
- Helm installed
- Sysdig Access Key
- Admin access to the cluster (kubectl and helm configured)
- ArgoCD UI access

---

## 1. Install ArgoCD into Your Kubernetes Cluster Using Helm

```bash
helm repo add argo https://argoproj.github.io/argo-helm
helm repo update
helm install argocd --create-namespace --namespace argocd argo/argo-cd
```

---

## 2. Fork and Modify This Repository

- Fork this repository into your own GitHub account.

- This repo contains three applications:

    - nginx (sample app)

    - grafana (sample app)

    - sysdig-shield (Cluster Shield + Host Shield)

- Update the Git repository URL in root-app/sysdig-shield.yaml on line 18 with the URL of your forked repo.

---

## 3. Create the Required Kubernetes Namespace and Secret
### Create the sysdig namespace:
```bash
kubectl create namespace sysdig
```

### Create a secret with your Sysdig access key:
```bash
kubectl create secret generic sysdig-access-key \
  --from-literal=access-key=<YOUR_ACCESS_KEY> \
  -n sysdig
```
- Replace `<YOUR_ACCESS_KEY>` with your actual Sysdig access key.

---

## 4. Deploy the Root App in ArgoCD
### Go to the ArgoCD UI and click on + NEW APP.

Fill in the form with the following values:

- Application Name: `root-app`
- Project Name: `default`
- SYNC POLICY: Manual (you can enable auto-sync later)
- Repository URL: https://github.com/<your-username>/argocd-sysdig-example
- Revision: HEAD
- Path: `./root-app`
- Cluster URL: https://kubernetes.default.svc
- Namespace: `argocd`

Click CREATE.

---

## 5. Sync the Root App
Once the root-app is created, click SYNC from the ArgoCD UI.
You should see the following apps being created:

- nginx

- grafana

- sysdig-shield

This setup ensures Sysdig Shield is deployed alongside other workloads in a structured, GitOps-friendly manner.

---
