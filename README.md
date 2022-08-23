# terraform-notes
## Introduction
Infrastructure as Code (IaC) is the managing and provisioning of infrastructure through code instead of through manual processes
![Types of IaC](iac.png?raw=true "IaC")
### IaC KeyPoints
- Immutable Infrastructure
- Idempotent
- Version Control

### Why Terraform? 
- Cloud agnostic
- Many providers
- HCL (HarshiCorp Connfiguration Language)
- Declarative
- Terraform State - Maintains the state
- Terraform Import - Import resources created by other IaC tools - Once done, the resource can be managed by Terraform

### Terraform workflow
- init - Download plugins (providers)
- plan - Validate the configuration and display the plan to be executed
- apply - Create the required infrasturcture resources
- destroy - Delete all the infrastucture resources
