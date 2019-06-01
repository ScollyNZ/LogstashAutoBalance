### Process

1. Set up a Kafka cluster in Kubernetes to interogate API for c-lag or c-group offsets per topic
   1. Aim to be able to list all topics, all c-groups connected to those topics and what the current c-lag is

   1. Deploy test-lofstash-autoscale.yaml into a K8s cluster

   1. first deployment accepted by Azure (tagged) but in crash loop

   1. reduced memory requirements, all good now. Kafka binaries are in /opt/bitnami/kafka/bin

   1. Create kafka topic via container shell

```bash
/opt/bitnami/kafka/bin$ ./kafka-topics.sh --create --bootstrap-server localhost:9092 --topic tenant1_device1 --partitions 5 --replication-factor 1

   /opt/bitnami/kafka/bin$ ./kafka-topics.sh --bootstrap-server localhost:9092 --list
   tenant1_device1
```

1. insert some messages into the topic

```bash
/opt/bitnami/kafka/bin$ ./kafka-console-producer.sh --broker-list localhost:9092 --topic tenant1_device1
>msg 1
>msg 2
>msg 3
>msg 4
```

1. Consume some messages so that a consumer group is created

```bash
/opt/bitnami/kafka/bin$ ./kafka-consumer-groups.sh --bootstrap-server localhost:9092 -list
```

1. Check consumer group has been created

```bash
/opt/bitnami/kafka/bin$ ./kafka-consumer-groups.sh --bootstrap-server localhost:9092 -list
console-consumer-88455
```

1. View offsets for all partitions

```bash
/opt/bitnami/kafka/bin$ ./kafka-consumer-groups.sh --bootstrap-server localhost:9092 -describe -group console-consumer-3241

TOPIC           PARTITION  CURRENT-OFFSET  LOG-END-OFFSET  LAG             CONSUMER-ID                                     HOST            CLIENT-ID
tenant1_device1 1          -               1               -               consumer-1-f19fa5c3-4879-4d2a-b324-817dfbb449a3 /10.240.0.15    consumer-1
tenant1_device1 0          -               0               -               consumer-1-f19fa5c3-4879-4d2a-b324-817dfbb449a3 /10.240.0.15    consumer-1
tenant1_device1 3          -               1               -               consumer-1-f19fa5c3-4879-4d2a-b324-817dfbb449a3 /10.240.0.15    consumer-1
tenant1_device1 2          -               1               -               consumer-1-f19fa5c3-4879-4d2a-b324-817dfbb449a3 /10.240.0.15    consumer-1
tenant1_device1 4          -               1               -               consumer-1-f19fa5c3-4879-4d2a-b324-817dfbb449a3 /10.240.0.15    consumer-1
```

1. Deployment .yaml at v0.2 tag deploys kafka, zookeeper, sqllist and python and keeps the alive for interactivity

1. Now to play with the kafka client library on the python node

```bash
$> kubectl exec -it tasking-manager-58df495f7c-9v8l7 -c monitoring-engine -- /bin/bash

/# pip install kafka-python
/# python
/# from kafka import KafkaConsumer
/# consumer = KafkaConsumer(bootstrap_servers=['10.240.0.10:9092'], group_id='consumer-1')
/# consumer.topics()

```

