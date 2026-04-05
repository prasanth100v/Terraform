# 🌈🔗 Terraform Dependency Handling
## How do you handle dependencies when one resource must wait for another?

 * Terraform automatically manages dependencies using resource references.
 * ✨ Terraform uses a built-in dependency graph (`Implicit Dependency`)
 * If needed, I use `depends_on` to define dependencies when they are not automatically detected.
 * ⚠️ Use Explicit Dependency (`depends_on`) : when Terraform cannot detect dependency

##  📄 Example (Implicit Dependency)
```
resource "aws_security_group" "example" {
  name = "my-sg"
}

resource "aws_instance" "web" {
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = "t2.micro"

  vpc_security_group_ids = [aws_security_group.example.id]         # Implicit Dependency
}
```

## ⚠️ Explicit Dependency (depends_on) : you can also use `depends_on`. 👉 when Terraform cannot detect dependency
```
resource "aws_instance" "app" {
  depends_on = [aws_db_instance.mydb]         
}
```

---

## ⚠️ You accidentally deleted the .tfstate file. What should you do?

- ☁️ If using a remote backend, download or restore from AWS S3 versioning. 
- ⚠️ Without state, ❌ Terraform loses track of resources and may recreate all resources, causing duplicates or conflicts.  
- 🔁 Use terraform import to bring existing resources back into state.
- 🚫 Avoid manual recreation 

### 🗣️ Interview Answer :
   * If the state file is lost, I first restore it from remote backend backups like S3 versioning.
   * If not available, I use terraform import to rebuild the state and avoid duplicating resources..

---

## 🏗️ You want to split Terraform code for a large project. How do you structure it?
### 📁 Use modules and separate directories:
```
/terraform
 ├── modules/
 │   ├── vpc/
 │   ├── ec2/
 │   └── rds/
 ├── environments/
 │   ├── dev/
 │   └── prod/
```
* For large projects, I use a modular structure with separate directories for environments.
* 📦 Modules handle reusable components, and each environment passes different variables `.tfvars`.
* 🎯 Approach : 📦 Modules → Reusable components, 🌍 Environments → Separate `configs + tfvars`

---

## 🔄 How can you import existing AWS resources into Terraform?

 * 🚀 Use terraform import Command : `terraform import aws_instance.my_ec2 i-0123456789abcdef0 `
 * 👉 Use Case When :
     * Resource already exists
     * Need to bring under Terraform

---

## ⏪ You want to roll back infrastructure changes. How do you do it?

 * 🚫 Terraform Limitation : ❌ Terraform doesn't support automatic rollback.
 * ✅ Solution
    * 🧾 Use Git version control
    * 🔙 Revert .tf files
    * ▶️ Run terraform apply

 You can:  
- 🧾 Use version-controlled .tf files (Git).  
- 🔙 Revert the .tf code to the previous version and run terraform apply.  

---

## 🔐 How do you avoid exposing secrets in Terraform logs or state files?

- 🛡️ Mark variables as sensitive.  
- 🔑 Use secrets managers (e.g., AWS Secrets Manager, HashiCorp Vault).  
- 🔒 Encrypt state file (enabled by default in S3 backend).  

variable "db_password" {
  type      = string  
  sensitive = true  
}

---

# 🌐 What is the Terraform Registry?

A public repository of:  
- 📦 Modules (reusable Terraform code)  
- 🔌 Providers (to interact with different platforms)  

🔗 URL: https://registry.terraform.io  

---

# ❌ What happens if the Terraform state file is deleted?

- ⚠️ Terraform loses track of resources.  
- 🔁 It will recreate resources on next apply unless a new import or recovery is done.  
- 💡 Solution: Use remote backend with versioning (e.g., S3 + DynamoDB for locking).  

---

# ✅ What are some best practices in Terraform?

- ☁️ Use remote state backend.  
- 🔁 Use modules for reusable code.  
- 🔐 Store secrets securely (not in code).  

---

# 🔍 How does Terraform detect changes in infrastructure?

- 🔄 Compares .tf configuration with .tfstate  
- ⚙️ Runs terraform refresh internally during plan/apply  
- 📊 Shows a diff of what will change  

---

# 🔗 How does Terraform handle dependency management between resources?

Terraform uses a built-in dependency graph. It automatically detects dependencies based on resource references (e.g., using aws_security_group.example.id).

Example:

resource "aws_security_group" "example" {
  name = "my-sg"
}

resource "aws_instance" "web" {
  ami           = "ami-0c55b159cbfafe1f0"  
  instance_type = "t2.micro"  
  vpc_security_group_ids = [aws_security_group.example.id]   # Dependency  
}

---

# 🔐 Explain Terraform remote backends and locking

Remote backends (e.g., AWS S3) store the state file remotely. Locking (using DynamoDB) ensures that only one person can run changes at a time. Locking prevents concurrent apply conflicts.

Example config:

terraform {
  backend "s3" {
    bucket         = "my-tf-state"  
    key            = "prod/terraform.tfstate"  
    region         = "us-east-1"  
    dynamodb_table = "terraform-lock"  
  }
}
