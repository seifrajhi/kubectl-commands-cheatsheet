# kubectl-commands-cheatsheet

Below is an enriched list of useful `kubectl` commands along with descriptions for a comprehensive README:

## Managing Pods:

### Sort Pods by Creation Timestamp:
```bash
kubectl get pod --sort-by '.metadata.creationTimestamp'
```
Sorts pods based on their creation timestamp in ascending order.

### Sort Pods by Restart Count:
```bash
kubectl get pod --sort-by '.status.containerStatuses[0].restartCount'
```
Sorts pods based on the restart count of the first container.

### List Nodes with Pod Count:
```bash
kubectl get pods -A -o jsonpath='{range .items[?(@.spec.nodeName)]}{.spec.nodeName}{"\n"}{end}' | sort | uniq -c | sort -rn
```
Displays the count of pods on each node.

### List Pods with Node Information:
```bash
kubectl get pods --all-namespaces -o wide --field-selector spec.nodeName=node-name
```
Lists pods with wide output, including node information.

## Taints and Tolerations:

### Add Taint to Node:
```bash
kubectl taint nodes node-name scheduling=enabled:NoSchedule
```
Configures a node to not accept new workloads.

### Remove Taint from Node:
```bash
kubectl taint nodes node-name scheduling=enabled:NoSchedule-
```
Removes the specified taint from the node.

## Monitoring Metrics:

### Node Metrics (All Nodes):
```bash
kubectl get --raw /apis/metrics.k8s.io/v1beta1/nodes | jq -C . | less -R
```
Displays metrics for all nodes in the cluster.

### Node Metrics (Individual Node):
```bash
kubectl get --raw /apis/metrics.k8s.io/v1beta1/nodes/$NODE_NAME
```
Displays metrics for a specific node.

### Pod Metrics (All Pods):
```bash
kubectl get --raw /apis/metrics.k8s.io/v1beta1/pods | jq . -C | less -R
```
Displays metrics for all pods in the cluster.

### Pod Metrics (Individual Pod):
```bash
kubectl get --raw /apis/metrics.k8s.io/v1beta1/namespaces/$NS/pods/$POD
```
Displays metrics for a specific pod.

### Node Resource Usage:
```bash
kubectl get --raw /apis/metrics.k8s.io/v1beta1/nodes | jq '[.items [] | {nodeName: .metadata.name, nodeCpu: .usage.cpu, nodeMemory: .usage.memory}]'
```
Displays CPU and memory usage for each node.

## Advanced Queries:

### Sum Up Requested CPUs for All Pods:
```bash
kubectl get pods -o json | jq -r '[.items[] | .spec.containers[].resources.requests.cpu | rtrimstr("m") | tonumber] | add'
```
Calculates the total requested CPUs across all pods.

### Check Container Image in Pods:
```bash
kubectl get po -o custom-columns=name:.metadata.name,status:.status.phase,image:'.spec.containers[0].image'
```
Lists pod names, status, and the image used by the first container.

