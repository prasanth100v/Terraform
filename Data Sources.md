## What are Data Sources in Terraform?
data sources in Terraform are used to read information about existing AWS resources (like VPCs, AMIs, subnets, IAM roles) without creating or modifying them. They are read-only and help reuse existing infrastructure.
> 👉 Data sources do NOT create anything. 👉 They are read-only that already exists
### Common real-world uses:
```
Get an existing VPC
Fetch AMI ID
Read IAM role
Get subnet IDs
Read security group details
```
Basic Syntax
```
data "<PROVIDER>_<RESOURCE>" "<NAME>" {
  # filters or identifiers
}

data "aws_vpc" "main" {
  default = true
}
```
### Why are data sources important in AWS Terraform?
> 👉Avoid hard-coding values (AMI IDs, VPC IDs)  👉 Reuse existing AWS infrastructure
### What happens if a data source resource does not exist in AWS?
> Terraform fails during plan or apply with an error saying the resource was not found.






