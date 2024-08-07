# Container Network Interface (CNI)

## [The Kubernetes network model](https://kubernetes.io/docs/concepts/services-networking/)

### Kubernetes fundamental requirements
1. pods can communicate with all other pods on any other node with NAT
2. agents on node (e.g. system daemons, kubelet) can communicate with all pods on that node

### Kubernetes Networking Addresses 4 Concerns:
1. Containers within a Pod use netowkring to communicate via `loopback`
2. Cluster networking provides communication between different Pods.
3. The `Service` API lets you expose an application running in Pods to be reachable from outside your cluster
  - `Ingress` provdies extra functionality specifically for exposing HTTP applications, websites and APIs.
  - `Gateway API` is an add-on that provides an expressive, extensible, and role-oriented family of API kinds of modeling service networking
  4. You can also use Services to publish services only for consumption inside your cluster

### Terminalogy
1. Service
Expose an application running in your cluster behind a single outward-facing endpoint, even when the workload is split across multiple backends.

2. Ingress
Make your HTTP (or HTTPS) network service available using a protocol-aware configuration mechanism, that understands web concepts like URIs, hostnames, paths, and more. The Ingress concept lets you map traffic to different backends based on rules you define via the Kubernetes API.

3. Ingress Controllers
In order for an Ingress to work in your cluster, there must be an ingress controller running. You need to select at least one ingress controller and make sure it is set up in your cluster. This page lists common ingress controllers that you can deploy.

4. Gateway API
Gateway API is a family of API kinds that provide dynamic infrastructure provisioning and advanced traffic routing.

5. EndpointSlices
The EndpointSlice API is the mechanism that Kubernetes uses to let your Service scale to handle large numbers of backends, and allows the cluster to update its list of healthy backends efficiently.

6. Network Policies
If you want to control traffic flow at the IP address or port level (OSI layer 3 or 4), NetworkPolicies allow you to specify rules for traffic flow within your cluster, and also between Pods and the outside world. Your cluster must use a network plugin that supports NetworkPolicy enforcement.

7. DNS for Services and Pods
Your workload can discover Services within your cluster using DNS; this page explains how that works.

8. IPv4/IPv6 dual-stack
Kubernetes lets you configure single-stack IPv4 networking, single-stack IPv6 networking, or dual stack networking with both network families active. This page explains how.

9. Topology Aware Routing
Topology Aware Routing provides a mechanism to help keep network traffic within the zone where it originated. Preferring same-zone traffic between Pods in your cluster can help with reliability, performance (network latency and throughput), or cost.

10. Service ClusterIP allocation

11. Service Internal Traffic Policy
If two Pods in your cluster want to communicate, and both Pods are actually running on the same node, use Service Internal Traffic Policy to keep network traffic within that node. Avoiding a round trip via the cluster network can help with reliability, performance (network latency and throughput), or cost.


### Cluster IP

1. **Definition**: A `Cluster IP` is a virtual IP address that is assigned to a Kubernetes service. It is used to provide a stable IP address for accessing the service within the cluster.

2. **Scope**: This IP address is only accessible within the Kubernetes cluster. It cannot be reached from outside the cluster.

3. **Purpose**: The `Cluster IP` allows other pods within the cluster to communicate with the service using a stable IP address, regardless of the underlying pod IPs that might change due to scaling or rescheduling.

### Example Scenario

Consider a service that fronts a set of pods running an application. Here’s how the `Cluster IP` works:

1. **Pods**: Multiple pods are running an instance of your application.
2. **Service**: You create a Kubernetes service to expose these pods.

#### Service Definition

Here is an example of how you might define a service:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  selector:
    app: my-app
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
  type: ClusterIP
```

#### Service Creation

When you create this service, Kubernetes assigns it a `Cluster IP`, say `10.96.0.1`.

### Accessing the Service

- **Within the Cluster**: Any pod within the cluster can access this service using the `Cluster IP` (`10.96.0.1`) or the service name (`my-service`).

```sh
curl http://10.96.0.1
# or
curl http://my-service
```

- **DNS Resolution**: Kubernetes DNS automatically maps the service name (`my-service`) to the `Cluster IP` (`10.96.0.1`).

### Summary of IP Types

- **Node IP**: The IP address of a Kubernetes node. Used for node-level communication and accessing NodePort services.
- **Pod IP**: The IP address of an individual pod. Used for communication between pods within the cluster.
- **Cluster IP**: A virtual IP address assigned to a Kubernetes service. Used for accessing the service from within the cluster.

### Diagram

Here’s a simple illustration:

```
+---------------------------+
| Node 1                    |
|                           |
|  +---------------------+  |
|  | Pod A (10.244.1.2)  |  |
|  +---------------------+  |
|                           |
|  +---------------------+  |
|  | Pod B (10.244.1.3)  |  |
|  +---------------------+  |
|                           |
+---------------------------+

Service (my-service)
Cluster IP: 10.96.0.1

Pods communicate using:
curl http://10.96.0.1
```

In this diagram, the service `my-service` has a `Cluster IP` of `10.96.0.1`, which other pods can use to access the service. The actual IPs of Pod A and Pod B are not relevant to the clients; they only need to know the `Cluster IP` or the service name.


## How to access configuration for CNI
```bash
kubectl get nodes -o wide
# configuration would be in all nodes
ssh -i /path/to/private-key.pem ubuntu@<node-ip-address>
# configuration are in this folder
ls /etc/cni/net.d/
# example of a calico plugin
cat /etc/cni/net.d/10-calico.conflist

```

```yaml
# 10-calico.conflist would look like this
{
  "name": "k8s-pod-network",
  "cniVersion": "0.3.1",
  "plugins": [
    {
      "type": "calico",
      "etcd_endpoints": "https://<etcd-server>:2379",
      "log_level": "info",
      "ipam": {
        "type": "calico-ipam"
      },
      "policy": {
        "type": "k8s",
        "k8s_api_root": "https://<kubernetes-api-server>:443",
        "k8s_auth_token": "<token>"
      },
      "kubernetes": {
        "kubeconfig": "/etc/cni/net.d/calico-kubeconfig"
      }
    },
    {
      "type": "portmap",
      "capabilities": {
        "portMappings": true
      }
    }
  ]
}
```