---

## Day 47: Kubernetes Rolling Updates with Zero Downtime — Practical Class

### 📘 Overview

**Rolling Updates** are a Kubernetes deployment strategy that updates pods in a controlled and sequential manner to ensure zero downtime. It replaces pods in small batches and monitors their readiness before proceeding, making it a safer way to deploy changes in production environments.

This session focuses on implementing Rolling Updates in Kubernetes, configuring strategies, and ensuring application availability during the process.

---

### Objectives

1. Understand the concept of Rolling Updates in Kubernetes.
2. Configure a Deployment for Rolling Updates.
3. Monitor the update process to ensure zero downtime.
4. Test rollback in case of issues during updates.

---

### Key Concepts

1. **Rolling Update**:
   - Updates a deployment incrementally while maintaining application availability.
   - Ensures old pods are terminated only after new pods are ready.

2. **Update Strategies**:
   - **MaxUnavailable**: Maximum number of pods that can be unavailable during the update.
   - **MaxSurge**: Maximum number of extra pods created during the update.

3. **Rollback**:
   - Reverts to a previous version if issues occur during the update.

---
