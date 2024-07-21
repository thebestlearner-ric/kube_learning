# Container Monitoring Resource
## Key Components of Kubernetes Cluster Monitoring:
1. Metrics Collection:
Metrics provide quantitative data about the system, such as CPU usage, memory consumption, and network traffic.
- Prometheus: A widely used open-source monitoring and alerting toolkit designed specifically for reliability and scalability in Kubernetes environments.
- Prometheus Server: Scrapes and stores time-series data.
- Node Exporter: Collects hardware and OS metrics.
- Kube-State-Metrics: Exposes cluster-level metrics, such as pod status, node status, and deployment status.
2. Logging:
Logging captures textual records of events within the cluster, which is crucial for debugging and auditing.
- Fluentd: A versatile log collector that aggregates logs from various sources and forwards them to centralized storage.
- Elasticsearch, Logstash, Kibana (ELK) Stack: Often used in conjunction with Fluentd, where Elasticsearch stores logs, Logstash processes logs, and Kibana provides a web interface for querying and visualizing logs.
3. Visualization:
Visualization tools help in interpreting metrics and logs through graphs, dashboards, and alerts.
- Grafana: A popular open-source platform for monitoring and observability that integrates with Prometheus, Elasticsearch, and other data sources to provide rich visualization capabilities.
4. Alerting:
Alerting systems notify administrators about issues that require attention, such as resource exhaustion or component failures.
- Alertmanager: A Prometheus component used to handle alerts sent by client applications like the Prometheus server.

### How to Deploy Monitoring Tools
```sh
helm install prometheus prometheus-community/prometheus
helm install grafana grafana/grafana
```
To set CPU alert and warnings
```yaml
# Prometheus alert rule for high CPU usage
groups:
  - name: cpu-usage-alerts
    rules:
      - alert: HighCPUUsage
        expr: sum(rate(container_cpu_usage_seconds_total[1m])) by (container) > 0.9
        for: 2m
        labels:
          severity: critical
        annotations:
          summary: "High CPU usage detected"
          description: "Container {{ $labels.container }} is using > 90% CPU for more than 2 minutes."

```

## How Resource Metric Pipeline works?
![alt text](/chapters/images/resource-metrics-pipeline.png)
1. cAdvisor: Daemon for collecting, aggregating and exposing container metrics included in Kubelet.
2. kubelet: Node agent for managing container resources. Resource metrics are accessible using the /metrics/resource and /stats kubelet API endpoints.
3. node level resource metrics: API provided by the kubelet for discovering and retrieving per-node summarized stats available through the /metrics/resource endpoint.
4. metrics-server: Cluster addon component that collects and aggregates resource metrics pulled from each kubelet. The API server serves Metrics API for use by HPA, VPA, and by the kubectl top command. Metrics Server is a reference implementation of the Metrics API.
Full Metric Pipeline
Kubernetes is designed to work with OpenMetrics. One such OpenMetrics project is Prometheus. 
1. An exporter reports the node problems and/or metrics to certain backends. The following exporters are supported:
2. Kubernetes exporter: this exporter reports node problems to the Kubernetes API server. Temporary problems are reported as Events and permanent problems are reported as Node Conditions.
3. Prometheus exporter: this exporter reports node problems and metrics locally as Prometheus (or OpenMetrics) metrics. You can specify the IP address and port for the exporter using command line arguments.
4. Stackdriver exporter: this exporter reports node problems and metrics to the Stackdriver Monitoring API. The exporting behavior can be customized using a configuration file.

Metrics API: Kubernetes API supporting access to CPU and memory used for workload autoscaling. To make this work in your cluster, you need an API extension server that provides the Metrics API.
Best practice: 
- cpu Requests should not be more than 1 Virtual Core, unless your application is optimized to use more than 1 core
- cpu usage will be throttled by k8s to prevent it from being evicted 
- kube-scheduler will check the cpu/memory Request and limit against the nodes in the cluster in a round robin manner where it can be scheduled in  
- There are namespace request and limit Quota that can be applied to control excessive cpu/memory usage 
    ```yaml
    apiVersion: v1
    kind: List
    items:
    - apiVersion: v1
    kind: ResourceQuota
    metadata:
        name: pods-high
    spec:
        hard:
        cpu: "1000"
        memory: 200Gi
        pods: "10"
        scopeSelector:
        matchExpressions:
        - operator : In
        scopeName: PriorityClass
        values: ["high"]
    ```
Demo of how k8s will throttle cpu usage if exceeds limit
watch: https://www.youtube.com/watch?v=dMca4jHaft8&ab_channel=AntonPutra
he explains how cAdvisor pulls metrics from 

