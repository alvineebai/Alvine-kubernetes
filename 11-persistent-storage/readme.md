**Note: This practice should be done in the EKS cluster**

## Persistent Storage in Kubernetes

- Volumes are important for data persistence and file sharing between pods.
- **Persistent volumes (PVs)** are volume plugins managed by kubernetes
- They are just like physical storage on a hard drive that are attached to your pods for data storage
- A **Persistent Volume Claim (PVC)** is a request for storage (PV) by a user.

### PV provisioning

PV Provisioning can be static or dynamic:

- **Static:** The cluster administrator create a number of PVs of various type, users can then define PVCs to use the PV created.
- **Dynamic:** kubernetes dynamically provision a volume for a specific PVC (using storage classes).

### How to create a PV or a PVC
When defining a PV or a PVC, there are some parameters we need to know:
- The **PV name** in the metadata section
- The **capacity** (storage size)
- the **access mode**
    - ReadWriteOnce (RWO)
    - ReadOnlyMany (ROX)
    - ReadWriteMany (RWX)
    - ReadWriteOncePod (RWOP)**
- The persistent volume **reclaim policy** (optional): specify what happens when the PV is released. It can take the values
    - Retain: keep the PV after the PVC deletion
    - Delete: Delete the PV after the PVC deletion
- the **storage backend:** define the storage type (Example: NFS, csi, local ...)
- the **storageClass name** (optional): for dynamic provisioning

### Setting up dynamic provisioning in EKS cluster with EBS volumes

**Note:** The cluster used for this practice is the one created with `eksctl` in the `00-cluster-setup` folder.

Before starting, you must create the cluster and update your `kubeconfig`

#### 1- Associate an IAM OIDC Provider with the Cluster

```bash
eksctl utils associate-iam-oidc-provider --region us-east-1 --cluster my-cluster --approve
```
#### 2- Create an IAM Service Account for the EBS CSI Driver

Create an IAM service account with permissions to manage Amazon EBS volumes. This example:
- Specifies the namespace `kube-system`, cluster name `my-cluster` and the region `us-east-1`.
- Attaches the **AmazonEBSCSIDriverPolicy**, which provides the necessary permissions for the EBS CSI driver.

**Note**: Copy the command and paste it in an editor if you have some modifications on the cluster name or the region for example. After modifications you can run it in your terminal.

```bash
eksctl create iamserviceaccount \
  --name ebs-csi-controller-sa \
  --namespace kube-system \
  --cluster my-cluster \
  --attach-policy-arn arn:aws:iam::aws:policy/service-role/AmazonEBSCSIDriverPolicy \
  --approve \
  --region us-east-1
```
#### 3- Install the Amazon EBS CSI Driver
Install the EBS CSI driver, which allows Kubernetes to manage EBS volumes dynamically. 

This command applies the configuration files from the official AWS EBS CSI driver GitHub repository.
```bash
kubectl apply -k "github.com/kubernetes-sigs/aws-ebs-csi-driver/deploy/kubernetes/overlays/stable/ecr/?ref=release-1.20"
```
#### 4- Verify EBS CSI Controller Pods
Verify that the EBS CSI controller pods are running in the kube-system namespace. This step ensures that the driver was installed correctly.
```bash
kubectl get pods -n kube-system -l app=ebs-csi-controller
```
#### 5- Verify DaemonSets and Deployments for the EBS CSI Driver
Verify that the EBS CSI driver DaemonSets and Deployments were successfully created. 

These resources should be running in the **kube-system** namespace.
```bash
kubectl get daemonset -n kube-system | grep ebs-csi
kubectl get deployment -n kube-system | grep ebs-csi
```
#### 6- Verify Available Storage Classes
List the storage classes to ensure the EBS CSI driver storage class is available. 

This storage class will be used to create dynamically provisioned volumes.
```bash
kubectl get storageclass
```
You can see the default storage class (**gp2**)

We will deploy a PVC using this default storage class and deploy a Pod that will comsume that PVC.

### LAB: Using dynamic storage with EBS to deploy a pod with persistent storage

This lab demonstrates how to create a **PersistentVolumeClaim (PVC)** using EBS with the default **gp2 StorageClass** in AWS EKS.

After enabling dynamic provisioning in EKS, you can create a PVC and deploy a pod that will consume that volume.

1. Create a manifest for the PVC and the pod (`ebs-pvc-pod.yaml`). 

The content should look like the following:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: ebs-pvc
spec:
  accessModes:
    - ReadWriteOnce  # Only one node can mount the volume
  resources:
    requests:
      storage: 5Gi  # Request 5GB of storage
  storageClassName: gp2  # Default StorageClass for EBS in AWS
---
## Pod that will consume the PVC
apiVersion: v1
kind: Pod
metadata:
  name: pod-ebs
spec:
  containers:
  - name: app
    image: busybox
    command: [ "sh", "-c", "echo 'Hello from EBS!' > /data/test.txt && sleep 3600" ]
    volumeMounts:
    - mountPath: "/data"
      name: storage
  volumes:
  - name: storage
    persistentVolumeClaim:
      claimName: ebs-pvc
