## The Deployment

A deployment is a higher level object that creates and manages ReplicaSets which then ensures that the desired number of pods are running. More than a simple replicaset, the deployment allows you to handle **rolling updates (rollout), rollbacks and version management** of applications.

**Example:** Create a deployment that will run three Nginx pods, each listening on port 80.
1- Create and verify the deployment
```bash
# create and apply the manifest
kubectl apply -f nginx-deployment.yaml
#
# Verify the deployment
kubectl get deployment
kubectl get pods
kubectl get rs
kubectl describe deployment nginx-deployment
#
# delete a pod and verify it is replaces
kubectl delete pod <pod-name>
kubectl get pods -o wide
```

2- Update the deployment
- Update the nginx image from 1.14.2 to 1.16.1
Let's update the nginx Pods to use the nginx:1.16.1 image instead of the nginx:1.14.2 image
```bash
kubectl set image deployment/nginx-deployment nginx=nginx:1.16.1
```
To see the rollout status, run:
```bash
kubectl rollout status deployment/nginx-deployment
```
Verify:
```bash
kubectl get deployments
kubectl get rs
kubectl get pods
kubectl describe deployment nginx-deployment
```
NB: you can also use the `kubectl edit` command to update the manifest

- Update the image including and error: instead of 1.16.1 use 1.161
```bash
kubectl set image deployment/nginx-deployment nginx=nginx:1.161
```
To see the rollout status, run:
```bash
kubectl rollout status deployment/nginx-deployment
```
Verify:
```bash
kubectl get deployments
kubectl get rs
kubectl get pods
kubectl describe deployment nginx-deployment
```
Note:
The Deployment controller stops the bad rollout automatically

3- Check the rollout history of a deployment
```bash
kubectl rollout history deployment/nginx-deployment
# Example see the details of revision 2
kubectl rollout history deployment/nginx-deployment --revision=2
```

4- Rolling back to a previous revision
```bash
# undo the current rollout and rollback to the previous revision
kubectl rollout undo deployment/nginx-deployment
kubectl get deployment nginx-deployment
kubectl describe deployment nginx-deployment
```
5- Scale the deployment up and down
```bash
kubectl scale deployment/nginx-deployment --replicas=5
kubectl get pods
kubectl get rs
kubectl scale deployment/nginx-deployment --replicas=2
kubectl get pods
kubectl get rs
```
6- Delete the deployment
```bash
kubectl delete deployment nginx-deployment
```


### The Deployment strategy type
The Strategy type in the deployment specification defined how the update process takes place. It can take two values:  
- **RollingUpdate (default value):** Here the pods are replaced gradually, reducing downtime. In this type, we have two key parameters that we can specify:
    - **maxUnavailable:** Number of pods that can be unavailable during update
    - **maxSurge:** Number of extra pods (above the specified number of replicas) allowed during update.
- **Recreate:** Deletes all old pods at once, then creates new ones.This is to be used when the app can handle downtime

Example: check the `deployment-rolling-update.yaml` and `deployment-recreate.yaml`

### Autoscaling a deployment (HPA)
To enable autoscaling for a deployment, we need a specific object called HPA (Horizontal Pod Autoscaler) that will automatically scale the application up or down based on the load.

Steps to get started with HPA
- Install the metrics server in the cluster
- Deploy an application (Deployment)
- Create the Horizontal Pod Autoscaler for the deployment And monitor the HPA events 
- Create the load and check how the HPA is working (it should scale up)
- Reduce the load (the HPA should scale down the pods)
- Clean up (optional)

NB: you can also use the `kubectl autoscale` command to autoscale a deployment when the HPA is enabled in your cluster.
