## Persistent Storage in Kubernetes

Volumes are important for data persistence and file sharing between pods.
Persistent volumes (PVs) are volume plugins managed by kubernetes
They are just like physical storage on a hard drive that are attached to your pods for data storage
A Persistent Volume Claim (PVC) is a request for storage (PV) by a user. Claims can request for volumes with specific size and access modes

PV Provisioning can be static or dynamic 
- Static: The cluster administrator create a number of PVs of various type
- Dynamic: kubernetes dynamically provision a volume for a specific PVC (using storage classes)

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

### Example: 

