# CI/CD using GitOps with Azure Arc-enabled Kubenetes

This repo contains the sample GitOps manifests for the Azure Arc GitOps CI/CD tutorial.

The GitOps repo can be found at <https://github.com/Azure/arc-cicd-demo-src>

Please see the tutorial at <https://docs.microsoft.com/azure/azure-arc/kubernetes/tutorial-gitops-cicd>

https://learn.microsoft.com/en-us/azure/azure-arc/kubernetes/tutorial-gitops-flux2-ci-cd#implement-cicd-with-github

## variables

azureContainerRegistry=""
clusterName=""
clusterRGName=""
clusterConfigName="cluster-config"
gitopsNamespace="cluster-config"
gitopsRepo="https://github.com/$organizaton/$repositoryName.git"
branch="master"
kustomizationNameDev="cluster-config-dev"
kustomizationNameStage="cluster-config-stage"

# Add ACR to AKS cluster
az aks update -n $clusterName -g $clusterRGName --attach-acr $azureContainerRegistry

# Add flux configuration
az k8s-configuration flux create --name $clusterConfigName --cluster-name $clusterName --namespace $gitopsNamespace --resource-group $clusterRGName -u $gitopsRepo --scope cluster --cluster-type managedClusters --branch $branch --kustomization name=$kustomizationNameDev prune=true path=arc-cicd-cluster-dev/manifests --kustomization name=$kustomizationNameStage prune=true path=arc-cicd-cluster-stage/manifests 

az k8s-configuration flux show -g $clusterRGName -c $clusterName -n $clusterConfigName -t managedClusters

## Create GitHub secrets

# Create GitHub repository secrets

AZURE_CREDENTIALS : {"clientId":"","clientSecret":"","subscriptionId":"","tenantId":""}
AZ_ACR_NAME	: 
MANIFESTS_BRANCH : master
VOTE_APP_TITLE : Voting Application
AKS_RESOURCE_GROUP : aks-rg-01
AKS_NAME :	aks01
# Create GitHub environment secrets

#  az-vote-app-dev environment
ENVIRONMENT_NAME : Dev
TARGET_NAMESPACE : dev

# az-vote-app-stage environment
ENVIRONMENT_NAME : Stage
TARGET_NAMESPACE : stage