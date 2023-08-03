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
kubectl apply -n argocd -f https://raw.githubusercontent.com/webofmars/argocd-base/main/bootstrap/main.yaml
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
    path: ./argocd
    directory:
      recurse: true
    repoURL: https://github.com/webofmars/argocd-base.git
    targetRevision: HEAD # or check latest release if you want to pin a version
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
    syncOptions:
      - CreateNamespace=true
      - ServerSideApply=true
```

### optional: add an ingress for ArgoCD UI

If you want you can deploy an ingress for argocd to acess the UI

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: argocd-server-ingress
  namespace: argocd
  annotations:
    cert-manager.io/cluster-issuer: letsencrypt-prod
    kubernetes.io/tls-acme: "true"
    nginx.ingress.kubernetes.io/ssl-passthrough: "true"
    # If you encounter a redirect loop or are getting a 307 response code
    # then you need to force the nginx ingress to connect to the backend using HTTPS.
    #
    nginx.ingress.kubernetes.io/backend-protocol: "HTTPS"
spec:
  ingressClassName: "nginx"
  rules:
  - host: argocd.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: argocd-server
            port:
              name: https
  tls:
  - hosts:
    - argocd.example.com
    secretName: argocd-secret # do not change, this is provided by Argo CD
```

## roadmap

* [ ] metrics-server
* [ ] prometheus stack
* [ ] istio
