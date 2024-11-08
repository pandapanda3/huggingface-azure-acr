# Setting variables in `main.yml`

## secrets.ACR_USERNAME and secrets.ACR_PASSWORD
In the corresponding GitHub repository, go to Settings → Secrets and variables → Actions → New repository secret.
The `secrets.ACR_USERNAME` and `secrets.ACR_PASSWORD` value is derived from the access key of the container registry. 

## secrets.AZURE_CREDENTIALS
Run the command `az ad sp create-for-rbac --name "my-ml-service-principal" --role Contributor --scopes /subscriptions/04c1b27c-fcd8-43ba-96a2-cfe04a58d0a5` to obtain the value for AZURE_CREDENTIALS. Then, store this value in GitHub as a repository secret. 
- **`az ad sp create-for-rbac`**: This part of the command tells Azure CLI to create a new service principal (SP) for role-based access control (RBAC). Service principals are identities in Azure Active Directory used by applications and automation tools to access Azure resources securely.

- **`--name "my-ml-service-principal"`**: This specifies the name of the service principal, in this case, `"my-ml-service-principal"`. You can choose any name that helps identify the purpose of the service principal.

- **`--role Contributor`**: This assigns the **Contributor** role to the service principal. The Contributor role grants full access to manage Azure resources, allowing the service principal to create, update, and delete resources within the specified scope.

- **`--scopes /subscriptions/04c1b27c-fcd8-43ba-96a2-cfe04a58d0a5`**: This specifies the scope of permissions for the service principal. Here, the scope is set to an Azure subscription with the ID `04c1b27c-fcd8-43ba-96a2-cfe04a58d0a5`. This means the service principal’s permissions (Contributor role) will apply to this entire subscription and all resources within it.
### How to Get the Subscription ID

1. Log in to the [Azure Portal](https://portal.azure.com/).
2. In the left sidebar, click on **Subscriptions**.
3. You’ll see a list of your subscriptions. The **Subscription ID** for each will be displayed in the list or by clicking on the specific subscription for more details.

