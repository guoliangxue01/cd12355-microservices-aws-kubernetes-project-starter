

## High Level Architecture
```
GitHub (code repo)-> CodeBuild (Docker build) -> Amazon ECR (image repo) -> Amazon EKS
```

Key responsibilities by component:

GitHub — source of code including code of application and K8s deployment YAML files. CodeBuild trigger are configured on PUSH event.

CodeBuild — build runtime. Builds the Docker image and pushes to ECR. buildspec.yml controls lifecycle (pre_build/build/post_build).

Amazon ECR — image storage. Acts as the artifact repository for downstream deploy stages.

Amazon EKS - Kubernetes cluster. User uses K8s CLI client to deployment resources to the cluster.

## Execution Process

### Build and Deploy to ECR

The Dockerfile defines a lightweight Python 3.10 image for the application.
AWS CodeBuild automatically builds the Docker image and pushes it into ECR whenever the source code are pushed by someone.

### Kubernetes Configuration

The deployment/ folder contains Kubernetes YAML manifests.
A Deployment file defines pods running the application container.
A Service file exposes the application to the cluster.
A ConfigMap provides non-sensitive environment variables such as database host, port, and username.
A Secret securely stores sensitive environment variables such as the database password.
A separate Database Service manifest defines a Postgres backend accessible within the cluster.

### Deployment Verification

The service deployment was verified using kubectl get svc, kubectl describe deployment, and kubectl get pods.
The Postgres database service was confirmed with kubectl describe svc postgresql-service.

### Logging and Monitoring

Application logs were collected using CloudWatch.
The logs confirm that the service runs without errors and periodically prints health status messages.

## Results
All results from Execution Process are saved in the /results folder in screenshot way.



