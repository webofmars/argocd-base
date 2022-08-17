# argocd-base

Boilerplate for deploying easy kubernetes cluster with argocd

Will install the following componenents:

* create a default project
* ingress nginx controller
* cert-manager
* letsencrypt default cluster issuer
* registry-creds

## usage

You should first install argocd

```sh
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
# or if you don't want argocd UI
# kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/core-install.yaml
```

## roadmap

* [ ] metrics-server
* [ ] prometheus stack
* [ ] istio
