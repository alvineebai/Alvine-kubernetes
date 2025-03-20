## What is kubernetes?
Kubernetes (K8s) is an open-source container orchestration platform that help to automate the deployment, scaling, and management of containerized applications.

## Why use kubernetes?
- Companies moved from **physical servers → VMs → containers → Kubernetes** to make applications run faster, more efficient (optimize resources usage), and easier to manage at scale.
- companies are moving from **monolithic** app to **microservices** architecture increasing the usage of containers and the need to efficiently manage them in various environments.
## Kubernetes architecture
Kubernetes has a **Master-Worker** architecture:
### Master node (control plane):
The control plane manages the worker nodes and the pods in the cluster. It contains:
- **API Server** – Entry point for commands (talks to users & other parts).
- **Scheduler** – Decides where to run new containers.
- **Controller Manager** – Handles tasks like scaling & failures.
- **etcd** – Stores all cluster data (like a database).
### Worker node (nodes):

- **Kubelet** – Talks to the master, runs & manages containers.
- **Container Runtime** – Runs containers (e.g., Docker, containerd).
- **Kube Proxy** – Manages network communication between containers.

NB: master also has **kubelet, kube-proxy and container runtime** because it runs some system pods 
### Pods
Smallest unit in K8s, holds one or more containers.

## Key features of kubernetes
- **Autoscaling** – Increases or decreases containers based on traffic.
- **Self-Healing** – Restarts failed containers, replaces unhealthy ones.
- **Load Balancing** – Distributes traffic across containers for efficiency.
- **Rolling Updates & Rollbacks** – Updates apps without downtime and rolls back if needed (high availability)
- **Service Discovery** – Finds and connects services without manual setup (using IP adress or DNS).
- **Storage Management** – Supports local, cloud, or network storage easily.
- **Multi-Cloud & Hybrid Support** – Runs across AWS, Google Cloud, Azure, and on-prem.
- **Automated Deployments** – Uses YAML files for easy and repeatable deployments.
- **Secrets & Config Management** – Manages configuration and sensitive data securely.
- **Resource Optimization** – Ensures best use of CPU, memory, and storage (automatic bin packing)
- **production Support**– Can be used for production environments

## K8s cluster setup
### For learning and local dev or testing
- **Minikube** – Runs a single-node K8s cluster on your laptop.
- **Kind (Kubernetes in Docker)** – Uses Docker to run lightweight K8s clusters.
- **K3s** – A lightweight Kubernetes distribution for low-resource systems.
- **MicroK8s** – A lightweight K8s version from Canonical.

### For production
#### 1. Manual setup (unmanaged)
    - using Kubeadm
    - Using kubespray
    - Using Kops
    - From scratch (complex)

#### 2.  Cloud based (managed)
    - Amazon EKS – Kubernetes on AWS.
    - Google GKE – Kubernetes on Google Cloud.
    - Azure AKS – Kubernetes on Microsoft Azure.
    - Oracle OKE - Kubernetes on Oracle
    - LKE - Kubernetes on Linode
    - ...

#### 3. Enterprise Kubernetes (K8s distributions)
    - Openshift – Red Hat’s enterprise Kubernetes with extra features.
    - Rancher – Multi-cluster Kubernetes management.
    - Tanzu - vSphere Tanzu Kubernetes Grid (TKG)
    - ...

## Interacting with cluster
- **Command Line Interface (CLI)**: using `kubectl` commands
- **User Interface (UI)**: using the native **K8s Dashboard** or tools like **Lens**
- **Kubernetes API**: Using programmatical access (ex. with python scripts, terraform codes etc.)

## How to use this repo
The repo is organized and ordered for you to practice the concepts progressively.