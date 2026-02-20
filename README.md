# 🚀 Spring Boot CI/CD GitOps Pipeline

This project demonstrates a complete end-to-end CI/CD pipeline using modern DevOps tools and GitOps principles. It showcases how application code moves from development to Kubernetes using automated CI processes and GitOps-based continuous delivery.

The architecture separates CI and CD responsibilities and uses Git as the single source of truth for infrastructure state.

---

# 📌 Project Objective

To design and implement a production-style CI/CD system where:

- Code changes trigger automated builds
- Static code analysis is performed
- Docker images are built as immutable artifacts
- Kubernetes deployments are defined declaratively
- ArgoCD continuously reconciles cluster state against Git
- Infrastructure drift is automatically detected and corrected

This project focuses on understanding system behavior, reconciliation logic, deployment strategies, and CI/CD architectural design.

---

# 🏗 Architecture Overview

Developer → GitHub → Jenkins (CI) → DockerHub → Git Update → ArgoCD (CD) → Kubernetes


---

# 🔄 End-to-End Workflow

1. A developer pushes code to GitHub.
2. Jenkins (CI pipeline):
   - Checks out source code
   - Builds the application using Maven
   - Runs SonarQube static analysis
   - Builds a Docker image
   - Pushes the image to DockerHub
   - Updates the Kubernetes deployment manifest with the new image tag
   - Commits and pushes the updated manifest to GitHub
3. ArgoCD (CD):
   - Monitors the Git repository
   - Detects changes in Kubernetes manifests
   - Compares desired state (Git) vs live state (Cluster)
   - Applies the updated manifest to the cluster
4. Kubernetes:
   - Pulls the new container image
   - Performs a rolling update
   - Maintains service availability

---

# 🔁 CI vs CD Separation

This architecture deliberately separates CI and CD.

## Jenkins (Continuous Integration)

Responsible for:
- Building the application
- Running static code analysis
- Creating Docker images
- Publishing artifacts to DockerHub
- Updating deployment manifests in Git

Jenkins does **not** communicate directly with Kubernetes.

## ArgoCD (Continuous Delivery / GitOps)

Responsible for:
- Monitoring Git for infrastructure changes
- Detecting configuration drift
- Applying manifests to Kubernetes
- Continuously reconciling cluster state

This pull-based model improves security and auditability.

---

# 🔐 Security Model

This project follows a GitOps pull-based architecture.

Security advantages:

- Jenkins does not store Kubernetes cluster credentials.
- Cluster credentials remain inside Kubernetes.
- All infrastructure changes are version-controlled in Git.
- Git commit history becomes a full audit trail.
- If Jenkins is compromised, the cluster cannot be directly accessed.

This reduces blast radius compared to push-based deployment models.

---

# 🧱 Deployment Strategy

The project uses Kubernetes default **RollingUpdate** strategy.

### Rolling Update Behavior

If replicas = 3:

1. A new pod with the updated image is created.
2. One old pod is terminated.
3. This continues until all pods are updated.

This ensures minimal downtime and controlled rollout.

Rollback can be performed using:

kubectl rollout undo deployment <deployment-name>


---

# 🔁 Reconciliation & Drift Detection

ArgoCD continuously compares:

Desired State (Git)
vs
Live State (Cluster)


If differences are detected:

- Application status becomes **OutOfSync**
- If Auto-Sync is enabled, ArgoCD reapplies the manifest
- Cluster state is restored to match Git

### Example: Manual Scaling

If someone runs:

kubectl scale deployment app --replicas=6


But Git defines:

replicas: 2


ArgoCD will scale it back to 2 (if Auto-Sync enabled).

### Example: Manual Resource Deletion

If a deployment is deleted manually:

- ArgoCD detects the resource is missing
- If Auto-Sync enabled → resource is recreated
- If Auto-Sync disabled → status becomes OutOfSync

### Example: Image Pushed Without Manifest Update

If a new image is pushed to DockerHub but `deployment.yaml` is not updated:

