# Go Web App • Kubernetes • Helm • CI/CD


A minimal Go HTTP service that’s containerized, packaged with Helm, and wired to a GitHub Actions pipeline that:
- builds & tests the app
- runs `golangci-lint`
- builds and pushes an image to Docker Hub
- updates the Helm chart’s image tag automatically

---

## 🔭 Tech Stack
- **Go 1.22**
- **Docker** (multi-stage build)
- **Kubernetes (EKS)** + **Ingress** (NGINX or ALB)
- **Helm** chart (`helm/go-web-app-chart`)
- **GitHub Actions** for CI/CD

---

## 🗂 Project Structure

.
├─ .github/
│ └─ workflows/
│ └─ ci.yaml # CI/CD pipeline
├─ helm/
│ └─ go-web-app-chart/
│ ├─ Chart.yaml
│ ├─ values.yaml # image repo/tag, service/ingress config
│ └─ templates/
│ ├─ deployment.yaml
│ ├─ service.yaml
│ └─ ingress.yaml
├─ k8s/
│ └─ manifests/ # raw k8s YAML (optional, for reference)
├─ main/ # Go source (module root has go.mod)
│ ├─ main.go
│ └─ main_test.go
├─ Dockerfile
└─ go.mod

# build local image
docker build -t marioaziz/go-web-app:dev .

# run
docker run -p 8080:8080 marioaziz/go-web-app:dev


🔁 CI/CD Pipeline (GitHub Actions)

Workflow: .github/workflows/ci.yaml

Jobs:

build – checkout, setup Go, go build, go test

code-quality – golangci-lint

push – Docker Buildx; login to Docker Hub; push:

marioaziz/go-web-app:${{ github.run_id }}

marioaziz/go-web-app:latest

update-newtag-in-helm-chart – commits the new image tag into values.yaml

Required Secrets

Add in Repo → Settings → Secrets and variables → Actions:

DOCKERHUB_USERNAME → marioaziz

DOCKERHUB_TOKEN → Docker Hub personal access token (Read/Write)
