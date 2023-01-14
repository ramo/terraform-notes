## Introduction

Infrastructure as Code (IaC) is the managing and provisioning of infrastructure through code instead of through manual processes

### Challanges with traditional IT Infrastructure

- Slow Deployment
- Expensive
- Human errors
- Limited Automation
- Wasted Resources
- Inconsistency

![Types of IaC](images/iac.png?raw=true 'IaC')

### IaC KeyPoints

- Immutable Infrastructure
- Idempotent
- Version Control

### Why Terraform?

- Cloud agnostic
- Many providers
- HCL (HarshiCorp Connfiguration Language)
- Declarative (*Ansible uses procedural approach*)
- Terraform State - Maintains the state (*This allows terraform to be declarative. Any given point in time, the configuration files and state file would be enough to give a clear definition of what the infra should look like*)
- Terraform Import - Import resources created by other IaC tools - Once done, the resource can be managed by Terraform


### Terraform workflow

- init - Download plugins (providers)
- plan - Validate the configuration and display the plan to be executed
- apply - Create the required infrasturcture resources
- destroy - Delete all the infrastucture resources

### HCL Basics

```terraform
# resource block
# local_file resource type - local = provider, file = resource
resource "local_file" "test" {  # test - resource name
  filename = "/tmp/test.txt"  # arguments
  content = "Hello world"     # arguments
}
```

### Terraform Registry

> https://registry.terraform.io

### Providers

- Official
- Verified
- Community

> registry.terraform.io/hashicorp/local
>
> - registry.terraform.io (Hostname)
> - harshicorp (Organizational Namespace)
> - local (Type)

`terraform init` need to be executed to download the plugins in to `.terraform` directory in the terraform project directory.

`terraform providers` command shows the provider requirements of the configuration in the current working directory.

`terraform version` command provides the version of the terraform as well as the version of the provider plugins downloaded in the configuration directory. 

#### Version Constraints

Default behavior of Terraform is to download the latest provider plugin. In practical scenarios this is not always desired and may introduce breaking changes. Version constraints are used to specify the version of providers.

```terraform
# main.tf
terraform {
  required_providers {
    local = {
      source = "harshicorp/local"
      version = "1.4.0"
    }
  }
}
```

##### Specify range of supported versions

- \> 1.2.0, < 2.0.0, != 1.4.0 (between 1.2.0 and 2.0.0 and not 1.4.0)
- ~> 1.2.0 (Greatest 1.2.x starting from 1.2.1)


### Configuration Directory

> We could have multiple resource definition configuration files like (cat.tf, dog.tf, etc).
> Usually main.tf will be used for defining main resource configurations

| Filename     | Purpose                                                 |
| ------------ | ------------------------------------------------------- |
| main.tf      | Main configuration file containing resource definitions |
| variables.tf | Contains variable declarations                          |
| outputs.tf   | Contains declarations of output of resources            |
| provider.tf  | Contains provider definitions                           |

> Note: Multiple providers can be used in same resource configuration file.

### Resources
```terraform
resource "local_file" test {
  filename = "test.txt"
  content = "hello"
}
```
> Resource address - \<resource type>.\<resource name>
> Eg. local_file.test is a resource address

> Arguments - Input provided to resources for creation.
> Attributes - After resource is created, it exposes attributes of the resources which can be used as output.


### Variables

- variables are defined in `variables.tf` file.
- Default value can be provided. This is an optional argument.
- If default value is not provided in the variable configuration file, when performing `terraform apply` the values for the variables will be asked.

| Warning |
|:---|
|The name of a variable can be any valid identifier except the following: `source`, `version`, `providers`, `count`, `for_each`, `lifecycle`, `depends_on`, `locals`.|

```terraform
# variables.tf
variable "filename" {
  default = "/tmp/test.txt"
}

variable "content" {
}

```

```terraform
# main.tf
resource "local_file" "test" {
  filename = var.filename
  content = "Hello"
}
```

#### Variable Types

- string (`"Hello"`)
- number (`1`)
- bool (`true/false`)
- any (default)
- list (`[1, 2, 3]`)
- set (no duplicates, no ordering)
- map (key value pair)
- tuple (~list)
- object (~map)

> `any` = no type conversion

> `list(any)` all values should be same type (or) should be convertible to same type at least.

#### Using variables

Different ways of variable definitions and their order or precedence.

- Environment variables - `export TF_VAR_variable=value`
- Automatically loaded
  - terraform.tfvars
  - terraform.tfvars.json
  - \*.auto.tfvars (_alphabetical order_)
  - \*.auto.tfvars.json (_alphabetical order_)