```
2. Apply your file in the cluster

```bash
kubectl apply -f ebs-pvc-pod.yaml
```
Verify that the PVC status is **Bound** and that the pod is running and using the volume
```bash
kubectl get pvc
kubectl get pod
kubectl exec -it pod-ebs -- cat /data/test.txt
```
If the pod is recreated on the same node, it should still have access to the data.

**Note:** In AWS EKS, **EBS does not support** **RWX(ReadWriteMany)**. It is limited to **RWO(ReadWriteOnce)** It means the volume created by ebs can only be mounted to one node. 

To allow many nodes to mount a volume, you need to use another service like **Amazon EFS** that support **RWX**.

### Setting up dynamic provisioning in EKS using Amazon EFS

#### Prerequisites

- **Amazon EKS Cluster:** Ensure you have a running EKS cluster.
- **AWS CLI:** Install the AWS Command Line Interface.
- **kubectl:** Install the Kubernetes command-line tool.
- **eksctl:** Install `eksctl` for EKS cluster management.
- **Helm:** Install Helm for managing Kubernetes packages.

#### Step 1: Create an IAM policy, Role  ad service account for the EFS CSI Driver
The Amazon EFS Container Storage Interface (CSI) driver requires specific AWS Identity and Access Management (IAM) permissions to manage EFS resources.

1. **Create an IAM Policy:**
First download the example policy document
```bash
   curl -O https://raw.githubusercontent.com/kubernetes-sigs/aws-efs-csi-driver/master/docs/iam-policy-example.json
```

Now, create the IAM policy with the policy document

```bash
aws iam create-policy \
  --policy-name AmazonEFSCSIDriverPolicy \
  --policy-document file://iam-policy-example.json
```

2. Associate an OIDC Provider with Your EKS Cluster:
```bash
eksctl utils associate-iam-oidc-provider --cluster my-cluster --region us-east-1 --approve
```

3. Create the IAM Role and Service Account:

Use eksctl to create the IAM role and associate it with a Kubernetes service account:

Replace:

- your-cluster-name with your EKS cluster name.
- aws-account-id with your AWS account ID.
- your region 

```bash
eksctl create iamserviceaccount \
  --name efs-csi-controller-sa \
  --namespace kube-system \
  --cluster your-cluster-name \
  --attach-policy-arn arn:aws:iam::<aws-account-id>:policy/AmazonEFSCSIDriverPolicy \
  --region us-east-1
  --approve
```

#### Step 2: Install the Amazon EFS CSI Driver
Here, you must have Helm installed on your system.

Install the **EFS CSI driver** using **Helm**:

```bash
helm repo add aws-efs-csi-driver https://kubernetes-sigs.github.io/aws-efs-csi-driver/
helm repo update
helm install aws-efs-csi-driver aws-efs-csi-driver/aws-efs-csi-driver \
  --namespace kube-system \
  --set controller.serviceAccount.create=false \
  --set controller.serviceAccount.name=efs-csi-controller-sa
```
This command installs the EFS CSI driver and configures it to use the previously created service account.

Verify that the pods are running
```bash
kubectl get pods -n kube-system
```

#### Step 3: Create an Amazon EFS File System

Create an EFS file system in your AWS region

Replace the region with your AWS region. Note the `FileSystemId` from the output for later use.

```bash
aws efs create-file-system --region us-east-1
```

⚠ **WARNING**: Ensure the security group associated with your EFS allows inbound NFS traffic (port 2049) from your EKS worker nodes.

Now we can use the `FileSystemId` to create a storage class for dynamic provisioning.

#### Create the storageClass for dynamic provisioning

Define a StorageClass that references your EFS file system:
```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: efs-sc
provisioner: efs.csi.aws.com
parameters:
  provisioningMode: efs-ap
  fileSystemId: your-efs-filesystem-id
  directoryPerms: "700"
```

Replace **your-efs-filesystem-id** with the **FileSystemId** obtained earlier. Apply this configuration:

```bash
kubectl apply -f storageclass.yaml
kubectl get storageclass
```
#### Create a PVC and a deployment 

Create a file for the PVC and the deployment (`efs-pvc-deployment.yaml`). The content should look like:

```yaml
### pvc definition
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: efs-pvc
spec:
  accessModes:
    - ReadWriteMany  # RWX for many nodes
  resources:
    requests:
      storage: 5Gi  # size (EFS is elastic thus adjust automatically)
  storageClassName: efs-sc
---
### deployment def
apiVersion: apps/v1
kind: Deployment
metadata:
  name: efs-app
spec:
  replicas: 3  # 3 Pods that can read and write in the same volume
  selector:
    matchLabels:
      app: efs-app
  template:
    metadata:
      labels:
        app: efs-app
    spec:
      containers:
      - name: app
        image: busybox
        command: [ "sh", "-c", "echo 'Hello from EFS!' > /data/test.txt && sleep 3600" ]
        volumeMounts:
        - mountPath: "/data"
          name: efs-storage
      volumes:
      - name: efs-storage
        persistentVolumeClaim:
          claimName: efs-pvc
```
Apply the file to the cluster

```bash
kubectl apply -f efs-pvc-deployment.yaml
kubetl get pvc
kubectl get pods -o wide
```
Check Shared Data Across all the Pods

```bash
kubectl exec -it <pod-name> -- cat /data/test.txt
```
All pods should see the same file test.txt since they share the EFS volume.
