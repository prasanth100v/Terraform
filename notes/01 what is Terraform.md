# 🌍⚙️ Terraform
## 🚀 What is Terraform?
 * ✨ Terraform is an open-source Infrastructure as Code (IaC) tool developed by HashiCorp.
 * 👉 It helps you:
   * Define infrastructure using code 🧾
   * Provision resources automatically ⚙️
   * Manage and update cloud infrastructure safely 🔄 📌 Uses HCL (HashiCorp Configuration Language) – (HCL is simple & human-readable Language).

👉 Terraform helps you:
| 🧩 Problem (Without Terraform) | 💡 Solution (With Terraform)           |
| ------------------------------ | -------------------------------------- |
| ❌ Manual setup                 | 🤖 Automation (Infrastructure as Code) |
| ❌ Error-prone                  | ✅ Consistent & repeatable deployments  |
| ❌ No tracking                  | 📊 Version control (Git integration & Track changes using Git)  |
| ❌ No consistency               | 🔁 Same setup across all environments  |
| ❌ Hard to reuse                | ♻️ Reusable modules                    |
 
 * ☁️ 👉 One tool → Manage everything! : It supports multiple cloud providers (AWS, Azure, GCP, etc.) and services, enabling consistent and repeatable infrastructure deployments.
 * 📊 Terraform creates an execution plan, shows what it will do, and applies the changes.


## 📌 Key Concepts
### 📝 Write Configuration
- Use `.tf` files to define infrastructure.
- Terraform maintains a `.tfstate` file to track the actual state of infrastructure.
- The file `terraform.tfstate` stores the current infrastructure status.
- 💡 Purpose:
  * Tracks created resources
  * Detects changes
  * Prevents duplication

### Remote Backend
- 🌐 Stores the Terraform state file in cloud storage (like S3) instead of locally.
- ✅ Benefits:
   * Team collaboration 👥
   * State locking 🔒
   * Avoid conflicts ⚠️

### Infrastructure as Code (IaC)
💡 IaC means managing infrastructure (like servers, databases, networks) using code instead of manual setup. 
- Ensures consistency
- Supports version control
- Enables automation

### 🔀 Supports Multiple Environments  
• Easily manage dev, test, and prod using different variable files and workspaces.

### 📌 Execution Plan
• `terraform plan` shows what will happen before applying changes., This helps to avoid unintended modifications. (🔍 Prevents mistakes & Safe preview)

### 🔁 terraform import
`terraform import` lets you bring existing infrastructure under Terraform management without re-creating it. (Already created EC2 → Import into Terraform)

### 🔹 depends_on
 🔗 Use `depends_on` when Terraform can’t automatically detect dependencies between resources. 

### 🔹 Terraform Outputs
 📤 `Terraform Outputs` : display useful information like IP addresses, Resource IDs etc.., After running `terraform apply`.

### 🛡 Lifecycle Rule
   🚨 Prevents accidental deletion of critical resources
```hcl
lifecycle {
  prevent_destroy = true
}
```
  🛡️ `prevent_destroy = true` : It’s a lifecycle rule that prevents a resource from being accidentally destroyed.

### 📈 terraform graph
  👉 Generates a visual dependency graph

---

## 🧰 Core Terraform Commands
| 🧩 Command                | 💡 Description                                                         |
| ------------------------- | ---------------------------------------------------------------------- |
| 🚀 `terraform init`       | 📦 Initializes project, It downloads downloads provider plugins, sets up working directory |
| 📊 `terraform plan`       | 🔍 Shows execution plan (what will change before apply)                |
| ✅ `terraform validate`    | 🧪 Checks syntax & configuration validity                              |
| ⚙️ `terraform apply`      | 🏗️ Applies changes and creates infrastructure                         |
| 💥 `terraform destroy`    | 🗑️ Deletes all resources (use carefully ⚠️)                           |
| 📋 `terraform state list` | 📂 Lists resources in state file                                       |
| 🔌 `terraform providers`  | 🌐 Shows providers used in configuration                               |

---

## 🔄 Terraform Workflow
👉 Terraform = Write ✍️ → Init 🚀 → Plan 📊 → Apply ⚙️ → Destroy 💥

| 🧩 Step                   | 📌 Action              | 💡 Description                                                        |
| ------------------------- | ---------------------- | ---------------------------------------------------------------------- |
| 1️⃣ ✍️ Write              | 📄 Define `.tf` files  | 🏗️ Write infrastructure as code in .tf files.                         |
| 2️⃣ ⚙️ Initialize         | 🚀 `terraform init`    | 📦 Run terraform init to Setup project & download providers            |
| 3️⃣ 📊 Plan               | 🔍 `terraform plan`    | 👀 Preview changes before applying (terraform plan shows what changes will be made without applying them.)   |
| 4️⃣ 🚀 Apply              | ⚙️ `terraform apply`   | 🏗️ Run terraform apply to Create/update infrastructure                    |
| 5️⃣ 🧹 Destroy (Optional) | 💥 `terraform destroy` | 🗑️ Run terraform destroy to Clean up all resources                       |

---

## 🔐 AWS Credentials in Terraform
  👉 Terraform needs permission to : Create resources, Modify resources and Delete resources..

### 🔐 Adding AWS Access Key and Secret Key to Terraform Configuration
 * When managing AWS resources with Terraform, you must provide AWS credentials for authentication..
 * 🔑 To pass AWS credentials to Terraform using an IAM user, I use the access key ID and secret access key.
 * These credentials can be passed securely in multiple ways such as:
   * Environment variables
   * AWS credentials file
   * Directly in the Provider block (not recommended for production security)


## 🥇 Method 1: AWS CLI Configuration (Recommended)
The most secure and recommended approach is to configure your credentials using the AWS CLI:  

1. 📥 First, install the AWS CLI if you haven't already:
```
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"  
unzip awscliv2.zip  
sudo ./aws/install  
```

2. ⚙️ Configure your AWS credentials:

` aws configure ` 

You'll be prompted to enter:  
• AWS Access Key ID  🔑
• AWS Secret Access Key  🔐
• Default region name 🌍 
• Default output format  

✅ Terraform will automatically reads these credentials. No need to specify them in your Terraform files. (Secure & No hardcoding)

---

## ⚠️ Method 2: Directly in Terraform Configuration (Not Recommended for Production)
```
provider "aws" {  
  region     = "us-east-1"  
  access_key = "your-access-key"  
  secret_key = "your-secret-key"  
}
```
⚠ This method is not secure and should only be used for testing.

#### ❌ Problems:
  * Secrets exposed
  * Risk of leaks
  * Not safe for production

---

### 🎯 Interview-Style Answer:
> "When using an IAM user, I configure AWS CLI using `aws configure` with the access key and secret.  
> Terraform automatically reads those credentials when I run it locally.  
> I also make sure not to hardcode secrets and keep credentials out of Git using `.gitignore`."

