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

## Version Constraints

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

#### Specify range of supported versions

- \> 1.2.0, < 2.0.0, != 1.4.0 (between 1.2.0 and 2.0.0 and not 1.4.0)
- ~> 1.2.0 (Greatest 1.2.x starting from 1.2.1)
