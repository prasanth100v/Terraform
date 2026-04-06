# 🌈🔗 Terraform Dependency Handling
## How do you handle dependencies when one resource must wait for another?

 * Terraform automatically manages dependencies using resource references.
 * ✨ Terraform uses a built-in dependency graph to determine the correct order of execution. (`Implicit Dependency`)
 * If required, I use `depends_on` to define dependencies when they are not automatically detected.
 * ⚠️ Use Explicit Dependency (`depends_on`) : when Terraform cannot detect dependency.

##  📄 Example (Implicit Dependency)
```hcl
resource "aws_security_group" "example" {
  name = "my-sg"
}

resource "aws_instance" "web" {
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = "t2.micro"

  vpc_security_group_ids = [aws_security_group.example.id]         # Implicit Dependency
}
```

## ⚠️ Explicit Dependency (depends_on) : 
 You can also use `depends_on`. 👉 when Terraform cannot detect dependency
```hcl
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
```hcl
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
    * 🧾 Use Git version-controlled `.tf` files.
    * 🔙 Revert `.tf` files to the previous version
    * ▶️ Run terraform apply

### 🗣️ Interview Answer :
  * Terraform doesn't support automatic rollback, so I rely on version control.
  * I revert the configuration to a previous version and reapply it to restore infrastructure.

---

## 🌐 What is the Terraform Registry?
  * Terraform Registry is a public repository where I can find reusable modules and providers.
  * It helps speed up development by using pre-built infrastructure components.
  * A public repository of:  
     * 📦 Modules (reusable Terraform code)
     * 🔌 Providers (to interact with different platforms)  
  * 🔗 URL: https://registry.terraform.io  

---

## ❌ What happens if the Terraform state file is deleted?

- ⚠️ Terraform loses track of resources.  
- 🔁 It will recreate resources on next apply unless a new import or recovery is done.  
- 💡 Solution: Use remote backend with versioning (e.g.,AWS S3 + locking).
- Terraform detects changes by comparing the `desired configuration` with the `current state file`.
- It then generates an `execution plan` showing what needs to be created, updated, or destroyed."

---

# 🔐 Remote Backend & Locking
## 🚀 What is Remote Backend?

 * 👉 Stores state file remotely (e.g.,AWS S3)
 * 🔒 State Locking Prevents : Multiple users running `apply` at same time

### ✅ Terraform S3 Backend with Native Locking :
```hcl
terraform {
  backend "s3" {
    bucket       = "my-tf-state"
    key          = "prod/terraform.tfstate"
    region       = "us-east-1"

    use_lockfile = true                    # ✅ Enables S3 native locking  # S3 native locking is supported in newer Terraform versions.
    encrypt      = true
  }
}
```

### 🗣️ Interview Answer
  * Remote backends store Terraform state in a shared location like AWS S3, and using S3 native locking
  * Ensures that only one person can make changes at a time, preventing conflicts.

---

# ✅ What are some best practices in Terraform?
## 🚀 Key Best Practices
  * ☁️ Use remote state backend.
     * 👉 Store state file in cloud (like AWS S3) instead of locally
     * ✔️ Enables team collaboration & Prevents state loss and Improves security
  
  * 🔁 Use modules for reusable code.
     * 👉 Break infrastructure into reusable components : ✔️ Easier maintenance ✔️ Clean structure 
  
  * 🔐 Store secrets securely (❌ Never hardcode secrets in code).
     * ✔️ Use: Environment variables & Secret managers and Encrypted backends

---

# 🔍 How does Terraform detect changes in infrastructure?
## 🚀 How It Works
 * Terraform follows a 3-step comparison process:
     * 1️⃣ Desired State      👉 Defined in `.tf` files
     * 2️⃣ Current State      👉 Stored in : `terraform.tfstate`
     * 3️⃣ Comparison (Diff)  👉 Terraform compares : `Desired State (.tf)  vs  Current State (.tfstate)`

 * 🔄 Compares `.tf` configuration with `.tfstate`
 * ⚙️ Runs terraform refresh internally during `plan/apply `
 * 📊 It generates an execution plan showing what needs to be `created`, `updated`, or `destroyed`.  

---

## 🏁 Final Summary

 * ✨ Dependencies → Auto (or `depends_on`)
 * ✨ State → Critical for tracking
 * ✨ Modules → Organize large projects
 * ✨ Security → Protect secrets
 * ✨ Backend → Enable collaboration

### 🧠 One-Line Memory Trick
| 🧩 Concept      | 💡 Meaning                                     |
| --------------- | ---------------------------------------------- |
| 🔗 Dependencies | 📊 Order of resource creation                  |
| 🧠 State        | 💾 Memory of infrastructure                    |
| ☁️ Backend      | 🔒 Shared & remote state storage (team safety) |
| 🔐 Secrets      | 🛡️ Always protect sensitive data              |

