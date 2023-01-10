### Problems in local state
- Each team member need to ensure that they are working on the latest state file.
- Nobody else runs Terraform at the same time.

| Note                                                                                                                   |
| :--------------------------------------------------------------------------------------------------------------------- |
| local terraform state file (terraform.tfstate) should not be stored in version control like GitHub as the state file might contain sensitive data |

### State Locking
- Multiple terrafrom operations can not run in parallel.
- When running one terraform operation, the state is locked by the terraform so other operations need to wait for the lock.

### Remote state
- Terraform state data is written to remote data store like s3, HarshiCorp Consul, Terraform Cloud, Google cloud storage, Azure blob storage, etc.,
- Remote backends like s3 supports **State Locking**.
- State files get automatically loaded and uploaded from remote backend
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
 