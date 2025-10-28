# Chapter 01: GitOps Fundamentals and ArgoCD Introduction

## 1.1 What is GitOps?

**GitOps** is a modern approach to **Continuous Deployment (CD)** that uses **Git** as the single source of truth for declarative infrastructure and applications.

### Concept
- Everything — infrastructure, configuration, and applications — is stored in a **Git repository**.  
- The **cluster state** (actual deployed resources) should always **match the desired state** defined in Git.  
- Any change to production is made by a **Git commit or pull request (PR)**, not manually.

### Key Principles
1. **Declarative Configuration:** Define your infrastructure and apps as code (YAML manifests, Helm charts, Kustomize, etc.)
2. **Version Control (Git):** Every change is versioned, reviewed, and auditable through Git commits.
3. **Automated Synchronization:** A GitOps operator (like **ArgoCD**) continuously monitors Git and syncs cluster state.
4. **Observability and Rollbacks:** Easily roll back by reverting a Git commit; changes are instantly reflected in your environment.

---

## 1.2 Why GitOps?

| Traditional CI/CD | GitOps |
|-------------------|--------|
| Push-based (CI pipeline pushes to cluster) | Pull-based (cluster pulls from Git) |
| Harder to track what’s running | Git is the single source of truth |
| Manual or scripted deployments | Automated, declarative deployments |
| Complex rollback | Simple `git revert` to roll back |

###  Benefits
- Improved **security** (no need for CI pipeline access to cluster)  
- **Auditability** through Git history  
- **Consistency** across environments (dev, staging, prod)  
- Easier **disaster recovery** — just reapply Git state  

---

## 1.3 What is ArgoCD?

**ArgoCD** (Argo Continuous Delivery) is a **declarative, GitOps-based CD tool** for Kubernetes.

###  Purpose
ArgoCD automates the deployment of desired application states to Kubernetes clusters by continuously monitoring Git repositories.

### How It Works
1. Define your **Kubernetes manifests** (YAML files, Helm charts, or Kustomize).  
2. Push them to a **Git repository**.  
3. ArgoCD continuously **watches the repo** for changes.  
4. When it detects a change, it **syncs** the new configuration to your **Kubernetes cluster**.

---

##  1.4 ArgoCD Core Concepts

| Concept | Description |
|----------|--------------|
| **Application** | The main ArgoCD object that maps a Git repo path to a cluster namespace. |
| **Source** | The Git repo URL and path containing the manifests. |
| **Target** | The destination Kubernetes cluster and namespace. |
| **Sync** | The process of applying Git changes to the cluster. |
| **Sync Policy** | Can be manual or automated (auto-sync with pruning and self-heal). |

---

##  1.5 GitOps Workflow with ArgoCD

### Example Flow
1. Developer commits changes to a Git repo (e.g., updates a Kubernetes Deployment YAML).  
2. ArgoCD detects the commit automatically.  
3. ArgoCD applies changes to the cluster.  
4. Dashboard shows **“Synced”** or **“Out of Sync”** status.  
5. If something goes wrong → rollback via Git revert.

---

##  1.6 ArgoCD Dashboard (Overview)

- **Real-time view** of app health and sync status  
- **Manual sync** and **rollback** options  
- Integration with **Helm**, **Kustomize**, and **plain YAML**  
- **Multi-cluster support** (manage many clusters from one UI)  

---

##  1.7 Summary

| Topic | Key Takeaway |
|--------|---------------|
| **GitOps** | Git as the single source of truth for infrastructure and app state |
| **ArgoCD** | Implements GitOps in Kubernetes |
| **Benefits** | Declarative, automated, auditable, and secure CD |
| **Rollbacks** | Simple via Git revert |
| **Visibility** | Real-time sync & health status in ArgoCD UI |

---
