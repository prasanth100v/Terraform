# Terraform state file
The Terraform state file (terraform.tfstate) is a JSON file that stores the current state of infrastructure managed by Terraform.
> 👉 Terraform State is the brain of Terraform.
Without the state file, Terraform: Cannot track existing resources
State file stores:
```
1. Resource IDs (EC2 ID, ALB ARN, etc.)
2. Resource attributes
3. Provider information
4. Dependencies
5. Metadata
6. Outputs
```
⚠️ It may also store sensitive data like passwords or tokens.
### Where is the Terraform state file stored?
Two options:
```
👉 Local backend (default)
👉 Production uses remote backend

remote backend allowing:
      1. Team collaboration
      2. State locking
      3. Disaster recovery
```
### What happens if the state file is deleted?
Terraform loses track of existing resources.
```
Effects:
    1. terraform plan shows everything as new
    2. Risk of duplicate resources
    3. Manual recovery required
Recovery options:
    1. Restore from S3 versioning
    2. terraform import
```
## Store Terraform state remotely in AWS S3 (instead of local terraform.tfstate)
## 🧱 STEP 1: Create an S3 bucket (for Terraform state)
Why?

> Terraform does NOT create the bucket for you. The bucket must exist before terraform init.

### Option A: Create via AWS Console
```
1. Open S3 → Create bucket
2. Bucket name (must be globally unique): mycompany-terraform-state
3. Region: us-east-1
4. Block all public access ✅
5. Enable Bucket Versioning ✅ (very important)
6. Default encryption: SSE-S3 (AES-256) ✅
7. Create bucket
```
### Option B: Create via AWS CLI
```
aws s3api create-bucket \
  --bucket mycompany-terraform-state \
  --region us-east-1
```
Enable versioning:
```
aws s3api put-bucket-versioning \
  --bucket mycompany-terraform-state \
  --versioning-configuration Status=Enabled
```
## 🧱 STEP 2: Configure AWS credentials (VERY IMPORTANT)
> Terraform must be able to authenticate to AWS.
Check credentials
```
aws sts get-caller-identity
```
> If this works → Terraform will also work ✅
#### Example (aws configure):
```
aws configure
AWS Access Key ID: ********
AWS Secret Access Key: ********
Region: us-east-1
```
## 🧱 STEP 3: Create Terraform backend configuration
🔐 What is S3 Native State Locking?
S3 Native State Locking is a Terraform 1.10+ feature that allows Terraform to lock the state file directly in Amazon S3, without using DynamoDB.
> It uses Amazon S3 Conditional Writes to ensure only one Terraform operation can modify the state at a time.

> Before Terraform 1.10: 👉 S3 backend required DynamoDB for locking 👉 Extra AWS service to manage 👉 Extra cost & IAM permissions
Create a file called backend.tf
```
terraform {
  backend "s3" {
    bucket       = "mycompany-terraform-state"          #S3 bucket where state is stored
    key          = "dev/terraform.tfstate"              #Path inside bucket
    region       = "us-east-1"
    use_lockfile = true
    encrypt      = true
  }
}
```
## 🧱 STEP 4: Create a simple Terraform resource (example)
Create main.tf
```
provider "aws" {
  region = "us-east-1"
}

resource "aws_s3_bucket" "example" {
  bucket = "my-demo-bucket-123456"
}
```
> This ensures Terraform has something to manage, so a state file gets created.

## 🧱 STEP 5: Initialize Terraform (MOST IMPORTANT STEP)
```
terraform init
```
### What happens internally:
```
1. Terraform reads backend.tf
2. Connects to S3
3. Creates backend configuration
4. Creates remote state file
5. If local state exists → asks to migrate
```
You’ll see:
> Successfully configured the backend "s3"!
## 🧱 STEP 6: Apply Terraform (state file is created here)
Run:
```
terraform apply
```
What happens now:
```
Terraform creates AWS resources
Terraform writes state file : ✔ State file is now stored in AWS
```
## 🧱 STEP 7: Verify state file in S3
```
S3 → mycompany-terraform-state → dev/
```
## 🧱 STEP 8: Understand state locking (use_lockfile = true)
Why locking?
```
Prevents:
1. Two engineers running terraform apply at same time
2. State corruption
```
# 🧱 Multi-environment structure (Best Practice)
> Separate state files per environment
```
s3://mycompany-terraform-state/
├── dev/terraform.tfstate
├── qa/terraform.tfstate
└── prod/terraform.tfstate
```
Each environment = separate state = safer deployments

## Can we edit terraform.tfstate manually?
> ❌ Not recommended
```
Reasons:
   1. High risk of corruption
   2. Hard to debug
Only edit manually in emergency cases, and always:
   1. ✔ Take backup
   2. ✔ Validate after edit
```
### How do you secure the Terraform state file?
```
1. Store in remote backend
2. Enable encryption
3. Restrict IAM access
4. Enable versioning
5. Use KMS encryption
6. Never commit to Git
```

> terraform plan → shows what will change

> terraform State → stores what already exists


## 📌 Terraform State Commands – Explained (Table Format)
| Command                            | Purpose               | What it Does                                          | When to Use                   |
| ---------------------------------- | --------------------- | ----------------------------------------------------- | ----------------------------- |
| `terraform state list`             | List resources        | Shows all resources tracked in the state file         | To see what Terraform manages |
| `terraform state show <resource>`  | Show resource details | Displays attributes of a specific resource from state | Debugging / verification      |
| `terraform state pull`             | Download state        | Outputs current remote state in JSON                  | Backup / inspection           |
| `terraform state push`             | Upload state          | Pushes a local state file to backend                  | Disaster recovery             |
| `terraform state mv`               | Move resource         | Renames or moves resources inside state               | Refactoring code              |
| `terraform state rm`               | Remove resource       | Removes resource from state only                      | Stop Terraform control        |
| `terraform state list -id`         | List IDs              | Shows resource IDs only                               | Quick ID reference            |
| `terraform state show -json`       | JSON output           | Outputs resource in JSON format                       | Automation / scripting        |

## 🆚 DynamoDB Locking vs S3 Native Locking
| Feature           | DynamoDB Locking | S3 Native Locking |
| ----------------- | ---------------- | ----------------- |
| Terraform version | Any              | **1.10+ only**    |
| Extra AWS service | Yes              | ❌ No              |
| Cost              | DynamoDB cost    | No extra cost     |
| Setup complexity  | Higher           | Simple            |
| Lock storage      | DynamoDB item    | S3 lock file      |
| Reliability       | Very high        | Very high         |
| Recommended now   | Legacy setups    | ✅ Preferred       |

