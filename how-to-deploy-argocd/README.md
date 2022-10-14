# Requirements
- Deploy ArgoCD in K8s cluster
- Link to Github repo
- Setup App Of Apps Pattern

# Requires
- helm
- kubectl

# Setup K8s cluster

You can use cloud services or manual setup your own cluster.

## Check kubectl and helm
```
kubectl version
helm version
```

# Create Github

## Create Github App

- Go to https://github.com/settings
- Developer settings
- GitHub Apps 
- New GitHub App
  - Homepage URL: Example https://argocd.example.com
  - Webhook: Deactivate
  - Repository permissions: default
  - Where can this GitHub App be installed?
    - Only on this account
    - Any account (if you're not org account)
  - Create Github App

## Install App
- https://github.com/settings/apps/{appname}
- Install App
- Choose an account to install: Your Org
- Install
- Select: Only select repositories
- https://github.com/settings/installations/

# Create app of apps


Declaratively specify one Argo CD app that consists only of other apps. [more information](https://argo-cd.readthedocs.io/en/stable/operator-manual/cluster-bootstrapping/#app-of-apps-pattern)


In this tutorial, we will use Kustomize base and overlay pattern.

Example:

```
~/someApp
├── base
│   ├── deployment.yaml
│   ├── kustomization.yaml
│   └── service.yaml
└── overlays
    ├── development
    │   ├── cpu_count.yaml
    │   ├── kustomization.yaml
    │   └── replica_count.yaml
    └── production
        ├── cpu_count.yaml
        ├── kustomization.yaml
        └── replica_count.yaml
```


## Create App Deployment

```
app-deployment
├── base
│   └── nginx
│       ├── deployment.yaml
│       └── kustomization.yaml
└── overlay
    ├── dev
    │   └── nginx
    │       └── kustomization.yaml
    └── prod
        └── nginx
            └── kustomization.yaml
```

```
argo-config
├── deployment
│   ├── base
│   │   ├── apps
│   │   │   ├── kustomization.yaml
│   │   │   └── nginx.yaml
│   │   └── project
│   │       ├── application.yaml
│   │       └── kustomization.yaml
│   └── overlay
│       ├── dev
│       │   └── kustomization.yaml
│       └── prod
│           └── README.md
└── management
    └── app-dev.yaml
```

# Deploy ArgoCD

## Install Chart 

```
helm dep update
```

## Create namespace
```
kubectl create namespace argocd
```


## Edit values.yaml

```
githubAppID: $GITHUB_APP_ID
githubAppInstallationID: "$GITHUB_APP_INSTALLATION_ID"
```

## Deploy

```
helm install argocd . -n argocd
```


# Reference

https://redhat-scholars.github.io/argocd-tutorial/argocd-tutorial/
https://github.com/redhat-developer-demos/openshift-gitops-examples


# Inprogress


