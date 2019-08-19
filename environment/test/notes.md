Development Steps
1. Find message generator, needs to be configurable for events per second
[This one](https://github.com/Kappaware/kfaker) provides a message delay setting, so should be controllable, up to a limit of 1000 eps

2. Install / start Kafka
```
kubectl apply -f environment/test/test-logstash-autoscale.yaml
```

confirm Kafka is running

```
$ kubectl get pods
NAME                              READY   STATUS    RESTARTS   AGE
basic-kafka-669cfdc777-fk45x      2/2     Running   1          3m27s
kafka-manager-6d8fd468dd-74q8x    1/1     Running   0          3m27s
tasking-manager-5f446bf8b-rq6v7   2/2     Running   0          3m26s

$ kubectl exec -it basic-kafka-669cfdc777-fk45x -- /bin/bash

$ kafka-topics.sh --create --bootstrap-server localhost:9092 --replication-factor 1 --partitions 1 --topic test
$ kafka-topics.sh --list
test

```





3. Install Kafka manager so that c-lag and EPS are visible



Kubernetes Python Client Libraries are available [here](https://github.com/kubernetes-client/python).

Notes:

Curl can be added with
```
FROM openjdk:8-jre-alpine

RUN apk --no-cache add curl
```

Auth for connecting to Azure Kubernetes Service
```
az aks get-credentials --resource-group groupname --name clustername
```
