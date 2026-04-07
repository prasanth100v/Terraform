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

## 🔄 Terraform Import Command

 * 📥 I use `terraform import` to bring existing infrastructure under Terraform management without recreating it.
 * 🧾 Basic Syntax: `terraform import <resource_type>.<resource_name> <existing_id>  `
      * 📄 Example:  `terraform import aws_instance.web i-1234567890abcdef0  `
  
---

# ⚙️ Explain the Terraform workflow

The workflow I follow daily is `Write → Plan → Apply  `

1. ✍️ Write `.tf` files (I always validate with `terraform validate` first)  
2. 📊 Run `terraform plan` to see execution steps - I review this carefully, especially for production  
3. 🚀 Apply changes with `terraform apply` - I never use `-auto-approve` in production  

### 🔒 Advanced Workflow (Production)
🔒 For critical systems, I add an extra step: save the plan output (terraform plan -out=tfplan) and have another team member review before applying.
```
 terraform plan -out=tfplan
               # Review by team 👥
 terraform apply tfplan
```

---

## 🌍 Use Case (Real-World):

In CI/CD pipelines, you:  
1. 📊 Run `terraform plan -out=tfplan`   → stores the plan  
2. 👀 Use `terraform show tfplan`        → to review or approve.  
3. 🚀 Then run `terraform apply tfplan`  → to apply only the reviewed plan.  

### 🎯 Benefit
   * ✔️ Controlled deployments
   * ✔️ Approval-based workflow
   * ✔️ No unexpected changes

---

## 🤝 How do you manage Terraform state in team environments?

From painful experience, I've learned state management is critical. My approach:  

- ☁️ Always use `remote backends` like `S3 with Native locking` - I once had a state conflict that took hours to resolve  
- 🧾 Implement `state versioning and backups` - we recovered from an accidental deletion this way
- 🛡️ Why state management Important : Prevent conflicts ⚠️, Ensures safe collaboration 👥, Recover from failures 🔄
- 🔐 For sensitive data, I either:  
   1) 🔑 Use `AWS Secrets Manager` with `Terraform data sources`  
   2) 🛡️ Mark variables as `sensitive` in Terraform `v0.14+ `

---

## ⚖️ Why did you choose Terraform over CloudFormation or other tools?
### 🚀 Key Reasons
 * 🌐 Multi-cloud support  : I prefer Terraform mainly because it gives me `flexibility` to use the same tool across `AWS`, `Azure`, and `GCP`.
 * 🧠 Cleaner syntax (HCL) : Also, the `syntax` is cleaner
 * 📦 Better module reuse  : I find the module system more `reusable` compared to `CloudFormation stacks`.
 * 📊 Plan preview before apply : The terraform `plan preview` and the `state management` features are also really helpful during reviews.

---

## ✅ What Terraform best practices do you enforce?

From my experience, these practices prevent the most issues:
| 🧩 Category          |       💡 Best Practice                                                                                                                                  |
| -------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 📦 Code Organization | 🧱 Use small, single-purpose modules<br>📁 Separate files (variables, outputs, resources)                                                                 |
| ⚙️ Execution         | 📊 Always run `terraform plan` before apply<br>🌍 Use workspaces or separate directory-per-environment                                                     |
| 🔐 Security          | 🚫 Never commit secrets (use Vault / AWS Secrets Manager)<br>🛡️ Follow Minimum IAM permissions for Terraform roles <br>🔍 Check drift regularly with plan |

---

## 📌 3. How do you manage Terraform state in your team?

 * 🚀 We use a remote backend with S3 for storing the `terraform.tfstate` file and enable `state locking`.
 * 🚀 This setup `prevents conflicts` when multiple team members are applying changes. and enables collaboration.
 * 🚀 We also encrypt the state file using `server-side encryption` and restrict access using `IAM policies`.
 * 🚀 Use separate state files per environment (`dev, staging, prod`) for isolation.  
 
---

## 🚢 Can you explain how you use Terraform for EKS setup?

 * I use the `terraform-aws-modules/eks/aws` module to provision EKS clusters. I structure the setup with `VPC, EKS, and IAM roles module`, and then deploy applications using `Helm or Argo CD`.
 * This module simplifies EKS provisioning and supports `managed node groups`, `OIDC`, and `IRSA`.
 * I typically break the code into:
     * 🌐 VPC module
     * ⚙️ EKS module
     * 🔐 IRSA module for IAM roles mapped to service accounts  

### ⚙️ Accessing the Cluster
After provisioning → `update-kubeconfig` through `AWS CLI` :
```Bash
aws eks update-kubeconfig --region <region> --name <cluster-name>
```
🔍 What This Command Does : Connects to AWS EKS, So I can use `kubectl` to interact with the EKS cluster

---

## 🏁 Final Summary

* ✨ plan -out → Save & approve changes
* ✨ import → Manage existing infra
* ✨ workflow → Write → Plan → Apply
* ✨ state → Remote + locked
* ✨ EKS → Modular + scalable
