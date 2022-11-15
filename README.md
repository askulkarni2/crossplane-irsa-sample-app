# IRSA using Crossplane and ArgoCD (GitOps Approach)

## Pre-Requisites

1. EKS cluster with OIDC enabled
2. [Crossplane](https://crossplane.io/docs/v1.9/getting-started/install-configure.html) running on the cluster
3. [Crossplane AWS provider](https://marketplace.upbound.io/providers/crossplane-contrib/provider-aws/v0.33.0) running on the cluster
2. ArgoCD running on the cluster
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

Here, we create a Crossplane composition for IRSA called XIRSA as an argocd application so we can manage them via gitops. See Crossplane documentation for more information on [Compositions](https://crossplane.io/docs/v1.9/concepts/composition.html). Crossplane compositions are cluster scoped resources.


```sh
argocd app create crossplane-compositions \
    --repo https://github.com/askulkarni2/crossplane-irsa-sample-app \
    --dest-server https://kubernetes.default.svc  \
    --path compositions --sync-policy=auto --self-heal
```

### Create Sample App

Here we create a namespace scoped claim to create an IRSA for our demo app that does `aws s3 ls` in a loop. 
It uses a service account called `s3-ls` that has the annotation for IRSA role with a `AmazonS3ReadOnlyAccess` managed policy. 


```sh
argocd app create workload \
    --repo https://github.com/askulkarni2/crossplane-irsa-sample-app \
    --dest-server https://kubernetes.default.svc  \
    --path app --sync-policy=auto --self-heal
```
