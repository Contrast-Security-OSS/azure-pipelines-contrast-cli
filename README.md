# Introduction 
Repository of Azure Pipeline stages that use the Contrast CLI to submit files to [Contrast Scan](https://docs.contrastsecurity.com/en/scan.html), or to use [Contrast SCA](https://docs.contrastsecurity.com/en/sca.html).

# Getting Started
1. Clone this repository to your internal SCM
1. Update the Azure key vault name, Azure subscription ID, Contrast organization ID and Contrast URL in `contrast-cli-setup.yml`
1. Update this README so the below `resources.repositories` reference is correct for your custom version
1. Add Contrast user credentials to your key vault:
```bash
VAULT_NAME=YOUR_VAULT_NAME

az keyvault secret set --name contrastCLI-apiKey --vault-name $VAULT_NAME --value YOUR_CONTRAST_API_KEY
az keyvault secret set --name contrastCLI-auth --vault-name $VAULT_NAME --value YOUR_CONTRAST_AUTH
```
Replacing
- `YOUR_VAULT_NAME` with your Azure Key Vault name
- `YOUR_CONTRAST_API_KEY` and `YOUR_CONTRAST_AUTH` with credentials of the Contrast account to use ([Finding personal keys](https://docs.contrastsecurity.com/en/personal-keys.html))

Initial setup is now complete.

# Using in a repository

Reference the repository containing these common files, e.g.:

```yaml
resources:
  repositories:
    - repository: common
      type: git
      name: internal/common
```
Replacing `internal/common` with the name of your copy of the repository.

To submit your repository to Contrast Scan, add the following line to a stage in your pipeline:
```yaml
- template: contrast-cli-scan.yml@common
```

To submit your repository to Contrast SCA, add the following line to a stage in your pipeline:
```yaml
- template: contrast-cli-audit.yml@common
```

Both templates accept the following optional parameters:

1. `project_name` - defaults to `$(Build.Repository.Name)`; provide this if you want to use a different project name.
1. `fail_severity` - defaults to `critical`; you may pass `high`, `medium`, `low` or `note` to lower the severity threshold that will cause the Scan/Audit to fail.

Example with both templates:

```yaml
stages:
- template: contrast-cli-scan.yml@common
  # parameters:
  #   fail_severity: high
  #   project_name: my_overridden_project_name
- template: contrast-cli-audit.yml@common
  # parameters:
  #   fail_severity: high
  #   project_name: my_overridden_project_name
```