## The configMap resource

It is a good practice is to keep your application code separate from your environments (dev, test, qa, prod) configuration.A ConfigMap is an object that allows you to store non-confidential configuration data as key-value pairs. With ConfigMaps, you can store configuration settings like:  connection strings, public credentials, hostnames, and URLs.

### How to create a configmap

You can create a configmap from the command line
- Using literal values with the option `--from-literal` 
Example
```bash
kubectl create configmap my-config1 --from-literal port=3090 
kubectl get cm
kubectl describe cm my-config1
```
- Using files and directories with the option `--from-file` to pass the filename or the directory 
```bash
vi app.configfile
```
The content should look like the following
```bash
port=2000
environment=staging
language=english
```

Create a configmap with this file
```bash
kubectl create configmap my-config2 --from-file app.configfile
kubectl get cm
kubectl describe cm my-config2
```
- Using a manifest
```bash
vi my-configmap.yaml
```

The content should look like the following
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: my-configmap
data:
  mydata: hello_world
```
Create the ConfigMap using the yaml 
```bash
kubectl create -f my-configmap.yaml
kubectl get cm
kubectl describe cm my-configmap
```

### How to use the configmap in pods or deployments

There are four different ways that you can use a ConfigMap to configure a container inside a Pod:

- Inside a container command and args
- As environment variables for the container
- As a read-only Volume for the application to read
- Write a program that will run inside the Pod and use the Kubernetes API to read the ConfigMap
Here, we will show how to use the 3 first methods

#### 1- Using Environment Variables: 
Example 1: Let’s create a Pod (`my-configmap-pod.yaml`) that will consume the data from the configmap called my-configmap (`my-configmap.yaml`) 
```bash
kubectl create -f my-configmap-pod1.yaml
kubectl get pods
```

Get into the pod and print all the environment variable
```bash
kubectl exec -it my-configmap-pod1 -- printenv
```
You can see the data from the the configmap is set as an environment variable in the pod.

Note: The env field is convenient for setting a few environment variables for the application. However, when the number of environment variables gets large, it’s more convenient to group them in a single ConfigMap and use the envFrom syntax instead.


#### 2- Using volumes

Let’s create a Pod (`pod-configmap-volume.yaml`) that will consume the data from the configmap (`my-configmap.yaml`) using volumes
```bash
kubectl create -f my-configmap-pod2.yaml

#If you are not on a Linux server, the following commands might throw a No such file or directory error.
#Just make sure you understand the concept.

kubectl exec -it my-configmap-pod2 -- ls /config

kubectl exec -it my-configmap-pod2 -- cat /config/app.configfile
```

#### 3- Using a container command and args

Let’s use this example. The configmap and the pod specifications are written in the same YAML file. You can seperate them if you want. Check `pod-configmap-arg.yaml`

This example runs the command given by the $STARTUP_COMMAND environment variable when the container starts. The variable’s value is set to the default_command key within the created ConfigMap.

Note: If you are on Windows the command might throw a validation error since we have put the specifications of 2 objects in the file. You can separate the ConfigMap and the pod objects in two different files and create them.

kubectl create -f pod-configmap-arg.yaml

You can learn more on configmaps using the kubernetes official documentation