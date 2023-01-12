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
- Local provisioners are used to run tasks/scripts locally in terraform machine.
- Useful when we want to write some terraform outputs to local machine.

```terraform
resource "aws_instance" "webserver" {
    ami = "ami-xyz"
    instance_type = "t2.micro"
    # Get called after creation of the resource
    provisioner "local-exec" {
        on_failure = fail # (continue/fail)
        command = "echo Instance ${aws_instance.webserver.public_ip} Created! > /tmp/instance_state.txt"
    }

    # Get called after deletion of the resource
    provisioner "local-exec" {
        when = destroy
        command = "echo Instance ${aws_instance.webserver.public_ip} Destroyed! > /tmp/instance_state.txt"
    }
}
```
|Note|
|:----|
|Terraform provisioners should be a last resort and as much as possible options natively available to resources should be used. Eg., userdata attribute in aws_instance resource|

### Considerations with Provisioners
- No provisioner information in plan
- Network connectivity and authentication is required for remote exec

|Note|
|:----|
|Instead of using Provisioners or other means of runtime configuration of resources, custom AMIs could be used with desired requirements. **Packer** can be used to build custom AMIs in a declarative way.|
