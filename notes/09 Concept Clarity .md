# 📊 terraform plan -out=tfplan

🚀 This command : `terraform plan -out=tfplan` ✨ Generates an `execution plan` and saves it to a file (`tfplan`)

## Why Use `-out=tfplan` ❓

- 💾 It saves the execution plan for later use, So you can review before applying.
- 🤖 Useful in CI/CD pipelines or Ensures approvals before applying.  

### 🔄 Workflow with Saved Plan
```
terraform plan -out=tfplan
terraform show tfplan
terraform apply tfplan
```
 * I use `terraform plan -out` to generate a reusable execution plan that can be `reviewed and approved` before applying,
 * Especially in production environments and CI/CD pipelines.


   * 💬 At my last role, we reduced provisioning time from `2 days to 15 minutes` by Terraforming our base infrastructure.
   * 📌 # `Provisioning` = process of creating and setting up IT infrastructure  

---

# 🔄 Terraform Import Command

- 📥 The terraform import command allows you to bring existing infrastructure under Terraform management.  
- 🧾 Basic Syntax: terraform import <resource_type>.<resource_name> <existing_id>  

📄 Example:  
terraform import aws_instance.web i-1234567890abcdef0  

💡 We can reuse configurations through modules - for example, I created a standard VPC module that's used across all our AWS environments  

---

# ⚙️ Explain the Terraform workflow

The core workflow I follow daily is Write → Plan → Apply  

1. ✍️ Write HCL configurations (I always validate with terraform validate first)  
2. 📊 Run terraform plan to see execution steps - I review this carefully, especially for production  
3. 🚀 Apply changes with terraform apply - I never use -auto-approve in production  

🔒 For critical systems, I add an extra step: save the plan output (terraform plan -out=tfplan) and have another team member review before applying."  

---

## 🌍 Use Case (Real-World):

In CI/CD pipelines, you:  
1. 📊 Run terraform plan -out=tfplan → stores the plan  
2. 👀 Use terraform show tfplan → to review or approve.  
3. 🚀 Then run terraform apply tfplan → to apply only the reviewed plan.  

---

# 🤝 How do you manage Terraform state in team environments?

From painful experience, I've learned state management is critical. My approach:  

- ☁️ Always use remote backends like S3 with DynamoDB locking - I once had a state conflict that took hours to resolve  
- 🧾 Implement state versioning and backups - we recovered from an accidental deletion this way  
- 🔐 For sensitive data, I either:  
  1) 🔑 Use AWS Secrets Manager with Terraform data sources  
  2) 🛡️ Mark variables as sensitive in Terraform v0.14+  

---

# ⚖️ Why did you choose Terraform over CloudFormation or other tools?

I prefer Terraform mainly because it gives me flexibility to use the same tool across AWS, Azure, and GCP.  
Also, the syntax is cleaner, and I find the module system more reusable compared to CloudFormation stacks.  
The terraform plan preview and the state management features are also really helpful during reviews.  

---

# ✅ What Terraform best practices do you enforce?

From my experience, these practices prevent the most issues:

## 1️⃣ Code organization:
- 📦 Small, single-purpose modules  
- 📁 Separate files for variables/outputs/resources  

## 2️⃣ Execution:
- 📊 Always plan before apply  
- 🌍 Use workspace or directory-per-environment  

## 3️⃣ Security:
- 🔐 Never commit secrets - use Vault or AWS Secrets Manager  
- 🛡️ Minimum IAM permissions for Terraform roles  
- 🔍 Regular terraform plan checks for drift  

---

# 🤝 How do you manage Terraform state in a team environment?

I use a remote backend — typically an S3 bucket with versioning enabled, along with a DynamoDB table for state locking. This prevents simultaneous updates  

---

# 📌 3. How do you manage Terraform state in your team?

We use a remote backend with S3 for storing the terraform.tfstate file and enable state locking using DynamoDB. This setup prevents conflicts when multiple team members are applying changes. and enables collaboration.We also encrypt the state file using server-side encryption and restrict access using IAM policies. Use separate state files per environment (dev, staging, prod) for isolation.  

---

# 🔐 6. How do you manage secrets in Terraform?

I avoid hardcoding secrets in .tf files. For AWS, I usually integrate with Secrets Manager or pass sensitive variables via environment variables or encrypted .tfvars files. Additionally, I mark outputs like DB passwords as sensitive = true to prevent accidental display in logs or terminal outputs.  

---

# ⚙️ What is Terraform’s lifecycle block and how have you used it?

I use lifecycle blocks when I want Terraform to behave differently from default behavior.  

📌 For example:  
- 🛡️ prevent_destroy = true for critical databases.  
- 🔄 create_before_destroy = true for zero-downtime updates.  
- 🚫 ignore_changes when external systems might alter tags or user-managed fields that I don’t want Terraform to reset.  

💡 This gives me more fine-grained control over resource updates.  

---

# 🚢 Can you explain how you use Terraform for EKS setup?

Yes. I use the terraform-aws-modules/eks/aws module. It simplifies EKS provisioning and supports managed node groups, OIDC, and IRSA. I typically break the code into:  

- 🌐 VPC module  
- ⚙️ EKS module  
- 🔐 IRSA module for IAM roles mapped to service accounts  

Then I use aws eks update-kubeconfig to access the cluster, and often pair this with Helm or Argo CD for application deployments.  
