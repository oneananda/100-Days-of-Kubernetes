---

## Day 76: Kubernetes Event-Driven Autoscaling (KEDA) for Serverless Workloads

### 📘 Overview

**Kubernetes Event-Driven Autoscaling (KEDA)** extends Kubernetes' native scaling capabilities by enabling event-driven autoscaling. KEDA allows workloads to scale based on metrics from external event sources like **Kafka**, **RabbitMQ**, and **Azure Queue**, making it an ideal solution for building serverless architectures on Kubernetes.

---

### Objectives

1. Understand the role of KEDA in enabling event-driven scaling.  
2. Learn to set up KEDA in a Kubernetes cluster.  
3. Scale workloads dynamically based on external event sources.  
4. Explore use cases for serverless architectures in Kubernetes.  

---

### Key Concepts

1. **KEDA:**  
   - A Kubernetes-based solution for event-driven autoscaling.  
   - Monitors event sources and scales workloads based on custom metrics.  

2. **Event Sources:**  
   - Includes Kafka, RabbitMQ, Azure Queue, AWS SQS, Prometheus, and more.  

3. **Serverless Workloads:**  
   - Workloads that scale to zero during inactivity and dynamically scale when events are received.  

---


### Practical Exercises: Deploying and Using KEDA

---

#### 1. Setting Up KEDA

##### Step 1: Install KEDA
Deploy KEDA using Helm:
```bash
helm repo add kedacore https://kedacore.github.io/charts
helm repo update
helm install keda kedacore/keda --namespace keda --create-namespace
```

Verify the installation:
```bash
kubectl get pods -n keda
```

##### Step 2: Confirm KEDA Components
Check that the KEDA Operator is running:
```bash
kubectl get deployment keda-operator -n keda
```

---

#### 2. Scaling Workloads Based on an Event Source

##### Step 1: Deploy an Event Source (Kafka)
Set up a Kafka cluster using Strimzi:
```bash
kubectl create namespace kafka
kubectl apply -f https://strimzi.io/install/latest?namespace=kafka
kubectl apply -f https://strimzi.io/examples/latest/kafka/kafka-persistent-single.yaml -n kafka
```

Verify Kafka pods:
```bash
kubectl get pods -n kafka
```

##### Step 2: Deploy a Sample Consumer Application
Create a `consumer-deployment.yaml`:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: kafka-consumer
  labels:
    app: kafka-consumer
spec:
  replicas: 0
  selector:
    matchLabels:
      app: kafka-consumer
  template:
    metadata:
      labels:
        app: kafka-consumer
    spec:
      containers:
      - name: consumer
        image: my-kafka-consumer:latest
        env:
        - name: KAFKA_BROKER
          value: my-cluster-kafka-bootstrap.kafka:9092
        - name: KAFKA_TOPIC
          value: my-topic
```

Apply the deployment:
```bash
kubectl apply -f consumer-deployment.yaml
```

##### Step 3: Create a KEDA ScaledObject
Define a `scaledobject.yaml`:
```yaml
apiVersion: keda.sh/v1alpha1
kind: ScaledObject
metadata:
  name: kafka-consumer-scaler
  namespace: default
spec:
  scaleTargetRef:
    name: kafka-consumer
  minReplicaCount: 0
  maxReplicaCount: 10
  triggers:
  - type: kafka
    metadata:
      bootstrapServers: my-cluster-kafka-bootstrap.kafka:9092
      topic: my-topic
      consumerGroup: my-consumer-group
      lagThreshold: "5"
```

Apply the ScaledObject:
```bash
kubectl apply -f scaledobject.yaml
```

---

#### 3. Testing Event-Driven Scaling

##### Step 1: Produce Messages to Kafka
Create a producer pod to send messages:
```bash
kubectl run kafka-producer --image=strimzi/kafka:latest-kafka-3.2.0 --restart=Never -- \
  kafka-console-producer.sh --broker-list my-cluster-kafka-bootstrap.kafka:9092 --topic my-topic
```

Send messages:
```bash
> {"message": "Hello, KEDA!"}
> {"message": "Scaling with events"}
```

##### Step 2: Observe Scaling Behavior
Check the number of replicas for the consumer:
```bash
kubectl get deployment kafka-consumer
```

Verify pod logs to confirm message processing:
```bash
kubectl logs -l app=kafka-consumer
```

---

#### 4. Exploring Serverless Use Cases

1. **Queue Processing:**  
   - Automatically scale workers based on the number of messages in a queue.  

2. **Log Aggregation:**  
   - Scale applications that aggregate and process logs from external sources.  

3. **Real-Time Data Processing:**  
   - Dynamically scale services that handle streaming data, such as video feeds or IoT telemetry.  

4. **Cost Optimization:**  
   - Use KEDA to scale workloads to zero during inactivity, reducing infrastructure costs.  

---

#### Cleanup

Remove all resources:
```bash
kubectl delete -f scaledobject.yaml
kubectl delete -f consumer-deployment.yaml
kubectl delete namespace kafka
helm uninstall keda -n keda
kubectl delete namespace keda
```

---


### Best Practices for Using KEDA

1. **Choose the Right Event Source:**  
   - Select supported event sources that align with your application architecture.  

2. **Set Sensible Scaling Parameters:**  
   - Define appropriate thresholds and replica limits to prevent over-scaling.  

3. **Monitor Autoscaling Behavior:**  
   - Use tools like Prometheus and Grafana to monitor metrics and scaling events.  

4. **Integrate with CI/CD:**  
   - Automate KEDA configuration updates as part of your deployment pipeline.  

---


### 📝 Document Your Progress

In your `day76.md` file, include:  
- Steps for setting up KEDA and event-driven scaling.  
- Test results of scaling based on external event sources.  
- Observations on serverless use cases and scaling performance.  

---

### 🎯 Outcome for Day 76

By the end of this session, you will:  
1. Understand event-driven autoscaling and its benefits.  
2. Deploy and configure KEDA in a Kubernetes cluster.  
3. Scale workloads dynamically based on external event sources.  

---

### 🔗 Additional Resources

- [KEDA Official Documentation](https://keda.sh/docs/)  
- [Supported Event Sources](https://keda.sh/docs/latest/scalers/)  
- [Kubernetes Autoscaling](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/)  
- [Strimzi Kafka Operator](https://strimzi.io/docs/)  

---