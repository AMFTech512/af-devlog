# Creating a local cluster

Use minikube

# Deploying
Start with the configs:

```bash
kubectl apply -f mongo-config.yaml
kubectl apply -f mongo-secret.yaml
```

Then create the database (because the web app depends on it)
```bash
kubectl apply -f mongo.yaml
```

Finally, create the web application
```bash
kubectl apply -f webapp.yaml
```

# Useful commands

## Get everything

```bash
kubectl get all
```

# Get nodes

```bash
kubectl get node
```

Or to get "wide" output

```bash
kubectl get node -o wide
```

## Get pods

```bash
kubectl get pods
```

or to get all pods

```bash
kubectl get pods -A
```

## Get configs and secrets

Get configs
```bash
kubectl get configmap
```

Get secrets
```bash
kubectl get secret
```

## Get details about a pod

```bash
kubectl describe pod POD_LABEL
```

## Get logs for a pod

```bash
kubectl logs POD_LABEL
```

To stream the logs, add `-f`

```bash
kubectl logs POD_LABEL -f
```
