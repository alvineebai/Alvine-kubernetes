## The Pod in Kubernetes
### Definition
A pod is the smallest and simplest Kubernetes object (Smallest or atomic unit in K8s) and can contain one or more containers. Each pod has its own IP address for communication. Containers in the pod share the pod IP address and namespace

Limitations: Pods are ephemeral ie they can die and be replaced easily

### Running a Pod
To run a pod in the cluster, you can use either a manifest or the `kubectl run` command with parameters

#### Using the manifest 

1- Create a a simple pod running the httpd image.
- Create a yaml file called `simple-pod.yaml`. Its content should look like the following
```yaml
apiVersion: v1
kind: Pod
metadata:
 name: utrains
spec:
 containers:
   - name: utrains-app
     image: httpd
```
- Create the pod in the cluster with the command:
```bash
kubectl create -f simple-pod.yaml
```
- List and describe pods
```bash
kubectl get pods 
kubectl get pods -A 
kubectl get pods -o wide
kubectl describe pod utrains
```
#### Using the command
Create a pod running the nginx image
```bash 
kubectl run app1 --image=nginx
kubectl get pod
kubectl get pod -o wide 
```
### Practice with pods
1- Create pod with a wrong image name then edit it to modify the image name
```bash 
kubectl run app2 --image nginxjdf
kubectl get pods
kubectl get pod -o wide
kubectl describe pod app2
kubectl edit pod app2
# modify the image name to nginx
kubectl get pods
```
2- Get the logs of a pod
```bash 
kubectl logs app1
kubectl logs app2
```

3- Delete pods
```bash 
kubectl delete pod app1
kubectl delete pod app2
kubectl delete pod utrains
```
4- Generate definition file for running a pod with nginx and write it into a file called pod.yaml
```bash
kubectl run nginx2 --image=nginx --dry-run=client -o yaml > pod.yaml
cat pod.yaml
```
5- create a pod in a specific namespace. check the pod-namespace.yaml manifest.
```bash
kubectl create ns dev
kubectl create -f pod-namespace.yaml
kubectl get pods -n dev
kubectl get pods
kubectl delete pod utrains -n dev
```

6- Create a pod in a specific namespace exposing a port (80) using TCP protocol
Check the `pod-with-port.yaml` manifest

### Pods Quality of Service (QoS)
When Kubernetes creates a Pod, it assigns a QoS class to the pod based on the resource (CPU, memory) requests and limits defined in that pod. There are 3 QoS classes
- Best effort: No resources requests or limits defined
- Burstable: resources requests < resources limits
- Guaranteed: resources requests = resources limits

Example: Check the `pod-qos-besteffort.yaml`, `pod-qos-burstable.yaml` and `pod-qos-guaranteed.yaml`

### Pods priorities (optional)
When creating pods, kubernetes also assign levels of priorities. By default, all the pods you create have the priority of 0 when no priority class is specified.
We have 3 levels of priorities: Critical, higher priority, lower priority. To use priorities, we need to define PriorityClasses and assign them to pods

Example: Check the `pod-priotity-class.yaml` and `pod-with-priority.yaml`

### Pods Probes (health checks)
A Probe is like a diagnostic that is periodically performed by the Kubelet on the containers of a Pod. The result of these checks can be Success, Failure or Unknown. There are 3 types of probes in K8s:
- LivenessProbe: indicates whether the container is running or not.
- ReadinessProbe: indicates whether the container is ready to respond to requests or not.  
- StartupProbe: indicates when a container application has started. This is very useful for slow starting containers.

Example: Check the `pod-liveness-probe.yaml`, `pod-readiness-probe.yaml`, `pod-startup-probe.yaml`
