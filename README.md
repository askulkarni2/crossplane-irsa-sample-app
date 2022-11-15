# IRSA using Crossplane and ArgoCD (GitOps Approach)

## Pre-Requisites

1. EKS cluster with OIDC enabled
2. ArgoCD running in the cluster
3. ArgoCD installed on local dev machine

## Setup

### ArgoCD Port-Forward

```sh
kubectl port-forward svc/argo-cd-argocd-server -n argocd 8080:443
```

### ArgoCD Login

```sh
argocd login localhost:8080
```

### Create Crossplane Compositions

```sh
argocd app create crossplane-compositions \
    --repo https://github.com/askulkarni2/crossplane-irsa-sample-app \
    --dest-server https://kubernetes.default.svc  \
    --path compositions --sync-policy=auto --self-heal
```

### Create Sample app

#### Create values.yaml

Edit the `s3-la.yaml` template file to replace `awsAccountID`, `eksOIDC` and `region` with your custom values.

#### Create Sample App
```sh
argocd app create workload \
    --repo https://github.com/askulkarni2/crossplane-irsa-sample-app \
    --dest-server https://kubernetes.default.svc  \
    --path app --sync-policy=auto --self-heal
```
