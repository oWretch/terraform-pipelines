# Azure DevOps Terraform Pipeline

This pipeline uses the [Terraform extension created by Microsoft DevLabs][ado-xtn] for running the Terraform commands.
It requires v0.1.14 or later.

The pipeline is configured to be used both for deployment and for pull request validation.
When a pull request is created, the pipeline will verify that the formatting meets `terraform fmt` best practices, and then validate the code before running a plan to ensure the Terraform code is as valid as can be verified.

## Template

The Azure pipeline uses a template to run the actual deployments ([`terraform-deployment.yml`][ado-tmpl]).
This template can be reused to take the same terraform code and deploy multiple environments by changing the variables passed to the deployment command.

### Variables

The template expects the following variables to be defined in the pipeline.
These can either be defined in the parent pipeline, or through a variable group.

- `tfstate.ResourceGroup` Name of the Resource Group where the storage account for holding the terraform state exists.
- `tfstate.StorageAccount` Name of the Storage Account where the Terraform state will be stored.
- `NotificationEmails` A string containing a JSON array of email addresses that should be notified when of the pending approval.

### Parameters

The following parameters need to be passed to the pipeline template:

- `serviceConnection` Name of the DevOps Service Connection which will be used to deploy the Terraform code.
- `tfStateConnection` Name of the DevOps Service Connection which will be used to access the Terraform state file.
- `tfStateContainer` Name of the container in the storage account where the state file will live. Must already exist.
- `tfStateKey` Key path of the state file within the storage container.

The following optional parameters can be passed to the pipeline template:

- `repository` Reference name of a Git repository defined in a [`repository` resource][ado-res-repo]. Defaults to `self`. Allows a different repository to be downloaded and deployed.
- `variables` A map of variables in `key: value` format of variables to pass to Terraform on the command line.

[ado-res-repo]: https://docs.microsoft.com/en-us/azure/devops/pipelines/yaml-schema/resources-repositories-repository?view=azure-pipelines
[ado-tmpl]: blob/master/azure-devops/terraform-deployment.yml
[ado-xtn]: https://marketplace.visualstudio.com/items?itemName=ms-devlabs.custom-terraform-tasks
