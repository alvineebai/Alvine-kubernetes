## The Daemonset
Daemonsets ensure that a copy of a desired pod run on all (or some) nodes of the cluster.It can be used in the following cases:
- To run a daemon for logs collection on each node (like Fluentd or logstash.
- To run a daemon for cluster storage on each node (like glusterd ceph) 
- To run a daemon for logs rotation and cleaning log files.
- To run a daemon for node monitoring (such as Prometheus Node Exporter, collectd.

Just like Deployments, Daemonsets have two strategies types:
- RollingUpdate: old DaemonSet pods will be killed and replaced progressively.
- OnDelete: new DaemonSet pods will only be created when you manually delete old pods.

Example: Create a daemonset for node monitoring using Prometheus Node Exporter. Check `prometheus-daemonset.yaml`
Example: Create a daemonset which runs the fluentd-elasticsearch Docker image in the cluster. Check `fluentd-daemonset.yaml`
