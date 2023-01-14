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