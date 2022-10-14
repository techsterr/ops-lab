# Initial Command
```sh
az login
az login --tenant tanant-name

az account set --subscription xxx
```

# VM
```sh
az vm list
az vm list-sizes --location southeastasia

az vm image list
```

# keyvault

```sh
az keyvault list
```
# aks

```sh
# Attach acr to aks
az aks update -n cluster_name -g rg-group --attach-acr acr-name
```

# acr

```sh
az acr login -n

```