- Command line flags
  - -var - `-var variable=value`
  - -var-file - `-var-file test.tfvars`

#### Resource attribute reference

Referencing an attribute of a resource - `resource_type.resource.attribute` - `local_file.myfile.content`

String interpolation is supported - `"${resource_type.resource.attribute}"`

#### Resource Dependencies

- Implicit Dependency - Refer a resource from another.
- Explicit Dependency - `depends_on` argument

### Output Variables

```terraform
# main.tf
output "<variable_name>" {
  value = "<Refer value from any resource>"
  <arguments: description, ...>
}
```

### Terraform States

Terraform maintains equivalent entries in **terraform.tfstate** file for the realworld infrastructure.

| Note                                                                                                                   |
| :--------------------------------------------------------------------------------------------------------------------- |
| After first `terraform apply`, the terraform state file is created and then gets updated for subsequent infra changes. |

#### Purpose of Terraform State

- Tracking Metadata (_Eg. dependencies between resources_)
- Performance (_When trying to refresh state of infra with high number of resources_)
- Collaboration (_Multiple people working with common state, usually a remote state_)

#### Terraform State Considerations

- Terraform state contains **sensitive** data
- Terraform state `terraform.tfstate` should be stored in Remote State backends like `Amazon S3, Terraform Cloud, Harshicorp Consul, ...`
- No manual edits, always use terraform state commands.

| Note                                                                                                                             |
| :------------------------------------------------------------------------------------------------------------------------------- |
| Use `-refresh=false` in terraform commands to avoid terrafrom updating the state in-memory cache with the remote objects's state |


## Terraform commands

| Command   | Description                                                                                                                                        |
| --------- | -------------------------------------------------------------------------------------------------------------------------------------------------- |
| init      | Initialize working directory. Download plugins.                                                                                                    |
| validate  | Checks whether the configuration is valid. Only syntax validation. Checks whether required providers are available in the configuration directory. |
| plan      | Show changes required by the current configuration                                                                                                 |
| apply     | Create or update infrastructure.                                                                                                                   |
| destroy   | Destroy previously created infrastructure                                                                                                          |
| fmt       | Reformats the configuration files in the terraform standard style                                                                                  |
| show      | Show the current state or a saved plan                                                                                                             |
| providers | Show the providers required for this configuration. `mirror` sub-command used to download the providers required in a target local directory       |
| output    | Show output values                                                                                                                                 |
| refresh   | Update the state to match remote systems                                                                                                           |
| graph     | Generates a Graphviz graph of the configuration. Use graphviz `dot` command to visualize the graph.                                                |

| Note                                                                                                             |
| :--------------------------------------------------------------------------------------------------------------- |
| Mutable infrastrcutre creates `Configuration Drift`. Terraform avoids this by createing Immutable infrastructure |

## Lifecycle Rules

```terraform
resource "local_file" "pet" {
  filename = "/tmp/test"
  lifecycle {
    create_before_destroy = true
  }
}
```

| Option                |                                                      |
| --------------------- | ---------------------------------------------------- |
| create_before_destroy | Creates the resource first and then destroy older    |
| prevent_destroy       | Prevents destroy of a resource                       |
| ignore_changes        | Ignore changes to resource attributes (specific/all) |

## DataSources

Similar to resources, but created by external tools and referred as data source in Terraform.

| Resource               | DataSource               |
| ---------------------- | ------------------------ |
| resource               | data                     |
| CRUD on Infrastructure | Read only Infrastructure |
| Managed Resource       | Data Resource            |

| Note |
|:---|
|`data` blocks don't support `lifecycle` meta argument|

## Meta Arguments

`depends_on` and `lifecycle` are example of Meta arguments.

### Count

Resources are created and managed as a list

```terraform
# main.tf
resource "local_file" "my-file" {
  filename = var.filename[count.index]
  count = length(var.filename)
}

```

```terraform
# variables.tf
variable "filename" {
  default = [
    "/tmp/1",
    "/tmp/2",
    "/tmp/3"
  ]
}

```

| Note                                                                                                                    |
| :---------------------------------------------------------------------------------------------------------------------- |
| Removing one entry from filename variable will force replacement of multiple files as all resources are managed as list |

### for-each

Resources are created and managed as map.

```terraform
# main.tf
resource "local_file" "my-file" {
  filename = each.value
  for_each = var.filename
}

```

```terraform
# variables.tf
variable "filename" {
  type = set(string)
  default = [
    "/tmp/1",
    "/tmp/2",
    "/tmp/3"
  ]
}
```

