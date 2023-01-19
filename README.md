<details>
<summary>Table of Contents</summary>

 - [Introduction](#introduction)
   * [Challanges with traditional IT Infrastructure](#challanges-with-traditional-it-infrastructure)
   * [IaC KeyPoints](#iac-keypoints)
   * [Why Terraform?](#why-terraform)
   * [Terraform workflow](#terraform-workflow)
   * [HCL Basics](#hcl-basics)
   * [Terraform Registry](#terraform-registry)
   * [Providers](#providers)
   * [`alias`: Multiple Provider Configurations](#alias-multiple-provider-configurations)
   * [Version Constraints](#version-constraints)
     + [Version Constraint Syntax](#version-constraint-syntax)
       - [Specify range of supported versions](#specify-range-of-supported-versions)
   * [Configuration Directory](#configuration-directory)
   * [Resources](#resources)
     + [Resource Targeting](#resource-targeting)
   * [Variables](#variables)
     + [Variable Types](#variable-types)
     + [Using variables](#using-variables)
     + [Resource attribute reference](#resource-attribute-reference)
     + [Resource Dependencies](#resource-dependencies)
   * [Output Variables](#output-variables)
   * [Local Values](#local-values)
   * [Terraform States](#terraform-states)
     + [Purpose of Terraform State](#purpose-of-terraform-state)
     + [Terraform State Considerations](#terraform-state-considerations)
 - [Terraform commands](#terraform-commands)
 - [Lifecycle Rules](#lifecycle-rules)
   * [Custom Condition Checks](#custom-condition-checks)
 - [DataSources](#datasources)
 - [Meta Arguments](#meta-arguments)
   * [Count](#count)
   * [for-each](#for-each)
 - [AWS Provider](#aws-provider)
   * [IAM](#iam)
   * [S3](#s3)
   * [Dynamo DB](#dynamo-db)
   * [EC2](#ec2)
   * [Problems in local state](#problems-in-local-state)
   * [State Locking](#state-locking)
   * [Remote state](#remote-state)
     + [S3 terraform remote state](#s3-terraform-remote-state)
   * [Terraform state commands](#terraform-state-commands)
 - [Terraform Provisioners](#terraform-provisioners)
   * [remote-exec](#remote-exec)
   * [local-exec](#local-exec)
   * [Considerations with Provisioners](#considerations-with-provisioners)
   * [Debugging](#debugging)
   * [Tainting Resources](#tainting-resources)
     + [taint](#taint)
     + [untaint](#untaint)
   * [Terraform import](#terraform-import)
 - [Terraform Modules](#terraform-modules)
   * [Why Modules?](#why-modules)
   * [What are Modules?](#what-are-modules)
   * [Calling a child module](#calling-a-child-module)
   * [Module resource address](#module-resource-address)
   * [Using modules from the Registry](#using-modules-from-the-registry)
 - [References to Values](#references-to-values)
   * [Resources](#resources-1)
   * [Input Variables](#input-variables)
   * [Local Values](#local-values-1)
   * [Child Module outputs](#child-module-outputs)
   * [Data Sources](#data-sources)
   * [Filesystem and Workspace Info](#filesystem-and-workspace-info)
   * [Block-Local Values](#block-local-values)
 - [Terraform buit-in functions](#terraform-buit-in-functions)
   * [Numeric Functions](#numeric-functions)
     + [max `max(number...)`](#max-maxnumber)
     + [min `min(number...)`](#min-minnumber)
     + [ceil `ceil(number)`](#ceil-ceilnumber)
     + [floor `floor(number)`](#floor-floornumber)
   * [String Functions](#string-functions)
     + [split `split(separator, string)`](#split-splitseparator-string)
     + [join (`Opposite to split`) `join(separator, list)`](#join-opposite-to-split-joinseparator-list)
     + [substr `substr(string, offset, length)`](#substr-substrstring-offset-length)
     + [lower `lower(string)`](#lower-lowerstring)
     + [upper `upper(string)`](#upper-upperstring)
     + [title `title(string)`](#title-titlestring)
   * [Collection Functions](#collection-functions)
     + [length `length(list|map|string)`](#length-lengthlistmapstring)
     + [index `index(list, value)`](#index-indexlist-value)
     + [element `element(list, index)`](#element-elementlist-index)
     + [contains `contains(list, value)`](#contains-containslist-value)
     + [keys `keys(map)`](#keys-keysmap)
     + [values `values(map)`](#values-valuesmap)
     + [lookup `lookup(map, key, default)`](#lookup-lookupmap-key-default)
   * [Filesystem Functions](#filesystem-functions)
     + [file `file(path)`](#file-filepath)
   * [Type Conversion Functions](#type-conversion-functions)
     + [toset `toset(list)`](#toset-tosetlist)
 - [Arithmetic and Logical Operators](#arithmetic-and-logical-operators)
   * [Order of operations](#order-of-operations)
 - [Expressions](#expressions)
   * [Conditional expressions](#conditional-expressions)
   * [For expressions](#for-expressions)
   * [Splat expressions](#splat-expressions)
   * [Dynamic Block](#dynamic-block)
 - [Terraform Settings](#terraform-settings)
 - [Terraform Workspaces](#terraform-workspaces)
   * [Workspace commands](#workspace-commands)
 - [Terraform Cloud](#terraform-cloud)
   * [What is Terraform Cloud?](#what-is-terraform-cloud-)
   * [What is Terraform Enterprise?](#what-is-terraform-enterprise-)
   * [Sentinel](#sentinel)
   * [Enterprise/Cloud Workspaces](#enterprise-cloud-workspaces)
   * [The UI and VCS driven Run workflow](#the-ui-and-vcs-driven-run-workflow)

 <small><i><a href='http://ecotrust-canada.github.io/markdown-toc/'>Table of contents generated with markdown-toc</a></i></small>
</details>

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
- HCL (HashiCorp Connfiguration Language)
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
> - hashicorp (Organizational Namespace)
> - local (Type)

`terraform init` need to be executed to download the plugins in to `.terraform` directory in the terraform project directory.

`terraform providers` command shows the provider requirements of the configuration in the current working directory.

`terraform version` command provides the version of the terraform as well as the version of the provider plugins downloaded in the configuration directory. 

### `alias`: Multiple Provider Configurations
- Per resource or per module basis, selection is made to use which provider.
- Primary reason is to support multiple region for a cloud platform. Targeting multiple docker hosts or consul hosts, etc.,
- Provider blocks with same provider name, but with different `alias` meta-argument is used.
```terraform
provider "aws" {
  region = "us-east-1"
}

provider "aws" {
  alias = "west"
  region = "us-west-2"
}
```
```terraform
terraform {
  required_providers {
    mycloud = {
      source  = "mycorp/mycloud"
      version = "~> 1.0"
      configuration_aliases = [ mycloud.alternate ]
    }
  }
}
```
- `<PROVIDER NAME>.<ALIAS>` is used to refer the required alternate provider configuration.
``` terraform
resource "aws_instance" "foo" {
  provider = aws.west
  # ...
}
```
``` terraform
module "aws_vpc" {
  source = "./aws_vpc"
  providers = {
    aws = aws.west
  }
}
```
| Note |
| :----- |
|Default configuration for a provider is the block without an `alias` meta-argument. Resources that don't set the `provider` meta-argument will use the default provider.|

| Warning |
|:----|
|If no default provider configuration is available, Terraform implicitly uses an empty block for the provider's default configuration. If the provider has any required configuration arguments, Terraform will raise an error.|


### Version Constraints
Version constraints are used when configuring:
- Modules
- Provider requirements
- The `required_version` setting in the terraform block

#### Version Constraint Syntax
`version = ">= 1.2.0, < 2.0.0"`

##### Specify range of supported versions
- `> 1.2.0, < 2.0.0, != 1.4.0` (between 1.2.0 and 2.0.0 and not 1.4.0)
- `~> 1.2.0` (Greatest 1.2.x starting from 1.2.1)

Default behavior of Terraform is to download the latest provider plugin. In practical scenarios this is not always desired and may introduce breaking changes. Version constraints are used to specify the version of providers.

```terraform
# main.tf
terraform {
  required_providers {
    local = {
      source = "hashicorp/local"
      version = "1.4.0"
    }
  }
}
```

### Configuration Directory

> We could have multiple resource definition configuration files like (cat.tf, dog.tf, etc).
> Usually main.tf will be used for defining main resource configurations

| Filename     | Purpose                                                 |
| ------------ | ------------------------------------------------------- |
| main.tf      | Main configuration file containing resource definitions |
| variables.tf | Contains variable declarations                          |
| outputs.tf   | Contains declarations of output of resources            |
| provider.tf  | Contains provider definitions                           |

| Note |
|:---|
|Multiple providers can be used in same resource configuration file. |

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

#### Resource Targeting
- Target specific resources in the infra using `-target`
- `-target` can be used in `plan` `apply` `destroy`
- Used for applying only part of a plan.
- Can be used for troubleshooting errors.
- You can pass multiple `-target` options to target several resources at once.

|Warning|
|:----|
|Targeting resources can introduce inconsistencies, so you should only use it in troubleshooting scenarios.|


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
- Explicit Dependency - `depends_on` meta-argument

### Output Variables

```terraform
# main.tf
output "<variable_name>" {
  value = "<Refer value from any resource>"
  <arguments: description, ...>
}
```
### Local Values
- Like local veriables in programming lanaguages.
- To assign a name to an expression, so the name can be used multiple times.
- The ability to easily change the value in a central place is the key advantage.
- `locals` block is used to define the local values.
- `local.<NAME>` is used to access a local value by name.

```terraform
locals {
  common_tags = {
    Service = local.service_name
    Owner   = local.owner
  }
}

resource "aws_instance" "example" {
  # ...
  tags = local.common_tags
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
- Terraform state `terraform.tfstate` should be stored in Remote State backends like `Amazon S3, Terraform Cloud, Hashicorp Consul, ...`
- No manual edits, always use terraform state commands.

| Note                                                                                                                             |
| :------------------------------------------------------------------------------------------------------------------------------- |
| Use `-refresh=false` in terraform commands to avoid terrafrom updating the terraform state with the remote objects's state |


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
| `create_before_destroy`(bool) | Creates the resource first and then destroy older    |
| `prevent_destroy`(bool)       | Prevents destroy of a resource                       |
| `ignore_changes`(list of attribute names)        | Ignore changes to resource attributes (specific/all) |
|`replace_triggered_by`(list of resource or attribute references)|Replace the resource when any of the referenced items change. Added in terraform 1.2|

### Custom Condition Checks
`precondition` and `postcondition` blocks with a `lifecycle` block is used specify any conditional checks for resources and data sources.

## DataSources

Similar to resources, but created by external tools and referred as data source in Terraform.

| Resource               | DataSource               |
| ---------------------- | ------------------------ |
| resource               | data                     |
| CRUD on Infrastructure | Read only Infrastructure |
| Managed Resource       | Data Resource            |

| Note |
|:---|
|`data` block doesn't support `lifecycle` meta argument like resources. But it is used for `precondition` and `postcondiditon`|

## Meta Arguments

`depends_on` and `lifecycle` are example of Meta arguments.

### count

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
  # each.key could be used as well.
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
    policy = <<-EOF
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
[COMMAND] <<-DELIMITER
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
  item = <<-EOF
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
- Terraform state data is written to remote data store like s3, HashiCorp Consul, Terraform Cloud, Google cloud storage, Azure blob storage, etc.,
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
- Destroy time provisioners can be created by `when=destroy` argument.
- `on_failure` meta-argument used to define failure behavior. `continue` and `fail` are accepted values. 
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

The syntax for specifying a module from Terraform registry is `<NAMESPACE>/<NAME>/<PROVIDER>`. Eg: `hashicorp/consul/aws`. 

For the private registry, the syntax is `<HOSTNAME>/<NAMESPACE>/<NAME>/<PROVIDER>`

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

## References to Values
### Resources
`<RESOURCE TYPE>.<NAME>`
- If used with `count`, refernce value is a `list` of objects.
- If used with `for_each`, reference value is a `map` of objects.

### Input Variables
`var.<NAME>`

### Local Values
`local.<NAME>`
- As long as there is no circular dependency, local values can refer other local values in the same `locals` block.

### Child Module outputs
`module.<MODULE NAME>`
- Output values of the module can be accessed as `module.<MODULE NAME>.<OUTPUT NAME>`
- If count is used, value is `list` of elements.
- If for_each is used, value is `map` of objects.

### Data Sources
`data.<DATA TYPE>.<NAME>`
- Same as Resources, only difference is `data.` prefix.

### Filesystem and Workspace Info
- `terraform.workspace` - Currently selected workspace
- `path.module` - File system parth of the module where the expression is placed. 
- `path.root` - Filesytem path of the root module.
- `path.cwd` - Filesystem path of the original working directory from where the terraform command ran.

### Block-Local Values
Local values available in the context of certain blocks. 
- `count.index` - available in resources which uses `count` meta-argument. 
- `each.key` / `each.value` - available in resources which uses `for_each` meta-argument.
- `self` - available in `provisioner` and `connection` blocks

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

## Expressions
### Conditional expressions
`condition ? true_val : false_val`
- The result type of values sholuld be same, otherwise Terraform will attempt to do auto-conversion.

### For expressions
- `for` expression creates complex type value by transforming another complex type value. 
- Supports `list` `set` `tuple` `map` `object` as input.
- Result depends on the brackets. `[] = tuple` and `{} = object`
- Element ordering is done by terraform using `lexical sorting`. In set of other types, arbitrary ordering is done, so the results should be marked explicitly say using `toset()`.
- Filtering the elements is supported by `if` clause.
- If result type is object `{}`, the key should be unique, otherwise Terraform will return an error. Grouping mode can be used to overcome this issue. Symbol `...` is used after value expression to activate grouping mode.
```
[for k, v in var.map : length(k) + length(v)]

# i = index
[for i, v in var.list : "${i} is ${v}"]

{for s in var.list : s => upper(s)}

[for s in var.list : upper(s) if s != ""]
```

``` terraform
locals {
  users_by_role = {
    for name, user in var.users : user.role => name...
  }
}
```

### Splat expressions
- A shorthand way of doing instead of `for` expression.
- Supports `list` `set` `tuple`
- `[for o in var.list : o.id]` can be written as `var.list[*].id`
- `[for o in var.list : o.interfaces[0].name]` can be written as `var.list[*].interfaces[0].name`
- Converts `null` value to `empty tuple`.
- Converts single value to single element `tuple`.
- Attribute only splat expression is legacy and uses `.*` instead of `[*]`.
- `[for o in var.list : o.interfaces][0].name` is written as `var.list.*.interfaces[0].name` using legacy attribute only splat expression.

```terraform
variable "website_setting" {
  type = object({
    index_document = string
    error_document = string
  })
  default = null
}

resource "aws_s3_bucket" "example" {
  # ...

  dynamic "website" {
    for_each = var.website_setting[*]
    content {
      index_document = website.value.index_document
      error_document = website.value.error_document
    }
  }
}
```
### Dynamic Block
- Some resource types uses `repeatable nested blocks` in their arguments. 
- `dynamic` blocks can be nested.
- Not possible to generate Meta arguments like `lifecyle` and `provisioner`
- Overuse will make the configuration hard to read and maintain. Wherever possible always write nested blocks directly. 

```terraform
variable "ingress_ports" {
  type = list
  default = [22, 8080]
}

resource "aws_security_group" "backend-sg" {
  name = "backend-sg"
  vpc_id = aws_vpc.backend-vpc.id
  
  dynamic "ingress" {
    iterator = port
    for_each = var.ingress_ports
    content {
      from_port = port.value
      to_port = port.value
      protocol = "tcp"
      cidr_blocks = ["0.0.0.0/0"]
    }
  }
}

output "to_ports" {
  value = aws_security_group.backend-sg.ingress[*].to_port
}
```
## Terraform Settings
- `terraform` configuration block is used to configure some behaviors of terraform itself like required minimum verison of Terraform.
- Only constant values should be used inside the block, no resources, variables, built-in-functions, etc., 
```terraform
# Terraform block syntax
terraform {
  # ...
}
```
- `cloud` block is used for configuring terraform cloud. CLI driven workflow.
- `backend` block is used for Terraform state backend.
- `required_version` argument is used for specifying the version of the Terraform.
- `required_providers` block is used to manage expected versions of the each provider plugin.
- `experiments` argument is used for opt in experiment features from Terraform.
- `provider_meta` nested block is used to pass module specific information to providers.





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

## Terraform Cloud
### What is Terraform Cloud?
- Teams can use consistent and reliable terraform environment together.
- Shared state and secret data.
- Private registry for sharing terraform modules.
- Policy control for governing the contents.
- Access control to approving the infrastructure changes.

### What is Terraform Enterprise?
- Self hosted distribution of **Terraform Cloud**
- Organizations with advanced security and compliance needs can purchase it.
- Private instance with advanced features.

### Sentinel
- Sentinel policies are availalbe in the **Terraform Cloud Team and Governance tier**.
- Uses `Sentinel policy language` to define the policies.
- Reusable libraries from terraform can be imported using `import`.
- `tfplan` `tfconfig` `tfstate` `tfrun` - 4 imports are provided by Terraform.

### Enterprise/Cloud Workspaces 
- Terraform Cloud manages infrastructure collections with workspaces instead of directories.
- State, Variable values, Credentials and Secrets are stored in Workspaces.
- State versions and Run history also available in Cloud workspaces.


### The UI and VCS driven Run workflow
- Terraform Cloud has three workflows.
  - UI/VCS driven run workflow (primary mode of operation)
  - API driven run workflow
  - CLI driven run workflow
- Every workspace is associated with a specific branch of VCS repo.
- Terraform Cloud registers Webhooks with VCS provider.
- **Speculative Plan** is performed when raising PR in the VCS for the specific branch.
- PRs originated from fork of the repo can't create Speculative plan. But, in Terraform Enterprise this can be overridden by admins.
- **At least one manual run is required before using VCS webhook to trigger auto runs.** 
- Auto apply is possible. 
- Health assessments (Drift detection and Continuous validation) are available in the Terraform Cloud Business tier.
