# ArgoCD App of Apps demo

## Install ArgoCD on minikube

```
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

## UI

```
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

```
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d | pbcopy
```

```
argocd login localhost:8080 

argocd app create apps \
    --dest-namespace argocd \
    --dest-server https://kubernetes.default.svc \
    --repo https://github.com/jgoelen/argo-app-of-apps-demo.git \
    --path apps/test

```


## Create sealed secrets

```
kubectl port-forward svc/sealed-secrets -n kubeseal 8090:8080
echo -n 'My secret' | kubeseal  --cert http://localhost:8090/v1/cert.pem --raw --from-file=/dev/stdin --namespace demo --name demo-secrets | pbcopy
```


