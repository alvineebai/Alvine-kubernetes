# Lab 2: Using Amazon EFS in AWS EKS (ReadWriteMany) for persistent volumes provisioning

This lab demonstrates how to create a PersistentVolumeClaim (PVC) using EFS, which allows multiple nodes to share the same storage.

### Enable dynamic provisioning for Amazon EFS in AWS EKS

#### Install the AWS EFS CSI Driver
Ensure the Amazon EFS CSI driver is installed in your EKS cluster.

```bash
kubectl apply -k "github.com/kubernetes-sigs/aws-efs-csi-driver/deploy/kubernetes/overlays/stable/ecr/?ref=release-1.3
```
#### Create the EFS StorageClass

This enables dynamic provisioning of volumes.

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: efs-sc
provisioner: efs.csi.aws.com
parameters:
  fileSystemId: fs-XXXXXXXX  # Replace with your EFS File System ID
  directoryPerms: "777"
  provisioningMode: efs-ap
  accessPoints: "true"
mountOptions:
  - tls
reclaimPolicy: Retain
volumeBindingMode: Immediate
```

### Create the PersistentVolumeClaim (PVC)

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: efs-pvc
spec:
  accessModes:
    - ReadWriteMany  # Multiple nodes can mount the volume
  resources:
    requests:
      storage: 5Gi  # EFS scales dynamically, so size is just a placeholder
  storageClassName: efs-sc  # Default StorageClass for EFS in AWS
```

### Create a Deployment with Multiple Pods Using EFS

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: efs-deployment
spec:
  replicas: 3  # Three pods to demonstrate multi-node access
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
          name: storage
      volumes:
      - name: storage
        persistentVolumeClaim:
          claimName: efs-pvc
```

### Deploy on AWS EKS
Add all the objects definitions in a file called `efs-volume.yaml`
```bash
kubectl apply -f efs-volume.yaml
```
Verify the Deployment

```bash
kubectl get pvc
kubectl get deploy
kubectl get pods -o wide
```

Check Shared Data Across Multiple Pods

```bash
kubectl exec -it <pod-name> -- cat /data/test.txt
```
All pods should see the same file test.txt since they share the EFS volume.

