# Cloud-Native Social Media App (Microservices)

GitHub: https://github.com/keshavp07-git/vprofile-project/tree/master
Duration: Feb 2025 – Jul 2025

---

## Project Overview

A production-grade, cloud-native social media application built on a microservices architecture and deployed on AWS. The project demonstrates end-to-end DevOps practices — from containerization and infrastructure provisioning to GitOps-based continuous delivery and full-stack observability. Deployment time was reduced from 20 minutes to 5 minutes (75% improvement) through automated CI/CD pipelines.

---

## Tech Stack

Category               | Tools & Technologies
-----------------------|-------------------------------------------------------------
Cloud Platform         | AWS (EKS, EC2, VPC, IAM, S3, RDS)
Containerization       | Docker
Container Orchestration| Kubernetes (AWS EKS), Helm, HPA
CI/CD                  | Jenkins, GitHub Actions
Infrastructure as Code | Terraform, Helm Charts
GitOps & Delivery      | ArgoCD (push-based & pull-based strategies)
Monitoring             | Prometheus, Grafana
Logging                | ELK Stack (Elasticsearch, Logstash, Kibana), Loki
Alerting               | Slack (integrated via Grafana)
Version Control        | Git, GitHub

---

## Architecture Diagram

                        +---------------------------+
                        |       Developer Push      |
                        |      (GitHub / GitLab)    |
                        +-------------+-------------+
                                      |
                          +-----------v-----------+
                          |   CI Pipeline         |
                          |  Jenkins + GitHub      |
                          |  Actions               |
                          |  - Build Docker Image  |
                          |  - Push to Registry    |
                          +-----------+-----------+
                                      |
                          +-----------v-----------+
                          |   ArgoCD (GitOps)     |
                          |  Pull-based Delivery  |
                          |  Syncs K8s manifests  |
                          +-----------+-----------+
                                      |
               +-----------------------+---------------------+
               |                       |                     |
   +-----------v------+   +------------v-----+   +----------v--------+
   |  Microservice A  |   |  Microservice B  |   |  Microservice C   |
   |  (Pod / EKS)     |   |  (Pod / EKS)     |   |  (Pod / EKS)      |
   +------------------+   +------------------+   +-------------------+
               |                       |                     |
               +-----------+-----------+---------------------+
                           |
               +-----------v-----------+
               |  Observability Stack  |
               |  Prometheus + Grafana |
               |  ELK Stack + Loki     |
               |  Slack Alerting       |
               +-----------------------+

---

## Setup & Installation

### Prerequisites

- AWS CLI configured with appropriate IAM permissions
- kubectl installed and configured
- Terraform >= 1.3
- Helm >= 3.x
- Docker installed locally

### Step 1 — Clone the repository

    git clone https://github.com/keshavp07-git/vprofile-project.git
    cd vprofile-project

### Step 2 — Provision AWS infrastructure with Terraform

    cd terraform/
    terraform init
    terraform plan
    terraform apply

This provisions: VPC, EKS cluster, IAM roles, RDS, and S3 buckets.

### Step 3 — Configure kubectl for EKS

    aws eks update-kubeconfig --region <your-region> --name <cluster-name>

### Step 4 — Deploy microservices using Helm

    helm upgrade --install vprofile ./helm/vprofile \
      --namespace production \
      --create-namespace \
      --values helm/vprofile/values.yaml

### Step 5 — Set up ArgoCD

    kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
    kubectl apply -f argocd/application.yaml

### Step 6 — Deploy monitoring stack

    helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
    helm upgrade --install monitoring prometheus-community/kube-prometheus-stack \
      --namespace monitoring --create-namespace

---

## CI/CD Pipeline Details

### Pipeline Flow

    Code Push
      |
      v
    GitHub Actions / Jenkins Triggered
      |
      +-- Checkout source code
      |
      +-- Build Docker image
      |
      +-- Push image to Docker registry (Sonatype Nexus / DockerHub)
      |
      +-- Update Helm chart image tag in Git repo
      |
      v
    ArgoCD detects change in Git (pull-based GitOps)
      |
      v
    ArgoCD syncs Kubernetes manifests to EKS cluster
      |
      v
    Rolling deployment with zero downtime
      |
      v
    Prometheus + Grafana + Loki confirm healthy rollout
    Slack alert sent on success / failure

### Key Metrics

- Deployment time: reduced from 20 min → 5 min (75% faster)
- Uptime: 99.9% via rolling updates + HPA auto-scaling
- Monitoring: 20+ metrics tracked with real-time Slack alerting
- Release consistency: improved by 50% with GitOps delivery
