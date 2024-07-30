# Container Network Interface (CNI)

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