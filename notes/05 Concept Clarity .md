# 🔗 Manage dependencies between resources in Terraform
## 🚀 How Terraform Handles Dependencies
✨ Terraform automatically understands dependencies when you reference one resource inside another.

```hcl
resource "aws_vpc" "main" {
  cidr_block = "10.0.0.0/16"
}

resource "aws_subnet" "subnet1" {
  vpc_id = aws_vpc.main.id
}
```

* 👉 Terraform understands  : `VPC must be created FIRST → Then Subnet`  ✔️ No need to manually define dependency.
* ⚠️ When to Use depends_on : 👉 Only when Terraform cannot detect dependency automatically  `depends_on = [aws_vpc.main]`

---

# 🌍 Managing Environments (Dev / Staging / Prod)
 * I manage environments using Terraform workspaces for lightweight setups and separate directories or modules for larger projects.
 * This allows me to separate configurations and state files across dev, staging, and production.

## 🚀 Approach 1
### 🧩 Workspace
```Bash
terraform workspace new dev  
terraform workspace new prod  
terraform workspace select dev  
```
* How It Works
   * 👉 Same code
   * 👉 Different state files

## 📁 Approach 2
### Folder Structure
```hcl
terraform/
 ├── dev/
 ├── staging/
 └── prod/
```

---

# 🤝 Sharing Terraform State (Team Collaboration)
## 🚀 Use Remote Backend (S3)
 * To share Terraform state, I use a remote backend like `AWS S3` with `state locking` enabled.
 * This allows multiple team members to collaborate safely without overwriting each other's changes.

```hcl
terraform {
  backend "s3" {
    bucket       = "mycompany-terraform-state"
    key          = "dev/terraform.tfstate"
    region       = "us-east-1"
    encrypt      = true
    use_lockfile = true
  }
}
```

### 🔐 Key Benefits
   * 👥 Shared state across team
   * 🔒 State locking (prevents conflicts)
   * 🛡️ Secure storage

---

## ⚠️ A resource was deleted manually from the AWS console. What will happen when you run terraform apply again?
 If a resource is deleted manually from AWS, Terraform will detect the drift the resource is missing (from state vs actual) and recreate it during the next plan/apply.

---

## 🛡️ You want to prevent accidental deletion of a resource. How?
I Use lifecycle block with `prevent_destroy` to protect critical resources from accidental deletion.

```hcl
resource "aws_s3_bucket" "my_bucket" {
  bucket = "secure-bucket"

  lifecycle {
    prevent_destroy = true
  }
}
```

---

## 🎯 How can you update only a specific resource without touching the rest?
  * Update Only One Resource Use the `-target` flag : `terraform apply -target=aws_instance.my_instance`
  * ⚠️ Note : 👉 Use carefully — `not recommended` for regular use
  * I can use the `-target flag` to apply changes to a specific resource, but I avoid using it frequently because it can bypass dependency checks.

---

## 🔁 How do you reuse Terraform modules with different parameters?
  * I reuse modules by passing different input variables, allowing the same module to create different resources for different environments (Dev / Staging / Prod) .
  * Use input variables when calling the module.

```hcl
module "vpc_dev" {                    # Dev VPC
  source     = "./vpc"
  cidr_block = "10.0.0.0/16"
}

module "vpc_prod" {                    # Prod VPC
  source     = "./vpc"
  cidr_block = "10.1.0.0/16"
}
```

---

# 🔐 Handling Secrets and Sensitive Data in Terraform
 * Managing sensitive data like passwords, API keys, and credentials securely is critical when working with Terraform.
 * Here are the best practices and methods for handling secrets properly:

## ❌ 1. Never Store Secrets in Plaintext
 Avoid committing secrets directly in your Terraform code:

```hcl
resource "aws_db_instance" "example" {
  username = "admin"
  password = "s3cr3tP@ssw0rd"                 # Exposed in state file and version control     # ❌ BAD PRACTICE - Never do this!
}
```
### 🚨 Problems:
  * Exposed in code
  * Stored in state file
  * Risk of leaks

---

## ✅ 2. Best Practices:
### 🌱 A. Use Environment Variables
Instead of hardcoding secrets, Terraform allows passing values securely using environment variables.

### 📌 Define a Sensitive Variable
```hcl
variable "db_password" {
  description = "Database password"
  type        = string
  sensitive   = true                       # Use sensitive = true in variable definitions
}
```
### 📌 Use the Variable in a Resource
```hcl
resource "aws_db_instance" "example" {
  password = var.db_password
}
```

### 📌 Pass Value via Environment Variable
```hcl
export TF_VAR_db_password="s3cr3tP@ssw0rd"
terraform apply
```

### 🔐 B. Secret Management Tools
👉 Use : Amazon Web Services Secrets Manager or HashiCorp Vault 

### 🔒 C. Secure Backend
👉 Use : Encrypted S3 Backend with KMS encryption (Mark Variables as Sensitive), Restrict access with IAM
