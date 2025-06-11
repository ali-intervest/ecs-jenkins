# 📦 ECS-Jenkins Hello World App Deployment (EC2 Launch Type)

This project provisions an **ECS Cluster (EC2 launch type)** running a **Dockerized Python app** using:

- ✅ **Terraform** (Infrastructure as Code)
- 🚀 **Amazon ECS**
- 🏗️ **EC2 instances**
- 📦 **ECR** (for storing container images)
- ⚙️ **Application Load Balancer (ALB)**
- 🤖 **Jenkins** for CI/CD automation

## 🧱 Architecture

```
                       ┌────────────────────────┐
                       │   GitHub Repo          │
                       └─────────┬──────────────┘
                                 │
                           Git Pull (Jenkins)
                                 │
                         ┌───────▼────────┐
                         │   Jenkins CI   │
                         └───────┬────────┘
                                 │
              Build + Push Docker Image to ECR
                                 │
                    ┌────────────▼─────────────┐
                    │  Amazon Elastic Container│
                    │     Registry (ECR)       │
                    └────────────┬─────────────┘
                                 │
                       ┌─────────▼──────────┐
                       │     ECS Cluster    │
                       │ (EC2 launch type)  │
                       └─────────┬──────────┘
                   Run Task      │
                                 │
              ┌──────────────────▼────────────────────┐
              │            EC2 Instances              │
              │ (ECS Optimized AMI, Docker, ECS Agent)│
              └──────────────────┬────────────────────┘
                                 │
                        ┌────────▼───────┐
                        │     ALB        │
                        │ (Listens on 80)│
                        └────────┬───────┘
                                 │
                             Internet
```

## 🛠️ Components

### 📁 Terraform Modules

- **VPC**: Subnets, Internet Gateway, Route Tables
- **ALB**: Listener + Target Group
- **ECS Cluster**: EC2 launch type
- **Auto Scaling Group**: Launch template with ECS user-data
- **IAM Roles**: ECS instance role and service role

### 🐳 Docker

- `hello-world-app/`: Python app with Dockerfile
- Built via Jenkins, tagged as `:latest` or `${BUILD_NUMBER}`

### 🤖 Jenkins

- Builds Docker image
- Pushes to ECR
- Deploys via `aws ecs update-service`

## 🚀 How to Deploy

### 1️⃣ Clone the Repo

```bash
git clone https://github.com/ali-intervest/ecs-jenkins.git
cd ecs-jenkins
```

### 2️⃣ Initialize Terraform

```bash
cd terraform/
terraform init
```

### 3️⃣ Apply the Infra

```bash
terraform apply -auto-approve
```

> Outputs include ALB DNS and ECS cluster name

## 🧪 Access the App

After deployment:

```bash
curl http://<ALB_DNS>
```

Or open in browser:

```
http://<your-alb-dns>.amazonaws.com
```

## 🔄 Jenkins CI/CD

1. Jenkins clones from GitHub
2. Builds and tags Docker image (`hello-world-app`)
3. Pushes to ECR
4. Deploys to ECS via `aws ecs update-service`

## 🧹 Cleanup

```bash
terraform destroy -auto-approve
```

## 🧾 Notes

- ECS AMI fetched via SSM `/aws/service/ecs/optimized-ami/amazon-linux-2/recommended/image_id`
- User-data ensures ECS agent starts and joins the cluster
- Ensure IAM roles have `AmazonEC2ContainerServiceforEC2Role` and ECR access policies

## 📬 Contact

Made with ❤️ by [@ali-intervest](https://github.com/ali-intervest)
