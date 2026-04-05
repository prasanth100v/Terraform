# 🔗 Manage dependencies between resources

Terraform automatically understands dependencies using references.

resource "aws_vpc" "main" {
  cidr_block = "10.0.0.0/16"
}

resource "aws_subnet" "subnet1" {
  vpc_id = aws_vpc.main.id
}

---

## 🌍 How do you manage different environments (dev, staging, prod) using Terraform?

Use Terraform workspaces or create separate directories/modules for each environment.

### 🧩 Workspaces

terraform workspace new dev  
terraform workspace new prod  
terraform workspace select dev  

### 📁 Folder structure:
terraform/  
/dev  
/staging  
/prod  

---

## 🤝 How do you share Terraform state between team members?

Use a remote backend like AWS S3 for shared state and S3 native state locking.

terraform {
  backend "s3" {
    bucket       = "mycompany-terraform-state"
    key          = "dev/terraform.tfstate"
    region       = "us-east-1"
    encrypt      = true
    use_lockfile = true
  }
}

---

## ⚠️ A resource was deleted manually from the AWS console. What will happen when you run terraform apply again?

Terraform will detect the resource is missing (from state vs actual) and recreate it during the next apply.

---

## 🛡️ You want to prevent accidental deletion of a resource. How?

Use the lifecycle block with prevent_destroy.

resource "aws_s3_bucket" "my_bucket" {
  bucket = "secure-bucket"

  lifecycle {
    prevent_destroy = true
  }
}

---

## 🎯 How can you update only a specific resource without touching the rest?

Use the -target flag:

terraform apply -target=aws_instance.my_instance

---

## 🔁 How do you reuse Terraform modules with different parameters?

Use input variables when calling the module.

module "vpc_dev" {
  source     = "./vpc"
  cidr_block = "10.0.0.0/16"
}

module "vpc_prod" {
  source     = "./vpc"
  cidr_block = "10.1.0.0/16"
}

---

# 🔐 Handling Secrets and Sensitive Data in Terraform

Managing sensitive data like passwords, API keys, and credentials securely is critical when working with Terraform. Here are the best practices and methods for handling secrets properly:

---

## ❌ 1. Never Store Secrets in Plaintext
Avoid committing secrets directly in your Terraform code:

# BAD PRACTICE - Never do this!
resource "aws_db_instance" "example" {
  username = "admin"
  password = "s3cr3tP@ssw0rd"   # Exposed in state file and version control
}

---

## ✅ 2. Recommended Approaches:

### 🌱 A. Use Environment Variables

variable "db_password" {
  description = "Database password"
  type        = string
  sensitive   = true      # Use sensitive = true in variable definitions
}

resource "aws_db_instance" "example" {
  password = var.db_password
}

Then pass the value via environment variable:

export TF_VAR_db_password="s3cr3tP@ssw0rd"
terraform apply

---

### 🔐 B. AWS Secrets Manager  
### 🏦 C. HashiCorp Vault  
### 🔒 D. Encrypted S3 Backend with KMS (Mark Variables as Sensitive)
