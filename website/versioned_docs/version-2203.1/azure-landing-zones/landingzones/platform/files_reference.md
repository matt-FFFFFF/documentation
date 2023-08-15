---
id: elsz-files-reference
sidebar_position: 3
---

# Core files settings reference

In this section, we will review the fundamental parameters to deploy customized stack and review the various parameters.

## ignite.yaml

The ```ignite.yaml``` contains all core parameters that will be reused across all levels, entries in this file will be used to generate configuration files and the readme files to execute all the commands in the various levels.
The root of this file is called ```caf_terraform:``` and contains the following sections:

### top level variables

| parameter                         | required | possible values                       | description                                                                                                                                                         |
|-----------------------------------|----------|---------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| customer_name                     | yes      | only lowercase with no spaces         | Is used to create the folder name to store the definition and configuration files.                                                                                  |
| caf_environment                   | yes      | any string                            | Name of the environment to use or to create. A ```caf_environment``` is used to discriminate multiple environments that could live on the same subscriptions.       |
| default_email_address             | yes      | email format                          | Individual or distribution list to send subscription health alerts.                                                                                                 |
| alz_mg_prefix                     | yes      | between 2 and 10 characters           | Azure landing zones management group prefix.                                                                                                                        |
| alz_mg_name                       | yes      | any string                            | Azure Management group name.                                                                                                                                        |
| azure_regions                     | yes      | map (key=region key, value= region)   | List of the Azure regions where services are authorized to be deployed. The region must be the small, lower-case with no space format (i.e: southeastasia)          |
| prefix                            | yes      | string                                | Use a prefix to put in front of all names generated.                                                                                                                |
| deployment_mode                   | yes      | 'platform' or 'asvm'                  | Inform the rover ignite if the deployments are for the platform or for an application landing zone.                                                                 |
| caf_landingzone_branch            | yes      | tag or branch name                    | Release number from the repo https://github.com/Azure/caf-terraform-landingzones/releases                                                                           |
| caf_regions                       | yes      | yaml list of objects                  | List of the Azure regions generated by the rover ignite from the azure_regions variable.                                                                            |
| resources_allowed_regions         | yes      | yaml list of string                   | List of the region's key used by Azure policies to defined the Azure regions where services can be deployed.                                                        |
| resource_groups_allowed_regions   | yes      | yaml list of string                   | List of the region's key used by Azure policies to defined the Azure regions where the resource groups can be deployed.                                             |
| default_region_key                | yes      | caf_regions key                       | Value of the region's key that will be used when a resource does not explicitly mention the region to use.                                                          |
| azuread_user_ea_account_owner     | yes      | user principal name                   | User Principal Name of the Azure AD principal deploying the platform landing zones.                                                                                 |
| ea_owner_object_id                | yes      | guid                                  | GUID of the Azure AD principal deploying the platform landing zones.                                                                                                |
| azuread_identity_mode             | yes      | 'service_principal'                   | Type of principal used to secure the levels in the CAF Terraform landing zones.                                                                                     |
| enable_azuread_groups             | yes      | boolean                               | Enable the creation of the Azure AD groups required to set the operation model.                                                                                     |
| enable_azuread_applications       | yes      | boolean                               | Grant the identity principal the Azure AD API privilege to manage Azure AD applications.                                                                            |
| enable_azure_subscription_vending_machine | yes | boolean                            | Enable the Azure Subscription Vending Machine to deploy Azure landing zone solutions accelerators.                                                                  |
| subscription_deployment_mode      | yes      | 'single_reuse'                        | 'single_resuse' deploys all platform and application landing zones into the same existing Azure subscription.                                                       |

```caf_regions``` blocks will contain a structure of keys with name and short names (slug) as in the following example:

```yaml
caf_regions:
  region1:
      # set the short form of the Azure region
    name: southeastasia  # Use the lower-case region's name, short version with no space
  region2:
    name: eastasia  # Use the lower-case region's name, short version with no space
```

