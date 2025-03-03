## The Replicaset
A ReplicaSet is a Kubernetes object that ensures a specified number of identical Pod replicas are running at any given time. If a Pod dies or is deleted, the ReplicaSet creates a new one. If you scale up, it adds more Pods; if you scale down, it removes extra Pods.

To define a replicaset, you must specify:
- A Selector that specifies how to identify Pods it can acquire
- A number of replicas indicating how many Pods it should be maintaining
- A pod template specifying the spec of the Pods that will be created.

Example: Create a replicaset running 3 replicas of pods using the httpd image
```bash
# Create the replicatset 
kubectl create -f replicaset-for-pods.yaml
# Check the pods
kubectl get pods
#
# Check the nodes where the pods are placed
kubectl get pods -o wide
#
# check the replicaset
kubectl get rs
#
# describe the replicaset
kubectl describe rs webserver
# 
# describe a pod
kubectl describe pod pod-name-here
#
# delete a pod and verify that the replicaset creates a new one
kubectl delete pod pod-name-here
#
# delete the replicaset
kubectl delete rs webserver
```
 
