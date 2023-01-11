## Terraform Provisioners
- Run tasks/scripts locally or remotely on resources.
- By default, the provisioners (local/remote) run after the resource creation. This is called Create time provisioners.
- Destroy time provisioners can be created by when argument. 
### remote-exec
- This provisioner used for running tasks/scripts in remote machines. 
- Network connectivity (Linux-SSH, Windows-WinRM) is required between terrform machine and the remote machine. 
- Authentication should be taken care.

```terraform
resource "aws_instance" "webserver" {
    ami = "ami-xyz"
    instance_type = "t2.micro"
    provisioner "remote-exec" {
        inline = [ "sudo apt update",
        "sudo apt install nginx -y",
        "sudo systemctl enable nginx",
        "sudo systemctl start nginx",
        ]
    }
    connection {
        type = "ssh"
        host = self.public_ip
        user = "ubuntu"
        private_key = file("/root/.ssh/web")
    }
    key_name = aws_key_par.web.id
    vpc_security_group_ids = [aws_security_group.ssh-access.id]
}

```

### local-exec