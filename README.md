# 🌍 Terraform Glossary (with Examples)
| **Term**               | **Meaning**                                                        | **Example**                      |
| ---------------------- | ------------------------------------------------------------------ | -------------------------------- |
| **Terraform**          | Infrastructure as Code (IaC) tool to create/manage cloud resources | Create EC2, S3, VPC using code   |
| **Provider**           | Plugin that lets Terraform talk to a cloud/service                 | `aws`, `azurerm`, `google`       |
| **Resource**           | A cloud object Terraform manages                                   | EC2 instance, S3 bucket          |
| **Data Source**        | Reads existing resources (does not create)                         | Fetch existing VPC               |
| **Module**             | Reusable group of Terraform files                                  | EC2 module used in many projects |
| **Variable**           | Input value to Terraform configuration                             | Region, instance type            |
| **Output**             | Value printed after apply                                          | EC2 public IP                    |
| **State File**         | Stores current infrastructure mapping                              | `terraform.tfstate`              |
| **Backend**            | Location where state file is stored                                | S3 backend                       |
| **Remote Backend**     | State stored remotely (shared)                                     | S3 + locking                     |
| **Local Backend**      | State stored locally                                               | Default backend                  |
| **Plan**               | Shows what Terraform *will* change                                 | `terraform plan`                 |
| **Apply**              | Creates or updates infrastructure                                  | `terraform apply`                |
| **Destroy**            | Deletes infrastructure                                             | `terraform destroy`              |
| **Init**               | Initializes provider & backend                                     | `terraform init`                 |
| **Validate**           | Checks syntax correctness                                          | `terraform validate`             |
| **Workspace**          | Separate environments with same code                               | dev, test, prod                  |
| **Provisioner**        | Runs scripts after resource creation                               | `remote-exec`                    |
| **Lifecycle**          | Controls resource behavior                                         | Prevent destroy                  |
| **Depends_on**         | Explicit dependency definition                                     | EC2 depends on VPC               |
| **Count**              | Create multiple resources                                          | 3 EC2 instances                  |
| **For_each**           | Loop using                                                         | Multiple IAM users               |
| **Locals**             | Local reusable values                                              | Naming convention                |
| **Sensitive**          | Hides output values                                                | Passwords                        |
| **Null Resource**      | Executes actions without resource                                  | Run scripts                      |
| **Import**             | Bring existing infra into Terraform                                | Import EC2                       |
| **Refresh**            | Sync state with real infra                                         | Update state                     |
| **Locking**            | Prevents parallel state changes                                    | S3 native locking                |
| **Terraform Registry** | Repo for providers/modules                                         | registry.terraform.io            |

