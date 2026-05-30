# 🌍⚙️ Terraform
## 🚀 What is Terraform?
 * ✨ Terraform is an open-source Infrastructure as Code (IaC) tool developed by HashiCorp.
 * 👉 It helps you:
   * Define infrastructure using code 🧾
   * Provision resources automatically ⚙️
   * Manage and update cloud infrastructure safely 🔄 📌 Uses HCL (HashiCorp Configuration Language) – (HCL is simple & human-readable Language).

### 👉 Why Terraform?
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
 * 💡 IaC means managing infrastructure (like servers, databases, networks) using code instead of manual setup. 
     - Ensures consistency
     - Supports version control
     - Enables automation

### 🔀 Supports Multiple Environments  
  * Easily manage dev, test, and prod using different variable files and workspaces.

### 📌 Execution Plan
  * `terraform plan` shows what will happen before applying changes., This helps to avoid unintended modifications. (🔍 Prevents mistakes & Safe preview)

### 🔁 terraform import
  * `terraform import` lets you bring existing infrastructure under Terraform management without re-creating it. (Already created EC2 → Import into Terraform)

### 🔹 depends_on
  *  🔗 Use `depends_on` when Terraform can’t automatically detect dependencies between resources. 

### 🔹 Terraform Outputs
  * 📤 `Terraform Outputs` : display useful information like IP addresses, Resource IDs etc.., After running `terraform apply`.

### 🛡 Lifecycle Rule
  * 🚨 Prevents accidental deletion of critical resources
```hcl
lifecycle {
  prevent_destroy = true
}
```
 * 🛡️ `prevent_destroy = true` : It’s a lifecycle rule that prevents a resource from being accidentally destroyed.

### 📈 terraform graph
  * 👉 Generates a visual dependency graph

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
 * 👉 Terraform = Write ✍️ → Init 🚀 → Plan 📊 → Apply ⚙️ → Destroy 💥

| 🧩 Step                   | 📌 Action              | 💡 Description                                                        |
| ------------------------- | ---------------------- | ---------------------------------------------------------------------- |
| 1️⃣ ✍️ Write              | 📄 Define `.tf` files  | 🏗️ Write infrastructure as code in .tf files.                         |
| 2️⃣ ⚙️ Initialize         | 🚀 `terraform init`    | 📦 Run terraform init to Setup project & download providers            |
| 3️⃣ 📊 Plan               | 🔍 `terraform plan`    | 👀 Preview changes before applying (terraform plan shows what changes will be made without applying them.)   |
| 4️⃣ 🚀 Apply              | ⚙️ `terraform apply`   | 🏗️ Run terraform apply to Create/update infrastructure                    |
| 5️⃣ 🧹 Destroy (Optional) | 💥 `terraform destroy` | 🗑️ Run terraform destroy to Clean up all resources                       |

---

## 🔐 AWS Credentials in Terraform
  * 👉 Terraform needs permission to : Create resources, Modify resources and Delete resources..

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

`aws configure` 

 You'll be prompted to enter:  
  * AWS Access Key ID  🔑
  * AWS Secret Access Key  🔐
  * Default region name 🌍
  * Default output format  

 * ✅ Terraform will automatically reads these credentials from :
   ```hcl
   ~/.aws/credentials
   ~/.aws/config
   ```
 * No need to specify them in your Terraform files. (Secure & No hardcoding)

---
## 🔑 Step 2: Set AWS Credentials as Environment Variables in Linux instance

```hcl
export AWS_ACCESS_KEY_ID="AKIAxxxxxxxxxxxx"
export AWS_SECRET_ACCESS_KEY="xxxxxxxxxxxxxxxxxxxxxxxx"
export AWS_DEFAULT_REGION="ap-south-1"
```
#### Verify :
```hcl
echo $AWS_ACCESS_KEY_ID
echo $AWS_DEFAULT_REGION
```
* ✨ No access key or secret key is needed in Terraform code.
* ✨ Terraform creates resources using the credentials stored in `environment variables`.
* ✨ This is a secure approach because credentials are kept `outside Terraform code` and can be managed safely in `CI/CD tool` like GitHub Actions..
 
---

## 🏆 Method 4: Use IAM Role with Terraform (AWS Best Practice)
 * ✅ IAM Role Way (Recommended)
   * Instead of giving Terraform an Access Key and Secret Key, you give the `EC2 instance permission directly`.

### ✨ Step-by-Step Example
 * 🚀 Step 1: Create IAM Role
   * Create a role named: `TerraformRole`
   * Attach permissions :
       * AmazonEC2FullAccess
       * AmazonS3FullAccess
  * 🚀 Step 2: **Attach Role to EC2**
   ```hcl
EC2 Instance
   ↓
Actions
   ↓
Security
   ↓
Modify IAM Role
   ↓
TerraformRole
```
   *  Now the EC2 instance has AWS permissions.
 * 🚀 Step 3: Login to EC2
    * `ssh -i mykey.pem ec2-user@PUBLIC-IP`
 * 🚀 Step 4: Check Identity
    * Run: `aws sts get-caller-identity`
    * Output:
      ```JSON
      {
       "Account": "123456789012",
       "Arn": "arn:aws:sts::123456789012:assumed-role/TerraformRole/i-123456"
      }
      ```
      * This proves AWS is automatically providing credentials.
  * 🚀 Step 5: Terraform Configuration
    * Create:
      ```hcl
      provider "aws" {
      region = "ap-south-1"
      }
      ```
      * That's it. Create Resource...
     
  #### How does Terraform authenticate to AWS without Access Keys?
   * ✨ Terraform uses the AWS provider credential chain. When running on an `EC2 instance with an attached IAM Role`,
   * ✨ the provider automatically retrieves temporary credentials from the `EC2 Instance Metadata Service (IMDS)`. No Access Key or Secret Key is required.

---

## ⚠️ Method 4: Directly in Terraform Configuration (Not Recommended for Production)
```hcl
provider "aws" {  
  region     = "us-east-1"  
  access_key = "your-access-key"  
  secret_key = "your-secret-key"  
}
```
 * ⚠ This method is not secure and should only be used for testing.

#### ❌ Problems:
  * Secrets exposed
  * Risk of leaks
  * Not safe for production

---

### 🎯 Interview-Style Answer:
 * 💥 When I run Terraform on an EC2 instance, I attach an IAM Role with the required permissions, such as `EC2`, `VPC`, or `S3 access`.
 * 🌐 In the Terraform provider configuration, I only specify the AWS region :
  ```hcl
  provider "aws" {
  region = "ap-south-1"
  }
  ```
 * 🔐 Terraform automatically uses the temporary credentials provided by the `IAM Role through the EC2 Instance Metadata Service (IMDS)`. This is more secure than using Access Keys..

