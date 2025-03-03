## Advanced scheduling techniques in Kubernetes

### Definitions
**Scheduling** is the process of placing pods on nodes for the applications to run as desired
**The Kube-scheduler** performs scheduling in kubernetes clusters by intelligently placing pods on nodes to optimize resources and maintain high availability of deployed applications.

Kubernetes allows you to define your scheduling algorithms or use advanced scheduling techniques such as: **Node Selector, Node Affinity, Pod Affinity/Anti-Affinity, Taints and Tolerations**

Note: To better observe the scheduling effect, you need a cluster with at least 2 worker nodes

### The Node Selector
Here the goal is to constraint the scheduler to place the pod on a specific node. To perform this, you need to add a label on the node then use that label in the pod specifications with the NodeSelector field.
- To add a label to a node, we can use the `kubectl label` command:
```bash
kubectl label nodes <node-name> key=value
# Example
kubectl label nodes node1 disktype=ssd
kubectl get nodes --show-labels
kubectl get nodes --show-labels | grep ssd 
```

- To create a pod that gets scheduled to your chosen node you must use the Nodeselector field and include the defined label
Check `pod-node-selector.yaml`

Note: You can also schedule a pod to one specific node via setting `nodeName`. Check `pod-node-name.yaml`

Note: When a there is no node selector specified in a pod, it can be placed on any node in the cluster. But when a pod has a node selector, it will remain pending if no node has the corresponding label.

### The Node Affinity

Node affinity is very similar to the node selector technique, but it has a better way of expressing constraints. Node affinity comes in two flavors: 
- preferredDuringSchedulingIgnoredDuringExecution (soft type)
- requiredDuringSchedulingIgnoredDuringExecution (hard type)

#### preferredDuringScheduling
1- The pod PREFERS to be scheduled on the nodes that meets the requirements. 
2- However the pod can still be placed on a node that does not meet one or more of the specified requirements.

#### requiredDuringScheduling
1- If the node meets the pod's requirements, then the pod can be scheduled there.
2- If no node meets the requirements, the pod remains pending.

#### IgnoredDuringExecution
1- A Pod is already running on the node 
2- The node no longer meets the requirements: The pod cannot be unscheduled, it continues its execution

Examples: Check `02-node-affinity` folder

#### Notes: 
- There is no Node Anti-Affinity field. To define the node anti-affinity behaviour, we use the value of the operator parameter.  You can use In, NotIn, Exists, DoesNotExist, Gt (greater than) and Lt (lower than). So you can use NotIN and DoesNotExist to configure node anti-affinity

- You can define a pod with both hard and soft type

- You can define pods with multiple node affinity constraints.

### The Pod Affinity/Anti-Affinity
Pod affinity enable us to schedule pods in the same location. (Schedule pod A near pod B)
Pod anti-affinity enable us to avoid scheduling pods in the same location. (Do not schedule pod A near pod B).

Just like the node affinity, it comes with a soft and a hard type (`preferredDuringSchedulingIgnoredDuringExecution`, `requiredDuringSchedulingIgnoredDuringExecution`)
Examples: Check `03-pod-affinity`

### Taints and tolerations

- Taints are properties set on nodes that allow them to repel a pod or a set of pods
- Tolerations are properties set on pods that enables them to be scheduled on the nodes with the matching taints.
Note: The master node is generally tainted in clusters

#### How to taint a node

To taint a node, you need to use the `kubectl taint` command with: The key, the value and the effect
```bash 
kubectl taint nodes node-name key=value:effect
```
- The **key** and the **value** (key=value) are used to identify the taint (just like a label).
- The **taint effect** defines what happens to pods that do not tolerate the taint.

There are three taint effects: 
- NoSchedule: Pods that do not tolerate the taint cannot be scheduled on the node. Running pods are not evicted 
- PreferNoSchedule: Pods that do not tolerate the taint prefer not to be scheduled on the node. Running pods are not evicted  
- NoExecute: Pods that do not tolerate the taint cannot be scheduled on the node. Running pods taht do not tolerate the taint are evicted.

#### How to set tolerations for pods
You specify a toleration for a pod in the tolerations field in the pod specifications. In this field, you use the key, value, effect but also operator parameter
The operator parameter can take two values: either “Equal” (this is the default) or “Exists”
- Equal: The key/value/effect field defined in the pod toleration must match the ones in the node taint
- Exists:
    - if key/effect are defined, they must match the taint
    - if key/effect are empty, the pod will tolerate any node
    - if effect is empty, the pod will tolerate any node tainted with the specified key

Practice: Check `04-taints-tolerations`
