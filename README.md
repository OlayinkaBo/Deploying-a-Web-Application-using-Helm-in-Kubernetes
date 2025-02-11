# Deploying a Web Application using Helm in Kubernetes

## Overview
We will deploy a simple web application in a kubernetes cluster using Helm. This demo covers using Helm charts, customizing `deployments` with `templates` and `values`, installing and running Helm, and integrating Helm into a CI/CD pipeline.

## Steps
1. Install Helm as well as other tools and packages.
2. Create a new Helm chart
3. Customize the Helm chart
4. Deploying and Application
5. Integrating Helm with CI/CD (Jenkins)
6. Update Helm chart and trigger Jenkins pipeline.


## Step 1: Install Helm
From Apt (Debian/Ubuntu)

```
curl https://baltocdn.com/helm/signing.asc | gpg --dearmor | sudo tee /usr/share/keyrings/helm.gpg > /dev/null
sudo apt-get install apt-transport-https --yes
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/helm.gpg] https://baltocdn.com/helm/stable/debian/ all main" | sudo tee /etc/apt/sources.list.d/helm-stable-debian.list
sudo apt-get update
sudo apt-get install helm
```

![alt text](<Images/Helm Install.PNG>)

![alt text](<Images/Helm Install2.PNG>)

### - Install minikube to run the kubernetes cluster for the demo

```
curl -LO https://github.com/kubernetes/minikube/releases/latest/download/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube && rm minikube-linux-amd64
```
![alt text](<Images/minikube Install.PNG>)

#### Start minikube 
`minikube start --driver=docker`

![alt text](<Images/start minikube.PNG>)

#### Install kubectl

`sudo snap install kubectl --classic`

![alt text](<Images/install kubectl.PNG>)


### - Install Docker to drive minikube

```
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
```

```
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```
![alt text](<Images/Docker Install.PNG>)

![alt text](<Images/Docker Install2.PNG>)

![alt text](<Images/Docker Install3.PNG>)

### - Install and configure AWS cli for access and permission

`sudo snap install aws-cli --classic`

![alt text](<Images/AWS CLI Install.PNG>)

`aws configure`

![alt text](<Images/AWS configure.PNG>)

## Step 2: Create a new Helm chart

- Create and cd into a new directory `my-webapp`
- Create a new Helm chart `my-app`

  `helm create my-app`
  
 ![alt text](<Images/create Helm chart.PNG>) 

- Initial git and push into remote git repository

![alt text](<Images/git init.PNG>)

![alt text](<Images/git push.PNG>)





