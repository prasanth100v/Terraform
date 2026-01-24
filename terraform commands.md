### What is ***terraform refresh?***
> It updates the state file to match the real infrastructure without making changes.
```
terraform refresh
```
### What is terraform import?
> Used to bring existing infrastructure under Terraform management.
```
terraform import aws_instance.web i-123456
```
### aws_instance.web i-123456
```
  1. aws_instance →    Terraform resource type
  2. web          →    Resource name in your Terraform configuration
  3. i-123456     →    Real EC2 Instance ID from AWS
```
> ⚠️ This resource must already exist in your .tf files.
 ### Does terraform import create resources❓
> ❌ No ✔ Only updates state file







