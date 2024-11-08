# Setting variables in `main.yml`

## secrets.ACR_USERNAME and secrets.ACR_PASSWORD
In the corresponding GitHub repository, go to Settings → Secrets and variables → Actions → New repository secret.
The `secrets.ACR_USERNAME` and `secrets.ACR_PASSWORD` value is derived from the access key of the container registry. 

## secrets.AZURE_CREDENTIALS
Run the command `az ad sp create-for-rbac --name "my-ml-service-principal" --role Contributor --scopes /subscriptions/04c1b27c-fcd8-43ba-96a2-cfe04a58d0a5` to obtain the value for AZURE_CREDENTIALS. Then, store this value in GitHub as a repository secret. 

When adding this JSON to GitHub as the `AZURE_CREDENTIALS` secret, ensure you map the fields correctly. For GitHub Actions, rename `appId` to `clientId`, `password` to `clientSecret`, and `tenant` to `tenantId`.

It should be something like:
```
{
  "clientId": "XXXX",
  "clientSecret": "XXX",
  "subscriptionId": "XXX",
  "tenantId": "XXX"
}
```

- **`az ad sp create-for-rbac`**: This part of the command tells Azure CLI to create a new service principal (SP) for role-based access control (RBAC). Service principals are identities in Azure Active Directory used by applications and automation tools to access Azure resources securely.

- **`--name "my-ml-service-principal"`**: This specifies the name of the service principal, in this case, `"my-ml-service-principal"`. You can choose any name that helps identify the purpose of the service principal.

- **`--role Contributor`**: This assigns the **Contributor** role to the service principal. The Contributor role grants full access to manage Azure resources, allowing the service principal to create, update, and delete resources within the specified scope.

- **`--scopes /subscriptions/04c1b27c-fcd8-43ba-96a2-cfe04a58d0a5`**: This specifies the scope of permissions for the service principal. Here, the scope is set to an Azure subscription with the ID `04c1b27c-fcd8-43ba-96a2-cfe04a58d0a5`. This means the service principal’s permissions (Contributor role) will apply to this entire subscription and all resources within it.
### How to Get the Subscription ID
#### Check from website
1. Log in to the [Azure Portal](https://portal.azure.com/).
2. In the left sidebar, click on **Subscriptions**.
3. You’ll see a list of your subscriptions. The **Subscription ID** for each will be displayed in the list or by clicking on the specific subscription for more details.
#### Check by CLI
```
az account list --output table
```
# If the AZURE_CREDENTIALS doesn't work
## Confirm the Service Principal's Existence and Permissions:
```az ad sp show --id b3b2dc57-73ad-41be-8312-86429b91c2f9```
## Create a new one
```
az ad sp create-for-rbac --name "github-actions-sp" --role Contributor --scopes /subscriptions/04c1b27c-fcd8-43ba-96a2-cfe04a58d0a5 --sdk-auth
```
For CI/CD or GitHub Actions: Use `--sdk-auth` to get the output in a format that can be directly used in tools like GitHub Actions, where it will likely be placed in a secret and referenced as `AZURE_CREDENTIALS`.
# Check the result
## Build and push container image to ACR registry
To check if a new image has been pushed in Azure, open the specified **Container Registry**, click on **Repositories**, and see if there are any new images listed.

eg: [ACR registry](https://portal.azure.com/#view/Microsoft_Azure_ContainerRegistries/RepositoryBlade/id/%2Fsubscriptions%2F04c1b27c-fcd8-43ba-96a2-cfe04a58d0a5%2FresourceGroups%2Fdemo-ml%2Fproviders%2FMicrosoft.ContainerRegistry%2Fregistries%2Fdemoalfredo/repository/pandapanda3%2Fhuggingface-azure-acr)
## New updates on Container App
In Azure, open the specified **Container App**, click on **Revisions and replicas**, and check if a new image is being deployed.

eg: [Container App](https://portal.azure.com/#@peggypandayeah.onmicrosoft.com/resource/subscriptions/04c1b27c-fcd8-43ba-96a2-cfe04a58d0a5/resourceGroups/demo-ml/providers/Microsoft.App/containerApps/azure-container-app-demo/revisionManagement)