| parameter | required | possible values       | description                                                |
|-----------|----------|-----------------------|------------------------------------------------------------|
| key       | yes      | any string            | region1 and region2 are the keys, they can be any string.  |
| name      | yes      | any Azure region name | name of the region as in the Azure available regions list. |

### naming_convention

The section is used to describe how names are being created in the platform, in development phases, we often use part of the name to be automatically generated so we can integrate and test faster, once you are reaching production, you will use name as passthrough in order to apply your own naming convention.

| parameter     | required | possible values         | description                                                                                                                                                          |
|---------------|----------|-------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| passthrough   | yes      | ```true```, ```false``` | When set to true, use the name as specified in the files after filtering. If set to false, will add generate names aligned to CAF guidance will prefixes if defined. |
| prefix        | yes      | string                  | Use a prefix to put in front of all names generated.                                                                                                                 |
| random_length | yes      | integer                 | The number or randomly generated characters at the end of the resource name, if passthrough is set to false.                                                         |
| inherit_tags  | yes      | ```true```, ```false``` | Define if resources will inherit the tags defined at the resource group level.                                                                                       |

### caf_launchpad

| parameter                         | required | possible values                       | description                                                                                                                                                         |
|-----------------------------------|----------|---------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| subscription_id                   | yes      | string                                | GUID of the subscription to use to deploy launchpad.                                                                                                                |
| subscription_name                 | yes      | string                                | Name of the subscription to use to deploy launchpad.                                                                                                                |
| tenant_id                         | yes      | string                                | Home tenant of the subscription to use to deploy launchpad.                                                                                                         |
| global_tags_propagated            | yes      | ```true```, ```false```               | If set to true, the tags defined in launchpad will be propagated to all resources in this environment.                                                              |
| tags                              | yes      | block of tags                         | A block of tags that will be added to the launchpad.                                                                                                                |

### billing_subscription_role_delegations

This section defines settings for the subscription billing role delegation from and EA or MCA account.

If you are not planning to generate subscriptions automatically, you can define it to false, you will still need to input the azuread_user settings.

| parameter                               | required | possible values         | description                                                                                                                                                                                                                                                                                         |
|-----------------------------------------|----------|-------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| enable                                  | yes      | ```true```, ```false``` | Enable the subscription billing account delegation functions.                                                                                                                                                                                                                                       |
| azuread_user_ea_account_owner           | yes      | string                  | UPN of the user doing the manual deployment of the platform. Must be populated even if you are not using automatic subscription generation.                                                                                                                                                         |
| azuread_user_ea_account_owner_object_id | yes      | string                  | GUID of the user doing the manual deployment of the platform. Must be populated even if you are not using automatic subscription generation. If that user is already loged-in to an azure cli session you can get the object_id by running: ```az ad signed-in-user show --query objectId -o tsv``` |
| billing_account_name                    | no       | number                  | Billing account name (typically 8 digits)                                                                                                                                                                                                                                                           |
| enrollment_account_name                 | no       | number                  | Enrollment account name (typically 6 digits)                                                                                                                                                                                                                                                        |

### configuration_folders

This section defines how and where the output files (Terraform configuration files) are going to be stored.

| parameter | required | possible values | description                           |
|-----------|----------|-----------------|---------------------------------------|
| platform  | yes      | block           | content of the block described below. |

The platform block takes the following settings:

| parameter                 | required | possible values                                | description                                                                                                                                                                                                                     |
|---------------------------|----------|------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| cleanup_destination       | yes      | ```true```, ```false```                        | true: forces the destination folder to be deleted and re-created before the files are created. false: create the target folder structure if it does not exist. On sub-sequent executions, the folder structure is reused as is. |

### management_groups

The settings in this section defines the core behavior of the environment including reference to the enterprise-scale architecture.
It is following the structure:

