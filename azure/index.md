# Azure 
Helpful Microsoft Azure cloud tips.

## Azure Cloud
The __cloud__ metaphor usually evokes a feeling of something that is experienced without understanding what it is or how it works, and Microsoft Azure [cloud computing](https://en.wikipedia.org/wiki/Cloud_computing) if no different.

To avoid the hype, let's approach the Azure Cloud as revolving around two major realms: **Resource** and **Security**. This is not how Microsoft typically documents Azure, but it's a more helpful perspective on how it is put together and how it works. In essence, the Azure cloud can be summarized by its two fundamental realms: Azure Resource Services and Azure Security Services.

## Azure Resource Services
What we call _Azure **Resource** Services_ here is essentially the comprehensive suite of [Products](https://azure.microsoft.com/en-us/products/) and tools to host applications, store data, and enable various services on Microsoft's Azure cloud. Institutions can access these wide array of **Azure Services** which include virtual machines (VM), databases, storage solutions, machine learning, artificial intelligence (AI), serverless computing, content delivery networks (CDN), and much more. The functions of these services are primarily managed via the Azure Resource Manager API at <https://management.azure.com>, and are detailed at <https://learn.microsoft.com/en-us/azure/azure-resource-manager>. Azure services are the core of the Azure ecosystem.

See below pages for specific tips on specific services:

- [Azure Data Factory (ADF)](adf/index.md): A cloud-based data integration service that orchestrates data movement and transformation between direct data compute resources.

## Azure Security Services
What we call _Azure **Security** Services_ here is essentially what Microsoft calls its _[Microsoft Identity Platform](https://learn.microsoft.com/en-us/entra/identity-platform/)_, and it plays a fundamental role in the Azure ecosystem, that permeates all of Azure. Like Azure Resources, it is a complementary suite of services that provides several essential cloud identity components, allowing institutions to control and manage access to services and applications that they host on Azure.

- [Microsoft Entra ID](https://learn.microsoft.com/en-us/entra/fundamentals/whatis) (formerly known as Azure Active Directory): This is the key element that serves as the primary **identity provider** for Azure, with tools and services to control and protect access to azure services and applications. It plays a vital role within the overall Azure identity and access management ([IAM](https://learn.microsoft.com/en-us/entra/fundamentals/introduction-identity-access-management)) framework for any institution. At a very high level, IAM can be summarized as the core framework that enables an organization to facilitate **1)** the right individuals, **2)** to access the right resources, **3)** at the right time, and **4)** for the right reasons. The Microsoft Entra ID functions are managed via the [MS Graph](https://learn.microsoft.com/en-us/graph/overview) API, typically via the <https://graph.microsoft.com> endpoint.
- Other important Azure security services are [Azure Policy](https://learn.microsoft.com/en-us/azure/governance/policy/overview) and [Microsoft Entra Privileged Identity Management (PIM)](https://learn.microsoft.com/en-us/entra/id-governance/privileged-identity-management/pim-configure). Moreover, the [Azure Security](https://learn.microsoft.com/en-us/azure/security/fundamentals/overview) page lists other essential elements within Azure security services.

## Azure Management Hierarchy
To manage access into Azure resource or security services you must first understand that these are indeed two (2) separate _realms_, but they are very closely _intertwined_. The Resource Realm is where Azure service objects live, and the Security Realm is where, well, security objects live. Luckily, these realms share the same hierarchy, which starts at the top, within an organization's Azure **tenant**.

- Tenant: This is the top of the hierarchy. It is here where all **security** objects such as Users, Groups, and Roles reside (for a list of all object types see _Services and features_ in the [Overview of Microsoft Graph](https://learn.microsoft.com/en-us/graph/overview)).
- Tenant Root Group: A special top-level object, essentially the same as the Tenant itself, but where all **resource** objects live. It is of resource type _Management Group_ (MG). This root MG can have other sub MGs defined underneath it, as well as individual subscriptions.
- Management Group (MG): This is where Azure subscriptions live. It is an object that allows an organization to logically arrange its resources.
- Subscription: This is where resource groups live. It is an additional _bucket_ that an organization can use to logically organize its resources.
- Resource Group (RG): This is where individual resources live. It is yet another _sub bucket_ to logically organized resources with the hierarchy.
- Individual Resource: This is an actual service object, such a VM, or a storage account. For more info on resource types see [What is Azure Resource Manager](https://learn.microsoft.com/en-us/azure/azure-resource-manager/management/overview) and [Azure products](https://azure.microsoft.com/en-us/products/).

> **Note**<br>
[Stack Overflow has this really good entry](https://stackoverflow.com/questions/47307368/what-is-the-difference-between-an-azure-tenant-and-azure-subscription), highlighting the relation of these objects within an Azure tenant.

**Important:**<br>
1. All security objects are available cross the entire individual tenant's hierarchy.
2. Organizations typically have multiple tenants, such as a **Development** tenant for a testing environment, and a **Production** one for their live environment, and so on.
3. Security objects are typically __not__ shared cross tenants, except for special objects that do support a more advanced and rare multi-tenant configuration.
4. Resource objects that are defined or deployed to only one area of the hierachy, are only available within that area. For instance, if a resource Role Based Access Control (RBAC) role _definition_ is deployed only to subscription A, it cannot be _assigned/consumed_ within subscription B.
5. It is almost always better to define resource objects, such as resource RBAC role definitions, at the _very top of the hierarchy_, the Tenant Root Group. That way they are always available to be used anywhere within the entire tenant.
6. Microsoft Azure makes available native, **Built-In** Entra ID roles (security realm) as well as resource RBAC roles (resource realm) for each service product offering. Microsoft makes these roles available globally, to every organization with a tenant in Azure cloud.
7. Many organization choose to create their own **Custom** roles based on the Built-In ones, because it affords them better version control of these roles. Microsoft is known for changing Built-In role behavior from time to time, and having your own _pinned_version of a role offers more control.

## Azure Access Methods
To do work in azure, whether to create resource or security objects, or to manage them, you have to access Azure in one of the following ways:

| Method | Secret | Typical Use |
| --- | --- | --- |
| Azure CLI | Yes, secured via browser popup | Interactive command line |
| OIDC | No | CI/CD automation |
| SP | Yes | Services and Application automated access |
| MI | No | Services and Application automated access |

Typical use column only describes the most common reason for using the specific method, but actual usage can vary depending on context.

## Azure Virtual Machines
- Azure allows different types of VMs, see [Azure Virtual Machines](https://learn.microsoft.com/en-us/azure/virtual-machines/overview) for more info.
- The [Azure VM Comparison page](https://azureprice.net/?sortField=linuxPrice&sortOrder=true) provides a quick and dirty view of all current VM instances types that Azure offers, and includes latest princing.

- Get instance metadata: 

```
curl -sH "Metadata:true" --noproxy "*"  http://169.254.169.254/metadata/instance?api-version=2021-02-01 | jq

Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance?api-version=2021-02-01" | ConvertTo-Json -Depth 64
```
See also <https://docs.microsoft.com/en-us/azure/virtual-machines/windows/instance-metadata-service?tabs=windows>

## Install Azure CLI
The [Azure CLI tool](https://learn.microsoft.com/en-us/cli/azure/what-is-azure-cli) (`az`) is a cross-platform utility that allows you to connect to Azure and execute administrative commands on Azure resources. Follow below table to install and use in the respective OS or environment:

| OS | Command |
| --- | --- |
| [macOS](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli-macos?view=azure-cli-latest) | `brew update && brew install azure-cli` |
| [Ubuntu Linux](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli-apt?view=azure-cli-latest) | `curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash` |
| [Windows](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli-windows?view=azure-cli-latest&tabs=azure-cli) | <== Click on link |
| [Docker](https://docs.microsoft.com/en-us/cli/azure/run-azure-cli-docker?view=azure-cli-latest)  | docker run -it mcr.microsoft.com/azure-cli |

## Create VM
Create a sample Azure VM using the `az` utility.

- Create VM, resource, group and VNET all at once
```
az vm create --name myvm01 --resource-group myrs --admin-username admin --admin-password secure --image "OpenLogic:CentoOS:7.6:7.6.20190402" --vnet name myvnet --subnet mysubnet --size Standard_B1ms
```

- Create VM into existing subnet (need subnet ID)
```
az vm create --name myvm01 --resource-group myrs --admin-username admin --admin-password secure --image "OpenLogic:CentoOS:7.6:7.6.20190402" --subnet "/subscriptions/long-ass-name" --size Standard_B1ms
```

## SSH to AKS Cluster Node
This is not generally recommended, but there are time, while experimenting in a lower environment when you may need to SSH into an ASK cluster node:

- Set account: `az account set -s sub1000`

- Get AKS node resource group
```
az aks show -n myakscluster --resource-group myresgroup --query nodeResourceGroup -o tsv
MC_myakscluster1_resgroup_eastus2
```

- List nodes in that RS
```
az vmss list --resource-group MC_myakscluster1_resgroup_eastus2 --query [0].name -o tsv
aks-agentx-18680543-vmss
```

 - Locate your SSH public key
```
cat /home/user1/.ssh/id_ed25519.pub
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIGdF94RyNcx7hrLqFrcymWrYhOUivuuGn3frnT1/g7g+ user1@example.com
```

- Add you SSH key to the set
```
az vmss extension set --resource-group MC_myakscluster1_resgroup_eastus2 --vmss-name aks-agentx-18680543-vmss --name VMAccessForLinux --publisher Microsoft.OSTCExtensions --version 1.4 --protected-settings "{\"username\":\"azureuser\", \"ssh_key\":\"ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIGdF94RyNcx7hrLqFrcymWrYhOUivuuGn3frnT1/g7g+ user1@example.com\"}"
```

- Sync key across the set
`az vmss update-instances --instance-ids "*" --resource-group MC_myakscluster1_resgroup_eastus2 --name aks-agentx-18680543-vmss`

- View Connected Devices in subnet to locate the IP address of the specific AKS node, usually the 1st is `.4`, and SSH to it:
`ssh azureuser@10.197.165.4`

- To remove this SSH access, after you are done troubleshooting:
```
az login
az account set -s MYACCOUNT
NodeResGroup=$(az aks show -n CLUSTER-NAME -g CLUSTER-RES-GRP --query nodeResourceGroup -o tsv)
VmssList=$(az vmss list -g $NodeResGroup --query [0].name -otsv)
az vmss extension list -g $NodeResGroup --vmss-name $VmssList -o tsv
az vmss extension delete -g $NodeResGroup --vmss-name $VmssList -n VMAccessForLinux
az vmss update-instances --instance-ids "*" -g $NodeResGroup -n $VmssList
``` 
Try SSH'ing to node to confirm there's no longer access.

## PowerShell

### Azure From PowerShell
- From Windows, PowerShell is of course built into Windows, simply open the PS Command Prompt then do: 

```
Install-Module Az
Import-Module Az
Connect-AzAccount blah blah blah
```

- From Linux, do : 

```
# Find the way to install `pwsh` for your version of Linux
pwsh
# Then run same above commands
```

- From macOS, do : 

```
brew install powershell
pwsh
# Then same above commands
```

- From Docker container, do :

```
docker run -it mcr.microsoft.com/azure-powershell pwsh
# Then run same above commands
```

### Common Commands
```
# List PS version
$PSversionTable

# Logon to Azure (see above section too)
Login-AzAccount

# Get RBAC role definition(s)
$r = Get-AzRoleDefinition "Monitoring Contributor"
$r
$r | ? {$_.IsCustom -eq $true} | FT Name, IsCustom

# Get all role definitions in tenant and store as UTF8 text (makes easier Unix grep parsing, etc)
Get-AzRoleDefinition | ? {$_.IsCustom -eq $true} | ConvertTo-JSON | jq | Out-File -Encoding utf8 custom-roles.json

# Get/Select Specific Azure Subscription
Get-AzSubscription
Select-AzSubscription
Set-AzSubscription

# Update existing role
Get-AzRoleDefinition -Name "Existing Role" | ConvertTo-Json | Out-File existing-role.json
# Modify JSON file accordingly, then
Set-AzRoleDefinition -InputFile "existing-role.json"

# Create new custom role, based on existing
Get-AzRoleDefinition -Name "Existing Role" | ConvertTo-Json | Out-File azure-support-basic.json
# Modify JSON file accordingly, then
New-AzRoleDefinition -InputFile ./azure-support-basic.json

# Install YAML module
Install-Module -Name powershell-yaml -Force -Repository PSGallery -Scope CurrentUser
# Grab from file:
$r = Get-Content -Raw -Path file-path.json | ConvertFrom-Json
# Or query Azure
$r = Get-AzRoleDefinition "Monitoring Contributor"
$j = $r | ConvertTo-Json
$y = $r | ConvertTo-YAML

# Get existing role assignment (to then create one)
$a = Get-AzRoleAssignment [-RoleDefinitionName <String>] -Scope <String> | ConvertTo-Json 

# Create role assignment
$id = (Get-AzAdGroup -DisplayName MYGROUP).Id
New-AzRoleAssignment -RoleDefinition "azure-support-basic" -ObjectId $id

# Remove role
Get-AzureRmRoleDefinition -Name "Virtual Machine Operator" | Remove-AzureRmRoleDefinition

# Remove role assignment
$id = (Get-AzAdGroup -DisplayName MYGROUP).Id
Remove-AzRoleAssignment -ObjectId $id -RoleDefinitionName "azure-support-basic" 
```

## PowerShell Service Principal Login
```
# Use the application ID as the username, and the secret as password
$credentials = Get-Credential
Connect-AzAccount -ServicePrincipal -Credential $credentials -Tenant <tenant ID>
``` 

## Create Service Principal
To quickly create an Azure service principal from the CLI, and grant it RBAC role `Contributor` at a specific subscription scope, run the following: 

```
az ad sp create-for-rbac -n sp-cli-mgmt --role="Contributor" --scopes="/subscriptions/<SubscriptionId>"
{
  "appId": "0c96e907-3875-4989-9e94-ed2f163629cf",
  "displayName": "sp-cli-mgmt",
  "password": "EF-a0RixjI2FlrZbYGBbPR.qBqr1G70AEt",
  "tenant": "e6cbf564-8faa-455b-b339-7cee0c829cf2"
}
```
If you omit the `-n <Name>` option, Azure will automatically pick a name like `azure-cli-2022-04-03-23-15-00` for you.

## WARNING: Use of _Microsoft Graph PowerShell_ SP
Using the built-in **Microsoft Graph PowerShell** Service Principal to logon to Azure APIs is highly **discouraged**.
- See <https://practical365.com/connect-microsoft-graph-powershell-sdk/>
- The problem is that this SP can collect too many aggregated API permissions over time
- This brings forth the distinct possibility the SP will become over-permissioned and therefore a security risk
- Obviously, this only means the specific use of the _Microsoft Graph PowerShell_ SP for authentication, not the PowerShell scripts themselves
- ALTERNATIVES:
  - Set up a dedicated automated pipeline job for the specific ad hoc task at hand (**PREFERRED**)
    - Use a specifically registered App/SP with permanent but limited privileges for the task
  - If you must write a semi-manual CLI script, then:
    - Again, use a specifically registered App/SP with permanent but limited privileges for the task
    - Do an MSAL interactive browser popup login
      - Use the built "_Azure PowerShell_" SP with ClientID _1950a258-227b-4e31-a9cf-717495945fc2_
      - See <https://stackoverflow.com/questions/30454771/how-does-azure-powershell-work-with-username-password-based-auth>
    - Use the MSAL libraries to generate an access token for the specific SPI:
      - MSAL supports many different languages such a Python, Go, and PowerShell
        - See <https://learn.microsoft.com/en-us/azure/active-directory/develop/msal-overview> 
      - From PowerShell you can then login with: `Connect-MgGraph -AccessToken $generated_token`
      - From Python scripts and Go utilities there are many different ways to logon and access APIs

## Azure Questions
- The functionalities of [Azure Resource Manager] (https://management.azure.com) and [MS Graph] (https://graph.microsoft.com) are very closely intertwined. Will these planes ever be bridged, integrated, or unified into a single platform and API?

- Will ARM ever get its own internal infrastructure as code (Iac) framework? Is that what Bicep was meant for? Why not use Bicep (or whatever declarative/configuration DSL) to drive the Azure Portal Web UI? That way everything one does via the Portal UI is natively and immediately kept as an IaC footprint?


## OIDC Github Action for Azure
OIDC allows workflows to authenticate and interact with Azure using short-lived tokens. This eliminates the need for long-lived personal access tokens (PAT) or service principaa with a secrets, providing a more secure and manageable approach to accessing cloud resources directly from GitHub Actions.

### How It Works
1. Configuration: You configure your Azure AD App Registration to trust an external identity provider by setting up a federation with that IdP. This involves specifying details about the IdP, such as the issuer URL, and possibly uploading metadata documents for SAML-based federations.

2. Authentication Flow: A user or service attempts to access an application protected by Azure AD and is redirected to sign in. Instead of presenting Azure AD credentials, the user or service presents credentials from the federated IdP. The federated IdP authenticates the user or service and issues a token. This token is presented to Azure AD, which validates it based on the trust configuration. Upon successful validation, Azure AD issues its own token to the user or service, granting access to the application.

### Setting Up
1. Enable OIDC in Azure
    - You still need an App Registration and Service Principal
    - Assign it the required Azure RBAC and Entra roles, at the required scope, to be able to do what you want it to do
    - But instead of configuring a **secret**, you configure a "federated" credential
    - [Configure a federated identity credential on an app](https://learn.microsoft.com/en-us/entra/workload-id/workload-identity-federation-create-trust?pivots=identity-wif-apps-methods-azp#github-actions)
    - click "Certificates & secrets" -> "Federated credentials", and "Add credential" and select "Github Actions deploying Azure resources"
    - Enter the Github Organization
    - The Github Repository name
    - The Entity type = "Branch"
    - And Based on selection = "main", or whatever that may be

2. Set below variables as secrets in your GitHub repository:
    - AZURE_AD_APPLICATION_CLIENT_ID: The client ID of the Azure AD application
    - AZURE_TENANT_ID: Your Azure tenant ID
    - AZURE_SUBSCRIPTION_ID: Your Azure subscription ID (may not be needed)

3. Setup a workflow that authenticates to Azure using OIDC, does something useful
    - Can be used with [Terraform](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/guides/service_principal_oidc)
    - Requires azurerm provider version 3.7.0 or higher
    - Azure CLI is set up with the token automatically, of course
    - Terraform picks up the token automatically also
    - Anything running within the Github Action job can use Azure CLI to get respective API tokens, as shown below
    - Example OIDC Github Action job using Azure CLI:
    ```yaml 
      jobs:
        task_leveraging_oidc_login:
          runs-on: ubuntu-latest
          permissions:
            id-token: write  # This is required for requesting the JWT
            contents: read   # This is required for actions/checkout
          steps:
            - name: checkout_github_action_code
              uses: actions/checkout@v4
              with:
                ref: main
            - name: azure_oidc_login
              uses: azure/login@v1
              with:
                client-id: ${{ secrets.ARM_CLIENT_ID }}
                tenant-id: ${{ secrets.ARM_TENANT_ID }}
                allow-no-subscriptions: true
            - name: capture_azure_tokens
              run: |
                # https://learn.microsoft.com/en-us/cli/azure/account?view=azure-cli-latest#az-account-get-access-token
                # [--resource-type {aad-graph, arm, batch, data-lake, media, ms-graph, oss-rdbms}]
                export MG_TOKEN="$(az account get-access-token --resource-type ms-graph --query accessToken -o tsv)"
                export AZ_TOKEN="$(az account get-access-token --resource-type arm --query accessToken -o tsv)"
                echo "MG_TOKEN=$MG_TOKEN" >> $GITHUB_ENV  # To use in another step 
                echo "AZ_TOKEN=$AZ_TOKEN" >> $GITHUB_ENV
                curl -sH "Content-Type: application/json" -H "Authorization: Bearer ${AZ_TOKEN}" -X GET "https://management.azure.com/subscriptions?api-version=2022-12-01" | jq
            - name: some_other_step
              run: |
                curl -sH "Content-Type: application/json" -H "Authorization: Bearer ${{ env.MG_TOKEN }}" -X GET "https://graph.microsoft.com/v1.0/users" | jq
    ```
    - Using PowerShell above 2 sample steps would become:
    ```yaml
            - name: capture_azure_tokens
              shell: pwsh
              run: |
                # PowerShell equivalent of az account get-access-token command
                $env:MG_TOKEN = (az account get-access-token --resource-type ms-graph --query accessToken -o tsv)
                $env:AZ_TOKEN = (az account get-access-token --resource-type arm --query accessToken -o tsv)
                echo "MG_TOKEN=$env:MG_TOKEN" | Out-File -Append -FilePath $Env:GITHUB_ENV  # To use in another step 
                echo "AZ_TOKEN=$env:AZ_TOKEN" | Out-File -Append -FilePath $Env:GITHUB_ENV
                $headers = @{
                  "Content-Type" = "application/json"
                  "Authorization" = "Bearer $($env:AZ_TOKEN)"
                }
                Invoke-RestMethod -Uri "https://management.azure.com/subscriptions?api-version=2022-12-01" -Method Get -Headers $headers | ConvertTo-Json
            - name: some_other_step
              shell: pwsh
              run: |
                $headers = @{
                  "Content-Type" = "application/json"
                  "Authorization" = "Bearer $env:MG_TOKEN"  # Assuming you want to use the Microsoft Graph token here
                }
                Invoke-RestMethod -Uri "https://graph.microsoft.com/v1.0/users" -Method Get -Headers $headers | ConvertTo-Json
    ```

### References
- [What is Github Action for Azure](https://learn.microsoft.com/en-us/azure/developer/github/github-actions) 
- [Configuring OpenID Connect in Azure](https://docs.github.com/en/actions/deployment/security-hardening-your-deployments/configuring-openid-connect-in-azure)
- [Azure login action](https://github.com/marketplace/actions/azure-login)

