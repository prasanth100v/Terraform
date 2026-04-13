# 🚀 Project: 
 * Deploy a Secure EC2 Instance in a Custom VPC Using Terraform
 * 🎯 Project Goal Creates :
     * 🌐 Custom VPC
     * 📡 Public Subnet
     * 🌍 Internet Gateway
     * 🛣️ Route Table
     * 🔐 Security Group
     * 🖥️ EC2 Instance with public IP

### 🛠️ How the Project Works
```
VPC
 └── Public Subnet
      └── Route Table → Internet Gateway
           └── EC2 Instance
                └── Security Group (SSH + HTTP)
```

## 📁 Project Structure
```hcl
terraform-aws-project/  
├── 📄 main.tf  
├── 📄 variables.tf  
├── 📄 outputs.tf  
├── 📄 terraform.tfvars  
```
---

## 🛠️ Step-by-Step Code

# 📄 main.tf
```hcl
provider "aws" {                        #👉 Connects Terraform to AWS in the selected region.
  region = var.aws_region
}
```

# 1️⃣ VPC
```hcl
resource "aws_vpc" "main_vpc" {
  cidr_block           = var.vpc_cidr           # 👉 Creates a custom VPC.
  enable_dns_support   = true                   # 👉 DNS support helps EC2 instances resolve names.
  enable_dns_hostnames = true

  tags = {
    Name = "main-vpc"
  }
}
```

# 2️⃣ Subnet
```hcl
resource "aws_subnet" "public_subnet" {
  vpc_id                  = aws_vpc.main_vpc.id              #👉 Creates a subnet inside the VPC.
  cidr_block              = var.public_subnet_cidr 
  availability_zone       = var.az
  map_public_ip_on_launch = true                             # ensures new instances get a public IP.

  tags = {
    Name = "public-subnet"
  }
}
```

# 3️⃣ Internet Gateway
```hcl
resource "aws_internet_gateway" "igw" {               # 👉 Allows internet access for resources in the VPC.
  vpc_id = aws_vpc.main_vpc.id

  tags = {
    Name = "main-igw"
  }
}
```

# 4️⃣ Route Table
```hcl
resource "aws_route_table" "public_rt" {
  vpc_id = aws_vpc.main_vpc.id

  route {
    cidr_block = "0.0.0.0/0"                            # 👉 Sends all internet traffic (0.0.0.0/0) to the Internet Gateway.
    gateway_id = aws_internet_gateway.igw.id
  }

  tags = {
    Name = "public-route-table"
  }
}

resource "aws_route_table_association" "public_assoc" {
  subnet_id      = aws_subnet.public_subnet.id
  route_table_id = aws_route_table.public_rt.id
}
```

# 5️⃣ Security Group
```hcl
resource "aws_security_group" "web_sg" {
  name   = "web-sg"
  vpc_id = aws_vpc.main_vpc.id

  ingress {
    from_port   = 22
    to_port     = 22                        # 🔐 Port 22 → SSH
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  ingress {
    from_port   = 80
    to_port     = 80                         # 🌐 Port 80 → HTTP
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]               # 🌍 Allow all outbound traffic
  }

  tags = {
    Name = "web-sg"
  }
}
```

# 6️⃣ EC2 Instance
```hcl
resource "aws_instance" "web_server" {
  ami                         = var.ami_id
  instance_type               = var.instance_type
  subnet_id                   = aws_subnet.public_subnet.id
  vpc_security_group_ids      = [aws_security_group.web_sg.id]
  associate_public_ip_address = true
  key_name                    = var.key_name

  tags = {
    Name = "Terraform-Web-Server"
  }
}
```

---

### 📄 variables.tf
 * Why Variables?
    * 👉 To make the project `reusable` and `flexible`.
  
```hcl
variable "aws_region" {
  default = "us-east-1"
}

variable "vpc_cidr" {
  default = "10.0.0.0/16"
}

variable "public_subnet_cidr" {
  default = "10.0.1.0/24"
}

variable "az" {
  default = "us-east-1a"
}

variable "ami_id" {
  description = "Amazon Linux 2 AMI"
  default     = "ami-0c55b159cbfafe1f0"
}

variable "instance_type" {
  default = "t2.micro"
}

variable "key_name" {
  description = "EC2 Key Pair name"
}
```

---

## 📄 outputs.tf
```hcl
output "ec2_public_ip" {
  value = aws_instance.web_server.public_ip       # 👉 Shows EC2 public IP after terraform apply.
}

output "vpc_id" {
  value = aws_vpc.main_vpc.id                      # 👉 Displays VPC ID.
}
```

## 📄 terraform.tfvars
```hcl
key_name = "your-key-pair-name"
```

---

## ▶️ How to Run
```yaml
terraform init  
terraform plan  
terraform apply  
```

### ✅ What You Will Get
  * After apply:
      * A new VPC 🌐
      * A public subnet 📡
      * Internet access 🌍
      * Security group with `SSH/HTTP` 🔐
      * EC2 instance with `public IP` 🖥️

---

# 🔗 What is a Resource Graph in Terraform?

 * Terraform creates a `resource graph` internally to understand dependencies between resources in our configuration..
 * This ensures resources are created, updated, or destroyed in the `correct order`.
 * I use terraform graph for `troubleshooting larger infrastructures` and visualize it with `Graphviz`.
 * 📌 For example:
     * If a subnet depends on a `VPC`, Terraform uses the graph to create the VPC first.
     * It also enables `parallel execution` where possible, improving `speed` and `accuracy` during deployment.
     * It helps Terraform: `Troubleshoot dependency issues`

## 🧭 Steps to Generate the Resource Graph

 ```yaml
   terraform init                             # 1️⃣ Initialize Terraform 

   terraform graph > graph.dot                # 2️⃣ Generate the resource graph in DOT format file  

   sudo apt install graphviz                          # Make sure Graphviz is installed          

   dot -Tpng graph.dot -o graph.png           # 3️⃣ Convert the DOT file into an image using Graphviz 
```
  👉 This creates a visual file `graph.png` .

---

## 🧩 How the Graph Looks (structure)

👉 Visually, it shows this:
```yaml
provider[aws]  
   ↓  
aws_vpc.main  
   ↓  
aws_subnet.public  
```
✨ It means:  
   - aws_vpc.main is created first  
   - aws_subnet.public depends on it

---

🏁 Final Summary

 * ✨ VPC → Network boundary
 * ✨ Subnet → Place EC2
 * ✨ IGW + Route Table → `Internet access`
 * ✨ SG → Security rules
 * ✨ EC2 → Compute
 * ✨ Graph → Dependency order
