# 100 ArgoCD & GitOps Interview Questions

## Basic Level (1-30)

### GitOps Fundamentals

1. **What is GitOps?**
   - GitOps is a modern approach to continuous delivery that uses Git as the single source of truth for declarative infrastructure and applications. All changes are made via Git commits, and automated tools ensure the system matches the desired state defined in Git.

2. **What are the core principles of GitOps?**
   - Declarative, Versioned & Immutable, Pulled Automatically, Continuously Reconciled

3. **What is the difference between push-based and pull-based CD?**
   - Push: CI/CD pipeline pushes changes to cluster (traditional). Pull: Operator running in cluster pulls changes from Git (GitOps)

4. **Why is Git used as the source of truth in GitOps?**
   - Git provides version control, audit trails, rollback capabilities, collaboration features, and is already familiar to development teams

5. **What are the benefits of GitOps over traditional CD?**
   - Better security (no cluster credentials in pipelines), easy rollback, audit trails, drift detection, declarative approach, consistency

### ArgoCD Basics

6. **What is ArgoCD?**
   - ArgoCD is a declarative, GitOps continuous delivery tool for Kubernetes that automatically syncs applications from Git repositories to Kubernetes clusters

7. **Who maintains ArgoCD?**
   - ArgoCD is part of the Argo Project, which is a CNCF (Cloud Native Computing Foundation) graduated project

8. **What programming language is ArgoCD written in?**
   - Go (Golang)

9. **What is the default namespace for ArgoCD?**
   - `argocd`

10. **What are the main components of ArgoCD?**
    - API Server, Repository Server, Application Controller, Redis, Dex (optional), ApplicationSet Controller

11. **What is the ArgoCD API Server responsible for?**
    - Exposes API for UI/CLI/CICD, handles authentication, manages applications, RBAC enforcement

12. **What is the role of the Repository Server?**
    - Maintains local cache of Git repos, generates Kubernetes manifests from various formats (Helm, Kustomize, plain YAML)

13. **What does the Application Controller do?**
    - Continuously monitors running applications, compares live state vs desired state, detects drift, triggers sync operations

14. **Why does ArgoCD use Redis?**
    - For caching data, improving performance, and storing temporary information

15. **What is Dex in ArgoCD?**
    - An identity service for SSO integration supporting OIDC, LDAP, SAML, GitHub, GitLab, etc.

### ArgoCD Installation & Setup

16. **What are the prerequisites for installing ArgoCD?**
    - Kubernetes cluster (v1.21+), kubectl, cluster admin access

17. **How do you install ArgoCD using kubectl?**
    - `kubectl create namespace argocd` then `kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml`

18. **How do you get the initial admin password for ArgoCD?**
    - `kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d`

19. **What is the default username for ArgoCD?**
    - `admin`

20. **How do you access ArgoCD UI locally?**
    - Port-forward: `kubectl port-forward svc/argocd-server -n argocd 8080:443`

21. **How do you install ArgoCD CLI?**
    - Download binary from GitHub releases, make executable, move to PATH

22. **How do you login to ArgoCD via CLI?**
    - `argocd login <server-address>` with username and password

23. **What are different ways to expose ArgoCD server?**
    - Port-forward, LoadBalancer, NodePort, Ingress

24. **How do you change the admin password in ArgoCD?**
    - Via CLI: `argocd account update-password` or via UI: User Info > Update Password

25. **What is the ArgoCD Helm chart repository?**
    - `https://argoproj.github.io/argo-helm`

### ArgoCD Applications

26. **What is an Application in ArgoCD?**
    - A Kubernetes CRD that represents a deployed application, defining source (Git) and destination (K8s cluster)

27. **What are the main fields in an ArgoCD Application spec?**
    - source (repo, path, targetRevision), destination (server, namespace), syncPolicy

28. **What is the difference between sync and refresh in ArgoCD?**
    - Refresh: Updates repo cache and checks for drift. Sync: Actually applies changes to cluster

29. **What does "OutOfSync" mean in ArgoCD?**
    - Live state in cluster differs from desired state in Git

