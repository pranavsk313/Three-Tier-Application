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

## 4. Getting Started
To get started with this project, refer to our [comprehensive guide](https://amanpathakdevops.medium.com/advanced-end-to-end-devsecops-kubernetes-three-tier-project-using-aws-eks-argocd-prometheus-fbbfdb956d1a) that walks you through IAM user setup, infrastructure provisioning, CI/CD pipeline configuration, EKS cluster creation, and more.

### Step 1.  We need to create an IAM user and generate the AWS Access key
- Create a new IAM User on AWS and give it to the AdministratorAccess for testing purposes (not recommended for your Organization's Projects)
- Go to the AWS IAM Service and click on Users.
- Generate Security Credentials: Access Key and Secret Access Key.

### Click on Create user and Attach **AdministratorAccess** directly.
![4_user_creation](https://github.com/pranavsk313/Three-Tier-Application/assets/122976840/1e1b44df-a3a0-4b59-b087-28029c283664)

### Creating the Access key for "pranav" user.
![6_Access_key](https://github.com/pranavsk313/Three-Tier-Application/assets/122976840/0b558d30-e36a-46e8-86ff-3328696f6d9d)

## Step 2: Launch an EC2 instance

### Launch an RHEL instance in your favourite region (eg. region `us-east-1`).
![1_instance launch](https://github.com/pranavsk313/Three-Tier-Application/assets/122976840/e0ac99dc-4cbe-4904-a102-a2cc7068975b)

### SSH into the instance from your local machine. (putty, MobaXterm)
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
## Step 4: Clone the Code
```bash



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
eksctl create cluster --name three-tier-cluster --region us-west-2 --node-type t2.medium --nodes-min 2 --nodes-max 2
aws eks update-kubeconfig --region us-west-2 --name three-tier-cluster
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
eksctl utils associate-iam-oidc-provider --region=us-west-2 --cluster=three-tier-cluster --approve
eksctl create iamserviceaccount --cluster=three-tier-cluster --namespace=kube-system --name=aws-load-balancer-controller --role-name AmazonEKSLoadBalancerControllerRole --attach-policy-arn=arn:aws:iam::626072240565:policy/AWSLoadBalancerControllerIAMPolicy --approve --region=us-west-2
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
eksctl delete cluster --name three-tier-cluster --region us-west-2
```

## Contribution Guidelines
- Fork the repository and create your feature branch.
- Deploy the application, adding your creative enhancements.
- Ensure your code adheres to the project's style and contribution guidelines.
- Submit a Pull Request with a detailed description of your changes.

## Rewards
- Successful PR merges will be eligible for exciting prizes!

## Support
For any queries or issues, please open an issue in the repository.

---
Happy Learning! 🚀👨‍💻👩‍💻
