# Trigger Auto Deployment for Azure Container App and Docker Hub

This project is an automated CI/CD pipeline designed to build, push, and deploy a container image to both an Azure Container App and Docker Hub. The pipeline uses GitHub Actions to trigger the deployment on every change made to the main branch or manually, allowing for seamless integration and continuous deployment.

## Project Overview

The pipeline consists of two primary jobs:

1. **Build and Push to Docker Hub**: Builds a Docker image from the code in the repository, tags it with both `latest` and the latest commit SHA, and pushes it to Docker Hub.
2. **Deploy to Azure Container App**: Deploys the built container image to an Azure Container App, updating the app with the latest image and resource configurations.

### Workflow for Docker Hub

This workflow pushes the Docker image to Docker Hub, tagged with:
- `latest`: Always updated with the most recent build.
- A unique tag based on the repository name and commit SHA, allowing you to identify the specific build.

### Workflow for Azure

This workflow builds a Docker image from the repository and pushes it to Azure Container Registry (ACR), then deploys it to an Azure Container App with the following characteristics:

- **Automated Deployment**: Triggers automatically on changes to the `main` branch or can be manually triggered.
- **Image Tagging**: Tags the image with the unique commit SHA, enabling version control and easy rollback.
- **Resource Configuration**: Sets CPU and memory limits for the Azure Container App to ensure optimized resource usage.
- **Azure Authentication**: Uses GitHub Secrets to securely authenticate to both ACR and Azure services.
- **Deployment Update**: Updates the Azure Container App to pull the latest image from ACR, ensuring the app is always up-to-date with the latest code.


## Environment Variables

The project uses the following environment variables:

- `AZURE_CONTAINER_APP_NAME`: The name of the Azure Container App. (In this case, `azure-container-app-demo`)
- `AZURE_GROUP_NAME`: The name of the resource group where the container app is hosted. (In this case, `demo-ml`)
- `secrets.AZURE_CREDENTIALS`: Created by `az ad sp create-for-rbac --name "github-actions-sp" --role Contributor --scopes /subscriptions/04c1b27c-fcd8-43ba-96a2-cfe04a58d0a5 --sdk-auth`
- `secrets.ACR_USERNAME`: The `secrets.ACR_USERNAME` and `secrets.ACR_PASSWORD` value is derived from the access key of the Azure Container Registry.
- `secrets.ACR_PASSWORD`: The `secrets.ACR_USERNAME` and `secrets.ACR_PASSWORD` value is derived from the access key of the Azure Container Registry.
- `secrets.DOCKER_HUB_USERNAME`: Docker Hub username for authenticating to Docker Hub.
- `secrets.DOCKER_HUB_ACCESS_TOKEN`: Docker Hub access token for authenticating to Docker Hub.

## Trigger Conditions

This workflow is triggered under the following conditions:

- **Push to Main**: Automatically runs when changes are pushed to the `main` branch.
- **Manual Trigger**: Allows the workflow to be triggered manually through the GitHub Actions interface (`workflow_dispatch`).

## Workflow Jobs and Steps

### Docker Hub Workflow

1. **Checkout**: Checks out the latest code from the repository.
2. **Authenticate to Docker Hub**: Logs in to Docker Hub using credentials stored in GitHub secrets (`DOCKER_HUB_USERNAME` and `DOCKER_HUB_ACCESS_TOKEN`).
3. **Lowercase the Repository Name**: Normalizes the repository name to lowercase and stores it in the environment as `REPO`, using only the repository name without the owner.
4. **Build and Push to Docker Hub**: Builds the Docker image from the Dockerfile and tags it with:
   - `latest`: for the latest build.
   - A unique tag combining the repository name and commit SHA (e.g., `repo-name-9b1e02d4a8b`), ensuring each image is versioned by commit.

### Azure Container App Workflow

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
- **`DOCKER_HUB_USERNAME`**: The username for accessing Docker Hub.
- **`DOCKER_HUB_ACCESS_TOKEN`**: The access token for Docker Hub authentication.

## Deployment Process

1. **Build and Push to Docker Hub**: The Docker image is built using the latest code in the repository and pushed to Docker Hub with two tags (`latest` and a unique commit SHA-based tag).
2. **Build and Push to Azure Container Registry**: (Optional, if required by your Azure deployment)
3. **Configure Azure Container App**: The ACR registry is set as the image source for the Azure Container App, and resource limits are applied.
4. **Update and Deploy Image**: The Azure Container App is updated to pull the latest Docker image based on the current commit SHA, ensuring the application is always up to date with the latest code.

## Usage

1. **Automatic Deployment**: Simply push changes to the `main` branch, and the workflow will automatically build and deploy the new version to both Docker Hub and Azure Container App.
2. **Manual Deployment**: Go to the GitHub Actions tab, select the workflow, and click on **Run workflow** under the `workflow_dispatch` option to trigger a deployment manually.

## Notes

- Ensure all required secrets are configured in the GitHub repository settings.
- The workflow uses resource configuration parameters (`CPU` and `memory`) for Azure that can be adjusted as needed.
- Docker Hub tags will include a `latest` tag, as well as a tag uniquely identifying each image by commit SHA for versioning.

This setup enables continuous delivery of containerized applications to both Docker Hub and Azure, providing a streamlined process for deploying new code with minimal manual intervention.
