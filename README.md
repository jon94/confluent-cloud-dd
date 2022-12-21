# confluent-cloud-dd

## Set Up

### Docker
Image locally built and pushed to docker hub.
Note that this image was built on M1 macbook pro, and it might not be compatible for machine running on intel based chip.
```
docker build -t jonathanlim799/kafkapoc --platform linux/amd64

docker login

docker push

https://hub.docker.com/repository/docker/jonathanlim799/kafkapoc
```

### Kubernetes
Used Minikube for cluster
To get endpoint for minikube - in order to hit /api/produce to produce message in Kafka Topic.
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
#### Case 1: Using Datadog to correlate KEDA HPA and Consumer Lag 
```
while true; do sleep 1; curl http://127.0.0.1:XXXX/api/produce; echo -e '\n\n\n\n'$(date);done
```
