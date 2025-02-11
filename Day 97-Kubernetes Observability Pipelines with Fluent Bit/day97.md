---

## Day 97: Kubernetes Observability Pipelines with Fluent Bit

### 📘 Overview

Efficient log management is vital for monitoring and troubleshooting in Kubernetes environments. **Fluent Bit** is a lightweight and high-performance log processor and forwarder designed to collect, filter, and transform logs from Kubernetes clusters. In this session, you'll learn how to set up Fluent Bit for log aggregation, apply filtering and transformation rules to streamline log data, and integrate with backend systems such as Elasticsearch, Loki, or Splunk for storage and analysis.

---


### Objectives

1. **Set Up Fluent Bit for Log Aggregation:**  
   - Deploy Fluent Bit in your Kubernetes cluster to collect logs from various sources.

2. **Filter and Transform Logs:**  
   - Apply rules to filter out noise and transform log data for better readability and relevance before forwarding.

3. **Integrate with Backend Systems:**  
   - Configure Fluent Bit to send processed logs to Elasticsearch, Loki, or Splunk, enabling centralized observability and analysis.

---

### Key Concepts

1. **Log Aggregation:**  
   - Centralizing log data from multiple sources to streamline monitoring and troubleshooting.

2. **Fluent Bit:**  
   - A lightweight log processor that can collect, filter, and forward logs efficiently.
   - Supports various input plugins (tailing log files, Kubernetes metadata) and output plugins (Elasticsearch, Loki, Splunk).

3. **Filtering and Transformation:**  
   - Use built-in filters to parse, modify, and enrich logs, removing unwanted information and structuring data for analysis.

4. **Backend Integration:**  
   - Forward logs to systems like **Elasticsearch** for powerful search and analytics, **Loki** for log aggregation with Prometheus-style labels, or **Splunk** for enterprise-level log analysis.

---


### Practical Exercises: Building an Observability Pipeline with Fluent Bit

#### 1. Setting Up Fluent Bit in Kubernetes

##### Step 1: Deploy Fluent Bit Using Helm
Install Fluent Bit with Helm:
```bash
helm repo add fluent https://fluent.github.io/helm-charts
helm repo update
helm install fluent-bit fluent/fluent-bit --namespace logging --create-namespace
```
Verify the deployment:
```bash
kubectl get pods -n logging -l app.kubernetes.io/name=fluent-bit
```

##### Step 2: Configure Fluent Bit Inputs
Configure Fluent Bit to tail container logs by mounting the host's log directory and reading Kubernetes metadata:
```yaml
# Example Fluent Bit ConfigMap snippet for inputs
apiVersion: v1
kind: ConfigMap
metadata:
  name: fluent-bit-config
  namespace: logging
data:
  fluent-bit.conf: |
    [SERVICE]
        Flush         1
        Daemon        Off
        Log_Level     info
        Parsers_File  parsers.conf

    [INPUT]
        Name          tail
        Path          /var/log/containers/*.log
        Parser        docker
        Tag           kube.*
        Refresh_Interval 5
        DB            /var/log/flb_kube.db
```
Apply the ConfigMap:
```bash
kubectl apply -f fluent-bit-config.yaml
```

---

#### 2. Filtering and Transforming Logs

##### Step 1: Use Filters to Enrich Logs
Add filters to parse and transform log data. For example, add a record modifier to tag logs with Kubernetes namespace and pod name:
```yaml
# Example filter configuration in fluent-bit.conf
[FILTER]
    Name                kubernetes
    Match               kube.*
    Kube_URL            https://kubernetes.default.svc:443
    Kube_CA_File        /var/run/secrets/kubernetes.io/serviceaccount/ca.crt
    Kube_Token_File     /var/run/secrets/kubernetes.io/serviceaccount/token
    Merge_Log           On
    Keep_Log            Off
```

##### Step 2: Parse and Format Log Messages
Utilize the parser to structure logs. In `parsers.conf`, define a parser for Docker logs:
```yaml
# Example parser configuration
[PARSER]
    Name         docker
    Format       json
    Time_Key     time
    Time_Format  %Y-%m-%dT%H:%M:%S.%L
```

---

#### 3. Integrating with Elasticsearch, Loki, or Splunk

##### Step 1: Configure Output Plugin for Elasticsearch
Configure Fluent Bit to forward logs to Elasticsearch:
```yaml
# Add this output configuration in fluent-bit.conf
[OUTPUT]
    Name            es
    Match           *
    Host            elasticsearch.example.com
    Port            9200
    Index           kubernetes-logs
    Type            _doc
    Logstash_Format On
```

##### Step 2: (Alternative) Configure Output for Loki
For Loki, adjust the output configuration accordingly:
```yaml
[OUTPUT]
    Name            loki
    Match           *
    Host            loki.example.com
    Port            3100
    Labels          {job="fluent-bit", cluster="production"}
    Line_Format     json
```

##### Step 3: (Alternative) Configure Output for Splunk
For Splunk integration, use the Splunk HEC output:
```yaml
[OUTPUT]
    Name            splunk
    Match           *
    Host            splunk.example.com
    Port            8088
    Splunk_Token    YOUR_SPLUNK_HEC_TOKEN
    Splunk_Send_Raw On
```

---