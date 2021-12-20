# Kubernetes manifests: The perfect cluster

## Default values
- Environment name: `dev`
- EKS cluster name: `theperfectk8scluster`
- Domain zone: `.theperfectk8scluster.com`
- Region: `us-east-1`

## Manifests

### Pod Security Policies

```
# Remove default EKS's Clusterrolebinding
kubectl delete clusterrolebinding eks:podsecuritypolicy:authenticated

kubectl auth can-i use podsecuritypolicy/eks.restricted --namespace test --as default
```

### my-webserver
- Nginx
- Livenessprobe, Readinessprobe and Resources Limits / Requests
- Running as non-root and readOnlyRootFilesystem
- Replicas, HPA (Horizontal Pod Autoscaler), PDB (Pod Disruption Budget)
- Config map and Secret mounted as environments variables

### Bad pods
Get access to the host via this bad-pods.

```
# Everthing enabled
kubectl apply -f manifests/bad-pod1/deployment.yaml
kubectl exec -it bad-pod-xxxxx -- chroot /host bash

# privileged: true, hostPID: true
kubectl apply -f manifests/bad-pod2/deployment2.yaml
kubectl exec -it bad-pod2-xxxxx -- bash
```

## Helm

### AWS Load Balancer Controller

```
kubectl create namespace ingress

helm repo add eks https://aws.github.io/eks-charts

helm upgrade -i -n ingress aws-load-balancer-controller eks/aws-load-balancer-controller --values helm/aws-load-balancer-controller/values.yaml
```

### Cluster Autoscaler
Requirements
- AWS IAM role and policy
- Complete the annotation `eks.amazonaws.com/role-arn`

```
helm repo add autoscaler https://kubernetes.github.io/autoscaler
helm repo update

helm upgrade -i -n kube-system cluster-autoscaler autoscaler/cluster-autoscaler --values helm/cluster-autoscaler/values.yaml
```

### ExternaDNS

```
helm repo add bitnami https://charts.bitnami.com/bitnami

helm upgrade -i -n kube-system external-dns bitnami/external-dns --values helm/external-dns/values.yaml
```

## Nvidia device plugin

```
helm repo add nvdp https://nvidia.github.io/k8s-device-plugin

helm upgrade -i -n kube-system nvidia-device-plugin nvdp/nvidia-device-plugin --values helm/nvidia-device-plugin/values.yaml
```

### Prometheus
Requirements
- metric-server

https://github.com/prometheus-community/helm-charts/

```
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update

kubectl create ns prometheus

helm upgrade -i -n prometheus prometheus prometheus-community/prometheus --values helm/prometheus/values.yaml
```

### Grafana
https://github.com/grafana/helm-charts

```
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update

kubectl create ns prometheus

helm upgrade -i -n prometheus grafana grafana/grafana --values helm/grafana/values.yaml
```

### Prometheus M$Teams
https://github.com/prometheus-msteams/prometheus-msteams

```
helm repo add prometheus-msteams https://prometheus-msteams.github.io/prometheus-msteams/
helm repo update

kubectl create ns prometheus

helm upgrade -i -n prometheus prometheus-msteams prometheus-msteams/prometheus-msteams --values helm/prometheus-msteams/values.yaml
```