30. **What are the possible health statuses in ArgoCD?**
    - Healthy, Progressing, Degraded, Suspended, Missing, Unknown

---

## Intermediate Level (31-60)

### ArgoCD Projects & RBAC

31. **What is an ArgoCD Project?**
    - A logical grouping of applications that provides multi-tenancy, restricts repos/clusters, and defines RBAC policies

32. **What is the default project in ArgoCD?**
    - `default`

33. **How do you restrict which repositories a project can use?**
    - Define `sourceRepos` in Project spec with allowed repo URLs or patterns

34. **How do you restrict destination clusters in a project?**
    - Define `destinations` in Project spec with allowed servers and namespaces

35. **What is RBAC in ArgoCD?**
    - Role-Based Access Control that defines what users/groups can do (get, create, update, delete, sync, override)

36. **Where are RBAC policies defined in ArgoCD?**
    - In the `argocd-rbac-cm` ConfigMap

37. **What is the format of ArgoCD RBAC policy rules?**
    - `p, <subject>, <resource>, <action>, <object>, <effect>`

38. **How do you create a read-only user in ArgoCD?**
    - Create RBAC policy with only `get` action for applications

39. **What is the difference between local users and SSO users in ArgoCD?**
    - Local: managed in ArgoCD. SSO: authenticated via external provider (OIDC, LDAP, etc.)

40. **How do you integrate ArgoCD with GitHub for SSO?**
    - Configure Dex with GitHub OAuth connector in `argocd-cm` ConfigMap

### Sync Strategies & Policies

41. **What is manual sync in ArgoCD?**
    - User must trigger sync manually via UI, CLI, or API

42. **What is automated sync in ArgoCD?**
    - ArgoCD automatically syncs when Git changes are detected

43. **What does `prune: true` mean in sync policy?**
    - Resources removed from Git will be deleted from cluster

44. **What does `selfHeal: true` mean?**
    - ArgoCD will revert manual changes to cluster to match Git state

45. **What are sync phases in ArgoCD?**
    - PreSync, Sync, PostSync, SyncFail

46. **What are sync hooks?**
    - Kubernetes resources with annotations that run during specific sync phases

47. **What is sync wave in ArgoCD?**
    - Controls order of resource creation during sync using `argocd.argoproj.io/sync-wave` annotation

48. **How do you skip a resource from being managed by ArgoCD?**
    - Add annotation: `argocd.argoproj.io/compare-options: IgnoreExtraneous`

49. **What is the difference between replace and force sync?**
    - Replace: uses kubectl replace instead of apply. Force: terminates existing resource and recreates

50. **Can ArgoCD sync a subset of resources in an application?**
    - Yes, using selective sync via UI or CLI with resource filters

### Multi-Tenancy & Multi-Cluster

51. **How does ArgoCD support multi-tenancy?**
    - Through Projects with resource restrictions and RBAC policies

52. **How do you add an external cluster to ArgoCD?**
    - `argocd cluster add <context-name>` or manually create Secret with cluster credentials

53. **Where are cluster credentials stored in ArgoCD?**
    - As Kubernetes Secrets in argocd namespace with label `argocd.argoproj.io/secret-type: cluster`

54. **What is the in-cluster context in ArgoCD?**
    - `https://kubernetes.default.svc` - the cluster where ArgoCD is installed

55. **Can ArgoCD manage multiple clusters from a single instance?**
    - Yes, this is a key feature of ArgoCD

56. **How do you remove a cluster from ArgoCD?**
    - `argocd cluster rm <server-url>` or delete the cluster Secret

57. **What is the difference between namespace-scoped and cluster-scoped ArgoCD?**
    - Cluster-scoped: can deploy to any namespace. Namespace-scoped: limited to specific namespaces

58. **How do you implement environment isolation (dev/staging/prod) with ArgoCD?**
    - Use separate Projects, Git branches/folders, different destination namespaces/clusters

59. **Can you deploy to on-premise and cloud clusters from the same ArgoCD?**
    - Yes, as long as ArgoCD can reach the cluster API servers

