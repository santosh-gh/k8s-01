# k8s-gitops

# create resource group with name: aks-rg
az group create --location eastus --name aks-rg

# create AKS cluster with name: aks-cluster
az aks create --name aks-cluster \
              --resource-group aks-rg \
              --node-count 1 \
              --network-plugin azure \
              --enable-managed-identity \
              --generate-ssh-keys

# connect to cluster
az aks get-credentials --name aks-cluster --resource-group aks-rg


az aks get-credentials --resource-group rg-onlinestore-dev-uksouth-001 --name aks-onlinestore-dev-uksouth-001

kubectl get ingress store-front 

az group delete --name rg-onlinestore-dev-uksouth-001 --yes --no-wait

alias k=kubectl

# Log in to ACR
az acr login --name $ACR_NAME

# Tag and push your Docker image
docker build -t order .
docker tag order:latest $ACR_NAME.azurecr.io/order:v1
docker push $ACR_NAME.azurecr.io/order:v1

docker build -t product .
docker tag product:latest $ACR_NAME.azurecr.io/product:v1
docker push $ACR_NAME.azurecr.io/product:v1

docker build -t store-front .
docker tag store-front:latest $ACR_NAME.azurecr.io/store-front:v1
docker push $ACR_NAME.azurecr.io/store-front:v1