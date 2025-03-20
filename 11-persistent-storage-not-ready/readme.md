## Persistent Storage in Kubernetes

Volumes are important for data persistence and file sharing between pods.
Persistent volumes (PVs) are volume plugins managed by kubernetes
They are just like physical storage on a hard drive that are attached to your pods for data storage
A Persistent Volume Claim (PVC) is a request for storage (PV) by a user. Claims can request for volumes with specific size and access modes

PV Provisioning can be static or dynamic 
- Static: The cluster administrator create a number of PVs of various type
- Dynamic: kubernetes dynamically provision a volume for a specific PVC (using storage classes).

### How to create a PV or a PVC
When defining a PV or a PVC, there are some parameters we need to know:
- The PV name in the metadata section
- The capacity (storage size)
- the access mode
    - ReadWriteOnce (RWO)
    - ReadOnlyMany (ROX)
    - ReadWriteMany (RWX)
    - ReadWriteOncePod (RWOP)
- the persistent volume reclaim policy (optional): specify what happens when the PV is released
    - Retain
    - Delete
- the storage backend: define the storage type (Example: NFS, csi, local ...)
- the storageClass name (optional): for dynamic provisioning

### Setting up dynamic provisioning in EKS cluster
Note: The cluster used for this practice is the one created with `eksctl` in the `00-cluster-setup` folder.

Before starting, you must create the cluster and update your `kubeconfig`

#### Associate an IAM OIDC Provider with the Cluster

```bash
eksctl utils associate-iam-oidc-provider --region us-east-1 --cluster my-cluster --approve
```
#### Create an IAM Service Account for the EBS CSI Driver

Create an IAM service account with permissions to manage Amazon EBS volumes. This example:
- Specifies the namespace (kube-system) and cluster name.
- Attaches the AmazonEBSCSIDriverPolicy, which provides the necessary permissions for the EBS CSI driver.

```bash
eksctl create iamserviceaccount \
  --name ebs-csi-controller-sa \
  --namespace kube-system \
  --cluster suzie-cluster \
  --attach-policy-arn arn:aws:iam::aws:policy/service-role/AmazonEBSCSIDriverPolicy \
  --approve \
  --region us-east-2
```
#### Install the Amazon EBS CSI Driver
Install the EBS CSI driver, which allows Kubernetes to manage EBS volumes dynamically. This command applies the configuration files from the official AWS EBS CSI driver GitHub repository.
```bash
kubectl apply -k "github.com/kubernetes-sigs/aws-ebs-csi-driver/deploy/kubernetes/overlays/stable/ecr/?ref=release-1.20"
```
#### Verify EBS CSI Controller Pods
Check that the EBS CSI controller pods are running in the kube-system namespace. This step ensures that the driver was installed correctly.
```bash
kubectl get pods -n kube-system -l app=ebs-csi-controller
```
#### Check DaemonSets and Deployments for the EBS CSI Driver
Verify that the EBS CSI driver DaemonSets and Deployments were successfully created. These resources should be running in the kube-system namespace.
```bash
kubectl get daemonset -n kube-system | grep ebs-csi
kubectl get deployment -n kube-system | grep ebs-csi
```
#### Verify Available Storage Classes
List the storage classes to ensure the EBS CSI driver storage class is available. This storage class will be used to create dynamically provisioned volumes.
```bash
kubectl get storageclass
```
You can see the default storage class (gp2)

Now you can create your own storage classes and pvc associated
