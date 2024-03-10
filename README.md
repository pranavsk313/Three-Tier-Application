# Welcome to the 'Three-Tier-App' repository!

## 1. Overview
This repository hosts the `Three-Tier-App`. 
This repository hosts the code for a Three-Tier Web Application (ThreeTierApp), developed using ReactJS, NodeJS, and MongoDB. The challenge involves deploying the application on AWS Elastic Kubernetes Service (EKS).
- Frontend: Developed with ReactJS, offering a dynamic and responsive user interface.
- Backend: Powered by NodeJS, providing robust server-side logic and API endpoints.
- Database: Utilizing MongoDB for flexible and scalable data storage needs.
- Deployment: Leveraging AWS EKS for orchestrating containers and managing the application's infrastructure in a scalable and resilient manner.

## 2. Prerequisites:
- Before starting the project, ensure you have the following prerequisites:
- An AWS account with the necessary permissions to create resources.
- Terraform and AWS CLI installed on your local machine.
- Basic familiarity with Kubernetes, Docker, Jenkins, and DevOps principles.

## 3. Features:
Seamless integration between frontend and backend components.
RESTful API endpoints for data interaction.
MongoDB database for efficient data storage and retrieval.
Scalable deployment on AWS EKS, ensuring high availability and fault tolerance.

## 4. Getting Started.

