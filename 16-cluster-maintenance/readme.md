## Cluster maintenance
Maintenance and troubleshooting tasks include:
- Node maintenance
- Control plane troubleshooting
- Network troubleshooting
- Application troubleshooting
- Cluster backup and upgrade
- and more

### Preparing node for maintenance
- cordon node: Mark the node as unschedulable. Running pods are not evicted.
- drain node: Mark the node as unschedulable. Running pods are evicted. Add --ignore-daemonsets option if daemonsets are runnig on the node
- uncordon node:Mark the node as schedulable after maintenance

### Troubleshooting in kubernetes clusters
Some commands that help troubleshoot:
- kubectl describe
- kubectl logs
- kubectl get pod/podName -o yaml > file-name
- kubectl exec podname -- command
- kubectl exec podname -c container-name -- command
- kubectl logs --previous podName containerName
- kubectl debug -it podName --image=imageName --target=podName

Checking the logs files
- /var/log/syslog for daemon logs like kubelet or docker
- /var/log/kube-apiserver.log, /var/log/kube-scheduler.log, /var/log/kube-controller-manager.log for kube-system processes
- Kube system pods logs are found at /var/log/pods or /var/lib/docker/containers for containers logs
- To check the logs at /var/log/kubelet.log or /var/log/kube-proxy.log for the kubelet and kube-proxy
- kubectl cluster-info dump: To get detailed information about the overall health of your cluster
- journalctl -u kubelet 

### Cluster High availability

In production environments, it is recommended to have many master nodes (odd numbers: 3, 5 ..) running for high availability.

### Backing up a cluster
For disaster recovery, it is important to backup cluster state periodically. This can be done by taking a snapshot of the etcd (`etcdctl snapshot save` with necessary parameters). To restore the state of the cluster, use `etcdctl snapshot restore` with necessary options.
It is also useful zo save cluster certificates.

See official documentation for more

### Upgrading kubeadm clusters
As kubernetes versions evolve, you might need to upgrade your cluster to a recent version.
See official documentation for more

