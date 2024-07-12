### Good commands
```bash
# get configmap or deployment or anything in yaml format and append it to a tmp file
kubectl get cm coredns -o yaml > /tmp/test.yaml
# get configmap or deployment or anything in json format and append it to a tmp file
kubectl get cm coredns -o json > /tmp/test.json

```

#### set kubectl to change context to target namespace
```bash
#### set kubectl to change context to target namespace
kubectl config set-context --current --namespace=kube-system
```
#### edit .bashrc or .zshrc
```bash
alias k="kubectl"
alias kgp="kubectl get pod"
alias kn="kubectl get namespace"
```