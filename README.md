# Deploying microservice applications in AKS using Azure DevOps and Helm

    A sample multi-container application with a group of microservices and web front ends 
    that simulate a retail scenario.

    Part1 (Manual Deployment using comand line tools):
    AzCLI, Docker Desktop and kubectl: https://github.com/santosh-gh/k8s-01
    YouTube: https://youtu.be/zoJ7MMPVqFY

    Part2 (Automated Deployment using Azure DevOps Pipeline): https://github.com/santosh-gh/k8s-02
    YouTube: https://youtu.be/nnomaZVHg9I

    Part3 (Automated Infra Deployment using Bicep and Azure DevOps Pipeline): https://github.com/santosh-gh/k8s-03
    YouTube: https://youtu.be/nnomaZVHg9I

# Architesture

![Store Architesture](aks-store-architecture.png)

    # Store front: Web application for customers to view products and place orders.
    # Product service: Shows product information.
    # Order service: Places orders.
    # RabbitMQ: Message queue for an order queue.

# Directory Structure

![Directory Structure](image.png) 

# YouTube: 

    https://youtu.be/zoJ7MMPVqFY    
 
# GitHub Repository (source code)

    https://github.com/santosh-gh/k8s-04


# Deploying microservice applications in AKS using 

    Infra deploy: using command line AzCLI/Bicep

    Docker build and push images to ACR: Docker Desktop command line

    App Deploymnet: Helm command

    Helmchart: Use helm command 
    helm install store-release ./helmchart

# Steps

    1. Infra deployment using AzCLI/Bicep
    2. Build and push images to ACR: Docker
    3. Helm install and Helmfy
    4. App deployment: helm install store-release ./helmchart
    5. Validate and Access the application
    6. Clean the Azure resources

# Infra deployment

    # Login to Azure

        az login
        az account set --subscription=<subscriptionId>
        az account show

    # Show existing resources

        az resource list

    # Create RG, ACR and AKS

        # AzCLI
        ./infra/azcli/script.sh

        OR

        # Bicep
        az deployment sub create --location uksouth --template-file ./infra/bicep/main.bicep --parameters ./infra/bicep/main.bicepparam

    # Connect to cluster

        RESOURCE_GROUP="rg-onlinestore-dev-uksouth-001"
        AKS_NAME="aks-onlinestore-dev-uksouth-001"
        az aks get-credentials --resource-group $RESOURCE_GROUP --name $AKS_NAME

    # Short name for kubectl

    # Show all existing objects

        k get all

# Docker Build and Push

    # Log in to ACR

        ACR_NAME="acronlinestoredevuksouth001"
        az acr login --name $ACR_NAME

    # Build and push the Docker images to ACR

        # Order Service
        docker build -t order ./app/order-service 
        docker tag order:latest $ACR_NAME.azurecr.io/order:v1
        docker push $ACR_NAME.azurecr.io/order:v1

        # Product Service
        docker build -t product ./app/product-service 
        docker tag product:latest $ACR_NAME.azurecr.io/product:v1
        docker push $ACR_NAME.azurecr.io/product:v1

        # Store Front Service
        docker build -t store-front ./app/store-front 
        docker tag store-front:latest $ACR_NAME.azurecr.io/store-front:v1
        docker push $ACR_NAME.azurecr.io/store-front:v1

        docker images

# Helm and Helmify

    # helmify 

    helmify -f ./manifests helmchart

    # Helm Deploy

    helm install store-release ./helmchart

# Verify the Deployment

    k get pods
    k get services
    curl <LoadBalancer public IP>:80
    Browse the app using http://<LoadBalancer public IP>:80

# Clean the Azure resources

    az group delete --name rg-onlinestore-dev-uksouth-001 --yes --no-wait