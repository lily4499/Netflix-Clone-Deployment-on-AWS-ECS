
# 🎬 Netflix Clone Deployment on AWS ECS 🚀

> Fully automated deployment of a Netflix Clone React app using **Amazon ECS (Fargate)** with **manual setup**, **Terraform infrastructure as code**, and **CI/CD pipeline using Jenkins**.

---

## 🌍 Real-World Scenario

You're a DevOps Engineer at a media startup building a **Netflix-like video platform**. Your job is to deploy and manage the app using modern DevOps practices.

---

## 📦 Project Structure

```bash
netflix-clone-ecs/
├── terraform/
│   ├── main.tf
│   ├── variables.tf
│   ├── outputs.tf
│   ├── ecs-cluster.tf
│   ├── ecs-task-def.tf
│   ├── ecs-service.tf
│   ├── alb.tf
│   ├── vpc.tf
│   ├── security-groups.tf
│   └── terraform.tfvars
├── Jenkinsfile
└── README.md
```
---
## file-setup.py

```python


```

---

### 🔁 Clone Starter Repo
Build, test image locally, and push the Netflix React Clone to DockerHub.

```bash
git clone https://github.com/lily4499/netflix-react-clone.git
cd netflix-react-clone
npm install    #This installs all the required packages listed in package.json.
npm start    #This runs the app in development mode.
npm test   #If the project has test scripts defined (check package.json under "scripts")
npm run build   #Optional: Build for Production. Builds the app for production to the build/ folder.


docker build -t laly9999/netflix-react-clone:latest .
docker run -d -p 3000:80 laly9999/netflix-react-clone:latest
docker push laly9999/netflix-react-clone:latest

```


---

## 🧱 Step 1: Manual ECS Setup via AWS UI

> 🧪 Purpose: Validate Netflix Clone runs correctly before automating.


### 🛠️ 1. Create ECS Cluster

- Go to **ECS → Clusters → Create Cluster**
- Choose **Networking only (Fargate)**
- Name it: `netflix-cluster`

### 🛠️ 2. Create Task Definition

- Launch Type: Fargate
- Task Name: `netflix-task`
- Container Name: `netflix-clone`
- Image: `laly9999/netflix-react-clone:latest`
- Port: `3000`
- Task Size: `1 vCPU`, `2GB`
- Click **Create**

### 🛠️ 3. Create Service

- ECS Cluster: `netflix-cluster`
- Task Definition: `netflix-task`
- Desired Tasks: `1`
- Load Balancer: Application Load Balancer
- Target Group: port 3000
- VPC: default or custom
- Subnets: pick 3 across AZs
- Security Group: Allow HTTP (80) and App port (3000)

**✅ Output:**  
Access the app via the **ALB DNS name**

---

## ⚙️ Step 2: ECS Infrastructure using Terraform

> 🧪 Purpose: Automate and codify the ECS infrastructure

### 🧩 Terraform Modules

- `vpc.tf`: Creates VPC and subnets
- `security-groups.tf`: Opens ports
- `ecs-cluster.tf`: Provisions Fargate cluster
- `ecs-task-def.tf`: App image, CPU, memory, container config
- `ecs-service.tf`: Deploys ECS service and attaches ALB
- `alb.tf`: ALB with listener and target group

### 🧪 Run Terraform

```bash
cd terraform/
terraform init
terraform plan
terraform apply
```

### ✅ Output

```text
ALB_DNS_NAME = http://<alb-dns-name>
```

---

## 🤖 Step 3: Full Automation with Jenkins + Terraform

> 🧪 Purpose: Automate infra and app deploy with each commit or image update

### 🔧 Jenkins Setup

- Install Plugins:
  - Terraform
  - Git
  - Docker
- Add Credentials:
  - AWS Access Key
  - DockerHub (if building images)
  - GitHub Access Token

### 🗂️ Jenkinsfile Example

```groovy
pipeline {
  agent any

  parameters {
    string(name: 'APP_IMAGE', defaultValue: 'laly9999/netflix-react-clone:latest', description: 'Docker Image')
    string(name: 'CLUSTER_NAME', defaultValue: 'netflix-cluster')
  }

  environment {
    AWS_DEFAULT_REGION = 'us-east-1'
  }

  stages {
    stage('Checkout') {
      steps {
        git url: 'https://github.com/lily4499/netflix-react-clone.git'
      }
    }

    stage('Terraform Init & Apply') {
      steps {
        dir('terraform') {
          sh 'terraform init'
          sh "terraform apply -auto-approve -var='app_image=${params.APP_IMAGE}' -var='cluster_name=${params.CLUSTER_NAME}'"
        }
      }
    }

    stage('Verify') {
      steps {
        echo 'Deployment complete. Access app via ALB DNS.'
      }
    }
  }

  post {
    always {
      cleanWs()
    }
  }
}
```

---

## 🔁 Dynamic Parameter Injection

- Set parameters dynamically using:
```groovy
-var='app_image=${params.APP_IMAGE}'
```

---

## ✅ Final Outcome

- Netflix Clone app deployed on **ECS Fargate**
- Load Balanced and Publicly Accessible
- Full infrastructure + deployment automated via **Jenkins**

---

## 📎 Useful Resources

- [DockerHub Image](https://hub.docker.com/r/laly9999/netflix-react-clone)
- [GitHub Source](https://github.com/lily4499/netflix-react-clone)
- [AWS ECS Documentation](https://docs.aws.amazon.com/ecs/latest/developerguide/what-is-ecs.html)

---
``` 

---

## 🚀 Project Recap: Netflix Clone on ECS

### ✅ **Step 1 – Manual ECS Deployment (UI)**  
- Use AWS Console to:
  - Create ECS Fargate Cluster  
  - Define a Task with Docker image  
  - Launch a Service with Load Balancer  
- Access the app via the **ALB DNS name**.

### ✅ **Step 2 – Infrastructure as Code with Terraform**  
- Codify ECS Cluster, Task Definition, Service, Load Balancer, and Networking.
- Reusable Terraform files for repeatable deployments.
- Commands: `terraform init`, `plan`, `apply`

### ✅ **Step 3 – Full Automation with Jenkins + Terraform**  
- Jenkins pipeline with dynamic parameters.
- Automates:
  - Cloning repo
  - Running Terraform
  - Deploying the app
- Clean CI/CD setup for continuous deployment.

---

## 🎯 Why Deploy to Amazon ECS?

| Feature                          | Benefit                                                                 |
|----------------------------------|-------------------------------------------------------------------------|
| **Fargate (Serverless)**         | No EC2 management — just specify CPU & memory                          |
| **Deep AWS Integration**         | Built-in IAM, VPC, CloudWatch, ALB support                             |
| **Scalability & Reliability**    | Automatically scales across AZs                                        |
| **Cost-Efficiency**              | Pay only for what you use                                              |
| **CI/CD Friendly**               | Easily integrated with Jenkins, Terraform, GitHub                      |
| **Simple & Fast to Use**         | Easier than managing Kubernetes (EKS) for many teams                   |

---

