# 🌍 Terraform Glossary (with Examples)
### ⭐ Infrastructure as Code (IaC) : Managing infra through code instead of manual setup
| **Term**               | **Meaning**                                                        | **Example**                      |
| ---------------------- | ------------------------------------------------------------------ | -------------------------------- |
| **Terraform**          | Infrastructure as Code (IaC) tool to create/manage cloud resources | Create EC2, S3, VPC using code   |
| **Provider**           | Plugin that lets Terraform talk to a cloud/service                 | `aws`, `azurerm`, `google`       |
| **Resource**           | A cloud object Terraform manages                                   | EC2 instance, S3 bucket          |
| **Data Source**        | Reads existing resources (does not create)                         | Fetch existing VPC ID            |
| **Module**             | Reusable group of Terraform files                                  | EC2 module used in many projects |
| **Variable**           | Input value to Terraform configuration                             | Region, instance type            |
| **Output**             | Value printed after apply                                          | EC2 public IP                    |
| **State File**         | Stores current infrastructure mapping                              | `terraform.tfstate`              |
| **Backend**            | Location where state file is stored                                | S3 backend                       |
| **Remote Backend**     | State stored remotely (shared)                                     | S3 + locking                     |
| **Local Backend**      | State stored locally                                               | Default backend                  |
| **Plan**               | Shows what Terraform *will* change    ( Preview infra changes  )   | `terraform plan`                 |
| **Apply**              | Creates or updates infrastructure                                  | `terraform apply`                |
| **Destroy**            | Deletes infrastructure                                             | `terraform destroy`              |
| **Init**               | Initializes provider & backend  ( First command )                  | `terraform init`                 |
| **Validate**           | Checks syntax correctness       ( Catch errors early  )            | `terraform validate`             |
| **Workspace**          | Separate environments with same code                               | dev, test, prod                  |
| **Provisioner**        | Runs scripts after resource creation   ( Run shell commands  )     | `remote-exec`                    |
| **Lifecycle**          | Controls resource behavior                                         | Prevent destroy                  |
| **Depends_on**         | Explicit dependency definition                                     | EC2 depends on VPC               |
| **Count**              | Create multiple resources                                          | 3 EC2 instances                  |
| **For_each**           | Loop using Creates resources using map/set                         | Multiple IAM users               |
| **Locals**             | Local reusable values                                              | Naming convention                |
| **Sensitive**          | Hides output values                                                | Passwords                        |
| **Null Resource**      | Executes actions without resource                                  | Run scripts                      |
| **Import**             | Bring existing infra into Terraform                                | Import EC2                       |
| **Refresh**            | Sync state with real infra                                         | Update state                     |
| **Locking**            | Prevents parallel state changes                                    | S3 native locking                |
| **Terraform Registry** | Repo for providers/modules                                         | registry.terraform.io            |
| **Root Module**        | Main working directory                                             | `main.tf`                            |
| **Child Module**       | Module called from root                                            | EC2 module                           |
| **Lifecycle**          | Controls resource behavior                                         | Prevent destroy                      |
