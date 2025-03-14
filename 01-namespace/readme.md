## Namespaces in kubernetes
A Namespace is a way to logically divide your resources into groups within the cluster. This is very useful when many teams create resources in a single cluster. 
Each object created belongs to a namespace
By default, kubernetes starts with 4 namespaces
- default: for the resources you create with no namespace 
- kube-system: for the objects created by the Kubernetes system 
- kube-public: for the resources that should be visible and publicly readable
- kube-node-lease: where Node Lease objects are stored

To create a namespace, you can use the command `kubectl create namespace <name>`
Or create a manifest where you can define your namespace spec:

**Example:** Create a namespace called dev for the dev team 
- Create a file called `namespace.yaml` 

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: dev
```

- Create the namespace in the cluster using the command:
`kubectl create -f namespace.yaml` or `kubectl apply -f namespace.yaml`

- You can verify with
```bash
kubectl get namespace
kubectl get ns
```

- You can describe the namespace with:
```bash
kubectl describe ns dev
```

To delete the namespace, run the command `kubectl delete ns <name>`

```bash
kubectl delete ns dev
```