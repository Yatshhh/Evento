# #Evento-Event Management Application

## Overview
Evento is an innovative application designed to streamline the process of organizing, hosting, and attending events. The platform offers a user-friendly interface for event planners, hosts, and attendees to create, manage, and join events effortlessly. Evento is a comprehensive solution catering to various event types, including conferences, workshops, social gatherings, and more.

## Key Features

**Event Creation and Management:**
Users can create events with detailed descriptions, schedules, and participant lists. Organizers have access to robust tools for managing invitations, RSVPs, and attendee communications.

**Hosting Tools:** 
Evento provides hosts with features such as venue management, ticketing solutions, and real-time updates to ensure seamless event execution.

**Attendee Experience:** 
Attendees can easily browse, search, and register for events. They receive timely notifications and can engage with other participants through integrated social features.

## Technical Architecture
Evento is deployed using Elastic Kubernetes Services (EKS), ensuring scalability, reliability, and high availability. The deployment process leverages Kubernetes manifest files, facilitating declarative management of the application’s infrastructure and services. This setup allows for easy scaling to accommodate varying loads, ensuring a smooth user experience even during peak times.

## DevSecOps Integration
Security and compliance are integral to Evento’s deployment strategy. The project incorporates DevSecOps practices, embedding security into every phase of the development lifecycle. Automated security checks, continuous integration/continuous deployment (CI/CD) pipelines, and regular audits ensure that the application remains secure, resilient, and compliant with industry standards.

## Project Details
🛠️ **Tools Explored:**
- Terraform & AWS CLI for AWS infrastructure
- Jenkins, Sonarqube, Terraform, Kubectl, and more for CI/CD setup
- Helm, Prometheus, and Grafana for Monitoring
- ArgoCD for GitOps practices

🚢 **High-Level Overview:**
- IAM User setup & Terraform magic on AWS
- Jenkins deployment with AWS integration
- EKS Cluster creation & Load Balancer configuration
- Private ECR repositories for secure image management
- Helm charts for efficient monitoring setup
- GitOps with ArgoCD - the cherry on top!

📈 **The journey covered everything from setting up tools to deploying a Three-Tier app, ensuring data persistence, and implementing CI/CD pipelines.**

## Getting Started

### Step 1: IAM Configuration
- Create a user `eks-admin` with `AdministratorAccess`.
- Generate Security Credentials: Access Key and Secret Access Key.

### Step 2: EC2 Setup
- Launch an Ubuntu instance in your favourite region (eg. region `us-east-2`).
- SSH into the instance from your local machine.

### Step 3: Install AWS CLI v2
``` shell
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
sudo apt install unzip
unzip awscliv2.zip
sudo ./aws/install -i /usr/local/aws-cli -b /usr/local/bin --update
aws configure
```

### Step 4: Install Docker
``` shell
sudo apt-get update
sudo apt install docker.io
docker ps
sudo chown $USER /var/run/docker.sock
```

### Step 5: Install kubectl
``` shell
curl -o kubectl https://amazon-eks.s3.us-west-2.amazonaws.com/1.19.6/2021-01-05/bin/linux/amd64/kubectl
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin
kubectl version --short --client
```

### Step 6: Install eksctl
``` shell
curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
sudo mv /tmp/eksctl /usr/local/bin
eksctl version
```

### Step 7: Setup EKS Cluster
``` shell
eksctl create cluster --name evento-cluster --region us-east-2 --node-type t2.medium --nodes-min 2 --nodes-max 2
aws eks update-kubeconfig --region us-west-2 --name Evento-cluster
kubectl get nodes
```

### Step 8: Run Manifests
``` shell
kubectl create namespace workshop
kubectl apply -f .
kubectl delete -f .
```

### Step 9: Install AWS Load Balancer
``` shell
curl -O https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.5.4/docs/install/iam_policy.json
aws iam create-policy --policy-name AWSLoadBalancerControllerIAMPolicy --policy-document file://iam_policy.json
eksctl utils associate-iam-oidc-provider --region=us-east-2 --cluster=Evento-cluster --approve
eksctl create iamserviceaccount --cluster=Evento-cluster --namespace=kube-system --name=aws-load-balancer-controller --role-name AmazonEKSLoadBalancerControllerRole --attach-policy-arn=arn:aws:iam::626072240565:policy/AWSLoadBalancerControllerIAMPolicy --approve --region=us-east-2
```

### Step 10: Deploy AWS Load Balancer Controller
``` shell
sudo snap install helm --classic
helm repo add eks https://aws.github.io/eks-charts
helm repo update eks
helm install aws-load-balancer-controller eks/aws-load-balancer-controller -n kube-system --set clusterName=my-cluster --set serviceAccount.create=false --set serviceAccount.name=aws-load-balancer-controller
kubectl get deployment -n kube-system aws-load-balancer-controller
kubectl apply -f full_stack_lb.yaml
```

### Cleanup
- To delete the EKS cluster:
``` shell
eksctl delete cluster --name evento-cluster --region us-east-2
```