- Kubernetes continues running the old image
- ArgoCD does nothing
- Git remains authoritative

Git is always the source of truth.

---

# ⚠️ Failure Handling

### Jenkins Build Failure
- Pipeline stops
- No image pushed
- No Git update
- No deployment triggered

### ArgoCD Sync Failure
If Kubernetes rejects a manifest:
- ArgoCD marks the application as **Degraded**
- Previous working deployment remains active
- No broken rollout is forced

### Broken Manifest in Git
If invalid YAML is committed:
- ArgoCD attempts sync
- Sync fails
- Application remains in degraded state
- Existing pods continue running

---

# 🧰 Prerequisites

## Application Requirements
- Java 17
- Maven
- Spring Boot project structure:
src/main/java
src/main/resources


## CI Infrastructure (EC2)
Installed:
- Java
- Maven
- Docker
- Jenkins
- SonarQube

Configured Jenkins credentials:
- DockerHub username/password
- SonarQube token
- GitHub token

## Docker
- Dockerfile located in `/app`
- DockerHub account
- Image naming format:
<docker-username>/<app-name>:<build-number>


## Kubernetes
- Minikube cluster
- Deployment and Service manifests

## ArgoCD
- Installed inside Kubernetes cluster
- Connected to Git repository
- Application configured
- Auto-sync enabled (optional)

---

# 📂 Project Structure

```bash
springboot-gitops-cicd/
├── README.md
├── app/
│   ├── Dockerfile
│   ├── pom.xml
│   ├── src/
│   └── target/
├── cicd/
│   └── Jenkinsfile
└── k8s/
    ├── deployment.yaml
    └── service.yaml
```

---

# 🔁 CI/CD Pipeline Stages

1️⃣ Checkout  
2️⃣ Build (`mvn clean package`)  
3️⃣ SonarQube Analysis (`mvn sonar:sonar`)  
4️⃣ Docker Build  
5️⃣ Docker Push  
6️⃣ Update Kubernetes Manifest  
7️⃣ ArgoCD Sync  

---

# 🧠 Key DevOps Concepts Demonstrated

- Continuous Integration
- Static Code Analysis
- Immutable Container Artifacts
- Registry-Based Image Promotion
- Kubernetes Rolling Updates
- GitOps Pull Model
- Declarative Infrastructure
- Continuous Reconciliation
- Drift Detection
- CI/CD Separation of Concerns

---

# 🎯 Learning Outcomes

This project demonstrates:

- End-to-end CI/CD workflow
- Secure GitOps-based deployment
- Reconciliation loop behavior
- Rolling update mechanics
- Failure handling scenarios
- Real-world DevOps system design

---

# 🧩 Architectural Decisions

### Why GitOps Instead of Jenkins Pushing Directly to Kubernetes?

- Git is authoritative infrastructure state.
- Continuous drift detection prevents configuration snowflakes.
- Cluster credentials remain inside Kubernetes.
- Infrastructure is reproducible.
- Multi-cluster scaling becomes easier.
- Audit trail is automatically maintained.

---

# ⚠️ Limitations (Not Production Hardened)

This project intentionally keeps complexity manageable.

Not implemented:

- Quality Gate enforcement blocking deployments
- Container vulnerability scanning (e.g., Trivy)
- Multi-environment promotion (dev/staging/prod)
- Horizontal Pod Autoscaler
- Resource limits and liveness/readiness probes
- External secret management
- Infrastructure as Code (Terraform)
- Blue/Green or Canary deployment strategy

---

# 📌 Future Improvements

- Add Helm charts
- Add environment-based deployment strategy
- Add image vulnerability scanning
- Add Quality Gate enforcement stage
- Add monitoring stack (Prometheus + Grafana)
- Add PR-based promotion workflow
- Add commit-SHA-based image tagging

---

# 👨‍💻 Author

Built as a hands-on DevOps learning project to deeply understand CI/CD pipelines, GitOps architecture, reconciliation behavior, and deployment strategies.
