# Kubernetes manifests: The perfect cluster

## Default values
- Domain zone: `.theperfectk8scluster.com`

## Manifests

### my-webserver
- Nginx
- Livenessprobe, Readinessprobe and Resources Limits / Requests
- Running as non-root and readOnlyRootFilesystem
- Replicas, HPA (Horizontal Pod Autoscaler), PDB (Pod Disruption Budget)
- Config map and Secret mounted as environments variables

## Helm

### Prometheus
Pre-requirements
- metric-server

https://github.com/prometheus-community/helm-charts/

```
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update

kubectl create ns prometheus

helm install -n prometheus prometheus prometheus-community/prometheus --values helm/prometheus/values.yaml
```

### Grafana
https://github.com/grafana/helm-charts

```
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update

kubectl create ns prometheus

helm install -n prometheus grafana grafana/grafana --values helm/grafana/values.yaml
```

### Prometheus M$Teams
https://github.com/prometheus-msteams/prometheus-msteams

```
helm repo add prometheus-msteams https://prometheus-msteams.github.io/prometheus-msteams/
helm repo update

kubectl create ns prometheus

helm install -n prometheus prometheus-msteams prometheus-msteams/prometheus-msteams --values helm/prometheus-msteams/values.yaml
```