| Note                                                                                                                                                                                   |
| :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Variable type should be `set` or `map`, otherwise helper functions like `toset` is used in resource block when declaring for_each meta argument. `for_each = toset(var.variable_name)` |

| Note                                                                                          |
| :-------------------------------------------------------------------------------------------- |
| Unlike `Count` removing one entry from variable filename will destroy only that file resource |


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

# Elastic IP
resource "aws_eip" "eip" {
  instance = aws_instance.cerberus.id
  vpc      = true

  provisioner "local-exec" {
    command = "echo ${aws_eip.eip.public_dns} > /root/cerberus_public_dns.txt"
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

### Problems in local state
- Each team member need to ensure that they are working on the latest state file.
- Nobody else should be running Terraform at the same time.

| Note                                                                                                                   |
| :--------------------------------------------------------------------------------------------------------------------- |
| local terraform state file (terraform.tfstate) should not be stored in version control like GitHub as the state file might contain sensitive data |

### State Locking
- Multiple terrafrom operations can not run in parallel.
- When running one terraform operation, the state is locked by the terraform so other operations need to wait for the lock.

### Remote state
- Terraform state data is written to remote data store like s3, HarshiCorp Consul, Terraform Cloud, Google cloud storage, Azure blob storage, etc.,
- Remote backends like s3 supports **State Locking**.
- State files get automatically loaded and uploaded from the remote backend
- Most of the remote backends support encryption at rest and in transit, so more secure.

#### S3 terraform remote state
```terraform
# terraform.tf
# Define s3 remote backend. 
# Run terraform init to create the remote state in s3.
terraform {
  backend "s3" {
    key = "terraform.tfstate"
    region = "us-east-1"
    bucket = "remote-state"
  }
}

```
### Terraform state commands
- Used for advance state management
- Usage: `terraform state <subcommand> [options] [args]`
- Changes made only on state file and not on the actual infrastructure.
- Every state modification command write to a backup file.

| Subcommand | Usage |
|------------- | -----------|
|list|`terraform state list` - Only prints the address of the resources.
|mv|`terraform state mv <source> <destination>` - Moves resource within same state file (rename) or different state file|
|pull|`terraform state pull` - Prints the terraform state json|
|rm|`terraform state rm <resource address>` - Removes a resource from state. Removed resource will no longer be managed by Terraform. The resource is not destroyed from the infra. Resource need to be removed from the configuration file manually.|
|show|`terraform state show <resource address>` - Show the details of the resource if available.|

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


### Debugging
- `TF_LOG` envirnonment variable can be set to one of the log levels (in order of decreasing verbosity) TRACE, DEBUG, INFO, WARN or ERROR.
- `TF_LOG_PATH` environment variable can be set to log file path.

### Tainting Resources
- Marking a terraform resource as tainted means, the object is degraded or damaged. 
- These objects will be replaced in the next `terraform apply`
- Terraform automatically marks an object as "tainted" if an error occurs during a multi-step "create" action, because Terraform can't be sure that the object was left in a fully-functional state.
- Resources are makred as tainted in the case of failures in provisioners also.

#### taint
```
terraform taint [options] <address>
```
|Note|
|:---|
|`taint` command is deprecated. For Terraform v0.15.2 and later, recommendation is to use `-replace` option with `terraform apply`. Eg:```terraform apply -replace="address"```|
#### untaint
- If the terraform object tainted by the Terraform is working correctly, we can use `untaint` command to remove the taint on the resource.
```
terraform untaint [options] address
```

### Terraform import
- To make the terraform manage an infra resource created externally by other means.
- Only makes changes to terraform state and not the configuration or the infra.

|Note|
|:---|
|Difference between using terraform import and Data Sources is former allows the terraform to manage the resource onwards and later only allows terraform to get the readonly resource attributes|

```
terraform import <resource address> <unique id>
# Eg: terraform import aws_instance.webserver i-1234567890abcdef0 
```

|Warning|
|:---|
|Terraform expects that each remote object is bound to only one terraform resource, Going against this rule will cause unwanted behaviors in Terraform.|


## Terraform Modules
### Why Modules?
Based on the size of the infrastructure, terraform configuration files can become a lot. Though you can put all the code in single .tf file, that still makes the maintainability of the project harder. Below list highlights the effect of terraform project without modules.

- Complex configuration files
- Duplicate code
- Increased risk of manual errors
- Not much reusability

### What are Modules?
- A container for multiple resources and a way to package and reuse the resources.
- Each configuration directory with .tf files is called the root module.
- When calling other modules, they become child module to this root module.

### Calling a child module
Define a module block in the .tf file in the root module as shown below.
```terraform
module "servers" {
  source = "./app-cluster" # mandtory
  servers = 5   # input variables
}
```
| Arguments | |
| --------- | ---- |
|source| (mandatory) can be absolute/relative address of local or remote module|
|version| recommended for remote modules|
|for_each, count|meta-arguments|
|input variables| Other arguments which we can pass to module which it supports|

### Module resource address
> module.\<module name defined on root module>.\<resource_type>.\<resource_name>
> Eg: module.us_payroll.aws_dynamodb_table.payroll_db 

### Using modules from the Registry
Two types of modules are available on the Terraform Registry.
- Verified modules
- Community modules

```terraform
module "security-group_ssh" {
    source = "terraform-aws-modules/security-group/aws/modules/ssh"
    version = "3.16.0"
    # insert the 2 required variables here
    vpc_id = "vpc-7d8215"
    ingress_cidr_blocks = [ "10.10.0.0/16" ]
    name = "ssh-access"
}
```
Use terraform `init` or `get` (if provider plugins are already downloaded) to download the modules from the registry.

|Warning|
|:---|
|If version is not provided in the module block, it will always download the latest version of the module and it may bring undesired results.|

## Terraform buit-in functions
- Terraform supports only buit-in functions, no support for user-defined functions.
- Terraform console `terraform console` can be used to experiment these funtions.

### Numeric Functions
#### max `max(number...)`
```
> max(12, 54, 3)
54
> max([12, 54, 3]...)
54
```
#### min `min(number...)`
```
> min(12, 54, 3)
3
> min([12, 54, 3]...)
3
```
#### ceil `ceil(number)`
```
> ceil(5)
5
> ceil(5.1)
6
```
#### floor `floor(number)`
```
> floor(5)
5
> floor(5.1)
5
```

### String Functions
#### split `split(separator, string)`
```
> split(",", "foo,bar,baz")
["foo", "bar", "baz"]
```
#### join (`Opposite to split`) `join(separator, list)`
```
> join(",", ["foo", "bar", "baz"])
foo,bar,baz
```
#### substr `substr(string, offset, length)`
```
> substr("hello world", 1, 4)
ello
```
#### lower `lower(string)`
```
> lower("HELLO")
hello
```
#### upper `upper(string)`
```
> upper("hello")
HELLO
```
#### title `title(string)`
```
> title("how are you")
How Are You
```
### Collection Functions
#### length `length(list|map|string)`
- string - number of characters and not bytes.
- map|list - number of elements in that collection.
#### index `index(list, value)`
```
> index(["a", "b", "c"], "b")
1
```
#### element `element(list, index)`
- zero based index.
- index is non-negative.
- error if used with empty array.
- built-in index syntax `list[index]` should be used in most cases.
- best suitable for its **wrap around** behavior shown below.
```
# wrap around behavior
> element(["a", "b", "c"], 3)
a
```
#### contains `contains(list, value)`
#### keys `keys(map)` 
- returns list of keys in lexographical order.
#### values `values(map)`
- returns list of values in lexographical order of their keys.
#### lookup `lookup(map, key, default)`
- Omitting default value is deprecated from v0.7, because that would be equivalent to `map[key]`

### Filesystem Functions
#### file `file(path)`
- returns the content of the file as string.

### Type Conversion Functions
#### toset `toset(list)`
- Removes duplicates and returns set from the list.


## Arithmetic and Logical Operators
- Arithmetic `+` `-` `*` `/` `%`
- Equality `==` `!=`
- Comparison `<` `<=` `>` `>=`
- Logical `||` `&&` `!`

### Order of operations
> Use Parentheses () to override the default order of operations.
1. `!` `-` (*multiplied by -1*)
2. `*` `/` `%`
3. `+` `-` (*subtract*)
4. `>` `>=` `<` `<=`
5. `==` `!=`
6. `&&`
7. `||`

## Terraform Workspaces
- Using same configuration directory for multiple set of infrastructures.
- Terraform state corresponding to each set of infra is stored at `terraform.tfstate.d` directory with the corresponding workspace name.
- Supported remote state can also be used as backends for workspaces.
- Workspaces alone are not suitable for system decomposition, corresponding to the architectural boundaries of the system, separate configuration and backend should be used in subsystems.

### Workspace commands
- `terraform workspace list`
- `terraform workspace select <workspace>`
- `terraform workspace new <workspace>`
- `terraform workspace delete <workspace>` - Infra becomes dangling. 
- `terraform workspace show` - Shows the current workspace




