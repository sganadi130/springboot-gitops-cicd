# 🚀 Spring Boot CI/CD GitOps Pipeline

This project demonstrates a complete end-to-end CI/CD pipeline using modern DevOps tools and GitOps principles.

It covers:

- Spring Boot application
- Jenkins CI pipeline
- SonarQube static analysis
- Docker containerization
- DockerHub image registry
- Kubernetes deployment
- ArgoCD GitOps continuous delivery

---

# 📌 Project Goal

To implement a production-style CI/CD workflow where:

- Code changes trigger automated builds
- Containers are built and pushed automatically
- Kubernetes deployments are managed via Git
- ArgoCD enforces Git as the single source of truth

---

# 🏗 Architecture

```
Developer → GitHub → Jenkins → DockerHub → Git Update → ArgoCD → Kubernetes
```

### Flow Explanation

1. Developer pushes code to GitHub.
2. Jenkins:
   - Builds the application
   - Runs SonarQube analysis
   - Builds Docker image
   - Pushes image to DockerHub
   - Updates Kubernetes manifest in Git
3. ArgoCD monitors Git repository.
4. ArgoCD syncs Kubernetes cluster.
5. Kubernetes pulls new image and deploys updated pods.

---

# 🧰 Prerequisites

## Application Requirements

- Java 17
- Maven
- Spring Boot project structure:
  ```
  src/main/java
  src/main/resources
  ```

---

## CI Infrastructure (EC2 Instance)

Installed on EC2:

- Java
- Maven
- Docker
- Jenkins
- SonarQube

Jenkins credentials configured:

- DockerHub credentials
- SonarQube authentication token
- GitHub token

---

## Docker

- Dockerfile present in `/app`
- DockerHub account
- Image naming format:
  ```
  <docker-username>/<app-name>:<build-number>
  ```

---

## Kubernetes

- Minikube (local cluster)
- Deployment manifest
- Service manifest

---

## ArgoCD

- Installed in Kubernetes cluster
- Git repository connected
- Application created
- Auto-sync enabled (optional)

---

# 📂 Project Structure

```
springboot-gitops-cicd
│
├── README.md
├── app
│   ├── Dockerfile
│   ├── pom.xml
│   ├── src/
│   └── target/
│
├── cicd
│   └── Jenkinsfile
│
└── k8s
    ├── deployment.yaml
    └── service.yaml
```

---

# 🔁 CI/CD Pipeline Stages

## 1️⃣ Checkout

Jenkins pulls source code from GitHub.

---

## 2️⃣ Build

```
mvn clean package
```

Builds executable JAR file.

---

## 3️⃣ SonarQube Analysis

```
mvn sonar:sonar
```

Performs static code analysis.

---

## 4️⃣ Docker Build

```
docker build -t username/app:build-number .
```

Creates Docker image.

---

## 5️⃣ Docker Push

```
docker push username/app:build-number
```

Pushes image to DockerHub.

---

## 6️⃣ Update Kubernetes Manifest

Jenkins updates:

```
k8s/deployment.yaml
```

Replaces image tag with latest build number.

Pushes changes back to GitHub.

---

## 7️⃣ ArgoCD Sync

ArgoCD:

- Detects Git change
- Compares desired state vs cluster state
- Synchronizes cluster automatically

---

# 🔐 GitOps Principle

## Why Git Is the Single Source of Truth

ArgoCD compares:

```
Desired State (Git)
vs
Actual State (Cluster)
```

If they differ:

Application becomes **OutOfSync**.

Git always overrides manual cluster changes.

---

## Example 1

If you manually scale:

```
kubectl scale deployment app --replicas=6
```

But Git says:

```
replicas: 2
```

ArgoCD scales it back to 2.

---

## Example 2

If you push a Docker image directly to DockerHub
but do NOT update deployment.yaml:

Kubernetes continues running the old image.

ArgoCD only trusts Git.

---

# 🚀 How to Run Locally

## Build Application

```
cd app
mvn clean package
```

---

## Run Application

```
java -jar target/spring-boot-web.jar
```

Access:

```
http://localhost:8080
```

---

# 🛠 Kubernetes Deployment

Apply manifests:

```
kubectl apply -f k8s/
```

Check:

```
kubectl get pods
kubectl get svc
```

---

# 🧠 Key DevOps Concepts Demonstrated

- Continuous Integration
- Static Code Analysis
- Containerization
- Immutable Deployments
- Kubernetes Orchestration
- GitOps Workflow
- Declarative Infrastructure
- Automated Reconciliation

---

# 🎯 Learning Outcome

This project demonstrates:

- End-to-end CI/CD implementation
- Docker image lifecycle management
- Git-driven Kubernetes deployments
- ArgoCD reconciliation behavior
- Real-world DevOps workflow simulation

---

# ⚠️ Notes

- This project is for educational and practice purposes.
- Not production hardened.
- Secrets are managed through Jenkins credentials.
- ArgoCD auto-sync behavior demonstrates GitOps enforcement.

---

# 📌 Future Improvements

- Add Helm charts
- Add unit tests
- Add GitHub Actions alternative pipeline
- Add multi-environment support (dev/staging/prod)
- Add monitoring integration
- Add image scanning (Trivy)

---

# 👨‍💻 Author

Built as a DevOps learning project to demonstrate CI/CD + GitOps principles.

