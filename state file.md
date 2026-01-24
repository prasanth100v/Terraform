# Terraform state file
The Terraform state file (terraform.tfstate) is a JSON file that stores the current state of infrastructure managed by Terraform.
> Without the state file, Terraform: Cannot track existing resources
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
```
s3://mycompany-terraform-state/
├── dev/terraform.tfstate
├── qa/terraform.tfstate
└── prod/terraform.tfstate
```
Each environment = separate state = safer deployments













