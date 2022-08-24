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

#### Providers

- Official
- Verified
- Community

> registry.terraform.io/hashicorp/local
>
> - registry.terraform.io (Hostname)
> - harshicorp (Organizational Namespace)
> - local (Type)

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

### Variables

- variables are defined in `variables.tf` file.
- Default value can be provided. This is an optional argument.
- If default value is not provided in the variable configuration file, when performing `terraform apply` the values for the variables will be asked.

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
