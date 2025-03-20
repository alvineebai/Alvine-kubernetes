## What is Helm?

Helm is a package manager for kuberetes. See it as apt for ubuntu or yum for redhat

With Helm, users can install, upgrade and uninstall applications on a Kubernetes cluster using few commands

### Helm architecture and terminology

- Helm client: command-line tool used to manage Kubernetes applications with Helm
- Helm Chart: A package of Kubernetes resources (templates & configs) used to deploy an app.
- Repository: A storage location for Helm charts (local or remote, public or private).
- Release: A running instance of a chart in a Kubernetes cluster.
- Release Namespace: The Kubernetes namespace where a release is deployed.
- Values: Configurable parameters used to customize a chart’s deployment.
- Template: Dynamic YAML files that generate Kubernetes resources using provided values.
- Tiller: Deprecated server component in Helm 2 (removed in Helm 3).
- Dependency: A chart that another chart relies on for deployment.
- Upgrade: Updating an existing release with new chart versions or configs.
- Rollback: Reverting a release to a previous working version.

### Helm chart structure
chart name
- Chart.yaml	Metadata about the chart (name, version, description).
- values.yaml	Default configuration values for templates.
- templates/	Contains Kubernetes YAML templates (Deployment, Service, Ingress, etc.).
- templates/_helpers.tpl	Defines reusable template functions.
- templates/NOTES.txt	Shows installation notes after deployment.
- charts/	Stores dependent charts if needed.
- README.md	Documentation for the chart.

## What is Lens?
Lens is a Kubernetes IDE (Integrated Development Environment) that provides a visual interface to manage and monitor Kubernetes clusters. 
Yon work without needing to use kubectl commands all the time.

### Key features
- Cluster Dashboard: View real-time metrics, logs, and events
- Multi-Cluster Management: Manage multiple Kubernetes clusters in one UI
- Built-in Terminal: Run kubectl commands directly in Lens
- Workloads Overview: See Deployments, Pods, Services, Ingress, and more
- Role-Based Access Control (RBAC): Manage users and permissions
- Helm Integration: Deploy and manage Helm charts
- Resource Monitoring: CPU, memory, and network usage

### Helm installation



 

