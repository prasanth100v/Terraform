# Terraform Provisioners
In Terraform, a provisioner is used to execute scripts or commands on a resource after it is created (or before it is destroyed). 
> Provisioners are mainly for tasks like installing and update software and Runs commands on the machine where Terraform is executed.

## 🔹 Example: EC2 with Remote Provisioner
What This Code Does (Big Picture)
```
This Terraform resource:
       1. Creates an EC2 instance
       2. Connects to it using SSH
       3. Installs and starts Nginx automatically
```
```
resource "aws_instance" "web" {
  ami           = "ami-0abcdef12345"
  instance_type = "t2.micro"
  key_name      = "mykey"

  connection {
    type        = "ssh"
    user        = "ubuntu"
    private_key = file("mykey.pem")
    host        = self.public_ip
  }

  provisioner "remote-exec" {
    inline = [
      "sudo apt update",
      "sudo apt install -y nginx",
      "sudo systemctl start nginx"
    ]
  }
}
```
🔹 What is the connection Block?
> The connection block tells Terraform how to connect to a remote resource (like an EC2 instance) so that: remote-exec can run commands..


## 🔹 local-exec
> Runs commands on the machine where Terraform is executed.
```
provisioner "local-exec" {
  command = "echo EC2 instance created!"
}
```

## 🔹 Destroy-Time Provisioners
> Executed before resource deletion.
```
provisioner "local-exec" {
  when    = destroy
  command = "echo Instance is being destroyed"
}
```
## 🔹 file
> Used to copy files or directories to a remote resource.
```
provisioner "file" {
  source      = "app.conf"
  destination = "/tmp/app.conf"
}
```

## 📌 Best Practices
✔ Use provisioners only for simple tasks    .✔ Keep scripts short

# 🔹 What is user_data?
user_data is a script that automatically executed when the instance boots for the first time.
## 🔹 Example: user_data in Terraform
```
resource "aws_instance" "web" {
  ami           = "ami-0abcdef12345"
  instance_type = "t2.micro"

  user_data = <<-EOF
    #!/bin/bash
    apt update
    apt install -y nginx
    systemctl start nginx
  EOF
}
```
> ➡️ After launch, Nginx is installed and running automatically

## 🔹 How user_data Works (Simple Flow)
```
1. Terraform creates the EC2 instance
2. OS boots
3. cloud-init reads user_data
4. Script runs automatically
5. Instance is ready
       No SSH is required ❌
```

## 🔥 Key Differences
| Feature              | `user_data`       | Provisioner             |
| -------------------- | ----------------- | ----------------------- |
| Execution time       | During first boot | After resource creation |
| Uses SSH             | ❌ No              | ✅ Yes                |
| Reliability          | ⭐⭐⭐⭐⭐          | ⭐⭐               |
| Production use       | ✅ Recommended     | ❌ Avoid              |


## ✅ Why user_data is Better
> No SSH needed, Faster, Cloud-native
## ❌ Problems with Provisioners
> SSH may fail (timing issues)

## ⚙️ Execution Flow Comparison
```
🟢 user_data Flow
      EC2 instance created
      OS boot
      cloud-init runs script
      Server is ready

🔴 Provisioner Flow
      EC2 instance created
      Terraform waits for SSH
      Commands run via SSH
      Apply may fail
```
## 🧠 Interview One-Liner (Very Important)
> “user_data is preferred over provisioners because it is cloud-native, reliable, and does not depend on SSH.”

