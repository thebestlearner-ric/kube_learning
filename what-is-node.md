# NODE
## What is in the node?
It has 3 component:
1. kubelet
2. container runtime (containerd for AWS EKS)
3. kube-proxy

## Management of node
## 2 ways to add node to API Server
1. The kubelet on a node self-registers to the control plane
2. You (or another human user) manually add a Node object
### AWS 
### Adding a Node in AWS EKS

In AWS EKS, adding a node typically involves creating and associating a new node group with the existing cluster. This can be done through the AWS Management Console or the AWS CLI.

1. **Using the AWS Management Console:**

   - Open the Amazon EKS console at [https://console.aws.amazon.com/eks/](https://console.aws.amazon.com/eks/).
   - Select the EKS cluster you want to add the node to.
   - Under the "Compute" tab, choose "Add node group".
   - Configure the node group with the desired settings (e.g., instance type, scaling configuration).
   - Complete the creation process.

2. **Using the AWS CLI:**

   - Create a new node group configuration file (`nodegroup-config.json`):

     ```json
     {
       "clusterName": "my-cluster",
       "nodegroupName": "my-node-group",
       "scalingConfig": {
         "minSize": 1,
         "maxSize": 3,
         "desiredSize": 2
       },
       "diskSize": 20,
       "instanceTypes": [
         "t3.medium"
       ],
       "amiType": "AL2_x86_64",
       "nodeRole": "arn:aws:iam::123456789012:role/eks-node-role"
     }
     ```

   - Use the `eks create-nodegroup` command to add the node group:

     ```sh
     aws eks create-nodegroup --cli-input-json file://nodegroup-config.json
     ```
     
### Add a Standalone EC2
Adding a node to a Kubernetes cluster can vary depending on the environment you are working in. Here are the steps for adding a node in Kind, AWS EKS, and to a standalone EC2 instance.



### Adding a Node to a Standalone EC2 Instance

For a standalone EC2 instance, you can manually install Kubernetes and join it to an existing cluster. Here are the high-level steps:

1. **Prepare the EC2 Instance:**

   - Launch a new EC2 instance with the desired specifications.
   - SSH into the instance:

     ```sh
     ssh -i /path/to/key.pem ec2-user@<ec2-instance-public-ip>
     ```

2. **Install Docker:**

   - Install Docker on the instance:

     ```sh
     sudo yum update -y
     sudo yum install -y docker
     sudo systemctl enable docker
     sudo systemctl start docker
     ```

3. **Install Kubernetes Components:**

   - Add the Kubernetes repository:

     ```sh
     cat <<EOF | sudo tee /etc/yum.repos.d/kubernetes.repo
     [kubernetes]
     name=Kubernetes
     baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-x86_64
     enabled=1
     gpgcheck=1
     repo_gpgcheck=1
     gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
     EOF
     ```

   - Install `kubelet`, `kubeadm`, and `kubectl`:

     ```sh
     sudo yum install -y kubelet kubeadm kubectl
     sudo systemctl enable kubelet
     ```

4. **Join the Node to the Cluster:**

   - On the master node, generate a join command:

     ```sh
     kubeadm token create --print-join-command
     ```

   - Execute the join command on the new node:

     ```sh
     sudo kubeadm join <master-node-ip>:6443 --token <token> --discovery-token-ca-cert-hash sha256:<hash>
     ```
- **Standalone EC2:** Manually prepare the EC2 instance with Docker and Kubernetes components, then join it to the cluster using the `kubeadm join` command.