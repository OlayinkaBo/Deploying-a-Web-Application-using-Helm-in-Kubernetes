# Deploying a Web Application using Helm in Kubernetes

## Why Helm charts are needed
- Helm charts are packages or collections of kubernetes resources.
- They simplify the deployment and management of applications in Kubernetes by binding all neccessary resources into a single manageable unit.
- Helm charts promote reusability and consistency, allowing you to define, install and upgrade even the most complex Kubernetes application easily.
- Helm installs the whole dependency tree of a project if you run the install command for the top-level chart. You just a single command to install your entire application, instead of listing the files to install via kubectl.

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


## Step 3: Customize the Helm chart

It's pretty easy to create a chart in Helm. First, you need to have Helm installed. Then, just type in helm create <chart name> and it will create a directory filled with files and other directories. Those files are required for Helm to create a chart.

Let's take a closer look at what this file tree looks like and what the files are within it:

- **chart.yaml:** This is where you'll put the information related to your chart. That includes the chart version, name, and description so you can find it if you publish it on an open repository. Also in this file you'll be able to set external dependencies using the dependencies key.

- **values.yaml:** Like we saw before, this is the file that contains defaults for variables.

- **templates (dir):** This is the place where you'll put all your manifest files. Everything in here will be passed on and created in Kubernetes.

- **charts:** If your chart depends on another chart you own, or if you don't want to rely on Helm's default library (the default registry where Helm pull charts from), you can bring this same structure inside this directory. Chart dependencies are installed from the bottom to the top, which means if chart A depends on chart B, and B depends on C, the installation order will be C ->B ->A.

There are other fields, but these are the most common, and they're the required ones.

**A quick note:** When installing Helm, make sure you're installing version 3. Version 2 still works, but it needs a server-side component called Tiller, which ties your helm installation to a single cluster. Helm 3 removed this need with the addition of several CRDs, but it's not supported in all Kubernetes versions.

1. Modify `values.yaml` using a text editor.

```
replicaCount: 2

image:
  repository: nginx
  tag: stable
  pullPolicy: IfNotPresent
```

![alt text](<Images/modify values.PNG>)

- This configuration will deploy 2 replicas (`replicaCount: 2`) of the nginx server.

- Save your changes

2. Customize `templates/deployment.yaml`:

- Open the `deployment.yaml` file in the `templates/` directory.

- Remove the line below from under 