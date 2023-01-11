## AWS Provider
```terraform
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 4.0"
    }
  }
}

# Configure the AWS Provider
provider "aws" {
  region = "us-east-1"
  access_key = "my-access-key"
  secret_key = "my-secret-key"
}

```
| Note                                                                                                                    |
| :---------------------------------------------------------------------------------------------------------------------- |
| Providng AWS credentials in the provider configuration is not a good option. Instead use aws credentials file or environment variables. AWS region can also be mentioned in the envionment variables.|


### IAM
```terraform
# Creating a user
resource "aws_iam_user" "admin-user" {
    name = "lucy"
    tags = {
        Description = "Technical team leader"
    }
}

# Creating an IAM policy
resource "aws_iam_policy" "adminUser" {
    name = "AdminUsers"
    policy = <<EOF
    {
        "Version": "2012-10-17",
        "Statement": [
            {
                "Effect": "Allow",
                "Action": "*",
                "Resource": "*"
            }
        ]
    }
    EOF
}

# Attaching IAM policy to the user
resource "aws_iam_user_policy_attachment" "lucy-admin-access" {
    user = aws_iam_user.admin-user.name
    policy_arn = aws_iam_policy.adminUser.arn
}

```
| Note                                                                                                                    |
| :---------------------------------------------------------------------------------------------------------------------- |
|Heredoc syntax is used in aws_iam_policy resource block to provide the policy JSON. `file()` function can also be used to provide json file as input here.|
```
# Heredoc Syntax
[COMMAND] <<DELIMITER
    Line1
    Line2
    Line3
DELIMITER
```

### S3
```terraform
# Create a bucket
resource "aws_s3_bucket" "marvel-cinematic-universe" {
  bucket = "mcu-202011121359"
}

# Create an Object in the bucket
resource "aws_s3_bucket_object" "upload" {
    bucket = "pixar-studios-2020"
    key = "woody.jpg"
    source = "/root/woody.jpg"
}

```
### Dynamo DB
```terraform
# Create a DynamoDB table
resource "aws_dynamodb_table" "project_sapphire_user_data" {
  name           = "userdata"
  billing_mode   = "PAY_PER_REQUEST"
  hash_key       = "UserId"

  attribute {
    name = "UserId"
    type = "N"
  }
}

# Create a DynamoDB table item
resource "aws_dynamodb_table_item" "upload" {
  table_name = aws_dynamodb_table.project_sapphire_inventory.name 
  hash_key = aws_dynamodb_table.project_sapphire_inventory.hash_key
  item = <<EOF
  {
    "AssetID": {"N": "1"},
    "AssetName": {"S": "printer"},
    "age": {"N": "5"},
    "Hardware": {"B": "true"}   
  } 
  EOF
}

```

### EC2
```terraform
# main.tf
resource "aws_instance" "webserver" {
  instance_type = "t2.micro"
  ami = "ami-xyz"
  tags = {
    Name = "webserver"
    Description = "An Nginx web server on Ubuntu"
  }
  user_data = <<-EOF
              #! /bin/bash
              sudo apt update
              sudo apt install nginx -y
              systemctl enable ngnix
              systemctl start nginx
              EOF
  key_name = aws_key_pair.web.id
  vpc_security_group_ids = [aws_security_group.ssh-access.id]
}

resource "aws_key_pair" "web" {
  public_key = file("/root/.ssh/web.pub")
}

resource "aws_security_group" "ssh-access" {
  name = "ssh-access"
  description = "Allow SSH access from internet"
  ingress {
    from_port = 22
    to_port = 22
    protocol = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }
}

output publicip {
  value = aws_instance.web_server.public_ip
}

# provider.tf
provider "aws" {
  region = "us-east-1"
}

```