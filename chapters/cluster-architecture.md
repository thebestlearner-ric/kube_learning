# What is k8s?
## k8s Cluster Components:
### 1. Control Plane:
   - What is a control plane?
   The control plane manages the worker nodes and the Pods in the cluster
   The control plane components make global decisions about the cluster (for example, scheduling), as well as detecting and responding to cluster events (example, starting new pod when a Deployment's replicas field is unsatisfied)
   - Fun fact: control plane components can be run on any machine in the cluster. meaning control plane can run acroos multiple machines
   Control Plane Components:
   1. kube-apiserver: is the API server of k8s control plane that exposes the kubernetes API. The API server is the frontend for the Kubernetes control plane. Kube-apiserver is designed to scale horizontally -- scaled by deploying more instances. You can run several instances of kube-apiserver and balance traffic between those instances.
   
   2. etcd: Consistent and highly-available key value store used as Kubernetes' backing store for all cluster data. Mainly used as a separate coordination service, in distributed systems and desined to hold small amounts of data that can fit entirely in memory. *Make sure to have a backup plan for the data
   
   3. kube-scheduler: Control plane component that watches for newly created Pods with no assigned node, and selects a node for them to run on.
   Factors taken into account for scheduling decisions:
   - individual resource requirement
   - collective resource requirement
   - anti-affinity specifications
   - data locality
   - inter-workload interference
   - deadlines
   
   4. kube-controller-manager: Control plane component that runs controller processes. Single process but mulit-targeted.
   Fun fact: Scalable horizontally
   Different types of controller:
   - Node controller: Responsible for noticing and responding when nodes go down.
   - Job controller: Watches for Job objects that represent one-off tasks, then creates Pods to run those tasks to completion.
   - EndpointSlice controller: Populates EndpointSlice objects (to provide a link between Services and Pods).
   - ServiceAccount controller: Create default ServiceAccounts for new namespaces.
   - Not exhaustive list
   
   5. cloud-controller-manager: Control plane component that embeds cloud-specific control logic. The cloud controller manager lets you link your cluster into your cloud provider's API, and
   separates out the components that only interact with your cluster.
   
   Fun fact: Single process but mulit-targeted. Scalable horizontally
   
   Following controllers can have cloud provider dependencies:
   - Node controller: For checking the cloud provider to determine if a node has been deleted in the cloud after it stops responding.
   - Route controller: For setting up routes in the underlying cloud infrastructure
   - Service controller: For creating, updating and deleting cloud provider load balancers 

     
### 2. Worker node or Node
   What is a worker node?
   Worker nodes host Pods that are the components of the application workload.
   Node Components:
   1. Kubelet: agent that runs on each node in the cluster. It makes sure that containers are running in a Pod.
   The kubelet takes a set of PodSpecs that are provided through various mechanisms and ensures that the containers described in those PodSpecs are running and healthy.
   2. kube-proxy: 
   - network-proxy that runs on each node in your cluster implementing part of the Kubernetes Service concept.
   - kube-proxy maintains network rules on nodes. These network rules allow network communication to your Pods from network sessions inside or outside of your cluster.
   - kube-proxy uses the operating system packet filtering layer (iptables, kernalspaces, ipvs) if there is one and it's available. Otherwise, kube-proxy forwards the traffic itself.
   kube-proxy can forward: UDP TCP SCTP
   kube-proxy responsible for implementing a virtual IP mechanism for Service except external name
   ![alt text](/chapters/images/kube-proxy.png)
   ```bash
   # Display endpoint ips
   kubectl get endpoints -n <namespace>
   # ssh to node
   minikube ssh
   # display iptables
   sudo iptables -t nat -L KUBE_SERVICES -n 
   ```
   https://www.youtube.com/watch?v=y58U33yOIhY