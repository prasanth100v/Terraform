## ⭐ Standard Interview Answer
 * 🌱 Terraform variables are used to pass `dynamic values` into configurations instead of hardcoding them.
 * They help us reuse the same code across multiple environments like `dev`, `test`, and `prod` by changing only the variable values.
 * ⚡Simple Example :
     * Instead of hardcoding an EC2 instance type, I define it as a variable.
     * In dev I can use `t2.micro`, and in prod I can use `t3.large`, without changing the Terraform code.

### ⭐ Why are variables important?
 * Variables prevent hardcoding, reduce duplication, improve readability, and make Terraform code scalable across multiple environments and teams.

### ❌ What NOT to Say (Common Mistake)
  * 🚫 “Variables are just values stored in Terraform” ✔️ Always mention reusability + environments + no hardcoding

## Types of Variables in Terraform
#### 🔹 Input Variables (`most common`)
```hcl
variable "instance_type" {
  description = "EC2 instance type"
  type        = string
  default     = "t2.micro"
}
```
##### Use it like this:
> instance_type = var.instance_type

### 🔹 Output Variables
 * 📜 Used to `display` or pass values after terraform apply.
```hcl
output "instance_id" {
  value = aws_instance.web.id
}
```
