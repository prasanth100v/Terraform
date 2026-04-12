# 🔁 What is a Dynamic Block?
  * 🚀 A dynamic block is used when you want to create multiple repeating parts of a resource using a `loop`, instead of writing them manually.
  * Why use it ❓
      * Sometimes you need to create repeated blocks (like `multiple security group rules`), and instead of writing each one manually, you can use a dynamic block.
      * Simple Idea `Manual repetition ❌ → Dynamic loop ✅`
      * 🔁 Avoid repeating code
      * 📦 Handle multiple similar configurations
      * ✨ Make code clean & reusable

## 🧾 Simple Example: Creating Security Group Rules
Let’s say you want to allow two ports — `80 and 443` — in a security group.

### ✍️ Without Dynamic Block (❌ `manual`)
```hcl
resource "aws_security_group" "example" {
  name = "example-sg"

  ingress {
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  ingress {
    from_port   = 443
    to_port     = 443
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }
}
```
👉 Manual repetition : You wrote two repeated blocks yourself. What if you need 10 or more? That’s too much work.


### ⚡ With a Dynamic Block (✅ easy and clean)
```hcl
variable "allowed_ports" {
  default = [80, 443]
}

resource "aws_security_group" "example" {
  name = "example-sg"

  dynamic "ingress" {
    for_each = var.allowed_ports                    # 🔁 loop through each port (80, 443)
    content {
      from_port   = ingress.value
      to_port     = ingress.value
      protocol    = "tcp"
      cidr_blocks = ["0.0.0.0/0"]
    }
  }
}
```

## ✅ Conclusion:

 - ✨ You don’t have to write each manually.  
 - 🧠 But it’s good to write `one or two manually` first just to understand the structure.  
 - 🔄 Then you can use `for_each` inside a dynamic block to make your code flexible and reusable.  

---

# 🔁 What is `for_each`?

 * for_each is used in Terraform to create multiple resources from a `list or map`.
 * It’s like a `loop` : instead of repeating code, Terraform creates resources based on input data.
 * I use `for_each` when I need to `create multiple resources with unique values`, as it provides better control and stability compared to `count`."

### 📄 Example 
```hcl
resource "aws_s3_bucket" "buckets" {
  for_each = {
    dev  = "dev-bucket"
    prod = "prod-bucket"
  }

  bucket = each.value
}
```

### 🔁 for_each vs 🔢 count in Terraform
| 🧩 Feature / Aspect    | 🔢 `count`                                  | 🔁 `for_each`                               |
| ---------------------- | -------------------------------------------- | ------------------------------------------- |
| 🎯 Purpose             | 📦 Create multiple resources using a number | 🗂️ Create multiple resources using map/set |
| 📥 Input Type          | 🔢 Integer (`count = 3`)                    | 🧩 Map/Set (`{a=1, b=2}` / `toset([...])`)  |
| 🔍 Accessing Resources | 📍 Index (`resource[0]`, `resource[1]`)     | 🔑 Key (`resource["a"]`, `resource["b"]`)   |
| 🎯 Best Use Case       | 📦 Identical resources                      | 🎨 Unique resources (different configs)     |
| 🔄 Flexibility         | ⚠️ Less flexible                            | ✅ More flexible                             |
| 🔁 Handling Changes    | ❌ Index shift → resource recreation         | ✅ Stable keys → no unnecessary recreation   |
| 📖 Readability         | ⚠️ Harder in complex cases                  | ✅ Better with meaningful keys               |
| 🔀 Conditional Usage   | ✅ Easy (`count = condition ? 1 : 0`)        | ⚠️ Slightly complex (use `{}` for false)    |
| 🧪 Example             | `count = 2`                                  | `for_each = {web=80, app=8080}`             |
| 🆔 Resource Identity   | 🔢 Based on index                           | 🔑 Based on key                             |

### ✅ When to Use What?
  * Use `count` 👉 when resources are `same and simple`
      * Index-based (`0,1,2`)
  * Use `for_each` 👉 when resources are different or need `unique names/values`
      * Key-based (`web, app`)

---

## 📦 4. What are `Terraform modules`, and how do you use them?

 * 🚀 Modules : 👉 Reusable Terraform components, Like Function (`write once, reuse everywhere`)
 * Modules help me write `DRY code` by reusing the same infrastructure logic across environments.
 * 📦 I create root modules for each environment and use `child modules` for resources like `VPC`, `EC2`, or `RDS`.
 * 📦 I also keep common modules in a `separate Git repo` or `folder structure` and version them using `Git tags`.
 * 🎯 I use modules to reuse infrastructure code across environments, keeping my Terraform code `clean`, `scalable`, and `maintainable`.

---

## 🛠️ Common Terraform CLI Commands (Interview-Style Summary):

 * I regularly use Terraform CLI commands like `init` to set up my project, plan to `preview changes`, apply to make them, and destroy to remove them.
 * I also use `fmt` and `validate` for formatting and syntax checking, and `import` or `taint` when I need to manage or recreate existing resources.
 * ⚡ One-line : `👉 Terraform = Init 🚀 → Plan 📊 → Apply ⚙️ → Destroy 💥 + Helpers 🎨`

### 🧰 Common Terraform CLI Commands
| 🧩 Command             | 💡 Purpose                                 |
| ---------------------- | ------------------------------------------ |
| 🚀 `terraform init`    | 📦 Initialize project & download providers |
| 📊 `terraform plan`    | 🔍 Preview changes before execution        |
| ⚙️ `terraform apply`   | 🏗️ Create/update infrastructure           |
| 💥 `terraform destroy` | 🗑️ Remove all resources                   |
| 🎨 `terraform fmt`     | 🧹 Format code (standard style)            |
| ✅ `terraform validate` | 🧪 Check syntax & configuration            |
| 🔄 `terraform import`  | 📥 Bring existing resources into state     |
| ⚠️ `terraform taint`   | 🔁 Force resource recreation               |


---

# 🚫 What is ignore_changes?

 * 🚀 Tells Terraform to ignore specific changes
 * Let’s say you have this EC2 instance. Now, someone (or another `script`) manually changes the Name tag (`MyServer`) in the AWS Console to "ChangedManually".
 * 🎯 When you run: `terraform plan `
 * 🛠️ Terraform will say : `Hey! This tag has changed. I want to fix it back to MyServer!` , But you don’t want Terraform to care about this change.
 * I use `ignore_changes` when I don’t want Terraform to react to small changes made outside Terraform — like someone changing a `tag manually` or a `system auto-updating` a value.
 * This `avoids unnecessary changes` during `terraform apply`. 

## 🛠️ Solution: Use ignore_changes
```hcl
lifecycle {
  ignore_changes = [tags]        # ✅ Ignore external changes 
}
```
Now, even if the tag is changed manually in AWS, Terraform will ignore it during `plan/apply`.

---

## 🏁 Final Summary

 * ✨ Dynamic → Repeat nested blocks
 * ✨ for_each → Loop resources
 * ✨ Modules → Reuse code
 * ✨ ignore_changes → Ignore drift
