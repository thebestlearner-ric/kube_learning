- Manage role based access control (RBAC).

    - [Kubernetes Documentation > Reference > Accessing the API > Using RBAC Authorization](https://kubernetes.io/docs/reference/access-authn-authz/rbac/)

- Use Kubeadm to install a basic cluster.

    - [Kubernetes Documentation > Getting started > Production environment > Installing Kubernetes with deployment tools > Bootstrapping clusters with kubeadm > Creating a cluster with kubeadm](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/)

- Manage a highly-available Kubernetes cluster.

    - [Kubernetes Documentation > Getting started > Production environment > Installing Kubernetes with deployment tools > Bootstrapping clusters with kubeadm > Creating Highly Available clusters with kubeadm](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/high-availability/)

- Provision underlying infrastructure to deploy a Kubernetes cluster.

    - [Kubernetes Documentation > Getting started](https://kubernetes.io/docs/setup/)

- Perform a version upgrade on a Kubernetes cluster using Kubeadm.

    - [Kubernetes Documentation > Tasks > Administer a Cluster > Administration with kubeadm > Upgrading kubeadm clusters](https://kubernetes.io/docs/tasks/administer-cluster/kubeadm/kubeadm-upgrade/)

- Implement etcd backup and restore.

    - [Kubernetes Documentation > Tasks > Administer a Cluster > Operating etcd clusters for Kubernetes](https://kubernetes.io/docs/tasks/administer-cluster/configure-upgrade-etcd/)


Helpful commands:

```bash
# Display addresses of the master and services
kubectl cluster-info

# Dump current cluster state to stdout
kubectl cluster-info dump

# List the nodes
kubectl get nodes

# Show metrics for a given node
kubectl top node my-node

# List all pods in all namespaces, with more details
kubectl get pods -o wide --all-namespaces

# List all services in all namespaces, with more details
kubectl get svc  -o wide --all-namespaces
```