60. **How do you handle cluster credentials securely?**
    - Use service accounts with minimal permissions, rotate credentials, use Secret encryption

---

## Advanced Level (61-100)

### ApplicationSets

61. **What is an ApplicationSet?**
    - A CRD that automatically generates multiple ArgoCD Applications from templates

62. **What are ApplicationSet generators?**
    - List, Cluster, Git, Matrix, Merge, SCM Provider, Pull Request

63. **What is the List generator used for?**
    - Generates applications from a list of key-value pairs

64. **What is the Cluster generator?**
    - Automatically generates applications for each cluster registered in ArgoCD

65. **What is the Git generator?**
    - Generates applications based on directories or files in a Git repository

66. **How does the Matrix generator work?**
    - Combines two generators to create applications from their cross product

67. **What is the Pull Request generator?**
    - Creates temporary applications for open pull requests (preview environments)

68. **Can ApplicationSets be used for multi-cluster deployments?**
    - Yes, especially with Cluster and Matrix generators

69. **How do you update an ApplicationSet?**
    - Modify the ApplicationSet resource; controller regenerates Applications automatically

70. **What happens to Applications when an ApplicationSet is deleted?**
    - Depends on the deletion policy: orphan (keep apps) or cascade (delete apps)

### App of Apps Pattern

71. **What is the App of Apps pattern?**
    - An ArgoCD Application that manages other ArgoCD Applications (GitOps for GitOps)

72. **When should you use App of Apps?**
    - Managing multiple related applications, bootstrapping clusters, organizing teams/environments

73. **What is the difference between App of Apps and ApplicationSets?**
    - App of Apps: manual definition of child apps. ApplicationSets: automatic generation based on templates

74. **Can you combine App of Apps with ApplicationSets?**
    - Yes, for flexible hierarchical application management

75. **How do you bootstrap a cluster using App of Apps?**
    - Create root Application that references a Git repo containing other Application manifests

### Config Management Tools

76. **What config management tools does ArgoCD support?**
    - Plain YAML/JSON, Helm, Kustomize, Jsonnet, custom plugins

77. **How does ArgoCD detect which tool to use?**
    - Automatically based on files present (Chart.yaml for Helm, kustomization.yaml for Kustomize)

78. **Can you override Helm values in ArgoCD?**
    - Yes, via `helm.values` in Application spec or external values file

79. **How do you use Kustomize overlays with ArgoCD?**
    - Point Application path to overlay directory (e.g., `overlays/prod`)

80. **What are ArgoCD Config Management Plugins (CMP)?**
    - Custom plugins to support additional config tools beyond built-in ones

### ArgoCD Notifications

81. **What is ArgoCD Notifications?**
    - Component that sends notifications about application events to various channels

82. **What notification channels does ArgoCD support?**
    - Slack, Email, Webhook, MS Teams, Telegram, PagerDuty, GitHub, GitLab, Grafana

83. **How do you configure Slack notifications?**
    - Create notification service in `argocd-notifications-cm` ConfigMap, add annotations to Applications

84. **What events can trigger notifications?**
    - Sync started, succeeded, failed, status changed, health degraded, etc.

85. **How do you customize notification templates?**
    - Define templates in `argocd-notifications-cm` ConfigMap

86. **Can you send notifications to different channels based on application?**
    - Yes, using annotations on Application resources

87. **How do you test notifications?**
    - `argocd admin notifications trigger <trigger-name> <app-name>`

### ArgoCD Image Updater

88. **What is ArgoCD Image Updater?**
    - Component that automatically updates container image versions in Git when new images are pushed

89. **How does Image Updater detect new images?**
    - Polls container registries (Docker Hub, ECR, GCR, ACR, etc.) for new tags

90. **What update strategies does Image Updater support?**
    - Latest, digest, semver, name-based

91. **How do you configure Image Updater for an application?**
    - Add annotations to Application: `argocd-image-updater.argoproj.io/image-list`

92. **Can Image Updater write directly to cluster or only to Git?**
    - Typically writes to Git (GitOps principle), but can write to cluster with ArgoCD Parameters

