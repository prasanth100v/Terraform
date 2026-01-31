## Terraform workspaces
Terraform workspaces are a clean way to manage multiple environments (dev / test / prod) using the same Terraform configuration, while keeping separate state files.
> 👉 One codebase, many environments, isolated states.
## Why use Workspaces?
> ✅ Avoid duplicating Terraform code ✅ Separate infrastructure per environment ✅ Easy switching between environments
## Basic Workspace Commands
1️⃣ List workspaces
> 👉 terraform workspace list
2️⃣ Create a new workspace
> 👉 terraform workspace new dev  & terraform workspace new prod
3️⃣ Switch workspace
> 👉 terraform workspace select dev
4️⃣ Show current workspace
> 👉 terraform workspace show
## How Terraform Stores State
```
terraform.tfstate.d/            # Each workspace gets its own state file
├── dev/terraform.tfstate
├── prod/terraform.tfstate
```
> (With remote backends like S3, Terraform handles this automatically.)
### Workspaces with Remote Backend (S3 Example)
```
backend "s3" {
  bucket = "my-terraform-states"
  key    = "ec2/terraform.tfstate"
  region = "ap-south-1"
}
```
### Using Variables with Workspaces (Best Practice)
```
variable "instance_type" {
  type = map(string)
  default = {
    dev  = "t3.micro"
    prod = "t3.large"
  }
}

resource "aws_instance" "example" {
  ami           = "ami-0abcd1234"
  instance_type = var.instance_type[terraform.workspace]
}
```









