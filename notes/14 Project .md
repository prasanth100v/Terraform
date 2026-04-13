# 🚀 Project: Deploy a Secure EC2 Instance in a Custom VPC Using Terraform

## 📁 Project Structure

terraform-aws-project/  
├── 📄 main.tf  
├── 📄 variables.tf  
├── 📄 outputs.tf  
├── 📄 terraform.tfvars  

---

## 🛠️ Step-by-Step Code

### 📄 main.tf

provider "aws" {
  region = var.aws_region
}

# 1️⃣ VPC
resource "aws_vpc" "main_vpc" {
  cidr_block           = var.vpc_cidr
  enable_dns_support   = true
  enable_dns_hostnames = true

  tags = {
    Name = "main-vpc"
  }
}

# 2️⃣ Subnet
resource "aws_subnet" "public_subnet" {
  vpc_id                  = aws_vpc.main_vpc.id
  cidr_block              = var.public_subnet_cidr
  availability_zone       = var.az
  map_public_ip_on_launch = true

  tags = {
    Name = "public-subnet"
  }
}

# 3️⃣ Internet Gateway
resource "aws_internet_gateway" "igw" {
  vpc_id = aws_vpc.main_vpc.id

  tags = {
    Name = "main-igw"
  }
}

# 4️⃣ Route Table
resource "aws_route_table" "public_rt" {
  vpc_id = aws_vpc.main_vpc.id

  route {
    cidr_block = "0.0.0.0/0"
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

# 5️⃣ Security Group
resource "aws_security_group" "web_sg" {
  name   = "web-sg"
  vpc_id = aws_vpc.main_vpc.id

  ingress {
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  ingress {
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }

  tags = {
    Name = "web-sg"
  }
}

# 6️⃣ EC2 Instance
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

---

### 📄 variables.tf

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

---

### 📄 outputs.tf

output "ec2_public_ip" {
  value = aws_instance.web_server.public_ip
}

output "vpc_id" {
  value = aws_vpc.main_vpc.id
}

---

### 📄 terraform.tfvars

key_name = "your-key-pair-name"

---

## ▶️ How to Run

terraform init  
terraform plan  
terraform apply  

---

# 🔗 What is a Resource Graph in Terraform?

Terraform creates a Resource Graph to understand the dependencies between resources in our configuration. This ensures that resources are created, updated, or destroyed in the correct order.

## 📌 For example:

If a subnet depends on a VPC, Terraform uses the graph to create the VPC first. It also enables parallel execution where possible, improving speed and accuracy during deployment.

---

## 🧭 Steps to Generate the Resource Graph

1️⃣ Initialize Terraform  
   terraform init  

2️⃣ Generate the resource graph in DOT format  
   terraform graph > graph.dot  

3️⃣ Convert the DOT file into an image using Graphviz  
   Make sure Graphviz is installed  
   sudo apt install graphviz  

   Then run:  
   dot -Tpng graph.dot -o graph.png  

   This creates a visual file graph.png.

---

## 🧩 How the Graph Looks (structure)

Visually, it shows this:

provider[aws]  
   ↓  
aws_vpc.main  
   ↓  
aws_subnet.public  

It means:  
- aws_vpc.main is created first  
- aws_subnet.public depends on it  

---

## 🎤 Say in Interview:

I use terraform graph to generate a dependency graph of all resources. For large configurations, it helps identify resource order, dependencies, and troubleshoot issues. I visualize the graph using Graphviz by converting the DOT file to PNG or PDF format.
