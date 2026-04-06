# ⚖️ What is the difference between count and for_each in Terraform?

Feature        count                    for_each  
------------------------------------------------------------  
Type           List/number              Map or set of strings  
Use case       Repeating identical resources    Creating resources from key-value pairs  

---

## 📄 Example (count):
resource "aws_instance" "example" {
  count         = 3
  instance_type = "t2.micro"
}

---

## 📄 Example (for_each):
resource "aws_s3_bucket" "buckets" {
  for_each = {
    dev  = "dev-bucket"
    prod = "prod-bucket"
  }

  bucket = each.value
}

---

## ⚠️ Your team uses Terraform to create EC2 instances. One day, someone manually deleted an instance from the AWS Console. What happens when you run terraform plan?

Terraform will notice the EC2 instance defined in .tf is missing from the real infrastructure, but still present in the state file. So, terraform plan will show that the EC2 instance will be recreated.

🔧 Fix:  
You can also run: terraform refresh  
to sync the actual infrastructure with the state.

---

## 🧩 You have a module that needs to create different-sized EC2 instances in dev and prod. How would you achieve this?

Use variables and terraform.tfvars files for each environment.

📦 module call:
module "ec2" {
  source        = "./modules/ec2"
  instance_type = var.instance_type
}

📄 dev.tfvars:
instance_type = "t2.micro"

📄 prod.tfvars:
instance_type = "t3.large"

▶️ Command:
terraform apply -var-file=dev.tfvars

---

## 🔄 You’ve changed a security group rule directly in AWS Console. What happens during the next terraform apply?

Terraform will detect a drift from the state and will try to revert the manual change to match the .tf code.  
💡 Solution: Either update the .tf code or re-import manually updated infrastructure.

---

## 🔗 How do you pass values from one module to another in Terraform?

Use output in one module and input in another.

📦 Module A:

output "subnet_id" {
  value = aws_subnet.main.id
}

📦 Module B:

module "vpc" {
  source = "./vpc"
}

module "ec2" {
  source    = "./ec2"
  subnet_id = module.vpc.subnet_id
}

---

## ⚡ What’s the difference between terraform apply -auto-approve and regular apply?

- 🟢 Regular terraform apply: Prompts for confirmation.  
- 🚀 -auto-approve: Skips the confirmation step and immediately applies changes — useful in automation.  

---

## 🏗️ How do you manage multiple AWS resources together using Terraform?

You can define multiple resources in .tf files or use modules for better structure.  

📌 Example: Create EC2 + S3 in one file:

resource "aws_instance" "ec2" {
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = "t2.micro"
}

resource "aws_s3_bucket" "bucket" {
  bucket = "my-combined-bucket"
}

---

## ❌ What happens if someone deletes an AWS resource from the console but not from Terraform?

- ⚠️ Terraform still thinks the resource exists (because of the state file).  
- 🔁 On next terraform plan, it will recreate the resource.  

---

## 🎯 How do you destroy only one AWS resource in Terraform?

Use terraform destroy -target:  
terraform destroy -target=aws_s3_bucket.my_bucket  

---

## ✅ What are some best practices for using Terraform with AWS?

- ☁️ Store state in S3 with locking (DynamoDB)  
- 🔁 Use modules for VPC, EC2, etc.  
- 📁 Use variable files per environment  
- 🔐 Keep secrets in AWS Secrets Manager  
- 📊 Use terraform plan before apply  