93. **How do you filter image tags (e.g., only production tags)?**
    - Use tag constraints: `argocd-image-updater.argoproj.io/image-list: <image>:~^v[0-9]`

### Monitoring & Observability

94. **What metrics does ArgoCD expose?**
    - Application sync status, health status, repo sync performance, controller performance, API requests

95. **How do you monitor ArgoCD with Prometheus?**
    - ArgoCD metrics are exposed at `/metrics` endpoint, scrape with Prometheus

96. **What Grafana dashboards are available for ArgoCD?**
    - Official dashboards for operational metrics, application metrics, notifications

97. **How do you troubleshoot a failing sync?**
    - Check Application logs, controller logs, verify Git repo access, check RBAC, review sync hooks

98. **How do you enable debug logging in ArgoCD?**
    - Set env var `ARGOCD_LOG_LEVEL=debug` in controller/server deployment

99. **What is the ArgoCD operational overview dashboard?**
    - Grafana dashboard showing cluster count, app count, sync status, health status

### Argo Rollouts & Advanced Deployments

100. **What is Argo Rollouts?**
     - Kubernetes controller providing advanced deployment strategies like Canary, Blue-Green with progressive traffic shifting

---

## Scenario-Based Questions

### 101. **Your application is stuck in "Progressing" state. How do you troubleshoot?**
- Check pod status, events, logs
- Verify resource availability (CPU, memory)
- Check readiness/liveness probes
- Review Application controller logs
- Check for image pull errors

### 102. **How do you implement a GitOps workflow for multiple environments?**
- Use separate Git branches or directories per environment
- Use Kustomize overlays or Helm value files
- Create separate ArgoCD Projects for each environment
- Implement branch protection and approval workflows

### 103. **Your dev team accidentally deleted resources in production. How does ArgoCD help?**
- If `selfHeal: true`, ArgoCD automatically recreates deleted resources
- If not, manual sync will restore from Git
- Git history shows who made the change and when
- Can revert Git commit to restore previous state

### 104. **How do you implement zero-downtime deployments with ArgoCD?**
- Use Argo Rollouts for Canary/Blue-Green deployments
- Configure proper readiness probes
- Use PodDisruptionBudgets
- Implement progressive sync waves
- Use PreSync hooks to run database migrations

### 105. **How would you migrate from Jenkins/GitLab CI to ArgoCD?**
- Keep CI pipeline for build/test/image push
- Remove deployment steps from CI
- Store K8s manifests in Git
- Create ArgoCD Applications pointing to manifest repos
- Implement automated sync or trigger sync from CI
- Train team on GitOps principles

---

## Best Practices Questions

### 106. **What are ArgoCD security best practices?**
- Change default admin password
- Implement RBAC with least privilege
- Use SSO for authentication
- Enable TLS/HTTPS
- Regularly update ArgoCD
- Use Secret management (Sealed Secrets, External Secrets)
- Audit Git commits
- Implement branch protection

### 107. **How do you handle secrets in GitOps with ArgoCD?**
- Sealed Secrets (Bitnami)
- External Secrets Operator
- SOPS (Secrets OPerationS)
- HashiCorp Vault integration
- AWS Secrets Manager / Azure Key Vault
- Never commit plain secrets to Git

### 108. **What are common ArgoCD anti-patterns to avoid?**
- Storing secrets in Git unencrypted
- Not using Projects for multi-tenancy
- Overly permissive RBAC
- Not implementing HA for production
- Not monitoring ArgoCD itself
- Manual changes to clusters (defeats GitOps)

### 109. **How do you scale ArgoCD for large organizations?**
- Increase controller/server replicas
- Use Redis HA
- Implement resource limits
- Use sharding for Application Controller
- Optimize repo server with cache
- Use multiple ArgoCD instances per team/region

### 110. **What is your disaster recovery strategy for ArgoCD?**
- Backup ArgoCD namespace (including CRDs)
- Store all manifests in Git (already done in GitOps)
- Document cluster connection procedures
- Automate ArgoCD installation
- Test recovery procedures regularly
- Use GitOps to reinstall ArgoCD itself (App of Apps)
