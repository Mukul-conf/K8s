<img src="https://r2cdn.perplexity.ai/pplx-full-logo-primary-dark%402x.png" style="height:64px;margin-right:32px"/>

# this is not one block give one block for the entire file

Here is the **complete markdown for the Kafka-on-Kubernetes Lab** as a single block.
Copy everything inside this box and save it as `kafka-on-kubernetes-lab.md` to see the full preview in any markdown-supported editor:

```
# 🥳 Kafka-on-Kubernetes Lab: Open Source Kafka Fun! (Kid Friendly)

**Duration:** 45 minutes  
**Difficulty:** Beginner/Easy  
**What you'll learn:** How to run Apache Kafka and Zookeeper on Kubernetes using Open Source images, like a real data messenger!

---

## ✨ What is Apache Kafka?

Imagine you have a magical post office that passes messages between your friends—fast and safely! Kafka is just like that: a messaging system that lets computers send messages to each other super quickly. When you combine Kafka and Kubernetes, you get a magical mail system that never sleeps!

---

## 🚦 What You'll Need
- A computer with internet
- Minikube installed (for your tiny Kubernetes cluster)
- kubectl installed (you've already learned this!)
- Basic YAML knowledge from your first lab

## 📚 Fast Fact: Terms for Today
- **Kafka Broker**: Like a magical postman who stores and delivers messages
- **Zookeeper**: The friendly animal-sitter who helps keep all the postmen (brokers) in sync
- **Topic**: The name of the mailbox for sending/receiving messages
- **Producer**: Someone who sends a message
- **Consumer**: Someone who gets a message

---

## 🎪 Assignment 2: Run Open Source Kafka & Send Your First Message

### Step 1: Start Minikube (2 minutes)
```

minikube start

```

### Step 2: Deploy Zookeeper (8 minutes)
Create a file called `zookeeper.yaml`:
```

apiVersion: apps/v1
kind: Deployment
metadata:
name: zookeeper
spec:
replicas: 1
selector:
matchLabels:
app: zookeeper
template:
metadata:
labels:
app: zookeeper
spec:
containers:
- name: zookeeper
image: wurstmeister/zookeeper:3.4.6
ports:
- containerPort: 2181
***
apiVersion: v1
kind: Service
metadata:
name: zookeeper-service
spec:
selector:
app: zookeeper
ports:

- port: 2181
targetPort: 2181

```
Apply it:
```

kubectl apply -f zookeeper.yaml
kubectl get pods

```
Wait for the `zookeeper` pod to be `Running`.

### Step 3: Deploy Kafka Broker (10 minutes)
Create a file called `kafka.yaml`:
```

apiVersion: apps/v1
kind: Deployment
metadata:
name: kafka
spec:
replicas: 1
selector:
matchLabels:
app: kafka
template:
metadata:
labels:
app: kafka
spec:
containers:
- name: kafka
image: wurstmeister/kafka:2.13-2.8.0
env:
- name: KAFKA_BROKER_ID
value: "1"
- name: KAFKA_ZOOKEEPER_CONNECT
value: "zookeeper-service:2181"
- name: KAFKA_ADVERTISED_LISTENERS
value: "PLAINTEXT://kafka-service:9092"
- name: KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR
value: "1"
ports:
- containerPort: 9092
***
apiVersion: v1
kind: Service
metadata:
name: kafka-service
spec:
selector:
app: kafka
ports:

- port: 9092
targetPort: 9092

```
Apply it:
```

kubectl apply -f kafka.yaml
kubectl get pods

```
Wait for the `kafka` pod to be `Running`.

### Step 4: Test Kafka With a Client Pod (10 minutes)
Create a file called `kafka-client.yaml`:
```

apiVersion: v1
kind: Pod
metadata:
name: kafka-client
spec:
containers:

- name: kafka-client
image: wurstmeister/kafka:2.13-2.8.0
command: ["sleep", "infinity"]

```
Apply and connect:
```

kubectl apply -f kafka-client.yaml
kubectl exec -it kafka-client -- /bin/bash

```
Inside the shell, try:
```


# Create a topic

kafka-topics.sh --create --bootstrap-server kafka-service:9092 --replication-factor 1 --partitions 1 --topic magic-topic

# List topics

kafka-topics.sh --bootstrap-server kafka-service:9092 --list

# Start a producer (type messages, press Ctrl-C to stop)

kafka-console-producer.sh --broker-list kafka-service:9092 --topic magic-topic
>> hello
>> world

# Open a new terminal (another bash shell into the same pod)

kubectl exec -it kafka-client -- /bin/bash

# Listen as a consumer

kafka-console-consumer.sh --bootstrap-server kafka-service:9092 --topic magic-topic --from-beginning

```
Watch your messages appear like magic!

### Step 5: Add Schema Registry (15 minutes)
Create a file called `schema-registry.yaml`:
```

apiVersion: apps/v1
kind: Deployment
metadata:
name: schema-registry
spec:
replicas: 1
selector:
matchLabels:
app: schema-registry
template:
metadata:
labels:
app: schema-registry
spec:
containers:
- name: schema-registry
image: confluentinc/cp-schema-registry:7.0.0
env:
- name: SCHEMA_REGISTRY_KAFKASTORE_BOOTSTRAP_SERVERS
value: "kafka-service:9092"
- name: SCHEMA_REGISTRY_HOST_NAME
value: "schema-registry"
ports:
- containerPort: 8081
***
apiVersion: v1
kind: Service
metadata:
name: schema-registry-service
spec:
selector:
app: schema-registry
ports:

- port: 8081
targetPort: 8081

```

Apply it:
```

kubectl apply -f schema-registry.yaml
kubectl get pods

```

Test the Schema Registry:
```


# Test from inside kafka-client

kubectl exec -it kafka-client -- /bin/bash

# Check if Schema Registry is working

curl http://schema-registry-service:8081/subjects

```

---

## 🧹 Don't Forget to Clean Up!
```

kubectl delete deployment kafka
kubectl delete deployment zookeeper
kubectl delete deployment schema-registry
kubectl delete pod kafka-client
kubectl delete service kafka-service
kubectl delete service zookeeper-service
kubectl delete service schema-registry-service

```

---

## 🧠 What Did You Learn?
- How to run open-source Kafka and Zookeeper on Kubernetes
- How to send and receive messages using topics
- That Kafka is like a magical postal system for computers
- How to add Schema Registry for data validation
- Kubernetes makes it super easy to control, heal, and scale your magic!

---

## 🤔 Extra Challenges
- Try running two Kafka Brokers instead of one!
- What if Zookeeper pod is deleted? What happens?
- Can you make a different topic for your favorite animal?
- Can you create a JSON schema and register it?

---

## 🎯 Advanced Learning Path
After mastering this lab, you can explore:
1. **Multi-broker Kafka clusters**
2. **Kafka partitioning and replication**
3. **Avro schemas with Schema Registry**
4. **Kafka Connect connectors**
5. **Kafka Streams applications**
6. **Production-ready Kafka on Kubernetes with operators**

---

## 🔧 Troubleshooting Tips
**Pods not starting?**
```

kubectl describe pod [pod-name]
kubectl logs [pod-name]

```

**Can't connect to Kafka?**
- Check if all pods are running
- Verify service names in YAML files
- Make sure Zookeeper is running before Kafka

**Services not accessible?**
```

kubectl get services
kubectl describe service [service-name]

```

---

## 😃 Remember! Everyone can be a data magician with Kafka and Kubernetes!

Welcome to the world of distributed data streaming! 🌟
```

Copy all of the above (from the FIRST to the LAST line) and paste in your markdown editor to see the full preview, or upload as a file.

