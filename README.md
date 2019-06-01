# LogstashAutoBalance

Balance logstash workers for multiple Kafka topics in K8s cluster.

## Background

In a system that requires a high number of Kafka topics, as well as a high number of Logstash nodes to process those topics, the burden of managing the Logstash processes can be quite high.

This project is an an experiment to see how practical Kubernetes is for distributing processes and services dynamically loaded queues with a low overhead monitoring and tasking engine.

## Basic Design

1. Define Kafka topic pattern (attributes?) for affinity (is this needed? Probably not.
1. Query Kafka for list of topics, remove any topics that are in the ignore list
1. Query Elastic for list of pipelines, matching on naming convention
1. Launch a replica-set for each topic with 1 replica
1. Sample c-lag every 10 seconds
1. If average of last 5 samples < 10 and replica settings > 1, reduce replicas
1. If average of last 5 samples > 500 and replica settings < limit, add replica (replica limit would logically be the partition count)
1.1 if limit hit raise alert
1. If average of last 20 samples = 0, remove deployment
1. If last sample > 0 and replicas = 0, add replica

Two components - Monitoring and Action

Monitoring runs above logic and creates tasks
Action Engine accepts tasks and makes changes

Monitoring Engine Tasks

- Query Kafka
-- Query each Kafka server to retrieve queues
		○ Remove ignored topics
		○ Add any new topics, remove any topics that no longer exist
		○ Pull c-lag for c-group, by naming convention
		○ Store results
	• Make Decisions
		○ For each topic, determine actions to take
		○ Queue actions in Kafka topic
	• Monitor actions topics list
		○ Raise alert if c-lag greater than one for > 10s

Action Engine Tasks
	•  Add processor
		○ If no processors exist, create one
		○ If processors exist and limit not reached, add one
		○ If limit reached alert
		○ {action:
	•  Remove processor
		○ If no processor exists, do nothing
		○ If > min exists, remove one

Failure Modes:
	• Logstash Action Engine crash / dies
		○ Run multiple - kafka will take care of once delivery
	• Kafka Monitoring Engine failure
		○ Need a watchdog service or an alive probe on the container running it
	• Action Latency so same action issued again
		○ Will self-recover when c-lag builds

Deployment
	• Kafka cluster
	• Monitoring Engine - Python, gitRepo mounted under
	• Action Engine(s) - Python, gitRepo mounted under, also build alive probe for watch-dog
	• Email server? Alerting, Dashboarding
	• Azure log analytics for monitoring and alerting




______________________________
Each topic has consumer groups. Each partition has an offset for each consumer group
If a partition has a lag > 0 and the consumer has > 1 partitions, a new consumer can be added
If a topic has a unique consumer per partition, adding new consumers won't help

Need to know
For each topic
 For each Partition
  lag, consumer



