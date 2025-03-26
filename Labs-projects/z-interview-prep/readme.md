### Set 1
1- what is kubernetes and why is it so popular?
2- what exactly is self healing in kubernetes?
3- do you think kubernetes is portable, if yes how?
4- in kubernetes basics we have pods, why do we use them?
5- how does kubernetes ensure the availability of an application?
6- what's the difference between kubernetes deployment and kubernetes service?
7- do you have any experience on creating a cluster? Explain, walk me thru one example
was the cluster unmanaged or completely managed? Any idea about self hosted clusters
8- any idea on scaling kubernetes deployments?
9- what is k8s namespace and why is it useful?
10- how does kubernetes handle the persistent storage requirements of an application?
11- how and why do you use config maps?
12- any idea about kubernetes service account?
13- as a DevOps you have surely come across some deployment strategies like rolling updates. How do you perform the rolling update in kubernetes deployment? Do you remember the command that can help you do that? Kubectl set or edit
How do you initiate rollback on a deployment?
14- when do we use statefulsets, and how does it differ from deployment?
15- are you kubernetes certified or something?
16- as a k8s admin if I ask you to secure my cluster and I do not have any information about that, how will you do it. Just walk me thru one or two ways
17- any experience with monitoring pods in kubernetes cluster?
18- what's the current release of k8s?
### Set 2
1- how do you package kubernetes applications?
2- what are init containers and what are they generally used for
3- what are some kubernetes scheduling techniques you've used?
4- how do you prepare a node for maintenance?
5- imagine you have two containers running in the same pod. One for nginx and another for WordPress. How do you access these containers in the browser using IP address?
Just do port forwarding with Kubectl port-forward
6- If I have many containers running inside a pod and I want a specific container to start before starting another one, how can I do?
use init containers
use sidecar pattern
7- what is the impact of upgrading kubelet if we leave the pods running on the worker node? Will it break running pods? Why?
8- how are services mapped to pods in kubernetes?

### set 3
- Why do we need kubernetes? what problem does it solve?
- So, what have you done with Kubernetes?
- Was your Kubernetes experience ON-prem or managed Kubernetes
- What version of kubernetes did you used
- What size of cluster are you using for your environments?
- How did you manage the databases for your applications running in the cluster?
- There are many way to setup ingress in a cluster, what way did you use?

- How do you troubleshoot a crashing container
- Did you ever had issues with pods stopping?
- How do you manage scaling in kubernetes
- How was security set up in your k8s clusters?
- What are some best practices you have used in securing a Kubernetes cluster?
- What was the hardest production issue you faced when using kubernetes?
- What is the function of the `kubectl explain pod --recursive` command?
- How do you configure certificates in your cluster? Example SSL certificates
- What is the function of the command `kubectl api-resources`?
- When should you use daemonsets instead of deployments? specify 2 use cases of daemonsets
- How do you update a ConfigMap, and what happens to the Pods using it?
- What are the problems you recently faced while maintaining your kubernetes cluster and how did you resolve them?