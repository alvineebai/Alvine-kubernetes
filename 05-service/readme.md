## The Service

Since pods are ephemeral (can be created and destroyed at any moment), their IP addresses cannot be relied on for communication, that’s where the service resource comes in the picture. Service provides a single IP address and port for a set of Pods, it can load-balance across them and do health checks.

### Expose a pod or a deployment using a service
To define a pod and expose it using a service, the label of the pod must match the selector of the service.
Example: check the `service-pod-def.yaml` and `service-deployment-def.yaml`

### Types of Service

- **ClusterIP (default)**
    - use case: Internal communication between Pods
    - Accessible from: Inside the cluster only
    - Load Balancer: No

- **NodePort**
    - use case: Exposes a service on a static port on each node
    - Accessible from: Outside the cluster via <Node-IP>:<port>
    - Load Balancer: No

- **Loadbalancer**
    - use case: Exposes service externally using a cloud provider's load balancer
    - Accessible from: Internet (External IP)
    - Load Balancer: yes

- **ExternalName**
    - use case: Maps a service to an external DNS name
    - Accessible from: Outside the cluster
    - Load Balancer: No

