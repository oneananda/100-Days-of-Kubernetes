---

## Day 86: Kubernetes Observability with Thanos  

### 📘 Overview  

**Observability** is crucial for managing and troubleshooting Kubernetes clusters. **Thanos** extends **Prometheus** by providing long-term metric storage, global querying, and high availability across multiple clusters. This session focuses on understanding Thanos, configuring it for long-term storage, and enabling multi-cluster observability with **Thanos Querier**.

---


### Objectives  

1. Understand **Thanos** and how it enhances Prometheus for large-scale observability.  
2. Set up **Thanos components** for long-term storage and high availability.  
3. Configure **Thanos Querier** to enable **multi-cluster metric aggregation**.  

---

### Key Concepts  

1. **Why Use Thanos?**  
   - Scales Prometheus horizontally by enabling multiple instances to work together.  
   - Provides **long-term storage** for metrics, overcoming Prometheus’s retention limits.  
   - Enables **multi-cluster observability**, allowing centralized monitoring of different Kubernetes environments.  

2. **Thanos Components:**  
   - **Thanos Sidecar:** Attaches to Prometheus to enable external storage and query federation.  
   - **Thanos Store:** Retrieves historical metrics from long-term storage (S3, GCS, etc.).  
   - **Thanos Compactor:** Optimizes and deduplicates stored metrics for efficient querying.  
   - **Thanos Query:** A single-pane-of-glass UI for querying Prometheus instances across multiple clusters.  

3. **Long-Term Storage:**  
   - Stores metrics in **object storage** (e.g., AWS S3, Google Cloud Storage) for infinite retention.  

---