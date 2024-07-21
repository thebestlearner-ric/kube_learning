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
   What is a Pods?
   Pod is composed of a group of containers in an isolated environment with resource constraints. In Kubernetes, pod is the smallest schedulable unit.
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
   DEMO: https://www.youtube.com/watch?v=y58U33yOIhY

   2. Container runtime:
   - A fundamental component that empowers Kubernetes to run containers effectively. It is responsible for managing the execution and lifecycle of containers within the Kubernetes environment.
   What is CRI?
   CRI (Container Runtime Interface) consists of a specifications/requirements (to-be-added), protobuf API, and libraries for container runtimes to integrate with kubelet on a node. The CRI API is currently in Alpha, and the CRI-Docker integration is used by default as of Kubernetes 1.7+.
   - supports container runtimes such as:
      1. containerd
      2. CRI-O. 
      3. rktlet
      4. frakti
      5. singularity-cri
   - Kubernetes CRI (Container Runtime Interface): https://github.com/kubernetes/community/blob/master/contributors/devel/sig-node/container-runtime-interface.md
   Why does Kubernetes need CRI? 
   - Previously K8s was using Docker as the main workhouse, however that means it was dependent on Docker to be its container manager and container runtime. There is also an overlap in the features that both K8s and Docker can do, such as networking. There is also demand for other runtime tools such are CRI-O. 
   - In the end, k8s decided that it is better to create/maintain a standard for other tools to adhere. So as long as the tools adhere to the Container Runtime Interface, it will be able to work with Kubernetes
   - Example: openshift uses CRI-O as it container runtime 

   3. DNS
   General able to use any DNS structure available as a plugin. However, coreDNS is the default since 1.13, before its kube-dns
   How does a DNS work?
   The 8 steps in a DNS lookup:
   1. A user types ‘example.com’ into a web browser and the query travels into the Internet and is received by a DNS recursive resolver.
   2. The resolver then queries a DNS root nameserver (.).
   3. The root server then responds to the resolver with the address of a Top Level Domain (TLD) DNS server (such as .com or .net), which stores the information for its domains. When searching for example.com, our request is pointed toward the .com TLD.
   4. The resolver then makes a request to the .com TLD.
   5. The TLD server then responds with the IP address of the domain’s nameserver, example.com.
   6. Lastly, the recursive resolver sends a query to the domain’s nameserver.
   7. The IP address for example.com is then returned to the resolver from the nameserver.
   8. The DNS resolver then responds to the web browser with the IP address of the domain requested initially.
   https://www.cloudflare.com/en-gb/learning/dns/what-is-dns/
   Iterative Vs Rescurive Query
   ![alt text](/chapters/images/iterativeVsRecursive.png)
   Different Levels of DNS
   ![alt text](/chapters/images/different-domain-levels
   .png)
   Recursive DNS resolver
   Example in pictures:
   ![alt text](/chapters/images/DNS-explained.png)
      - Different types of Records
      1. CNAME or Canonical Name: is a DNS record that basically points a domain name at another domain name.
         - CNAME must always be a domain, never an IP address
         - useful when running multiple service on a single domain. eg www.example.com, ftp.example.com
         Flow: Web browser -> DNS Recursive Resolver -> DNS Root Nameserver -> DNS TLD Nameserver -> cloudflare.com Authoritative Nameserver -> blog.cloudflare.com Authoritative Nameserver         
      2. NS: authoritative name servers for a domain
         - a domain have generally 2 Authoritative name server for reliablity
         - 1 primary, 1 secondary
         Flow: Web browser -> DNS Recursive Resolver -> DNS Root Nameserver -> DNS TLD Nameserver -> Cloudflare.com Authoritative Nameserver
      3. A: indicate the IPv4 addresses available for resolution
      4. AAAA: indicate the  IPv6 address available for resolution
      ```bash
         #queries for NS
         nslookup -type=ns google.com
         #queries for CNAME
         nslookup -q=cname test.google.com # returns the domain google.com
         dig google.com CNAME 
         #queries for A
         nslookup -q=A google.com # returns the IP addresses
         dig google.com A
      ```

   CoreDNS
   - Able to integrate with etcd and cloud vendor (AWS Route 53) eg, authoriative server seats in AWS. coreDNS can pull from there. 
   - support Prometheus metrices
   - port 53 by default
   ![alt text](/chapters/images/coredns-example.png)
   ```bash
   # access Corefile
   kubectl describe configmap corefile -n kube-system
   # Although the name is coredns is it still a kube-dns system
   ```
   example of a stub domains:
   main point: 
   1. Forward some out-of-cluster domains directly to the right authoritative DNS server
   2. Handle internal corporate domains
   ```yaml
      corporate.com:53 {
         proxy . <DNS-IP-corporate>
      }
      on.prem.organisation.com:53 {
         proxy . <DNS-IP-local-corporate>
      }
      .:53 {
         errors            #Enable error logging
         health {          #Serve liveness status on http 8080
            lameduck 5s
         }
         ready
         kubernetes cluster.local in-addr.arpa ip6.arpa { #Backend to k8s for cluster.local and reverse domains
            pods insecure                                 #Mimic kube-dns pod records behaviour
            fallthrough in-addr.arpa ip6.arpa             #Resolve CNAME targets upstream
            ttl 30                                        #Continue searching reverse zones
         }
         prometheus :9153                                 #Serve prometheus metrics
         forward . /etc/resolv.conf {
            max_concurrent 1000
         }
         proxy mycompany.com 10.2.4.5
         proxy . /etc/resolv.conf                         #Forward other domains to /etc/resolv.conf ns
         cache 30                                         #Cache for up to 30 seconds
         loop                                             #DNS protocol loop check
         reload                                           #Reload server if the Corefile change
         loadbalance                                      #Shuffle order of returned records
      }
   ```
   coredns Cache tuning
   1. Allow specific configuration for known zone, cache, logging ...
   ```yaml
   corporate.com:53 {
      errors
      log innerservices.coporate.com
      proxy local.corporate.com <DNS-IP-local>
      proxy . <DNS-IP-corporate>
      cache 3600
   }
   on.prem.organisation.com:53 {
      proxy . <DNS-IP-local-corporate>
   }
   .:53 {
      ...
      kubernetes cluster.local ... {
         ...
      }
      proxy . /etc/resolv.conf
      ...
      cache 30
   }
   ```
   Common Options for Cluster DNS
   1. pods [disabled|insecure|verified]
   2. endpoint_pod_names
   3. ttl (time to live)
   4. fallthrough [ZONES]

   All Kubernetes Plugin Options
   ```yaml
   kubernetes [ZONES...] {
      endpoint URL
      tls CERT KEY CACERT
      kubeconfig KUBECONFIG [CONTEXT]
      namespaces NAMESPACE...
      labels EXPRESSION
      pods POD-MODE
      endpoint_pod_names
      ttl TTL
      noendpoints
      fallthrough [ZONES...]
      ignore empty_service
   }
   ```
   Connect to Kubernetes with CoreDNS running outside the cluster:
   ```yaml
   kubernetes cluster.local {
      endpoint https://k8s-endpoint:8443
      tls cert key cacert
   }
   ```

   