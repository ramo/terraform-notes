## AWS Provider
```
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
```
resource "aws_iam_user" "admin-user" {
    name = "lucy"
    tags = {
        Description = "Technical team leader"
    }
}

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
### Dynamo DB