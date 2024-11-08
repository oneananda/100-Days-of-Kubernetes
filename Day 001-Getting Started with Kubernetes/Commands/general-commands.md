# General Verification Commands

1. **List all the nodes (or worker machines) in the cluster**

   ```bash
   kubectl get nodes
   ```
    **Information shown**
   ```
	NAME: The name of each node in the cluster.
	STATUS: The current status of the node (e.g., Ready, NotReady).
	ROLES: Indicates the role of the node (e.g., master, worker).
	AGE: The duration since the node was added to the cluster.
	VERSION: The version of Kubernetes the node is running.
	```
2. **Check Cluster Information**:

   ```bash
   kubectl cluster-info
   ```
