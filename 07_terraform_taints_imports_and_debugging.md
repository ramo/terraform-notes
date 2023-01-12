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