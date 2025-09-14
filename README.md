

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
AWS CodeBuild automatically builds the Docker image and pushes it into ECR whenever the source code are pushed by users.

### Cluster Configuration and Deployment

The application was deployed to an Amazon EKS cluster.

#### Cluster Setup

An EKS cluster was created in the us-east-1 region.

```
eksctl create cluster --name udacity-cluster --region us-east-1 --nodegroup-name udacity-nodes --node-type t3.small --nodes 1 --nodes-min 1 --nodes-max 2
```

To connect the local environment to the cluster, the kubeconfig file was updated with:

```
aws eks --region us-east-1 update-kubeconfig --name udacity-cluster
```

This ensures that kubectl can authenticate and communicate with the cluster.

The active context was verified with:
```
kubectl config current-context
```

The output confirmed the context is bound to the EKS cluster, for example:

```
arn:aws:eks:us-east-1:493339161712:cluster/udacity-cluster
```

For further inspection, the full kubeconfig file was reviewed using:
```
kubectl config view
```
#### Deployment Process

Once kubeconfig was correctly set up, the Kubernetes manifests in the deployment/ directory were applied:

```
kubectl apply -f pvc.yaml
kubectl apply -f pv.yaml
kubectl apply -f postgresql-deployment.yaml
kubectl apply -f postgres-service.yaml
```

This will create database, and use db/ directory to create the tables and insert the users and tokens data.

```
kubectl apply -f configmap.yaml
```

This will provide the environment variables (including sensitive data which uses Secret) to the application.

```
kubectl apply -f coworking.yaml
```
This will create the coworking application, in this file, it refers all environemnt variables from configmap file and use the container image which created by the codebuild.

#### Validation

The following commands were used to verify deployment status:
```
kubectl get svc
kubectl describe deployment coworking
kubectl get pods
kubectl describe svc postgres-service
```

kubectl get svc confirmed the LoadBalancer and database services were created.

kubectl describe deployment confirmed the Deployment’s pod template and replicas.

kubectl get pods showed pods in a READY and RUNNING state.

kubectl describe svc confirmed that the Postgres service was successfully exposed inside the cluster.

### Logging and Monitoring

Application logs were collected using CloudWatch.
The logs confirm that the service runs without errors and periodically prints health status messages.

## Results
All results from Execution Process are saved in the /results folder in screenshot way.
