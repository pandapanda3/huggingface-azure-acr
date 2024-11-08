# Trigger Auto Deployment for demo-container

This project is an automated CI/CD pipeline designed to build, push, and deploy a container image to an Azure Container App. The pipeline uses GitHub Actions to trigger the deployment on every change made to the main branch or manually, allowing for seamless integration and continuous deployment.

## Project Overview

The pipeline consists of two primary jobs:

1. **Build**: Builds a Docker image from the code in the repository, tags it with the latest commit SHA, and pushes it to the Azure Container Registry (ACR).
2. **Deploy**: Deploys the built container image to an Azure Container App, updating the app with the latest image and resource configurations.

## Environment Variables

The project uses the following environment variables:

- `AZURE_CONTAINER_APP_NAME`: The name of the Azure Container App. (In this case, `azure-container-app-demo`)
- `AZURE_GROUP_NAME`: The name of the resource group where the container app is hosted. (In this case, `demo-ml`)
- `secrets.AZURE_CREDENTIALS`: Created by `az ad sp create-for-rbac --name "github-actions-sp" --role Contributor --scopes /subscriptions/04c1b27c-fcd8-43ba-96a2-cfe04a58d0a5 --sdk-auth`
- `secrets.ACR_USERNAME`: The `secrets.ACR_USERNAME` and `secrets.ACR_PASSWORD` value is derived from the access key of the container registry.
- `secrets.ACR_PASSWORD`: The `secrets.ACR_USERNAME` and `secrets.ACR_PASSWORD` value is derived from the access key of the container registry.

## Trigger Conditions

This workflow is triggered under the following conditions:

- **Push to Main**: Automatically runs when changes are pushed to the `main` branch.
- **Manual Trigger**: Allows the workflow to be triggered manually through the GitHub Actions interface (`workflow_dispatch`).

## Workflow Jobs and Steps

### Build Job

1. **Checkout**: Checks out the latest code from the repository.
2. **Set up Docker Buildx**: Configures Docker Buildx to support multi-platform builds.
3. **Log in to ACR**: Logs in to Azure Container Registry using credentials stored in GitHub secrets (`ACR_USERNAME` and `ACR_PASSWORD`).
4. **Lowercase the Repository Name**: Normalizes the repository name to lowercase and stores it in the environment as `REPO`.
5. **Build and Push to ACR**: Builds the Docker image from the Dockerfile and tags it with the commit SHA. The image is then pushed to the Azure Container Registry.

### Deploy Job

1. **Print Image Name**: Outputs the image name and tag (using commit SHA) to the console for reference.
2. **Azure Login**: Authenticates with Azure using credentials from `AZURE_CREDENTIALS` stored in GitHub secrets.
3. **Deploy to Container App**:
   - Configures ACR as the container registry for the Azure Container App, allowing it to pull the image.
   - Sets CPU and memory limits for the container app.
   - Updates the container app to use the latest image from ACR, tagged with the latest commit SHA.

## Required GitHub Secrets

The following secrets are required for the workflow to function:

- **`ACR_USERNAME`**: The username for accessing the Azure Container Registry.
- **`ACR_PASSWORD`**: The password for accessing the Azure Container Registry.
- **`AZURE_CREDENTIALS`**: A JSON-formatted service principal credential for Azure login, containing `clientId`, `clientSecret`, `subscriptionId`, and `tenantId`.

## Deployment Process

1. **Build and Push**: The Docker image is built using the latest code in the repository and pushed to ACR.
2. **Configure Azure Container App**: The ACR registry is set as the image source for the Azure Container App, and resource limits are applied.
3. **Update and Deploy Image**: The Azure Container App is updated to pull the latest Docker image based on the current commit SHA, ensuring the application is always up to date with the latest code.

## Usage

1. **Automatic Deployment**: Simply push changes to the `main` branch, and the workflow will automatically build and deploy the new version.
2. **Manual Deployment**: Go to the GitHub Actions tab, select the workflow, and click on **Run workflow** under the `workflow_dispatch` option to trigger a deployment manually.

## Notes

- Ensure all required secrets are configured in the GitHub repository settings.
- The workflow uses resource configuration parameters (`CPU` and `memory`) that can be adjusted as needed.

This setup enables continuous delivery of containerized applications to Azure, providing a streamlined process for deploying new code with minimal manual intervention.
