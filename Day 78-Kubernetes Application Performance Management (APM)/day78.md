---

## Day 78: Kubernetes Application Performance Management (APM)

### 📘 Overview

Application Performance Management (APM) is essential for monitoring and optimizing the performance of distributed applications running on Kubernetes. Tools like **Jaeger**, **OpenTelemetry**, and **Elastic APM** provide distributed tracing and detailed insights into application behavior. This session focuses on setting up APM tools, monitoring application performance, and identifying bottlenecks in Kubernetes.

---


### Objectives

1. Understand the role of APM tools like Jaeger, OpenTelemetry, and Elastic APM in Kubernetes.  
2. Set up distributed tracing for monitoring application performance.  
3. Debug performance slowdowns and identify bottlenecks in distributed systems.  

---

### Key Concepts

1. **Application Performance Management (APM):**  
   - Tools and practices used to monitor, diagnose, and optimize application performance in real time.  

2. **Distributed Tracing:**  
   - Tracks requests as they traverse multiple services in a distributed system.  

3. **Key APM Tools:**  
   - **Jaeger:** An open-source tool for end-to-end distributed tracing.  
   - **OpenTelemetry:** A framework for generating and exporting telemetry data like metrics, logs, and traces.  
   - **Elastic APM:** Provides full-stack observability, including tracing, metrics, and logs.  

---


### Practical Exercises: Setting Up APM in Kubernetes

---

#### 1. Deploying Jaeger for Distributed Tracing

##### Step 1: Deploy Jaeger in Kubernetes
Install Jaeger using Helm:
```bash
helm repo add jaegertracing https://jaegertracing.github.io/helm-charts
helm repo update
helm install jaeger jaegertracing/jaeger --namespace observability --create-namespace
```

Verify the deployment:
```bash
kubectl get pods -n observability
```

##### Step 2: Expose Jaeger UI
Expose the Jaeger UI as a service:
```bash
kubectl port-forward -n observability service/jaeger-query 16686:16686
```

Access the UI at [http://localhost:16686](http://localhost:16686).

---

#### 2. Instrumenting Applications with OpenTelemetry

##### Step 1: Deploy OpenTelemetry Collector
Install OpenTelemetry Collector:
```bash
kubectl apply -f https://github.com/open-telemetry/opentelemetry-collector-releases/releases/latest/download/otel-collector.yaml
```

Verify the deployment:
```bash
kubectl get pods -n opentelemetry-collector
```

##### Step 2: Configure Application Tracing
Inject OpenTelemetry SDK into your application code. Example for Python:
```bash
pip install opentelemetry-api opentelemetry-sdk opentelemetry-exporter-jaeger
```

Configure the application to export traces:
```python
from opentelemetry import trace
from opentelemetry.sdk.trace import TracerProvider
from opentelemetry.exporter.jaeger.thrift import JaegerExporter
from opentelemetry.sdk.trace.export import BatchSpanProcessor

trace.set_tracer_provider(TracerProvider())
jaeger_exporter = JaegerExporter(
    agent_host_name="jaeger-agent.observability",
    agent_port=6831,
)
span_processor = BatchSpanProcessor(jaeger_exporter)
trace.get_tracer_provider().add_span_processor(span_processor)

tracer = trace.get_tracer(__name__)

with tracer.start_as_current_span("example-request"):
    print("Tracing this request...")
```

Deploy the application with tracing enabled.

---

#### 3. Monitoring with Elastic APM

##### Step 1: Deploy Elastic APM Server
Install Elastic APM Server using Helm:
```bash
helm repo add elastic https://helm.elastic.co
helm repo update
helm install apm-server elastic/apm-server --namespace observability --create-namespace
```

Verify the deployment:
```bash
kubectl get pods -n observability
```

##### Step 2: Configure Application for APM
Add the Elastic APM agent to your application. Example for Node.js:
```bash
npm install elastic-apm-node --save
```

Configure the APM agent in your application:
```javascript
const apm = require('elastic-apm-node').start({
  serviceName: 'my-node-app',
  serverUrl: 'http://apm-server.observability.svc:8200',
});
```

Deploy the application with Elastic APM instrumentation.

---

#### 4. Debugging Performance Issues

##### Step 1: Use Jaeger for Distributed Tracing
Trace a request through multiple services in Jaeger:
1. Access the Jaeger UI.  
2. Search for traces by service name.  
3. Analyze the span durations and dependencies to identify bottlenecks.

##### Step 2: Monitor Application Metrics in Elastic APM
Use the Elastic APM UI to:
1. Identify slow transactions and database queries.  
2. View application errors and exceptions.  
3. Monitor key performance indicators (KPIs) like latency and throughput.

##### Step 3: Use OpenTelemetry for Custom Metrics
Configure OpenTelemetry to emit custom metrics:
```python
from opentelemetry.metrics import MeterProvider
from opentelemetry.sdk.metrics import MeterProvider

meter_provider = MeterProvider()
meter = meter_provider.get_meter(__name__)
counter = meter.create_counter("requests_count", unit="1", description="Counts incoming requests")
counter.add(1)
```

Visualize metrics in tools like Prometheus or Grafana.

---

#### Cleanup

Remove the installed components:
```bash
helm uninstall jaeger -n observability
helm uninstall apm-server -n observability
kubectl delete -f https://github.com/open-telemetry/opentelemetry-collector-releases/releases/latest/download/otel-collector.yaml
kubectl delete namespace observability
```

---


### Best Practices for APM in Kubernetes

1. **Instrument All Critical Services:**  
   - Ensure every service in your application is instrumented for tracing.  

2. **Leverage Distributed Tracing:**  
   - Use distributed tracing to monitor and optimize request flows across services.  

3. **Monitor Key Metrics:**  
   - Track latency, throughput, error rates, and resource utilization.  

4. **Integrate with CI/CD Pipelines:**  
   - Automate instrumentation testing and performance validation.  

---