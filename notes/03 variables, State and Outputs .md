# 🧩 4. Variables
## 🚀 What are Variables?
 * ✨ Variables make Terraform code dynamic, flexible, and reusable., 👉 Instead of hardcoding values, you pass them as inputs.
 * I use input variables to pass environment-specific values like instance type or region.
 * I also use `tfvars` files to separate configurations for `dev`, `stage`, and `prod`.
 * For example, I define the instance type using a variable and refer to it using `var.instance_type`.
 * 💡 This way, I can launch the same infrastructure in multiple environments just by changing variable values.

```
Hardcoding ❌ → Fixed values
Variables  ✅ → Dynamic values
```

## 📌 Types of Variables:
- 🟢 Input Variable      – Used to give values to Terraform  
- 📤 Output Variables    – Used to display useful info after terraform apply (like IPs, IDs).  


## ❓ Why do we use Variables?
- 🚫 To avoid hardcoding values (like region, AMI, instance type).  
- 🔁 To reuse the same code in different environments (Dev, Staging, Prod).  
- ⚡ Easy updates without changing code to make changes easier and reduce human errors.  

## 📄 Example: Input Variable
```hcl
variable "instance_type" {
  default = "t2.micro"                     # input variable
}
```

## 🔧 Usage in Resource :
```hcl
resource "aws_instance" "example" {
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = var.instance_type
}
```

## 💡 Real DevOps Practice  (Use .tfvars files) :
```hcl
# dev.tfvars
instance_type = "t2.micro"

# prod.tfvars
instance_type = "t3.medium"
```


# 💾 5. State
 * 👉 `terraform.tfstate` is the source of truth (memory of infrastructure)
 * Terraform uses a state file (terraform.tfstate) to track the current state of your infrastructure.
 * 💡 It acts as Terraform’s memory — it knows what you’ve already created, updated, or deleted.

 Simple Understanding : `Terraform Code → Desired State 📄`   `Terraform State → Current State ⚡`

## 📌 Details:
In Terraform, this file stores:  
• 📦 What resources (like EC2, S3, etc.) are already created  
• 🔍 Their current infrastructure status (IDs, settings), Should NOT be manually edited.  
• 🔄 Created and updated after : `terraform apply `


## 📍 Where Is It Stored?
• 🖥️ By default: Locally in your project folder (as `terraform.tfstate`)  
• ☁️ In teams: It’s best to store it remotely (e.g., in `S3 with locking`) using a backend.  (Best Practice ⭐)

## 🔐 Security Tip:
 If the state file contains sensitive data (like passwords or secrets), secure it:  
   * 🛡️ Use `sensitive = true` in variable definitions.
   * 🔒 Use remote backend with encryption (like `S3 with KMS`).
   * 🚫 Avoid exposing secrets in state 


---

### 🗣️ Interview Answer (State) :
 
 * Terraform state file stores the current state of infrastructure. Terraform uses it to compare the desired configuration with actual resources. ( what’s deployed vs. what’s defined in code.)
 * In my projects, I use remote backends like S3 with DynamoDB locking to ensure team collaboration and prevent conflicts.
 * We never commit .tfstate to Git, and we secure it to avoid leaking secrets.


## ⚠️ You lost the terraform.tfstate file
### 🎤 Interview-Style Answer:
 * In one case, we accidentally lost the terraform.tfstate file. Fortunately, we were using S3 with versioning enabled, so I was able to restore the latest version from the AWS console.  
 * In another case where no backup existed, I used terraform import to manually rebuild the state for each AWS resource.
 * It took time, but it helped avoid downtime or duplicate resources.

---

# 📤 6. Outputs
 * ✨ In Terraform, the output block is used to display useful information after you run terraform apply.
 * like IP addresses, resource IDs, S3 bucket name and SubnetID etc.
 * 👉 Outputs are stored in : `terraform.tfstate`

## ❓ Why do we use Outputs?
- 👀 To see important values after resources are created  
- 🔗 To pass values to other Terraform modules  
- 🧪 To debug and verify values 

## 🧾 Syntax of Output Block
```hcl
output "name" {
  value = <expression>
}
```

## 📄 Example:
```hcl
output "instance_ip" {
  value = aws_instance.example.public_ip
}
```

## 📊 After running terraform apply, you will see:
```hcl
Outputs:
instance_public_ip = "3.85.100.22"
```

---

## 🔐 Example : Output with sensitive = true  
If your output is a password or secret:
```hcl
output "db_password" {
  value     = "mysecretpassword"
  sensitive = true
}
```
🔒 Terraform hides the output value on terminal screen.

---

## 🎤 How to Explain in Interview (Sample Answer):
 
 * In Terraform, I use the output block to display important information after provisioning infrastructure.
 * ✨ For example, when I provision an EC2 instance, its public IP address is automatically displayed in the output, enabling immediate SSH access.
 * Outputs are also useful when working with modules — I can expose values from one module and reuse them in another module or resource block.
 * If the output is sensitive, like a **password**, I mark it with `sensitive = true` to prevent it from being displayed.

💡 **Bonus Tip** : Outputs are stored in the `Terraform state file` so that they can be reused in automation.

### 🏁 Final Summary

 * ✨ Variables →  Make code flexible
 * ✨ State     →  Tracks infrastructure (source of truth)
 * ✨ Outputs   →  Show useful results
