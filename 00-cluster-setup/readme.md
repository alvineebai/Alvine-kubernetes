# K8s Cluster setup

In this part, you will learn how to setup a kubernetes cluster in AWS using eksctl. 

**Note:** To avoid charges in AWS while practicing simple notions, you could use a kubernetes playground. Killercoda is a platform that provides various kubernetes playgrounds and sandboxes to practice: [Link here](https://killercoda.com/). Select the latest a playground with the latest version of Kubernetes available and get started!

## Create EKS cluster via eksctl

### Prerequisites
Before proceeding, ensure that you have the following prerequisites:

- **AWS Account:** You need to have an AWS account with the appropriate permissions to create and manage EKS clusters.

- **IAM Permissions:** Ensure that your IAM user has sufficient permissions to create an EKS cluster, EC2 instances, and other resources required by EKS.

- **AWS CLI:** The AWS Command Line Interface (CLI) must be installed and configured with your AWS credentials. You can configure it using the following command:
```bash
aws configure
```
- **kubectl:** The Kubernetes command-line tool (kubectl) should be installed. You can install it following the [instructions here](https://kubernetes.io/docs/tasks/tools/#kubectl).
**Note:** On windows, you can use `choco`. Scroll down in the official documentation link given above to find the name of the package.

### Install eksctl
Refer to the following documentation to install eksctl on your system: [Link here](https://eksctl.io/installation/)
**Note:** On windows, you can use `choco`. Scroll down in the official documentation link given above to find the name of the package.

### Create the cluster
Use the following command to create a cluster:
```bash
eksctl create cluster --name my-cluster --region us-east-1 --nodegroup-name my-nodes --node-type t3.small --nodes 2 --nodes-min 1 --nodes-max 2
```

### Update the Kubeconfig

After creating the cluster, you need to update the context in the local Kubeconfig file in order to interact with the cluster.

```bash
aws eks --region us-east-1 update-kubeconfig --name my-cluster
```
Verify that you can access the cluster
```bash
kubectl get nodes
```
### Delete the EKS Cluster (When you finish practice)

When done, always delete resources to avoid further charges 
```bash
eksctl delete cluster --name my-cluster --region us-east-1
```

## Basic commands
| **Command**                                      | **Description**                                                                 |
|--------------------------------------------------|---------------------------------------------------------------------------------|
| `kubectl cluster-info dump`                      | Get details about the cluster (API server, components, etc.).                   |
| `kubectl config view`                            | view the Kubeconfig file                                                        |
| `kubectl get nodes`                              | List all nodes in the Kubernetes cluster.                                       |
| `kubectl get nodes -o wide`                      | Show more details on node                                                       |
| `kubectl get pods`                               | List all pods in the default namespace.                                         |
| `kubectl get pods -o wide`                       | Show more details (e.g., pod IP, node, etc.).                                   |
| `kubectl get deployments`                        | List all deployments in the default namespace.                                  |
| `kubectl get services`                           | List all services in the defaut namespace                                       |
| `kubectl describe pod <pod_name>`                | Display informations about the pod specified                                    |
| `kubectl logs <pod_name>`                        | Display pod logs, useful for troubleshooting                                    |
| `kubectl proxy`                                  | Access the Kubernetes cluster dashboard (if set up).                            |

## Kubernetes Cheat sheet
Kubernetes provides a quick reference to all kubernetes commands used to manage the resources and objects in the cluster: [Official link here](https://kubernetes.io/docs/reference/kubectl/quick-reference/)


## Other methods for setting up a cluster

- Setup a single node cluster locally with Minikube: [Offical documentation link](https://minikube.sigs.k8s.io/docs/start/)
- Setup a local cluster with kind: [Offical documentation link](https://kind.sigs.k8s.io/docs/user/quick-start/#installation)

- Install and configure a 3 nodes self-managed cluster with kubeadm: [Official documentation link](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/)
- Create EKS cluster via AWS console: [Link here](https://docs.aws.amazon.com/eks/latest/userguide/create-cluster.html#step2-console)
- Automate EKS cluster creation using terraform [Link to the repo](https://github.com/utrains/provision-eks-cluster-with-terraform.git)

