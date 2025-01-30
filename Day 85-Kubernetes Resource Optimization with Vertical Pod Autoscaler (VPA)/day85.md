---

## Day 85: Kubernetes Resource Optimization with Vertical Pod Autoscaler (VPA)

### 📘 Overview

Kubernetes applications require proper resource allocation to run efficiently. **Vertical Pod Autoscaler (VPA)** automatically adjusts CPU and memory requests for pods based on usage patterns, helping optimize resource allocation. This session covers understanding VPA, configuring it to optimize resource usage, and best practices for balancing cost and performance.

---


### Objectives  

1. Understand how **Vertical Pod Autoscaler (VPA)** works and its differences from **Horizontal Pod Autoscaler (HPA)**.  
2. Install and configure VPA for dynamic resource adjustments.  
3. Apply best practices for optimizing cost and performance in Kubernetes.  

---

### Key Concepts  

1. **Vertical vs. Horizontal Autoscaling:**  
   - **Vertical Pod Autoscaler (VPA):** Adjusts the CPU and memory requests of a pod dynamically.  
   - **Horizontal Pod Autoscaler (HPA):** Adjusts the number of pod replicas based on CPU/memory utilization or custom metrics.  

2. **Why Use VPA?**  
   - Prevents resource starvation or over-provisioning.  
   - Helps right-size pods based on actual usage trends.  
   - Improves cost efficiency by optimizing resource requests.  

3. **VPA Components:**  
   - **Recommender:** Suggests CPU/memory resource adjustments based on historical data.  
   - **Updater:** Restarts pods to apply new resource recommendations.  
   - **Admission Controller:** Modifies new pod requests before scheduling based on VPA recommendations.  

---