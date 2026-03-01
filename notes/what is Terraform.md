# ✅ Terraform

Terraform is an open-source **Infrastructure as Code (IaC)** tool developed by HashiCorp.  
It allows you to define, provision, and manage cloud infrastructure using a simple, declarative language called **HCL (HashiCorp Configuration Language)**.

It supports multiple cloud providers (AWS, Azure, GCP, etc.) and services, enabling consistent and repeatable infrastructure deployments.

Terraform creates an execution plan, shows what it will do, and then applies the changes.

---

## 📌 Key Concepts

### Write Configuration
- Use `.tf` files to define infrastructure.

### State File
- Terraform maintains a `.tfstate` file to track the actual state of infrastructure.
- The file `terraform.tfstate` stores the current infrastructure status.
- It helps track what resources are created and manage updates.

### Remote Backend
- Stores the Terraform state file in cloud storage (like S3) instead of locally.
- Helps in team collaboration and state locking.

### Infrastructure as Code (IaC)
IaC means managing infrastructure (servers, databases, networks) using code instead of manual setup.
- Ensures consistency
- Supports version control
- Enables automation

---

## 🌍 Multiple Environments

Terraform supports managing:
- Development (dev)
- Testing (test)
- Production (prod)

Using:
- Different variable files
- Workspaces

---

## 📋 Execution Plan

`terraform plan` shows what will happen before applying changes.  
This helps avoid unintended modifications.

---

## 🔹 terraform import

`terraform import` lets you bring existing infrastructure under Terraform management without re-creating it.

---

## 🔹 depends_on

Use `depends_on` when Terraform cannot automatically detect dependencies between resources.

---

## 🔹 Terraform Outputs

Outputs display useful information like:
- IP addresses
- Resource IDs
- URLs

After running `terraform apply`.

---

# 🚀 Core Terraform Commands

| Command | Description |
|----------|-------------|
| `terraform init` | Initializes your Terraform project. Downloads provider plugins and sets up the working directory. |
| `terraform plan` | Shows an execution plan (what will happen before applying changes). |
| `terraform validate` | Checks if the code syntax is correct. |
| `terraform apply` | Applies changes and provisions infrastructure. |
| `terraform destroy` | Destroys all resources defined in Terraform code. Use carefully. |
| `terraform state list` | Lists resources in the state file. |
| `terraform providers` | Lists all providers used in the configuration. |
| `terraform graph` | Generates a visual dependency graph. |

---

# 🔄 Terraform Workflow

1. **Write** – Define infrastructure in `.tf` files.
2. **Initialize** – Run `terraform init` to set up providers.
3. **Plan** – Run `terraform plan` to verify changes.
4. **Apply** – Run `terraform apply` to create/update resources.
5. **Destroy (Optional)** – Run `terraform destroy` to clean up.

---

# 🛡 Lifecycle Rule

```hcl
lifecycle {
  prevent_destroy = true
}
```

`prevent_destroy = true` prevents a resource from being accidentally destroyed.

---

# 🔐 Adding AWS Access Key and Secret Key to Terraform

When managing AWS resources with Terraform, you must provide AWS credentials for authentication.

These credentials can be passed securely in multiple ways:
- Environment variables
- AWS credentials file
- Provider block (not recommended for production)

---

## 🎤 Interview-Style Answer

> "When using an IAM user, I configure AWS CLI using `aws configure` with the access key and secret.  
> Terraform automatically reads those credentials when I run it locally.  
> I also make sure not to hardcode secrets and keep credentials out of Git using `.gitignore`."

---

# ✅ Method 1: AWS CLI Configuration (Recommended)

### Step 1: Install AWS CLI

```bash
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
```

### Step 2: Configure AWS Credentials

```bash
aws configure
```

You will be prompted to enter:
- AWS Access Key ID
- AWS Secret Access Key
- Default region name
- Default output format

Terraform automatically uses these credentials.  
No need to specify them in Terraform files.

---

# ⚠ Method 2: Directly in Terraform Configuration (Not Recommended for Production)

```hcl
provider "aws" {
  region     = "us-east-1"
  access_key = "your-access-key"
  secret_key = "your-secret-key"
}
```

⚠ This method is not secure and should only be used for testing.
