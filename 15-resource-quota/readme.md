## Resource Quota in a cluster

When a cluster is shared by multiple teams, it becomes necessary to limit the amount of resources that can be used by each team. Resource Quota allows you to limit resource usage in the cluster.
- We can create resource quotas per namespace on compute resources such as memory, cpu etc.
- We can also create resource limits based on the number of objects of a certain type created in a namespace.
Example: Limiting 4 pods and 3 deployments in the dev namespace 