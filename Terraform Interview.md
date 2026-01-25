# 🔰 Basic Terraform Interview Questions
## What is Terraform?
Terraform is an Infrastructure as Code (IaC) tool by HashiCorp used to provision and manage cloud and on-prem infrastructure
## What language does Terraform use?
Terraform uses HCL (HashiCorp Configuration Language), which is human-readable and supports variables, loops, and conditions.
## What are Terraform Providers?
Providers are plugins that allow Terraform to interact with APIs like: AWS & Azure & GCP & Kubernetes
## What is a Terraform Resource?
A resource represents an infrastructure object (EC2, S3, VPC, etc.).
## What is terraform init?
```
Initializes the Terraform project by:
        Downloading providers
        Initializing backend
        Preparing modules
```
## What is terraform plan?
Shows what changes Terraform will make before applying them.
## Why is state file important?
```
Tracks resource IDs
Enables collaboration
```
## What is a Backend in Terraform?
A backend defines where the state file is stored.
```
terraform {
  backend "s3" {
    bucket  = "tf-state-bucket"
    key     = "dev/terraform.tfstate"
    region  = "us-east-1"
  }
}
```
## How do you lock the Terraform state?
```
Earlier: S3 + DynamoDB
Now (Terraform ≥1.10): S3 Native State Locking
```
> This prevents multiple users from modifying state simultaneously.
## What are Terraform Variables?
Used to make code reusable and dynamic.
```
variable "instance_type" {
  default = "t2.micro"
}
```
## What are Output Values?
Outputs expose information after apply.
```
output "instance_ip" {
  value = aws_instance.web.public_ip
}
```
## What is Terraform Workspace?
Used to manage multiple environments (dev, stage, prod).
```
terraform workspace new dev
```
## How do you manage secrets?
```
1. Environment variables
2. AWS Secrets Manager
3. Terraform Cloud variables
      ❌ Never hardcode secrets
```
## How do you handle multiple environments?
```
1. Workspaces
2. Separate state files
3. Directory structure (dev/prod)
```
## What are Terraform Best Practices?
```
1. Use remote backend
2. Use modules
3. Lock state
4. Version pin providers
5. Use terraform fmt & validate
```
## How do you prevent accidental deletion?
```
lifecycle {
  prevent_destroy = true
}
```
## What is terraform taint?
Marks a resource for recreation.

# 🔥 Scenario-Based Terraform Interview Questions
## Your terraform apply failed midway. What happens?
Terraform may have partially created resources. The state file records what was created.

Fix:
```
1. Run terraform plan to see current state
2. Re-run terraform apply
3. If broken → manually fix or terraform taint the resource
```
## Two engineers ran Terraform at the same time. What happens?
If state locking is enabled, the second run will fail with a lock error. This prevents state corruption.
> 👉 That’s why we use: S3 remote backend & Native state locking (Terraform ≥1.10)

## How do you manage Terraform in CI/CD?
Typical pipeline:
```
1. terraform init
2. terraform validate
3. terraform plan
4. Manual approval
5. terraform apply
     👉  Tools used: GitHub Actions
```
## How do you separate dev, stage, prod?
Separate folders & Separate state files & Different variables per env
> Example: terraform apply -var-file=dev.tfvars
## A resource was deleted manually in AWS. What will Terraform do?
Terraform detects drift.
```
terraform plan → shows resource will be recreated
terraform apply → recreates it
```
## Difference between terraform apply & apply -auto-approve?
```
apply → asks confirmation
apply -auto-approve → no confirmation (used in CI/CD)
```
## Can one module call another module?
❌ No. Only the root module can call child modules.
## State file got deleted. What now?
```
1. Restore from S3 versioning
2. Re-import resources using terraform import
3. Worst case → rebuild infra
```
## What is Sentinel?
> Policy as Code used in Terraform Enterprise to enforce rules. Example: “No public S3 buckets allowed”
## Explain Terraform workflow in your project
```
“We use Terraform with remote backend and state locking. Code is modularized. 
CI/CD runs validate and plan, and apply is done after approval. 
Separate state files are used for each environment.”
```