```yml
management_groups:
  region1:    // key of the default region used by enterprise-scale.
    es:       // alz_mg_prefix.
      {{enterprise_scale}}    // block as defined bellow.

```

| parameter        | required | possible values | description                           |
|------------------|----------|-----------------|---------------------------------------|
| enterprise_scale | yes      | block           | content of the block described below. |

The enterprise_scale block takes the following parameters:

| parameter                                 | required | possible values                                                    | description                                                                                                                                                                                                                       |
|-------------------------------------------|----------|--------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| management_group_name                     | yes      | strings                                                            | The base path to generate the files.                                                                                                                                                                                              |
| management_group_prefix                   | yes      | strings                                                            | The second part of the path where to generate the configuration files.                                                                                                                                                            |
| deploy_core_landing_zones                 | yes      | ```True```, ```False```                                            | true: forces the destination folder to be deleted and re-created before the files are created. false: create the target folder structure if it does not exist. On sub-sequent executions, the folder structure is reused as is.   |
| clean_up_destination_folder               | yes      | ```True```, ```False```                                            | The second part of the path where to generate the configuration files.                                                                                                                                                            |
| root_parent_id                            | no       | strings                                                            | The name of the root parent id where you want to deploy the enterprise scale management groups hierarchy.                                                                                                                         |
| update_lib_folder                         | yes      | ```True```, ```False```                                            | Update your definition lib folder                                                                                                                                                                                                 |
| version_to_deploy                         | yes      | 'v1.1.3', 'v1.1.1'                                                 | Set the enterprise-scale module version to deploy.                                                                                                                                                                                |

### subscriptions

This settings defines the Azure subscriptions that will be created or re-used to deploy the platform landing zones.

It is following the structure:

```yml
subscriptions:
  launchpad:    # key of launchpad.   Do not rename the key
    {{subscription}}
  identity:    # key of launchpad.   Do not rename the key
    {{subscription}}
  connectivity:    # key of launchpad.   Do not rename the key
    {{subscription}}
  management:    # key of launchpad.   Do not rename the key
    {{subscription}}

```

| parameter        | required | possible values | description                           |
|------------------|----------|-----------------|---------------------------------------|
| subscription     | yes      | block           | content of the block described below. |

The subscription block takes the following parameters:

| parameter                                 | required | possible values                  | description                                                                                                                                               |
|-------------------------------------------|----------|----------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------|
| name                                      | yes      | ```true```, ```false```          | Name of the subscription.                                                                                                                                 |
| create_alias                              | no       | ```true```, ```false```          | When re-using an existing subscription by setting a value to subscription_id, create an alias if the principal has API subscription creation permissions. |
| subscription_id                           | no       | guid                             | When set, reuse that subscription instead of creating a new one.                                                                                          |

### networking_topology

| parameter         | required | possible values   | description                                                                                                         |
|-------------------|----------|-------------------|---------------------------------------------------------------------------------------------------------------------|
| deployment_option | yes       | ```virtual_wan``` | Currently supported mode is only ```virtual_wan```, this might change in the future depending on customer requests. |

### platform_identity

| parameter                 | required | possible values                               | description                                                                                                                                                                              |
|---------------------------|----------|-----------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| tenant_name               | yes      | string                                        | Name of the primary domain name of the Azure Active Directory tenant (*.onmicrosoft.com or custom domain name)                                                                           |
| azuread_identity_mode     | yes      | ```logged_in_user```, ```service_principal``` | Currently supported mode for production is ```service_principal```, for lab and learning purposes, we support ```logged_in_user``` to run command from rover in interactive environment. |
| caf_platform_maintainers  | yes      | list of principal names                       | Ignored in logged in user mode, contains the list of UPN or groups to be added in the security group ```caf_platform_maintainers```                                                      |
| caf_platform_contributors | yes      | list of principal names                       | Only used in service_principal mode, contains the list of UPN or groups to be added in the security group ```caf_platform_contributors```                                                |
