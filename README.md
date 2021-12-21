# ArgoCD App of Apps demo

This repo contains a basic implementation of the [App Of Apps Pattern](https://argo-cd.readthedocs.io/en/stable/operator-manual/cluster-bootstrapping/) for ArgoCD.

It is be possible to test the setup on a local machine using [minikube](https://minikube.sigs.k8s.io/docs/).

## Install ArgoCD on minikube

Start minikube and use max available resources:

```bash
minikube start --memory=max --cpus=max
```

Install ArgocCD:

```bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

## Access UI

Use [Port Forwarding](https://kubernetes.io/docs/tasks/access-application-cluster/port-forward-access-application-cluster/) to access the UI server:

```bash
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

Open a browser and go to http://localhost:8080.

Use the admin user to login. The password can be retrieved using the following command:

```bash
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
```

## Install CLI

https://argo-cd.readthedocs.io/en/stable/cli_installation/

 ## Install Demo App of Apps

Login using the CLI:

```bash
argocd login localhost:8080 
```

The same UI credentials can be used.

Create all applications of the `test` environment:

```
argocd app create apps \
    --dest-namespace argocd \
    --dest-server https://kubernetes.default.svc \
    --repo https://github.com/jgoelen/argo-app-of-apps-demo.git \
    --path apps/test
```

## Appendix

### Create Sealed Secrets

Use Port Forwarding to access the [Sealed Secret controller](https://github.com/bitnami-labs/sealed-secrets):

```bash
kubectl port-forward svc/sealed-secrets -n kubeseal 8090:8080
```

Encrypt a secret using [kubeseal](https://github.com/bitnami-labs/sealed-secrets#installation):

```bash
echo -n 'My secret' | kubeseal  --cert http://localhost:8090/v1/cert.pem --raw --from-file=/dev/stdin --namespace demo --name demo-secrets
```