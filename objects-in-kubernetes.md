# Objects in Kubernetes
## A record of intent
A Kubernetes object is a "record of intent" meaning, once you create the object. The Kubernetes system will working to ensure that the object exists. 
Effectively saying, this is your cluster's desired state.

## Describing a Kubernetes object
### Required Fields
In the manifest files (JSON or YAML) the Kubernetes object must have:
1. `apiVersion` - Which version of the Kubernetes API you're using to create this object
2. `kind` - What kind of object you want to create
3. `metadata` - Data that helps uniquely identify the object, including a `name` string, `UID`, and `namespace`(optional)
4. `spec` - What state you desire for the object
### Deployment spec breakdown
Examples:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment # Name of the deployment object
spec:
  selector: # This field defines how the created ReplicaSets finds which Pods to manage
    matchLabels:
      app: nginx
  replicas: 2 # tells deployment to run 2 pods matching the template
  template:
    metadata:
      labels:
        app: nginx # Pod name
    spec: 
      containers:
      - name: nginx
        image: nginx:1.14.2
        ports:
        - containerPort: 80

```
How to apply the manifest?
```sh
kubectl apply -f https://k8s.io/examples/application/deployment.yaml
```

## Rollback to Previous Revision
```sh
kubectl rollout undo deployment/nginx-deployment
```
You can select the specific revision to rollback
```sh
kubectl rollout undo deployment/nginx-deployment --to-revision=2
```
## Scaling a Deployment
```sh
kubectl scale deployment/nginx-deployment --replicas=10

```
Assuming there is Horizontal Pod autoscaling. You can set your Deployment to choose the minimum and maximum number of Pod you want to run based on the CPU utilisation of your existing Pods 
```sh
kubectl autoscale deployment/nginx-deployment --min=10 --max=15 --cpu-percent=80
```

## Proportional scaling
What is proportional scaling?
- support multiple versions of an application at the same time
- The Deployment Controller balances the additional replicas in the existing active ReplicaSet
TDLR: K8S will try to maintain both versions of the ReplicaSet to its desired state
Proportional scaling in Kubernetes deployments refers to the ability to scale the number of replicas of a deployment up or down in proportion to the current number of replicas. When a deployment is scaled, Kubernetes ensures that the new desired state of the deployment maintains the same proportional relationship among all the components.

Here's a simplified diagram to illustrate proportional scaling in a Kubernetes deployment:

```
             +---------------------+
             | Kubernetes Cluster  |
             +---------------------+
                      |
                      |
              +---------------+
              | Deployment    |
              |---------------|
              | app: my-app   |
              +---------------+
                      |
                      |
        +-------------------------+
        |                         |
+-------------+            +-------------+
| ReplicaSet  |            | ReplicaSet  |
| version: v1 |            | version: v2 |
+-------------+            +-------------+
     |                          |
     |                          |
+--------+  +--------+    +--------+  +--------+
| Pod 1  |  | Pod 2  |    | Pod 3  |  | Pod 4  |
+--------+  +--------+    +--------+  +--------+

Initial State: 2 Pods for v1, 2 Pods for v2
```

When we perform a proportional scaling operation, such as scaling the deployment from 4 replicas to 8 replicas, Kubernetes will ensure that the ratio of the replicas for each version remains the same.

```
             +---------------------+
             | Kubernetes Cluster  |
             +---------------------+
                      |
                      |
              +---------------+
              | Deployment    |
              |---------------|
              | app: my-app   |
              +---------------+
                      |
                      |
        +-------------------------+
        |                         |
+-------------+            +-------------+
| ReplicaSet  |            | ReplicaSet  |
| version: v1 |            | version: v2 |
+-------------+            +-------------+
     |                          |
     |                          |
+--------+  +--------+    +--------+  +--------+
| Pod 1  |  | Pod 2  |    | Pod 3  |  | Pod 4  |
+--------+  +--------+    +--------+  +--------+
| Pod 5  |  | Pod 6  |    | Pod 7  |  | Pod 8  |
+--------+  +--------+    +--------+  +--------+

After Scaling: 4 Pods for v1, 4 Pods for v2
```

### Explanation

1. **Initial State**: 
    - The deployment has two ReplicaSets, `version: v1` and `version: v2`, each with 2 pods, making a total of 4 pods.

2. **Scaling Operation**:
    - When scaling the deployment from 4 replicas to 8 replicas, Kubernetes will proportionally increase the number of replicas for each version.
    - The ratio of replicas for `version: v1` and `version: v2` remains the same. In this case, it is 1:1 (2 replicas each out of a total of 4).

3. **Resulting State**:
    - After scaling, the deployment will have 4 replicas for `version: v1` and 4 replicas for `version: v2`, maintaining the 1:1 ratio.

### Practical Example

Here's a practical example using `kubectl` to scale a deployment:

1. **Create a Deployment**:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
spec:
  replicas: 4
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - name: my-container
        image: nginx:latest
```

Apply the deployment:

```sh
kubectl apply -f my-app-deployment.yaml
```

2. **Scale the Deployment**:

```sh
kubectl scale deployment my-app --replicas=8
```

Kubernetes will proportionally scale the replicas across all ReplicaSets in the deployment.

### Summary

Proportional scaling ensures that when you scale a deployment, the distribution of replicas among different versions or configurations remains consistent, maintaining the desired state and proportion of each component within the deployment.