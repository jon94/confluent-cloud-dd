# confluent-cloud-dd

## Set up
### Kubernetes
Used Minikube
To get endpoint for minikube 
```
minikube service dep --url
```

### Keda
Reference: 
- https://keda.sh/docs/2.7/deploy/
- https://keda.sh/docs/2.8/scalers/apache-kafka/#example
```
helm repo add kedacore https://kedacore.github.io/charts
helm repo update
kubectl create namespace keda
helm install keda kedacore/keda --namespace keda
```

## Observations
### Kafka
1. Spammed produce endpoint 
```
http://127.0.0.1:56825/api/produce
```
