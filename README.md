# Production-Ready Kubernetes Deployment on AWS EKS with Ingress and Istio

## Overview

This project demonstrates an end-to-end Kubernetes platform built on AWS EKS. It includes infrastructure provisioning using Terraform, application deployment, ingress-based routing using NGINX, and migration to Istio service mesh for advanced traffic control.

The project reflects real-world DevOps and Site Reliability Engineering practices including infrastructure as code, container orchestration, networking, and troubleshooting.

---

## Architecture

### NGINX-Based Architecture

User → AWS LoadBalancer → NGINX Ingress Controller → Kubernetes Service → Pods

### Istio-Based Architecture

User → AWS LoadBalancer → Istio Ingress Gateway → VirtualService → Kubernetes Service → Pods

---

## Technologies Used

- AWS (EKS, EC2, IAM, VPC, Load Balancer)
- Terraform
- Kubernetes
- Docker
- Helm
- NGINX Ingress Controller
- Istio Service Mesh
- kubectl
- eksctl

---

## Project Structure


```text
eks-terraform/
├── terraform/
│   ├── main.tf
│   ├── variables.tf
│   └── outputs.tf
├── k8s/
│   ├── deployment.yaml
│   ├── service.yaml
│   ├── ingress.yaml
│   └── test-deployment.yaml
├── istio/
│   ├── gateway.yaml
│   └── virtualservice.yaml
├── cronjob/
│   └── cronjob.yaml
└── README.md
```

---

## Step 1: Infrastructure Setup

Provision AWS resources using Terraform:

- VPC and subnets
- EKS cluster
- Node groups
- IAM roles

Commands:


terraform init
terraform plan
terraform apply


---

## Step 2: Configure kubectl


aws eks --region <region> update-kubeconfig --name <cluster-name>
kubectl get nodes


---

## Step 3: Deploy Applications

Deploy frontend and test applications:


kubectl apply -f deployment.yaml
kubectl get pods


---

## Step 4: Create Services

Expose applications internally:


kubectl apply -f service.yaml


---

## Step 5: NGINX Ingress Setup

Install NGINX Ingress Controller and configure routing:

- `/` → main application
- `/second` → test application

Flow:

User → LoadBalancer → NGINX → Service → Pod

---

## Step 6: Troubleshooting

### ImagePullBackOff
- Cause: Private image
- Fix: Added docker-registry secret

### Pod Scheduling Failure
- Cause: Too many pods / IP limits
- Fix: Reduced replicas and scaled nodes

### Ingress Not Working
- Cause: Missing controller
- Fix: Installed NGINX

### RBAC Issues
- Cause: Missing permissions
- Fix: Added ClusterRole and bindings

---

## Step 7: Migration to Istio

Installed Istio and replaced NGINX Ingress.

### Install Istio


istioctl install --set profile=demo -y


### Enable Injection


kubectl label namespace capstone-ns1 istio-injection=enabled
kubectl rollout restart deployment -n capstone-ns1


### Create Gateway

Handles incoming traffic.

### Create VirtualService

Routes traffic:

- `/` → main service
- `/second` → test service

Flow:

User → LoadBalancer → Istio Gateway → VirtualService → Service → Pod

---

## Step 8: CronJob Setup

Created scheduled jobs using CronJob.

### Issue

Deprecated API version (`batch/v1beta1`)

### Fix

Updated to:


apiVersion: batch/v1


---

## Key Learnings

- Difference between Ingress and Istio Gateway
- Importance of ingress controllers
- IAM permissions and PassRole
- Node capacity planning
- Kubernetes debugging techniques
- Handling private container registries
- Service mesh fundamentals

---

## Challenges Faced

- Resource limits with t3.micro
- Istio installation failures
- IAM permission issues
- LoadBalancer delays
- Deprecated API errors

---

## Cost Optimization

- Used minimal nodes
- Upgraded only when needed
- Destroyed infrastructure after testing

---

## Future Improvements

- CI/CD pipeline (GitHub Actions)
- Monitoring (Prometheus, Grafana)
- HTTPS with TLS
- AWS ECR integration
- Autoscaling
- Canary deployments using Istio

---

## Conclusion

This project demonstrates a complete DevOps workflow from infrastructure provisioning to advanced traffic routing using Istio. It highlights real-world challenges and solutions in Kubernetes environments.
