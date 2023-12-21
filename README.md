
# jenkins-pipeline-demo-python-flask  
       
   [or]

# Automating CI/CD with Jenkins, Docker, Argocd, and Minikube

[`YouTube Video  click here....`](https://youtu.be/MlZUvEAyiGc?si=VQA7EBkICe_9OIAM)


## Overview

This project demonstrates a CI/CD pipeline using Jenkins for building and deploying a sample application to a Kubernetes cluster using ArgoCD. The project structure includes two branches in the GitHub repository: `master` for the application code and `manifest` for deployment manifests.

<img src="screenshots/Screenshot from 2023-12-20 22-37-03.png">

## Prerequisites

- GitHub Account
- AWS Account
- Kubernetes Cluster [Minikube or aws eks]
- [`AWS CLI`](https://aws.amazon.com/cli/)
- [Docker](https://docs.docker.com/get-docker/)
- [Jenkins](https://www.jenkins.io/doc/book/installing/)
- [ArgoCD](https://argoproj.github.io/argo-cd/getting_started/)

## Steps

### 1. GitHub Repository

Create a new GitHub repository with two branches:

in the `master` branch we used an python flask application 

<img src="screenshots/Screenshot from 2023-12-20 20-00-51.png">

```bash
# Create master branch for application code
git checkout -b master
```
<img src="screenshots/Screenshot from 2023-12-20 19-58-59.png">

```bash
# Create manifest branch for deployment.yaml
git checkout -b manifest
```
<img src="screenshots/Screenshot from 2023-12-20 19-58-19.png">


### 2. AWS EC2 Instance

https://github.com/yuva19102003/DEVOPS-TOOL/tree/master/Jenkins

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
<img src="screenshots/Screenshot from 2023-12-20 19-53-48.png">


### 3. Jenkins Pipeline

- Open Jenkins in your browser 
- Unlock Jenkins using the provided initialAdminPassword.
- Install recommended plugins.
- -------------------------------------------------------------------------------------------------
- Install the following plugins for the demo.
- 
- Amazon EC2 plugin (No need to set up Configure Cloud after)
- Docker plugin  
- Docker Pipeline
- GitHub Integration Plugin
- Parameterized trigger Plugin
- ------------------------------------------------------------------------------------------------
- Create a new pipeline job and configure it to pull code from the `master` branch.
- Add a Jenkinsfile to define the pipeline stages.
<img src="screenshots/Screenshot from 2023-12-20 19-53-32.png">

-all the build images will pushed to the docker hub repository
<img src="screenshots/Screenshot from 2023-12-20 19-53-57.png">

- then create an another pipeline for `mainfest` branch.
<img src="screenshots/Screenshot from 2023-12-20 19-53-16.png">


### 4. webhook for trigger the jenkins pipeline

-after creating the jenkins pipeline for the master branch, copy the url of the jenkins.
-go to the github repository 
-go to settings
-then go to webhook
-click create webhook and paste url of jenkins { http://<ip-of-jenkins-ec2>:8080/github-webhook/ }
-and click trigger for every push request
<img src="screenshots/Screenshot from 2023-12-20 19-54-08.png">


### 5. Kubernetes Cluster

Ensure you have a running Kubernetes cluster.
<img src="screenshots/Screenshot from 2023-12-20 19-59-25.png">

### 5. ArgoCD Installation

Install ArgoCD in your Kubernetes cluster:
https://github.com/yuva19102003/DEVOPS-TOOL/tree/master/Argocd

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
<img src ="/screenshots/Screenshot from 2023-12-20 19-52-32.png">
Visit ArgoCD UI to monitor the deployment.

## Conclusion

This project showcases a complete CI/CD pipeline from code commit to application deployment using Jenkins and ArgoCD. Adjust configurations and settings based on your specific requirements and infrastructure.
