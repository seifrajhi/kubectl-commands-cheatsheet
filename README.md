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


Certainly! Here's an enriched set of `kubectl` commands with concise descriptions for your README:

## Verify AppArmor Support on Nodes:

```bash
kubectl get nodes -o=jsonpath=$'{range .items[*]}{@.metadata.name}: {.status.conditions[?(@.reason=="KubeletReady")].message}\n{end}'
```
Check for AppArmor support on nodes by inspecting the KubeletReady condition message.

## List Container Images using Go-Template:

```bash
kubectl get pods --all-namespaces -o go-template --template="{{range .items}}{{range .spec.containers}}{{.image}} {{end}}{{end}}"
```
List container images across all namespaces using a Go-Template.

## Get a List of Pods for Each Node:

```bash
kubectl get pods --all-namespaces -o json | jq '.items | map({podName: .metadata.name, nodeName: .spec.nodeName}) | group_by(.nodeName) | map({nodeName: .[0].nodeName, pods: map(.podName)})'
```
Retrieve a list of pods grouped by nodes, displaying node names and associated pods.

## Check for Pods that are Currently Not Ready:

```bash
kubectl get pods --all-namespaces -o json | jq -r '.items[] | select(.status.phase != "Running" or ([.status.conditions[] | select(.type == "Ready" and .state == false)] | length) == 1) | .metadata.namespace + "/" + .metadata.name'
```
Identify pods that are not in the "Running" state or have readiness conditions set to false.


