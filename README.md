# ShopSphere Helm Chart

A Helm chart for deploying the **ShopSphere E-Commerce Application** on Kubernetes (AWS EKS).

## Helm Repository

This chart is hosted via **GitHub Pages** and published through **ArtifactHub**.

```
Repository URL: https://smprajwal.github.io/ss-aws-eks-helm-chart
```

### Add the Repository

```bash
helm repo add shopsphere https://smprajwal.github.io/ss-aws-eks-helm-chart
helm repo update
```

### Install the Chart

```bash
helm install shopsphere shopsphere/shopsphere \
  --set services.frontend.imageName=<FRONTEND_ECR_URL> \
  --set services.backend.imageName=<BACKEND_ECR_URL> \
  --set dbConnectionString=<DB_CONNECTION_STRING> \
  --set s3BucketName=<S3_BUCKET_NAME> \
  --set flaskSecret=<FLASK_SECRET> \
  --set adminUsername=<ADMIN_USERNAME> \
  --set adminPassword=<ADMIN_PASSWORD>
```

### Upgrade the Release

```bash
helm upgrade shopsphere shopsphere/shopsphere -f custom-values.yaml
```

### Uninstall the Chart

```bash
helm uninstall shopsphere
```

## Chart Details

| Field       | Value                              |
|-------------|------------------------------------|
| Name        | `shopsphere`                       |
| Version     | `1.0.0`                            |
| API Version | `v2`                               |
| Description | ShopSphere E-Commerce Application  |

## Architecture

The chart deploys the following Kubernetes resources:

| Resource                   | Description                                                        |
|----------------------------|--------------------------------------------------------------------|
| **Deployment** (x2)       | Frontend and Backend application pods                              |
| **Service** (x2)          | ClusterIP services exposing port 80 → 8000 for each deployment     |
| **Ingress**               | AWS ALB Ingress (internet-facing) routing traffic to the frontend  |
| **ConfigMap**              | Non-sensitive configuration (backend API URL, AWS region, S3 bucket) |
| **Secret**                 | Sensitive data (Flask secret, admin credentials, DB connection string) |
| **HorizontalPodAutoscaler** (x2) | Auto-scales frontend and backend (1–3 replicas, 70% CPU target) |

## Configuration

The following table lists the configurable parameters in `values.yaml`:

### Global

| Parameter            | Description                        | Default       |
|----------------------|------------------------------------|---------------|
| `flaskSecret`        | Flask application secret key       | `""`          |
| `adminUsername`      | Admin panel username               | `""`          |
| `adminPassword`      | Admin panel password               | `""`          |
| `dbConnectionString` | Database connection string         | `""`          |
| `AwsRegion`          | AWS region for services            | `ap-south-1`  |
| `s3BucketName`       | S3 bucket name for asset storage   | `""`          |
| `ingressName`        | Name of the Ingress resource       | `shopsphere-app-ingress` |

### Services

Each service (`frontend`, `backend`) supports the following parameters:

| Parameter        | Description                  | Frontend Default        | Backend Default         |
|------------------|------------------------------|-------------------------|-------------------------|
| `deploymentName` | Name of the Deployment       | `frontend-app`          | `backend-app`           |
| `label`          | Pod label selector           | `app: frontend`         | `app: backend`          |
| `imageName`      | Container image (ECR URL)    | `""`                    | `""`                    |
| `imageVersion`   | Container image tag          | `1.0`                   | `1.0`                   |
| `hpaName`        | Name of the HPA              | `frontend-hpa`          | `backend-hpa`           |
| `serviceName`    | Name of the Service          | `frontend-app-service`  | `backend-app-service`   |
| `cpu`            | CPU resource request         | `100m`                  | `150m`                  |
| `memory`         | Memory resource request      | `128Mi`                 | `192Mi`                 |

## Prerequisites

- **Kubernetes** 1.23+
- **Helm** 3.x
- **AWS EKS** cluster with the [AWS Load Balancer Controller](https://docs.aws.amazon.com/eks/latest/userguide/aws-load-balancer-controller.html) installed
- Container images pushed to an accessible registry (e.g., Amazon ECR)

## Project Structure

```
.
├── Chart.yaml              # Chart metadata
├── values.yaml             # Default configuration values
├── index.yaml              # Helm repository index (GitHub Pages)
├── shopsphere-1.0.0.tgz   # Packaged chart archive
└── templates/
    ├── configmap.yaml      # Application ConfigMap
    ├── deployment.yaml     # Frontend & Backend Deployments
    ├── hpa.yaml            # HorizontalPodAutoscaler for both services
    ├── ingress.yaml        # AWS ALB Ingress
    ├── secret.yaml         # Application Secrets
    └── service.yaml        # ClusterIP Services
```

## Links

- **Helm Repository**: [https://smprajwal.github.io/ss-aws-eks-helm-chart](https://smprajwal.github.io/ss-aws-eks-helm-chart)
- **ArtifactHub**: [https://artifacthub.io](https://artifacthub.io/packages/search?repo=ss-aws-eks-helm-chart)
- **GitHub**: [https://github.com/smPrajwal/ss-aws-eks-helm-chart](https://github.com/smPrajwal/ss-aws-eks-helm-chart)
