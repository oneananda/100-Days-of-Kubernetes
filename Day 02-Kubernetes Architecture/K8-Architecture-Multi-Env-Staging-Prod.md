# K8-Architecture-Multi-Env-Staging-Prod

In this [example](https://github.com/oneananda/100-Days-of-Kubernetes/blob/main/Day%20002-Kubernetes%20Architecture/Screenshots/K8-Architecture-Multi-Env-Staging-Prod.png), we have two environments: **Production** and **Staging**, represented by two Kubernetes clusters. Each environment has its own **Master Node** and **Worker Node**, along with their associated services.

### Key Components

1. **User**:
   - Represents the end user or client that interacts with the cluster through various means, such as `kubectl` commands or the Kubernetes Dashboard.

2. **Kubernetes Cluster - Production**:
   - The **Production** cluster is meant for running actual workloads that serve end-users. It contains a **Master Node** and a **Worker Node**.

3. **Master Node (Production)**:
   - **API Server (`apiserver_prod`)**: The central control component that handles all requests (CRUD operations) to the Kubernetes cluster. The API server serves as the communication gateway between users and the cluster.
   - **Scheduler (`scheduler_prod`)**: This component assigns Pods to worker nodes based on resource availability and defined scheduling policies.
   - **Controller Manager (`controller_prod`)**: Manages control loops to maintain the desired state of the cluster. It ensures that resources such as Pods, Endpoints, and Replication Controllers are running as intended.
   - **etcd (`etcd_prod`)**: A distributed key-value store that persists all cluster-related data, including configuration, state, and metadata.

4. **Worker Node (Production)**:
   - **Kubelet (`kubelet_prod`)**: An agent running on each worker node that receives instructions from the scheduler and ensures that containers (defined by Pods) are running as desired.
   - **Kube Proxy (`kubeproxy_prod`)**: Handles networking for services, ensuring communication between different Pods, both within the node and across nodes.
   - **Pod (`pod_prod`)**: The smallest deployable unit in Kubernetes, which encapsulates one or more containers.
   - **Container Runtime (`container_runtime_prod`)**: The software responsible for running the containers (such as Docker, containerd, or CRI-O).

5. **Services - Production**:
   - **ClusterIP (`clusterip_prod`)**: Exposes services internally within the Kubernetes cluster, providing an internal IP for the services.
   - **NodePort (`nodeport_prod`)**: Exposes services on a specific port of the worker nodes, allowing external access to the service.
   - **LoadBalancer (`loadbalancer_prod`)**: Used to provide external, load-balanced access to services, often through integration with cloud providers.

### Staging Environment

The **Staging** cluster has similar components to the **Production** cluster but serves a different purpose. The **Staging** environment is used for testing before deployments are moved to production. It is an isolated setup that allows developers and QA teams to verify changes safely.

1. **Master Node (Staging)**:
   - Contains components such as **API Server (`apiserver_stage`)**, **Scheduler (`scheduler_stage`)**, **Controller Manager (`controller_stage`)**, and **etcd (`etcd_stage`)** that operate in the same manner as in the Production cluster.

2. **Worker Node (Staging)**:
   - Contains **Kubelet (`kubelet_stage`)**, **Kube Proxy (`kubeproxy_stage`)**, **Pod (`pod_stage`)**, and **Container Runtime (`container_runtime_stage`)** to run test workloads.

3. **Services - Staging**:
   - Similar to the Production environment, **ClusterIP (`clusterip_stage`)**, **NodePort (`nodeport_stage`)**, and **LoadBalancer (`loadbalancer_stage`)** provide internal and external access for testing services.

### Workflow Explanation

- The **User** interacts with both **Production** and **Staging** clusters via the **API Server**. This interaction could be to deploy workloads, scale applications, or monitor cluster health.
  
- The **API Server** interacts with **etcd** to store or retrieve cluster state. It also instructs the **Scheduler** and **Controller Manager** to manage the cluster's workloads.
  
- The **Scheduler** assigns **Pods** to appropriate **Worker Nodes** based on available resources. The **Controller Manager** ensures that the desired state is maintained.
  
- The **Kubelet** on the **Worker Node** takes instructions from the **Scheduler** to manage **Pods** and their associated containers. It ensures that the containers are running as specified in the Pod configuration.
  
- The **Kube Proxy** handles network connectivity, allowing **Pods** to communicate with each other, either within the node or across nodes.
  
- The **Services** like **ClusterIP**, **NodePort**, and **LoadBalancer** provide different levels of access to the **Pods**:
  - **ClusterIP** is used for internal cluster communication.
  - **NodePort** exposes the service on a port on each node for external access.
  - **LoadBalancer** integrates with a cloud provider's load balancer for providing high availability to external clients.

### Multi-Environment Use Case

- In this example, the **Production** and **Staging** environments are separated, which is a common practice for ensuring reliability and avoiding untested code impacting end users.
- The **Production** environment runs the actual workloads serving customers, while the **Staging** environment allows teams to test updates, new features, or changes.
- This separation provides a safer workflow, ensuring that any issues in the **Staging** environment do not affect the live **Production** environment.

The key idea here is that each environment has its own dedicated Master and Worker nodes to isolate workloads and ensure that Production workloads remain stable and unaffected by experimental changes. This multi-environment setup is crucial for reducing risks and maintaining high availability of production services while facilitating a robust development cycle.

