# k8s

# Prerequisites

    A valid Azure subscription
    Azure CLI : https://learn.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest
    kubectl: https://kubernetes.io/docs/tasks/tools/
    bash shell: Most Linux/macOS systems already have this. For Windows, use Git Bash or WSL: https://www.atlassian.com/git/tutorials/git-bash
    Docker Desktop for building and pushing images locally: https://docs.docker.com/desktop/

# Login to Azure

    az login
    az account set --subscription=<subscriptionId>
    az account show

# Show existing resources

    az resource list

# Create RG, ACR and AKS

    ./infra/azcli/script.sh

# Variables

    RESOURCE_GROUP="rg-onlinestore-dev-uksouth-001"
    AKS_NAME="aks-onlinestore-dev-uksouth-001"
    ACR_NAME="acronlinestoredevuksouth001"

# Connect to cluster

    az aks get-credentials --resource-group $(RESOURCE_GROUP) --name $(AKS_NAME)

# Short name for kubectl

    alias k=kubectl

# Show all existing objects

    k get all

# Log in to ACR

    az acr login --name $ACR_NAME

# Build and push the Docker images to ACR

    # Order Service
    docker build -t order ./app/order-service 
    docker tag order:v1 $ACR_NAME.azurecr.io/order:v1
    docker push $ACR_NAME.azurecr.io/order:v1

    # Product Service
    docker build -t product ./app/product-service 
    docker tag product:v1 $ACR_NAME.azurecr.io/product:v1
    docker push $ACR_NAME.azurecr.io/product:v1

    # Store Front Service
    docker build -t store-front ./app/store-front-service 
    docker tag store-front:v1 $ACR_NAME.azurecr.io/store-front:v1
    docker push $ACR_NAME.azurecr.io/store-front:v1

# Write Kubernetes YAML Manifests for the applicatons

    configmap.yml
    order-deployment.yml
    order-service.yml
    product-deployment.yml
    product-service.yml
    store-front-deployment.yml
    store-front-service.yml
    rabbitmq-deployment.yml
    rabbitmq-service.yml

# Deploy the services to AKS using kubectl and manifest files

    k apply -f ./manifests

# Verify the Deployment

    k get pods
    k get services
    Browse the app using http://<LoadBalancer public IP>:80

# Clean the Azure resources

    az group delete --name rg-onlinestore-dev-uksouth-001 --yes --no-wait