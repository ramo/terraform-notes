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
