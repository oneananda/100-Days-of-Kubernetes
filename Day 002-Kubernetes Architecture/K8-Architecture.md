# K8-Architecture Flow

Explanation of each component in the Kubernetes architecture depicted in the [diagram](Day 002-Kubernetes Architecture/Screenshots/K8-Architecture.png):

1. **User**: Represents the person or application that interacts with the Kubernetes cluster, typically using `kubectl` or a web interface. Users communicate with the **API Server** to manage resources.

2. **Master Node**: Manages the entire cluster, ensuring that workloads are appropriately scheduled, desired state is maintained, and cluster state is persisted.
   - **API Server (apiserver)**: The central management component that exposes the Kubernetes API. It serves as the entry point for all administrative tasks. Users and system components interact with Kubernetes via the API Server.
   - **Scheduler (scheduler)**: The scheduler is responsible for assigning workloads to nodes based on resource availability and other constraints. It determines which worker node is best suited to run a particular Pod.
   - **Controller Manager (controller)**: A component responsible for maintaining the desired state of the cluster. It ensures that the specified number of Pods is running, and manages other control loops (e.g., replication controllers).
   - **etcd**: The key-value store used as the backend storage for all cluster data, including configuration, state, and metadata.

3. **Worker Node**: The node that actually runs the workloads. Each worker node has several components:
   - **Kubelet (kubelet)**: An agent that runs on each worker node. It ensures that the containers described in the Pod specifications are running and healthy.
   - **Kube Proxy (kubeproxy)**: A network proxy that manages network rules on each node. It enables communication between different Pods and services.
   - **Pod (pod)**: The smallest and simplest Kubernetes object that can be created or deployed. A Pod is a group of one or more containers that are managed together.
   - **Container Runtime (container_runtime)**: Software that runs and manages containers. Examples include Docker, containerd, and CRI-O.

4. **Services**: Kubernetes services provide stable endpoints to access Pods. They abstract and expose a logical set of Pods for external and internal access.
   - **ClusterIP (clusterip)**: Provides a stable IP address for accessing services from within the cluster.
   - **NodePort (nodeport)**: Exposes services on a port of each worker node, making them accessible externally.
   - **LoadBalancer (loadbalancer)**: Integrates with cloud provider load balancers to provide a public IP address to services, allowing for external access.

**Connections in the Diagram**:
- **User --> API Server**: Users interact with the cluster through the API Server.
- **API Server --> etcd**: The API Server stores and retrieves the cluster state in **etcd**.
- **API Server --> Scheduler**: The Scheduler receives scheduling requests from the API Server to place Pods on worker nodes.
- **API Server --> Controller Manager**: The Controller Manager receives tasks from the API Server to maintain cluster state.
- **Scheduler --> Kubelet**: The Scheduler instructs the **Kubelet** on where to launch new Pods.
- **Controller Manager --> Kubelet**: The **Controller Manager** ensures that the **Kubelet** enforces the desired state of resources.
- **Kubelet --> Pod**: The **Kubelet** manages the lifecycle of Pods running on the worker node.
- **Kube Proxy --> Pod**: The **Kube Proxy** manages networking rules and facilitates communication between Pods.
- **Pod --> Container Runtime**: Each Pod contains one or more containers, and the **Container Runtime** is responsible for managing them.
- **Pod --> ClusterIP --> NodePort --> LoadBalancer**: These components form a chain to provide service access from within or outside the cluster, depending on the configuration.




