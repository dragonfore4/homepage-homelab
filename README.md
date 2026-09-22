# Homepage

Kubernetes manifests for [gethomepage/homepage](https://github.com/gethomepage/homepage), managed by Argo CD.

The application is exposed at:

```text
https://homepage.home.sirasith.net
```

## Local validation

```bash
kubectl apply --dry-run=client -k k8s
kubectl kustomize k8s
```

## Manual apply

```bash
kubectl apply -k k8s
kubectl -n homepage get pods,svc,ingress
kubectl -n homepage logs deploy/homepage
```

## Argo CD

Create an Argo CD `Application` whose `spec.source.path` is `k8s` in this repository. The repository URL is intentionally not embedded here so this template can be used with the final Git remote.

Example:

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: homepage
  namespace: argocd
spec:
  project: default
  source:
    repoURL: https://github.com/<owner>/<repository>.git
    targetRevision: main
    path: k8s
  destination:
    server: https://kubernetes.default.svc
    namespace: homepage
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
    syncOptions:
      - CreateNamespace=true
```

## Discovering services

Homepage uses the Kubernetes API through the `homepage` ServiceAccount. Add Homepage annotations to the Ingress, Service, or other supported resource that should appear in the dashboard, for example:

```yaml
metadata:
  annotations:
    gethomepage.dev/enabled: "true"
    gethomepage.dev/name: Lobby
    gethomepage.dev/group: Applications
    gethomepage.dev/icon: mdi-home
```
