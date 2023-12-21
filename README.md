
# jenkins-pipeline-demo-python-flask  

## Overview

This project demonstrates a CI/CD pipeline using Jenkins for building and deploying a sample application to a Kubernetes cluster using ArgoCD. The project structure includes two branches in the GitHub repository: `master` for the application code and `manifest` for deployment manifests.

## Prerequisites

- GitHub Account
- AWS Account
- Kubernetes Cluster [Minikube or aws eks]
- [AWS CLI](https://aws.amazon.com/cli/)
- [Docker](https://docs.docker.com/get-docker/)
- [Jenkins](https://www.jenkins.io/doc/book/installing/)
- [ArgoCD](https://argoproj.github.io/argo-cd/getting_started/)

## Steps

### 1. GitHub Repository

Create a new GitHub repository with two branches:

```bash
# Create master branch for application code
git checkout -b master

# Create manifest branch for deployment.yaml
git checkout -b manifest
```

### 2. AWS EC2 Instance

Create an EC2 instance and install Jenkins and Docker:

```bash
# Install Docker
sudo yum update -y
sudo amazon-linux-extras install docker -y
sudo service docker start
sudo usermod -a -G docker ec2-user

# Install Jenkins
sudo wget -O /etc/yum.repos.d/jenkins.repo http://pkg.jenkins.io/redhat/jenkins.repo
sudo rpm --import http://pkg.jenkins.io/redhat/jenkins.io.key
sudo yum install jenkins -y
sudo service jenkins start
```

### 3. Jenkins Pipeline

- Open Jenkins in your browser 
- Unlock Jenkins using the provided initialAdminPassword.
- Install recommended plugins.
- Create a new pipeline job and configure it to pull code from the `master` branch.
- Add a Jenkinsfile to define the pipeline stages.


### 4. webhook for trigger the jenkins pipeline

after creating the jenkins pipeline for the master branch, copy the url of the jenkins.
go to the github repository 
go to settings
then go to webhook
click create webhook and paste url of jenkins { http://<ip-of-jenkins-ec2>:8080/github-webhook/ }
and click trigger for every push request


### 5. Kubernetes Cluster

Ensure you have a running Kubernetes cluster.

### 5. ArgoCD Installation

Install ArgoCD in your Kubernetes cluster:

```bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

### 6. ArgoCD Application Deployment

Login to ArgoCD:

```bash
argocd login your-argocd-server
```

Create an application using the `manifest` branch:

```bash
argocd app create your-app --repo https://github.com/yourusername/your-repo.git --path=manifest --dest-server=https://kubernetes.default.svc --dest-namespace=default
```

Sync the application:

```bash
argocd app sync your-app
```

Visit ArgoCD UI to monitor the deployment.

## Conclusion

This project showcases a complete CI/CD pipeline from code commit to application deployment using Jenkins and ArgoCD. Adjust configurations and settings based on your specific requirements and infrastructure.
