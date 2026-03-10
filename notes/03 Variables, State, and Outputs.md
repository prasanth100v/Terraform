# 🌈 Variables, State, and Outputs

------------------------------------------------------------------------

## 🔑 4. Variables

In **Terraform**, variables help make infrastructure code **flexible,
reusable, and environment‑friendly**.

I use **input variables** to pass environment-specific values such as: -
Instance type - Region - AMI IDs

I also use **`.tfvars` files** to separate configurations for: - 🧪
Dev - 🚧 Stage - 🚀 Production

Example: defining instance type as a variable and referencing it using
`var.instance_type`.

This allows the **same Terraform code to deploy infrastructure in
multiple environments** just by changing variable values.

------------------------------------------------------------------------

### 🔹 Types of Variables

  -----------------------------------------------------------------------
  Type                Description
  ------------------- ---------------------------------------------------
  **Input Variables** Provide values to Terraform configurations

  **Output            Display useful information after `terraform apply`
  Variables**         (IPs, IDs, etc.)
  -----------------------------------------------------------------------

------------------------------------------------------------------------

### 🎯 Why Do We Use Variables?

✅ Avoid hardcoding values\
✅ Reuse the same code across environments\
✅ Reduce human errors\
✅ Make infrastructure easier to maintain

------------------------------------------------------------------------

### ✅ Example Variable

``` hcl
variable "instance_type" {
  default = "t2.micro"
}
```

### ✅ Usage Example

``` hcl
resource "aws_instance" "example" {
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = var.instance_type
}
```

------------------------------------------------------------------------

# 📦 5. Terraform State

### 📄 terraform.tfstate

Terraform uses a **state file (`terraform.tfstate`)** to track the
current state of infrastructure.

Think of it as **Terraform's memory**.

It keeps track of: - Resources created (EC2, S3, etc.) - Resource IDs -
Current configuration - Metadata about infrastructure

⚠️ **Important:** This file should **NOT be manually edited.**

------------------------------------------------------------------------

### 📌 When Is State Updated?

State file is **created or updated after running:**

``` bash
terraform apply
```

------------------------------------------------------------------------

### 🧠 Where Is It Stored?

**Default Location** - Local project folder → `terraform.tfstate`

**Best Practice for Teams** - Store remotely in: - AWS S3 - Azure
Storage - Terraform Cloud

Example best practice:

-   **S3 bucket** → store state
-   **DynamoDB table** → state locking

------------------------------------------------------------------------

### 🔒 Security Tip

State files may contain **sensitive data**, such as:

-   Passwords
-   Secrets
-   API keys

To secure them:

✅ Use `sensitive = true` in variables\
✅ Use **remote backend with encryption** (S3 + KMS)\
✅ Never commit `.tfstate` files to Git

------------------------------------------------------------------------

### 🎤 Interview‑Style Answer

> Terraform state (`terraform.tfstate`) stores the current state of
> infrastructure.\
> Terraform uses it to compare **what exists vs what is defined in
> code**.
>
> In team environments, we store the state in **S3 with DynamoDB
> locking** to prevent multiple engineers from modifying infrastructure
> at the same time.
>
> We also **never commit `.tfstate` to Git** because it may contain
> sensitive data.

------------------------------------------------------------------------

## 🆘 Scenario: Lost terraform.tfstate File

### 🎤 Interview Answer

In one situation, the **Terraform state file was accidentally deleted**.

Fortunately: - We were using **S3 backend with versioning enabled** - I
restored the previous version directly from the AWS console.

In another scenario where **no backup existed**:

I used:

``` bash
terraform import
```

This command allowed me to **rebuild the Terraform state manually** by
importing existing AWS resources.

Although time‑consuming, it **prevented downtime and avoided creating
duplicate infrastructure**.

------------------------------------------------------------------------

# 🔑 6. Outputs

In Terraform, the **output block** is used to display useful information
after running:

``` bash
terraform apply
```

Common examples include:

-   🌍 Public IP addresses
-   🆔 Resource IDs
-   🪣 S3 bucket names
-   🌐 Subnet IDs

------------------------------------------------------------------------

### 🧠 Why Do We Use Outputs?

✅ View important values after infrastructure is created\
✅ Pass values between Terraform modules\
✅ Debug and verify configuration

------------------------------------------------------------------------

### 📘 Output Syntax

``` hcl
output "name" {
  value = <expression>
}
```

------------------------------------------------------------------------

### ✅ Example Output

``` hcl
output "instance_ip" {
  value = aws_instance.example.public_ip
}
```

After running:

``` bash
terraform apply
```

Terraform displays:

    Outputs:

    instance_public_ip = "3.85.100.22"

------------------------------------------------------------------------

### 🔐 Sensitive Outputs

If an output contains sensitive information (like passwords):

``` hcl
output "db_password" {
  value     = "mysecretpassword"
  sensitive = true
}
```

Terraform will **hide the value in CLI output**.

------------------------------------------------------------------------

### 💬 Interview‑Style Explanation

> In Terraform, the `output` block is used to display useful information
> after infrastructure provisioning.
>
> For example, when launching an EC2 instance, I output the **public IP
> address** so it can be used for SSH access.
>
> Outputs are also useful in **Terraform modules**, where values from
> one module can be passed to another.
>
> If the output contains sensitive data, I mark it with
> **`sensitive = true`** so Terraform hides it from the console.

------------------------------------------------------------------------

### 🧠 Bonus Tip

Terraform outputs are also **stored in the Terraform state file**,
allowing them to be:

-   Referenced by automation scripts
-   Used in other modules
-   Integrated into CI/CD pipelines

------------------------------------------------------------------------

# 🚀 Terraform Quick Summary

  Concept     Purpose
  ----------- ----------------------------------------
  Variables   Make code reusable and configurable
  State       Terraform's memory of infrastructure
  Outputs     Display useful values after deployment

------------------------------------------------------------------------

✨ **Terraform becomes powerful when variables, state management, and
outputs are used together in modular infrastructure design.**