### Step 1.  We need to create an IAM user and generate the AWS Access key
- Create a new IAM User on AWS and give it to the AdministratorAccess for testing purposes (not recommended for your Organization's Projects)
- Go to the AWS IAM Service and click on Users.
- Generate Security Credentials: Access Key and Secret Access Key.

### 1. Click on Create user and Attach **AdministratorAccess** directly.
![4_user_creation](https://github.com/pranavsk313/Three-Tier-Application/assets/122976840/1e1b44df-a3a0-4b59-b087-28029c283664)

### 2. Creating the Access key for "pranav" user.
![6_Access_key](https://github.com/pranavsk313/Three-Tier-Application/assets/122976840/0b558d30-e36a-46e8-86ff-3328696f6d9d)

## Step 2: Launch an EC2 instance

### 1. Launch an RHEL instance in your favourite region (eg. region `us-east-1`).
![1_instance launch](https://github.com/pranavsk313/Three-Tier-Application/assets/122976840/e0ac99dc-4cbe-4904-a102-a2cc7068975b)

### 2. SSH into the instance from your local machine. (putty, MobaXterm)
![1 1_local_access](https://github.com/pranavsk313/Three-Tier-Application/assets/122976840/25e79d77-a1c6-469f-8b3a-f2a87cccc4e1)

## Step 3: Install AWS CLI v2
### Downloading the zip file for AWS-cli 
```shell
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
```
### To "unzip" this file install:
```bash
sudo yum install unzip
unzip awscliv2.zip
```
### To update an existing installation of the AWS CLI
```bash
sudo ./aws/install -i /usr/local/aws-cli -b /usr/local/bin --update
```
### Now Configure the AWS Credentials.
```bash
aws configure
```
![7_aws Configuration](https://github.com/pranavsk313/Three-Tier-Application/assets/122976840/38ac6be9-02bd-48df-92e7-747418ede8a5)

## Step 3: Install Docker on your system.
### To uninstall old version of docker 
```bash
sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
```
### Set up the repository
```bash
sudo yum install -y yum-utils
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
```
### To install the latest version
```bash
sudo yum install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```
### Start and Enable docker
```bash
sudo systemctl start docker
sudo systemctl enable docker
```
### If you are using the privillaged user then: 
```bash
sudo usermod -aG docker user_name
```
## Step 4: Clone the Github Code:
```bash
sudo yum install git -y
https://github.com/pranavsk313/Three-Tier-Application
```
## Step 5: 
### Inside directry create the Dockerfile for frontend and backend.
### 1. Frontend
### Dockerfile for Frontend:
```bash
FROM node:14
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
CMD [ "npm", "start" ]
```
### 2. Backend 
### Dockerfile for Backend:
```bash
FROM node:14
WORKDIR /app
COPY package*.json ./
RUN npm install 
COPY . .
CMD ["node", "index.js"]
```

## Step 6: Create the Public repository on ECR (Elastic Container Registry):
![8_ECR_registry_creation](https://github.com/pranavsk313/Three-Tier-Application/assets/122976840/81eac788-41fe-4330-aff1-e254ac49cf2d)


### 1. How to Push image to ECR:
![9_push_command](https://github.com/pranavsk313/Three-Tier-Application/assets/122976840/a15f2e37-8549-4058-a186-4798264cb040)

### 2. Build and push the docker images:
![10_image_pushed_to_ECR](https://github.com/pranavsk313/Three-Tier-Application/assets/122976840/3432f8f9-caad-479c-b78c-7c3d30e61c60)

### 3. Output of ECR:
![11_ECR_image](https://github.com/pranavsk313/Three-Tier-Application/assets/122976840/1a4e6ebf-e5d6-4d57-b2b2-f08a2bd06016)

![15_ECR_verified](https://github.com/pranavsk313/Three-Tier-Application/assets/122976840/d7fbbcfd-e9e0-497c-9535-0a672f5c5c8b)

# [Note:- Create same for backend ]


## Step 7: Install kubectl:
- We are installing **kubectl** to control the kubernetes cluster.
```bash
curl -o kubectl https://amazon-eks.s3.us-west-2.amazonaws.com/1.19.6/2021-01-05/bin/linux/amd64/kubectl
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin
kubectl version --short --client
```

## Step 8: Install eksctl:
**"eksctl"** used to create the kubernetes cluster on the EKS.
```bash
curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
sudo mv /tmp/eksctl /usr/local/bin
eksctl version
```


## Step 9: Setup EKS Cluster
```bash
eksctl create cluster --name three-tier-cluster-app --region us-east-1 --node-type t2.medium --nodes-min 2 --nodes-max 2
aws eks update-kubeconfig --region us-east-1 --name three-tier-cluster-app
kubectl get nodes
```
### 1. Installation process:
![17_EKS_cluster_creation](https://github.com/pranavsk313/Three-Tier-Application/assets/122976840/a6099e65-a97a-4344-8535-afbde912f5ec)
### 2. Cluster is ready on AWS console:

![17 1_cluster_ready](https://github.com/pranavsk313/Three-Tier-Application/assets/122976840/a8e1e9b6-3fe0-4b4a-8c48-e4e83d967839)

## Step 9: Go inside Kubernetes-manifests-file and deploy the Application on EKS: 
## 1. Deploy Database :
#### 1. First Create the Namespace:
```bash
kubectl create namespace three-tier
```

#### 2. Create the Deployment, Secret and Service file:
```bash
cd Three-Tier-Application/Kubernetes-Manifests-files/Database
kubectl apply -f deployment.yaml
kubectl apply -f secrets.yaml
kubectl apply -f service.yaml
```
#### 3. To view the deployment, pods, service:
```bash
kubectl get deployment -n three-tier
kubectl get pods -n three-tier
kubectl get svc -n three-tier
kubectl get secrets -n three-tier
```
#### 4. Output:
![18_k8s_database](https://github.com/pranavsk313/Three-Tier-Application/assets/122976840/f5272591-89a4-4bcd-8541-5528f9e8f2cd)

## 2. Backend:
####  1. Create the Deployment and Service file:  
### [Note: While deploying Backend Make sure to Change ECR URL:]
```bash
cd Three-Tier-Application/Kubernetes-Manifests-files/Backend
vi deployment.yaml
```
![19_image_backend](https://github.com/pranavsk313/Three-Tier-Application/assets/122976840/46d96872-4b37-4eeb-9e3d-329fec2a1575)

![19 1_image_ecr](https://github.com/pranavsk313/Three-Tier-Application/assets/122976840/c600e159-c305-48af-b2ae-44b437b5c452)

#### 2. Makes Changes and Deploy:
```bash
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
kubectl get deployment -n three-tier
kubectl get pods -n three-tier
kubectl get svc -n three-tier
```
![19 2_backend_output](https://github.com/pranavsk313/Three-Tier-Application/assets/122976840/475255c4-fe04-4d5f-b713-71795c0b9749)

#### 3. Ensure Database is connected to Backend:
```bash
kubectl logs <podname> -n three-tier
```
![19 3_backend_connected_to_databse](https://github.com/pranavsk313/Three-Tier-Application/assets/122976840/847d748d-fc06-4c96-a868-dc0fe8b2f78a)

## 3. Frontend:
#### 1. Create the Deployment and Service file:
### [Note: While deploying Frontend Make sure to Change ECR URL:]
```bash
cd Three-Tier-Application/Kubernetes-Manifests-files/Frontend
vi deployment.yaml
```
![20 1_change_image](https://github.com/pranavsk313/Three-Tier-Application/assets/122976840/2a28c5d3-cf53-4a32-9363-c1dd6d2cc524)

#### 2. Makes Changes and Deploy:
```bash
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
kubectl get deployment -n three-tier
kubectl get pods -n three-tier
kubectl get svc -n three-tier
```
#### 3. Output of Frontend:
![20_frontend_creation](https://github.com/pranavsk313/Three-Tier-Application/assets/122976840/9cd941db-99b9-45af-ac01-f47f66ecc0c8)

### Step 9: Install AWS Load Balancer
``` bash
curl -O https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.5.4/docs/install/iam_policy.json
aws iam create-policy --policy-name AWSLoadBalancerControllerIAMPolicy --policy-document file://iam_policy.json
eksctl utils associate-iam-oidc-provider --region=us-east-1 --cluster=three-tier-cluster-app --approve
eksctl create iamserviceaccount --cluster=three-tier-cluster-app --namespace=kube-system --name=aws-load-balancer-controller --role-name AmazonEKSLoadBalancerControllerRole --attach-policy-arn=arn:aws:iam::223065826485:policy/AWSLoadBalancerControllerIAMPolicy --approve --region=us-east-1
```
![20_LOAD-BALANCER](https://github.com/pranavsk313/Three-Tier-Application/assets/122976840/67d379a5-104f-4fdd-ad96-02602476ef22)


### Step 10: Deploy AWS Load Balancer Controller
```bash
sudo snap install helm --classic
helm repo add eks https://aws.github.io/eks-charts
helm repo update eks
helm install aws-load-balancer-controller eks/aws-load-balancer-controller -n kube-system --set clusterName=my-cluster --set serviceAccount.create=false --set serviceAccount.name=aws-load-balancer-controller
kubectl get deployment -n kube-system aws-load-balancer-controller
kubectl apply -f full_stack_lb.yaml
```
## Output:
![22_output](https://github.com/pranavsk313/Three-Tier-Application/assets/122976840/1831d8d8-5762-4160-9f1b-7c587abb9bfb)

### Cleanup
- To delete the EKS cluster:
``` bash
eksctl delete cluster --name three-tier-cluster-app --region us-east-1
```
---
Thank you..! 
