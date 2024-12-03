---

## Day 27: Kubernetes Pod Disruption Budgets — Ensuring Application Availability

### 📘 Overview

**Pod Disruption Budgets (PDBs)** in Kubernetes help maintain application availability during voluntary disruptions, such as node maintenance or rolling updates. A PDB specifies the minimum or maximum number of pods that must remain available, ensuring critical applications are not entirely disrupted.

---

### What is a Pod Disruption Budget?

1. **Purpose**:
   - Define limits for voluntary disruptions (e.g., draining nodes or rolling updates).
   - Ensure the application remains operational while accommodating maintenance tasks.

2. **Voluntary vs. Involuntary Disruptions**:
   - **Voluntary**: User-initiated actions like node drain or scaling.
   - **Involuntary**: Unexpected failures like crashes or hardware issues.

3. **PDB Specification**:
   - `minAvailable`: Minimum number of pods that must remain running.
   - `maxUnavailable`: Maximum number of pods that can be unavailable at a time.

---
