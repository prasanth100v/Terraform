# 🌍 Terraform Module
 * A Terraform module is a collection of `.tf` files in a directory that are used together to create and manage infrastructure.

## 📦 Types of Terraform Modules
```hcl
1️⃣ Root module – main working directory
2️⃣ Child module – reusable module called by root or other modules
3️⃣ Remote module – module stored in Terraform Registry, GitHub, S3, etc.
```

## Why do we use Terraform modules❓
  * ♻️ Reuse infrastructure code
  * 🧹 Reduce duplication
  * 🔧 Easy maintenance
  * 🌐 Support multi-environment setups (`dev/test/prod`)

## 📁 What files are typically present in a module?
```hcl
📄 main.tf       – resources
📄 variables.tf  – input variables
📄 outputs.tf    – exposed values
📄 versions.tf   (optional) – provider versions
```

## ⚙️ How do you call a module in Terraform?
```hcl
module "ec2" {
  source        = "./modules/ec2"    #or Git repo & Terraform Registry & S3 bucket
  instance_type = "t2.micro"
  ami           = "ami-123456"
}
```

---

# 🚀 Why use modules?
```hcl
♻️ Reusability – same code for dev / test / prod
📦 Clean structure – less mess in main code
👥 Team-friendly – easy to share & standardize
```

## 📂 Example structure:
```hcl
terraform-ec2-project/
├── main.tf              # Root module
├── variables.tf
├── outputs.tf
├── provider.tf
├── terraform.tfvars
└── modules/
    └── ec2/
        ├── main.tf      # Child module
        ├── variables.tf
        └── outputs.tf
```

```hcl
modules/
└── ec2/
    ├── main.tf
    ├── variables.tf
    ├── outputs.tf
    ├── versions.tf       # Terraform & provider versions
    ├── README.md         # documentation
```

* ✅ What each file does:
   * 📄 `main.tf` → resources
   * 📄 `variables.tf` → inputs
   * 📄 `outputs.tf` → exposed values

## 🧠 Important Note
 * A Terraform module contains ***main.tf, variables.tf, and outputs.tf.***
 * 🔗 Root module calls child modules via `source`.
 * ♻️ Modules improve reusability, consistency, and maintainability.

## 💡 Think of modules like:
  * 🧩 Functions in programming

---

## 📤 How do you get values from a module?
Using outputs.
```hcl
output "instance_id" {
  value = aws_instance.this.id
}
```

## 🔄 Can one module call another module?
* ✅ Yes. This is called module composition (nested modules).
