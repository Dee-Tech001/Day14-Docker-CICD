Flask Application Docker CI/CD Pipeline

![GitHub Actions](https://img.shields.io/badge/GitHub%20Actions-CI%2FCD-blue?logo=githubactions)
![Docker](https://img.shields.io/badge/Docker-Hub-blue?logo=docker)
![Python](https://img.shields.io/badge/Python-3.x-yellow?logo=python)
![Flask](https://img.shields.io/badge/Flask-Framework-green?logo=flask)
![Security](https://img.shields.io/badge/Security-Trivy%20Scanner-brightgreen?logo=aquasec)

A complete, production-grade DevOps workflow featuring an automated CI/CD pipeline for a Python/Flask microservice. The pipeline runs unit and smoke tests, performs container vulnerability scanning, builds immutable Docker images, and publishes them to Docker Hub.

---

## 📌 Architecture & Features

- **Automated Testing**: Runs smoke tests and validates the `/health` endpoint on every commit.
- **Security Scanning**: Integrates **Trivy** to scan built Docker container images for vulnerabilities prior to publishing.
- **Immutable Container Tagging**: Tags published images using Git commit SHAs (`FULL_COMMIT_SHA`) to ensure strict traceability and reproducibility across environments.
- **Automated Image Publishing**: Authenticates and pushes validated images directly to **Docker Hub** (`jhayzee/winter-arc-app`).
- **Pipeline Safety Guardrails**: Prevents broken or vulnerable builds from being pushed to the container registry.

---

## 🛠️ Tech Stack

- **Application**: Python, Flask
- **Containerization**: Docker
- **CI/CD Orchestration**: GitHub Actions
- **Registry**: Docker Hub
- **Security**: Trivy Vulnerability Scanner

---

## 🔑 GitHub Secrets Configuration

To run this pipeline successfully, configure the following secrets in **Settings > Secrets and variables > Actions**:

| Secret Name | Description | Scope / Permission |
| :--- | :--- | :--- |
| `DOCKERHUB_USERNAME` | Your Docker Hub account username (`jhayzee`) | Read |
| `DOCKERHUB_TOKEN` | Docker Hub Personal Access Token | **Read & Write** |

---

## 🚀 Local Setup & Operations

### 1. Clone the Repository
```bash
git clone [https://github.com/Dee-Tech001/Day14-Docker-CICD.git](https://github.com/Dee-Tech001/Day14-Docker-CICD.git)
cd Day14-Docker-CICD
2. Authenticate with Docker Hub
Bash
echo "<YOUR_DOCKERHUB_TOKEN>" | docker login -u jhayzee --password-stdin
3. Pull & Run Published Images
To pull and run a specific published build using its commit SHA:

Bash
# Get your commit SHA
git rev-parse HEAD

# Pull image from Docker Hub
docker pull jhayzee/winter-arc-app:<COMMIT_SHA>

# Run the container locally
docker run -d --name published-api -p 5001:5000 jhayzee/winter-arc-app:<COMMIT_SHA>

# Verify application health
curl -f http://localhost:5001/health

# Clean up local container
docker rm -f published-api
🔄 Rollback Procedure
In the event of an issue in production, perform a rollback to a known-good image tag:

Bash
# Pull the previous known-good image tag
docker pull jhayzee/winter-arc-app:<PREVIOUS_GOOD_SHA>

# Stop running instance and deploy rollback version
docker rm -f winter-app || true
docker run -d --name winter-app -p 5000:5000 \
  --restart unless-stopped jhayzee/winter-arc-app:<PREVIOUS_GOOD_SHA>

# Verify system health after rollback
curl -f http://localhost:5000/health
🛡️ CI/CD Pipeline Failure Simulation
The pipeline was validated by simulating a failure on a feature branch (test-failure) by breaking the /health endpoint. GitHub Actions successfully caught the failure during the test phase and aborted the build before any image was generated or published to Docker Hub.


---

### How to push it to GitHub:
```bash
git add README.md
git commit -m "docs: add comprehensive README for CI/CD pipeline"
git push origin main