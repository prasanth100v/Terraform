## 📦 What is a Module?

A **module in Terraform** is simply a group of `.tf` files that perform
**one specific task** (for example creating an EC2 instance or a VPC)
and can be **reused multiple times**.

Think of it like a **reusable package**. Instead of writing the same
code again and again, you **write once and reuse many times**.

------------------------------------------------------------------------

## 💬 In Simple Words

-   A **module is like a function in programming**
-   You can **call it with different values**
-   It **saves time**
-   It **avoids repeating code**
-   It helps keep infrastructure **organized and reusable**

------------------------------------------------------------------------

## 🎤 Interview Answer

**In Terraform, I use modules to reuse code and organize
infrastructure.**\
For example, I created an **EC2 module** where AMI and instance type are
defined as input variables.\
I can reuse the same module with different values in **dev, test, and
production environments**.

Modules help make Terraform code:

-   ✅ Cleaner\
-   ✅ Reusable\
-   ✅ Easier to manage

------------------------------------------------------------------------

# 🗂️ Module Structure

    modules/
    └── vpc/                # Module name
        ├── main.tf         # Resources (VPC, Subnets, etc.)
        ├── variables.tf    # Input variables
        ├── outputs.tf      # Output values
        └── README.md       # Documentation

------------------------------------------------------------------------

# 🚀 Using a Module

## 1️⃣ Local Module (Same Repository)

``` hcl
module "vpc" {
  source     = "./modules/vpc"
  cidr_block = "10.0.0.0/16"
}
```

------------------------------------------------------------------------

## 2️⃣ Remote Module (Terraform Registry)

``` hcl
module "vpc" {
  source  = "terraform-aws-modules/vpc/aws"
  version = "3.14.0"
  cidr    = "10.0.0.0/16"
}
```

------------------------------------------------------------------------

## 3️⃣ Git Based Module

``` hcl
module "eks" {
  source = "git::https://github.com/terraform-aws-modules/terraform-aws-eks.git"
}
```

------------------------------------------------------------------------

# ⭐ Benefits of Terraform Modules

🌈 **Code Reusability**\
Write once and reuse multiple times.

📦 **Better Organization**\
Infrastructure becomes structured and easier to maintain.

⚡ **Faster Development**\
No need to write the same code again.

🌍 **Community Modules**\
You can use ready‑made modules from the **Terraform Registry**.

------------------------------------------------------------------------

# 🧠 Pro Tip

Most professional DevOps teams create **separate modules for**:

-   VPC
-   EC2
-   EKS
-   RDS
-   Security Groups
-   Load Balancers

This makes Terraform infrastructure **clean, scalable, and
production‑ready**.

------------------------------------------------------------------------

✨ *Terraform Modules make Infrastructure as Code powerful, reusable,
and easy to maintain.*
