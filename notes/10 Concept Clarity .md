## 🔄 Have you ever migrated existing AWS resources into Terraform? How?
  * Yes. In one project, we had manually created `S3 buckets and IAM roles`.
  * 📥 I used `terraform import` to bring those resources into the `state file`.
  * ✍️ After importing, I wrote `.tf` code to match the real config, `validated` it using `terraform plan`, and ensured there were `no drift` issues.
  * ⚠️ It was tricky when some resource not managed by Terraform — we handled those using `lifecycle`{ `ignore_changes` = [...] }.


## ⚙️ Can you explain what happens internally when you run terraform apply?

 When I run `terraform apply`, Terraform:  
 1. 📂 Loads all `config files` (.tf).  
 2. ☁️ Fetches state from the backend (like` S3`).  
 3. 🔍 Compares the `current state` with the `desired config` (diff).  
 4. 🧩 Builds a dependency graph.  
 5. 🚀 Applies changes in the `correct order`.
 6. 💾 The state is then updated and pushed back to the backend. If anything fails `midway`, it stops and the state reflects only completed changes, allowing safe retries.
      * Terraform compares the desired configuration with the current state, builds a dependency graph, and applies changes in order while updating the state file.


## 🛠️ What’s your approach to avoid downtime when updating infrastructure?
 
  * 📊 I always review `terraform plan` carefully, especially when modifying resources like `load balancers`, `EKS nodes`, or `route tables`.
  * 🔄 I use `blue-green` or `rolling update` strategies where possible.
  * 📌 For example,  
     * when replacing an Auto Scaling group, I create a new one, attach it, test, then detach and delete the old one — all in Terraform.
     * Also, I use `create_before_destroy` in `lifecycle blocks` to minimize downtime:
```
lifecycle {
  create_before_destroy = true
}
```

---

## 🌐 After applying Terraform, your public subnet EC2 instance doesn’t get internet. How do you debug?

 * ✅ I check if the subnet has `map_public_ip_on_launch = true`, and confirm that the route table has a `0.0.0.0/0` route pointing to an internet gateway.
 * ✅ I also verify that the `security group` allows outbound traffic.
 * 🗣️ Interview Answer : `I verify subnet settings, route table configuration, and security group rules to ensure proper internet connectivity.`


## 🔐 How do you enable a Kubernetes Pod to access an S3 bucket securely?

 * We used IRSA (`IAM Roles for Service Accounts`) in EKS to securely allow pods to access AWS services. I enable `OIDC` in EKS.
 * We created `IAM roles` with `required permissions` and linked them to Kubernetes `Service Accounts`.
 * When a pod uses that Service Account, it automatically gets `temporary AWS credentials` through `OIDC`, so we don’t need to store `access keys` in the application.

---

## 🔄 What is GitOps with Terraform and Argo CD?

 - ⚙️ Terraform is used to provision infrastructure (e.g., `EKS`, `VPC`, `IAM`, `S3`).  
 - 🚀 `Argo CD` is used to deploy and manage Kubernetes applications from Git repositories.  
 - 🔁 GitOps workflow: everything (`infra + app`) is defined in Git and applied automatically.  

## 🎤 Real Interview-Style Answer:
  * 🧩 In our GitOps workflow, we use Terraform to provision infrastructure like `EKS, subnets, IAM roles`, and `OIDC setup for IRSA`.
  * 🧩 Once Terraform completes, our Argo CD instance (`deployed in EKS`) continuously `syncs` Kubernetes manifests from a `Git repo` to deploy our microservices.
  * 🧩 This clean separation helps us manage `infra` and `apps` independently.
  * 🗣️ Interview Answer :  We used `Terraform for infrastructure provisioning` and `Argo CD for Kubernetes deployments`, both driven by `Git in a GitOps model`.


## 📦 Have you worked with Terraform modules from the registry or written your own?

  * Yes, I’ve used modules from the Terraform AWS Registry — like the `EKS` and `VPC modules`.
  * But in most cases, I prefer writing `custom modules` for better control and flexibility.
  * I use both `public` and `custom modules`, depending on the level of control and customization required.

---

## 🚢 How do you use Terraform to deploy an EKS cluster?

 * 👉 I use the `terraform-aws-modules/eks/aws` module. It simplifies the creation of the `EKS control plane`, `node groups`, and the `OIDC provider`.
 * 👉 I pass `subnet IDs` from a separate VPC module and customize node group settings like instance types and desired capacity.
 * 👉 After apply, I configure `kubectl` using `aws eks update-kubeconfig`.


## 🧪 Your EC2 instance is not reachable after Terraform deployment. What are your debugging steps?

 * 🌐 I check if the subnet is `public` and associated with the correct `route table`.
 * 📡 Then I verify that the instance has a `public IP`, and the security group allows `inbound access` (e.g., port `22` for SSH).
 * I also check if the `NACL` or `IAM role` is causing a block.

---

## ✅ What are some best practices you follow in Terraform?

Some key practices I follow:  
 - 🔐 Use `remote state` with `locking `
 - 📦 Split code into `reusable modules`  
 - 🧹 Use terraform `fmt`, `validate`, and `tflint`
 - 🧾 Use variable types, validation, and descriptions  
 - 🚫 Avoid hardcoding values; prefer `variables` and tfvars  
 - 📊 Always review `terraform plan` before apply  

### 🎯 Bonus Tip for Interviews:
 * Speak with clarity like:  
   * In my last project, I managed multiple environments using `Terraform Workspaces` and separate `tfvars` files.
   * I created reusable `modules`, set up `remote state`, and integrated Terraform with `GitHub Actions` for automation and safety.

---

## 🔒 terraform.lock.hcl – Explained Clearly

 * The `terraform.lock.hcl` file is automatically created by Terraform to lock `provider versions` used in your project.
 * Lock provider versions - Ensures consistent provider versions across teams, avoiding issues due to `version mismatches`.
 * 📌 It’s created in your Terraform root folder after running: `terraform init `

### 📄 Example Content of `terraform.lock.hcl` :
```
provider "registry.terraform.io/hashicorp/aws" {
  version     = "5.35.0"  
  constraints = ">= 5.0.0"  
}
```

  - 🔢 version        : exact version used.  
  - 📏 constraints    : what versions are allowed (if defined in .tf).  


## 🎤 Interview-Style Answer Example:

 * terraform.lock.hcl ensures everyone on the `team` uses the `same provider versions`.
 * It’s automatically generated during `terraform init`


### 🏁 Final Summary

 * ✨ Import → Manage existing infra
 * ✨ Apply → Executes plan safely
 * ✨ IRSA → Secure pod access
 * ✨ GitOps → Git-driven infra + apps
 * ✨ Lock file → Version consistency
