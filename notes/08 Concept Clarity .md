## 🌐 How do you assign a public IP to an EC2 instance using Terraform?

Set `associate_public_ip_address = true` inside the network interface.

### 📄 Example:
```
resource "aws_instance" "web" {
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = "t2.micro"

  network_interface {
    device_index                = 0
    subnet_id                   = aws_subnet.public.id
    associate_public_ip_address = true                               # 👉 Public IP works only if Subnet is public & Route table has Internet Gateway (0.0.0.0/0)
    security_groups             = [aws_security_group.web_sg.id]
  }
}
```

---

## 🗄️ How do you create an RDS MySQL database using Terraform?

Use `aws_db_instance`.

### 📄 Example:
```
resource "aws_db_instance" "mydb" {
  allocated_storage    = 20
  engine               = "mysql"
  instance_class       = "db.t2.micro"
  name                 = "mydb"
  username             = "admin"
  password             = "admin123"             # ⚠️ Best Practice : 🔐 Don’t hardcode password, Use variables or secrets manager
  skip_final_snapshot  = true
}
```

---

## ☁️ Common AWS Services Managed with Terraform

- 🖥️ EC2 – virtual machines  
- 📦 S3 – object storage  
- 🌐 VPC/Subnets – networking  
- 🗄️ RDS – relational databases  
- 🔐 IAM – permissions and policies  
- 📊 CloudWatch – monitoring and logs  
- 🚢 ECS/EKS – container services  
- ⚖️ ALB/ELB – load balancers  

---

## 🚀 How do you launch multiple EC2 instances at once using Terraform?

Use `count`.

### 📄 Example:
```
resource "aws_instance" "web" {
  count         = 3                              # 👉 Creates: web[0], web[1], web[2]
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = "t2.micro"
}
```

---

## ⚖️ How do you provision a Load Balancer using Terraform?

Use `aws_lb`, `aws_lb_target_group`, and `aws_lb_listener`.

### 📄 Example (Application Load Balancer):
```
resource "aws_lb" "my_alb" {
  name               = "my-app-lb"
  internal           = false
  load_balancer_type = "application"
  subnets            = [aws_subnet.subnet1.id, aws_subnet.subnet2.id]
  security_groups    = [aws_security_group.lb_sg.id]
}
```

---

# ⚙️ user_data Block in Terraform
## 🚀 What is user_data?

 * ✨ The `user_data` block in Terraform is used to run a `shell script` automatically when an EC2 instance is launched.
 * It’s commonly used for tasks like `installing packages` 📦, `starting services ⚙️`, or `configuring the server` 🖥️.

### 📄 Example:
```
resource "aws_instance" "web" {
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = "t2.micro"
  subnet_id     = aws_subnet.public_1.id
  vpc_security_group_ids = [aws_security_group.web_sg.id]

  user_data = <<-EOF
    #!/bin/bash
    yum update -y
    yum install -y httpd                                        # 👉 On instance launch: Apache installs & Web server starts
    systemctl start httpd
    systemctl enable httpd
    echo "<h1>Hello from Terraform EC2</h1>" > /var/www/html/index.html
  EOF

  tags = {
    Name = "WebServer"
  }
}
```

---

## 🎯 Bonus: Tips for Answering Terraform Questions in Interviews

- 💬 Mention real-world usage (e.g., "In my EKS project, I used remote state in `AWS S3` with `native S3 locking`..")  
- 🧠 Explain why you chose `modules`, `backends`, or `certain strategies`.  
- 🔐 Always emphasize best practices like `security`, `automation`, `state management`, and `reusability`.
