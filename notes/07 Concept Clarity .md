# ⚖️ What Difference between count and for_each in Terraform
| 🧩 Feature  | 🔢 `count`                       | 🔁 `for_each`                  |
| ----------- | -------------------------------- | ------------------------------ |
| 🧱 Type     | 🔢 Number / List                 | 🗂️ Map or Set                 |
| 🎯 Use Case | 📦 Create identical resources    | 🎨 Create unique resources     |
| 🔍 Indexing | 📍 `count.index` (numeric index) | 🔑 `each.key`, 📦 `each.value` |

  * In Terraform, `count` is used to create multiple identical resources (same setup, just multiple copies).
  * whereas `for_each` is used when each resource requires a unique configuration.

### 📄 Example (count):
```hcl
resource "aws_instance" "example" {
  count         = 3                       # 👉 Creates 3 EC2 instances (same config)
  instance_type = "t2.micro"
}
```

### 📄 Example (for_each):
```hcl
resource "aws_s3_bucket" "buckets" {
  for_each = {
    dev  = "dev-bucket"
    prod = "prod-bucket"                 # 👉 Creates: dev-bucket , prod-bucket
  }

  bucket = each.value
}
```

---

## ⚠️ Your team uses Terraform to create EC2 instances. One day, someone manually deleted an instance from the AWS Console. What happens when you run terraform plan?

 * Terraform will notice the EC2 instance defined in `.tf `is missing from the real infrastructure, but still present in the `state file`.
 * ⚠️ Terraform still thinks the resource exists (because of the `state file`).  
 * 🔁 So On next, `terraform plan`, it will recreate the resource. 
 * 🔧 **Fix** :
     * run : `terraform refresh`  to sync the actual infrastructure with the state.

---

## 🧩 You have a module that needs to create different-sized EC2 instances in dev and prod. How would you achieve this?
 Use variables and terraform.tfvars files for each environment.

### 📦 module call:
```
module "ec2" {
  source        = "./modules/ec2"
  instance_type = var.instance_type
}
```

### 📄 dev.tfvars:
```
instance_type = "t2.micro"
```

### 📄 prod.tfvars:
```
instance_type = "t3.large"
```

▶️ Command : `terraform apply -var-file=dev.tfvars`

---

## 🔄 You’ve changed a security group rule directly in AWS Console. What happens during the next terraform apply?

 * Terraform will detect mismatch from the `state` and will try to `revert` the manual change to match the `.tf code` .
 * 💡 Solution :
     * Either update the `.tf code` or `re-import` manually updated infrastructure.

---

## 🔗 How do you pass values from one module to another in Terraform?

Use output in one module and input in another.

📦 Module A:
```
output "subnet_id" {
  value = aws_subnet.main.id
}
```
📦 Module B:
```
module "vpc" {
  source = "./vpc"
}

module "ec2" {
  source    = "./ec2"
  subnet_id = module.vpc.subnet_id       # Module A → Output → Module B → Input
}
```
---

## ⚡ What’s the difference between terraform apply `-auto-approve` and `regular apply`?
|   🧩 Command           |         💡 Behavior                                                 |
| ---------------------- | -------------------------------------------------------------------- |
| 🚀 `terraform apply`   | ❓ Asks for confirmation before applying changes                    |
| ⚡ `-auto-approve`     | ✅ Skips confirmation (applies directly) — useful in automation.    |

* I use -auto-approve in automation pipelines, but for manual runs I prefer confirmation to avoid accidental changes.

---

## 🏗️ How do you manage multiple AWS resources together using Terraform?
💡 You can define multiple resources in `.tf files` or use `modules` for better structure for large setups.  

### 📌 Example: Create EC2 + S3 in one file:
```
resource "aws_instance" "ec2" {                       # AWS EC2
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = "t2.micro"
}

resource "aws_s3_bucket" "bucket" {                   # AWS S3
  bucket = "my-combined-bucket"
}
```

---

## ✅ What are some best practices for using Terraform with AWS?

| 🧩 Practice                     | 💡 Description                                                |
| ------------------------------- | -------------------------------------------------------------- |
| ☁️ Remote State (S3 + DynamoDB) | 🔒 Store state in S3 with locking for team safety            |
| 🔁 Use Modules                  | ♻️ Reuse code for VPC, EC2, etc.                               |
| 📁 Environment Variables        | 🌍 Separate configs per env (dev, prod)                       |
| 🔐 Secrets Management           | 🛡️ Store secrets in AWS Secrets Manager                       |
| 📊 Plan Before Apply            | 🔍 Always run `terraform plan` before apply to verify changes  |

