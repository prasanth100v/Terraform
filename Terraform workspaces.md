## Terraform workspaces
Terraform workspaces are a clean way to manage multiple environments (dev / test / prod) using the same Terraform configuration, while keeping separate state files.
> 👉 One codebase, many environments, isolated states.
### 📌 Folder Structure
```
terraform-ec2-workspace/
├── main.tf
├── provider.tf
├── variables.tf
├── terraform.tfstate.d/
│   ├── dev/
│   └── prod/
```

## Why use Workspaces?
> ✅ Avoid duplicating Terraform code ✅ Separate infrastructure per environment ✅ Easy switching between environments
## Basic Workspace Commands
1️⃣ List workspaces
> 👉 terraform workspace list

2️⃣ Create a new workspace
> 👉 terraform workspace new dev  & terraform workspace new prod

3️⃣ Switch workspace
> 👉 terraform workspace select dev

4️⃣ Show current workspace
> 👉 terraform workspace show

## How Terraform Stores State
```
terraform.tfstate.d/            # Each workspace gets its own state file
├── dev/terraform.tfstate
├── prod/terraform.tfstate
```
> (With remote backends like S3, Terraform handles this automatically.)
### Workspaces with Remote Backend (S3 Example)
```
backend "s3" {
  bucket = "my-terraform-states"
  key    = "ec2/terraform.tfstate"
  region = "ap-south-1"
}
```

## 🧾 Create Terraform Files
#### 📄 provider.tf
```
📄 provider.tf
provider "aws" {
  region = "ap-south-1"
}
```

### Using Variables with Workspaces (Best Practice)
#### 📄 variables.tf
```
variable "instance_type" {
  type = map(string)
  default = {
    dev  = "t3.micro"
    prod = "t3.large"
  }
}
```
#### 📄 main.tf
```
resource "aws_instance" "ec2" {
  ami           = "ami-0f5ee92e2d63afc18" # Amazon Linux 2 (Mumbai)
  instance_type = var.instance_type[terraform.workspace]

  tags = {
    Name = "ec2-${terraform.workspace}"
    Env  = terraform.workspace
  }
}
```


### Initialize Terraform
```
terraform init
```
### Create DEV & PROD Workspaces
```
terraform workspace new dev
terraform workspace new prod
```
### Check workspaces
```
terraform workspace list
```
### Select Workspace
```
terraform workspace select dev
```
### Check current workspace:
```
terraform workspace show
```
### Create EC2 Instance (DEV)
```
terraform plan
terraform apply
```

> 👉 This will create: EC2 with t3.micro & Name tag: ec2-dev

### Create EC2 for PROD Switch workspace:
```
terraform workspace select prod
```
Apply again:
```
terraform apply
```
> 👉 This will create: EC2 with t3.small & Name tag: ec2-prod

## 📦 Verify in AWS Console
> You will see 2 EC2 instances: ec2-dev & ec2-prod

Each is from the same code, but different workspace & state.

## 🧹 Destroy (Optional)
Destroy DEV only:
```
terraform workspace select dev
terraform destroy
```
Destroy PROD:
```
terraform workspace select prod
terraform destroy
```
