# 🧩 7. Modules

A module in Terraform is just a group of .tf files that do one task (like creating an EC2 or a VPC) and can be reused in different places.  
💡 Think of it like a reusable package. Instead of writing the same code again and again you can write once and use many times. you can create a module and reuse it.

## 🔹 In Simple Words:
- 🔁 A module is like a function in programming.  
- 🎯 You can call it with different values.  
- ⏱️ It saves time and avoids repeating the same code.  

---

## 🎤 Answer in Interview:

In Terraform, I use modules to reuse code and organize my infrastructure. For example, I created an EC2 module with AMI and instance type as inputs. I can use this module multiple times with different values in different environments like dev, test, and prod. Modules help make Terraform code cleaner and easier to manage.

---

## 📁 Module Structure:

modules/  
├── vpc/               # Module name (e.g., "vpc")  
│   ├── main.tf        # Resources (VPC, subnets, etc.)  
│   ├── variables.tf   # Input variables  
│   ├── outputs.tf     # Output values  
│   └── README.md      # Documentation  

---

## ⚙️ Using a Module

### 🅰️ A. Local Module (Same Repository)

# main.tf
module "vpc" {
  source = "./modules/vpc"        # Path to the module  
  cidr_block = "10.0.0.0/16"  
}

---

### 🌐 B. Remote Module (Terraform Registry)

module "vpc" {
  source  = "terraform-aws-modules/vpc/aws"  
  version = "3.14.0"        # Optional: Pin a version  
  cidr    = "10.0.0.0/16"  
}

---

### 🧬 C. Git-Based Module

module "eks" {
  source = "git::https://github.com/terraform-aws-modules/terraform-aws-eks.git"  
}

---

## ✅ Benefits:
- 🔁 Encourages code reuse.  
- 📖 Makes configuration more readable and modular.  
- 🌍 You can use public modules from the Terraform Registry.  

---

# 🔄 8. Lifecycle Rules

In Terraform, the lifecycle block helps control how resources are managed.  
For example, I use prevent_destroy to avoid accidental deletion of critical resources like RDS.  
I also use ignore_changes to ignore drift caused by manual changes in the console.  
If I'm updating a resource like an ALB or S3 bucket, I use create_before_destroy to avoid downtime.

---

## 📌 Example:

## 🧠 Explanation of Each Option

### 1. 🛡️ prevent_destroy :

If someone tries to destroy the resource, Terraform will show an error.  
(Prevents Terraform from destroying the resource., Useful for critical resources like databases.)

resource "aws_db_instance" "production" {
  allocated_storage = 100  
  engine           = "mysql"  

  lifecycle {
    prevent_destroy = true   # Blocks 'terraform destroy'  
  }
}

⚠️ Override: Use -destroy flag to force removal

---

### 2. 🚫 ignore_changes :

Ignores changes to specific arguments.  
(If someone manually updates a tag in the AWS console, Terraform will not try to overwrite it.)

resource "aws_instance" "web" {
  lifecycle {
    create_before_destroy = true  
    ignore_changes = [ami]        # Force full replacement on AMI updates  
  }
}

---

### 3. 🔄 create_before_destroy

Tells Terraform to create a new resource before destroying the old one.  
Prevents downtime during updates.

resource "aws_instance" "web" {
  ami           = "ami-0c55b159cbfafe1f0"  
  instance_type = "t3.micro"  

  lifecycle {
    create_before_destroy = true        # New instance spins up BEFORE old is destroyed  
    # When to use: Load balancers, databases, stateful services  
  }
}

---

## ⚙️ Lifecycle Customization

- 🛡️ prevent_destroy, 🔄 create_before_destroy, and 🚫 ignore_changes help manage resource lifecycles more safely.

---

# 🗄️ 9. Backend

backend is used to define where Terraform stores its state file. By default, Terraform saves the state locally in a file called terraform.tfstate. However, in a team or production environment, you use a remote backend to store the state file securely and allow team collaboration.

---

## ☁️ Example: AWS S3 Backend

terraform {
  backend "s3" {
    bucket         = "my-terraform-state-bucket"  
    key            = "dev/terraform.tfstate"  
    region         = "us-east-1"  
    dynamodb_table = "terraform-locks"    # For state locking  
    encrypt        = true  
  }
}

🔁 You must run terraform init again after configuring or changing a backend.

---

## ❓ Why Use a Backend?

- 👥 Collaboration – Multiple people can work on the same infrastructure.  
- 🔒 Locking – Prevents two people from changing the infrastructure at the same time.  
- 🛡️ Security – Encrypt state files (e.g., S3 + KMS).  
