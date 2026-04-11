# 🎨 terraform fmt Command – Explained Clearly

 * 🚀 I use `terraform fmt` to ensure consistent formatting across Terraform configuration files (.tf and .tfvars)
 * which improves readability and maintains code standards in teams.
 ### ⚙️ What it does:
   * 🧹 Fixes `indentation`
   * ➕ `Adds/removes` spaces as per Terraform style guide
   * 📄 Makes your `.tf files` clean and readable  

---

# 🔄 What is terraform taint?

 * The `terraform taint command` marks a resource for `destruction and recreation` during the next `terraform apply`.
 * 💡 Think of it as saying : `"I want Terraform to replace this resource, even if the config hasn't changed."`
 * 📌 Sometimes, a resource (like an `EC2 instance`) is not broken but needs to be recreated — maybe because it was changed manually, is `out of sync`, or has an issue.
 * By using `terraform taint resource_name`, I tell Terraform to destroy and recreate that resource the next time `I apply`.
    * 🧾 Syntax: terraform taint <resource_type.resource_name>
    * Example: `terraform taint aws_instance.web`
    * 🔄 This tells Terraform :  `"On the next apply, destroy and recreate aws_instance.web."`
    * ✅ Then run: `terraform apply` Terraform will recreate that EC2 instance — even if nothing changed in the `.tf` code.

## ⚠️ Important Notes:
   * 📌 The taint mark is stored in the state file, not in the code.
   * ✅ The resource must exist (`it can't be tainted if not yet created`).
   * 🔁 You can untaint if needed: `terraform untaint aws_instance.web`  

### 🎤 Interview-Style Answer:
   * ⚙️ I use terraform taint when I need to forcefully `recreate` a specific resource
   * For example, if an `EC2 instance` becomes unstable or if I want to re-run a user_data script without making `code changes`.
   * 📌 For Example
      * If I taint` aws_instance.web`, Terraform will `not delete` it immediately.
      * But on the next `terraform apply`, it will first destroy the `old instance` and then create a `new one` with the `same config`."

---

# 📥 What is terraform import?
  
  * 👉 Brings existing resources under Terraform management
  * The terraform import command brings existing `cloud resources` (like an `EC2, S3, VPC,` etc.) into Terraform's `state file`
  * So Terraform can manage them without `re-creating`. It does not generate code, only `updates` the state.
  * 🧾 Syntax: `terraform import <resource_address> <real_resource_id> `
      * EC2 Instance : `terraform import aws_instance.web i-0123456789abcdef0  `
      * 📌 This tells Terraform: "Start managing this EC2 instance as aws_instance.web."  
  * After importing, I must `manually` write the matching `.tf` configuration file so Terraform knows what the resource should look like.
  * ⚠️ Important Points:
      * 📄 Only imports the resource into the `state`, not the `.tf code`.
      * ✍️ You must manually write the correct .tf code afterward.
      * 🔄 Useful for `migrating` existing infrastructure into Terraform.  

---

# 🔐 How do you manage secrets in Terraform securely?

To manage secrets securely in Terraform, I follow best practices to avoid exposing sensitive data like `passwords`, `API keys`, or `credentials`.

| 🏷️ Best Pracices                       | 💡 Description                                                                                                                                                                            |
| --------------------------------- ----- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 🌍 Using environment variables         | I avoid hardcoding secrets in `.tf files`. Instead, I set them using environment variables like: <br><br>`bash<br>export AWS_ACCESS_KEY_ID=xxxx<br>export AWS_SECRET_ACCESS_KEY=xxxx<br>` |
| 📁 Using .tfvars files with .gitignore | I store secrets in separate `.tfvars` files and make sure they are not committed to Git by adding them to `.gitignore`.                                                                   |
| 🏦 Using Terraform Cloud or Vault      | For better security, I use tools like `HashiCorp Vault` to store and dynamically fetch secrets.                                                                                           |
| 🚫 Avoid printing secrets in logs      | I make sure to `mark sensitive` variables with `sensitive = true` to prevent them from showing in `terraform plan` or `apply` outputs.                                                    |
| ☁️ Remote state encryption             | When using remote backends (like `S3`), I enable `encryption` at rest and bucket policies to restrict IAM access.                                                                         |


## 🎯 Interview-Style Answer:
  * In my projects, I never hardcode secrets in Terraform files. I either use `environment variables` or `pull secrets` securely from HashiCorp Vault.
  * I also mark sensitive variables with `sensitive = true` to prevent them from appearing in `logs`.
  * For team use, we store the `state` in an `encrypted S3 bucket` and restrict access via `IAM`. This keeps our infrastructure `secure` and `auditable`.

---

# 🔍 What is terraform show?

 * 🚀 The `terraform show` command displays the `current state of our infrastructure` from the Terraform `state file` (terraform.tfstate).
 * Use terraform show to inspect the `current state of infrastructure` and verify `what resources Terraform has created`.
 * It helps you see what resources `exist`, their `attributes`, and `current values`.
 * 🎯 Use Cases
     * 🔍 Debugging
     * 📊 Reviewing deployed resources
     * 🔄 Inspecting state

---

## 🏁 Final Summary
 * ✨ fmt → Clean code
 * ✨ taint → Force recreate
 * ✨ import → Manage existing infra
 * ✨ show → Inspect state
 * ✨ secrets → Secure handling

