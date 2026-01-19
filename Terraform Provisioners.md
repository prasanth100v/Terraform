# Terraform Provisioners
In Terraform, a provisioner is used to execute scripts or commands on a resource after it is created (or before it is destroyed). 
> Provisioners are mainly for tasks like installing and update software and Runs commands on the machine where Terraform is executed.

## 🔹 Example: EC2 with Remote Provisioner
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
