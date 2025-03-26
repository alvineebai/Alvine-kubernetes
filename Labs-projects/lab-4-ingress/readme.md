## The ingress resource
Ingress exposes HTTP and HTTPS routes from outside the cluster to services within the cluster. Traffic routing is controlled by rules defined on the Ingress resource.

To use the ingress resource, you must configure an ingress controller such as ingress-nginx, HAproxy, AWS Load balancer

### Why Use Ingress?
- Reduces Cost – Uses a single entry point instead of multiple LoadBalancers.
- Supports Routing – Directs traffic based on URL paths or hostnames.
- Enables HTTPS (SSL) – Provides security for web applications.
- Load Balancing – Distributes traffic across multiple services.

### How Ingress Works
1- User sends a request to myapp.com/api.
2- Request reaches Ingress (single entry point).
3- Ingress checks rules and routes traffic.
4- Request is sent to the correct service inside the cluster.
5- The response is sent back to the user.

### Exercise
This hands-on exercise will help you understand how Ingress works by deploying two simple web services and configuring an Ingress resource to route traffic. Here, you will:
- Deploy two web services (web-service & api-service).
- Create a ClusterIP Service for each.
- Set up an Ingress resource to route traffic.
- Test it using minikube or a cloud Kubernetes cluster.

1- Create the manifests for the deployments and services (`web-deployment.yaml`, `api-deployment.yaml`) and apply them in the cluster
2- Create the manifest for the ingress resource and apply it in the cluster (`ingress.yaml`)
3- Setup the ingress controller 
Note: Here we are working with a eks cluster
- install the ingress controller in EKS
```bash
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
helm repo update
helm install nginx-ingress ingress-nginx/ingress-nginx \
  --set controller.publishService.enabled=true \
  --set controller.service.type=LoadBalancer
```
This creates an NGINX Ingress Controller and exposes it using an AWS LoadBalancer.
Verify the installation 
```bash
kubectl get pods -n default
kubectl get svc nginx-ingress-ingress-nginx-controller
```
Look for the EXTERNAL-IP. This is the AWS LoadBalancer created by Kubernetes.

