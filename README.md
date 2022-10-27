# argocd-base

Boilerplate for deploying easy kubernetes cluster with argocd

Will install the following componenents:

* create a default project
* ingress nginx controller
* cert-manager
* letsencrypt default cluster issuer
* registry-creds

## usage

* You should first install argocd

```sh
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
# or if you don't want argocd UI
# kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/core-install.yaml
```

* wait a bit ☕️ ...

* Apply the default project manifest :

```sh
kubectl apply -n argocd -f https://raw.githubusercontent.com/webofmars/argocd-base/main/0_default-project.yaml
```

* Apply the app of apps manifest :

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: app-of-apps
  namespace: argocd
spec:
  destination:
    namespace: argocd
    server: https://kubernetes.default.svc
  project: default
  source:
    path: ./
    repoURL: https://github.com/webofmars/argocd-base
    targetRevision: v1.0.0
  syncPolicy:
    syncOptions:
    - CreateNamespace=true
```

## roadmap

* [ ] metrics-server
* [ ] prometheus stack
* [ ] istio
