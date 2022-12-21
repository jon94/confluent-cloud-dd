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
- Followed documentation to link Confluent Cloud to Datadog with API Key owned by Service Account with Metrics Viewer Role https://docs.datadoghq.com/integrations/confluent_cloud/#overview
```
while true; do sleep 1; curl http://127.0.0.1:XXXX/api/produce; echo -e '\n\n\n\n'$(date);done
```

##### Confluent Cloud Message Lag
![Billing   payment - Confluent Cloud 2022-12-21 at 10 29 10 AM](https://user-images.githubusercontent.com/40360784/208806978-7bc611a3-a7b5-486d-9b8d-11d29a8e038d.jpg)

##### Datadog Dashboard
- Observe that there is some lag when Datadog is obtaining these metrics from Confluent Cloud. (?) There must be some settings to increase the scrap frequency.
![image](https://user-images.githubusercontent.com/40360784/208806926-d0df0faf-601f-45d2-b386-24c13d463ed8.png)

##### Pods get autoscaled by KEDA HPA when lag > 50 (as per settings in manifest)
![jonathan lim — kubectl get po -w — 214×64 2022-12-21 at 10 35 22 AM](https://user-images.githubusercontent.com/40360784/208807211-8da119a2-42ed-44d4-9038-858c0cb4da98.jpg)

##### Pods get scaled down by KEDA HPA to 1 when lag is back to healthy range
![image](https://user-images.githubusercontent.com/40360784/208808247-b93d6662-865f-47b6-b365-5c2e9ca8c46a.png)
![image](https://user-images.githubusercontent.com/40360784/208808302-7d5eed1c-af09-490d-8cc7-e90df2b10b7a.png)

##### Datadog Dashboard
![image](https://user-images.githubusercontent.com/40360784/208808481-e9588e96-17cf-43c8-b711-387b32c86d69.png)
![image](https://user-images.githubusercontent.com/40360784/208808538-e557bd6f-8381-436e-ac71-d044cda1f857.png)
![image](https://user-images.githubusercontent.com/40360784/208808582-802fc810-00ef-475d-8f41-f854eecc7249.png)
