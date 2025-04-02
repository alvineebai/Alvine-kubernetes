## Cluster maintenance
Maintenance and troubleshooting tasks include:
- Node maintenance
- Control plane troubleshooting
- Network troubleshooting
- Application troubleshooting
- Cluster backup and upgrade
- and more

Note: These operations executed by cluster administrators. Maintenance operations may vary depending on how your cluster is setup (cloud or On-prem)

### Preparing node for maintenance
- **cordon node**: Mark the node as unschedulable. Running pods are not evicted.
- **drain node**: Mark the node as unschedulable. Running pods are evicted. Add **--ignore-daemonsets** option if daemonsets are runnig on the node
- **uncordon node**:Mark the node as schedulable after maintenance

### Cluster High availability

In production environments, it is recommended to have many nodes in the control plane (odd numbers: 3, 5 ..) running for high availability.

### Backing up a cluster
For disaster recovery, it is important to backup cluster state periodically. This can be done by taking a snapshot of the etcd (`etcdctl snapshot save` with necessary parameters). To restore the state of the cluster, use `etcdctl snapshot restore` with necessary options.
It is also useful to save cluster certificates.

See official documentation for more

### Upgrading kubeadm clusters

As kubernetes versions evolve, you might need to upgrade your cluster to a recent version.
See official documentation for more

## Troubleshooting in kubernetes clusters

### Kubectl Commands & Their Roles

Some commands that help troubleshoot:

| Command | Role/Purpose | When to Use |
|---------|-------------|-------------|
| `kubectl describe <resource>` | Shows detailed configuration, events, and state of a resource (Pod, Node, Service, etc.) | Debugging why a Pod is stuck, checking Node conditions, or inspecting Service endpoints |
| `kubectl logs <pod>` | Retrieves logs from a running Pod's primary container | Debugging application errors or crashes |
| `kubectl get pod/<pod> -o yaml > file.yaml` | Exports the full Pod manifest (YAML) to a file | Analyzing Pod specs, debugging misconfigurations, or backing up definitions |
| `kubectl exec <pod> -- <command>` | Executes a command inside a Pod's primary container | Running diagnostic tools (`curl`, `ping`, `nslookup`) or checking files |
| `kubectl exec <pod> -c <container> -- <command>` | Executes a command in a specific container (multi-container Pods) | Debugging sidecar containers |
| `kubectl logs --previous <pod> -c <container>` | Shows logs from a previously crashed container | Diagnosing `CrashLoopBackOff` errors |
| `kubectl debug -it <pod> --image=<image> --target=<pod>` | Creates an ephemeral debug container attached to a Pod | Troubleshooting containers or when the Pod lacks shells (`/bin/sh`) |

### Key Log Files & Their Roles

| Log File | Role/Purpose | When to Check |
|----------|-------------|---------------|
| `/var/log/syslog` | System-wide logs (includes `kubelet`, `docker`, `containerd`) | Node-level issues (e.g., `kubelet` crashes, Docker daemon failures) |
| `/var/log/kube-apiserver.log` | API server logs (control plane) | Authentication errors, API throttling, or `kubectl` connectivity issues |
| `/var/log/kube-scheduler.log` | Scheduler decision logs | Debugging Pod scheduling failures (`Pending` state) |
| `/var/log/kube-controller-manager.log` | Controller manager logs (replica sets, deployments) | Issues with scaling or replication |
| `/var/log/pods/` | Logs for all Pods (symlinked to container logs) | Debugging application-specific logs without `kubectl` |
| `/var/lib/docker/containers/` | Raw Docker container logs (if using Docker runtime) | Low-level container runtime issues |
| `/var/log/kubelet.log` | `kubelet` logs (node agent) | Pod startup failures, volume mounting errors |
| `/var/log/kube-proxy.log` | `kube-proxy` logs (network rules) | Service networking issues (e.g., `ClusterIP` not working) |

### Cluster-Wide Debugging Commands

| Command | Role/Purpose | When to Use |
|---------|-------------|-------------|
| `kubectl cluster-info dump` | Dumps full cluster state (Pods, Nodes, Events) to stdout/files | Post-mortem analysis or opening support tickets |
| `journalctl -u kubelet` | Systemd logs for the `kubelet` service | When `kubelet` fails to start or crashes |

### Summary of Use Cases

- **Pod Issues**: Use `kubectl describe`, `logs`, `exec`, and `/var/log/pods/`
- **Node Issues**: Check `kubectl get nodes`, `journalctl -u kubelet`, and `/var/log/syslog`
- **Control Plane Issues**: Inspect `kube-apiserver.log`, `kube-scheduler.log`, and `cluster-info dump`
- **Networking Issues**: Debug with `kube-proxy.log` and `kubectl get endpoints`



