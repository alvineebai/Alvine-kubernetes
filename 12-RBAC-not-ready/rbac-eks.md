# Setting Up RBAC for a New User in Amazon EKS

## **Overview**

We have a new user called Adam in dev team and we need to give him access to the cluster resources. As an administrator, setup RBAC to allow Adam to list, create and delete pods in the dev to 
This guide explains how to configure **Role-Based Access Control (RBAC)** in an **Amazon EKS** cluster to allow a new team member to **only create, delete and list pods** in the `dev` namespace.

---

## **Step 1: Create an IAM User and Get Their ARN**
1. **Create an IAM user** for your Adam in AWS if not already done.
2. Attach the following **IAM policy** to allow authentication to EKS:

   ```json
   {
     "Version": "2012-10-17",
     "Statement": [
       {
         "Effect": "Allow",
         "Action": "eks:DescribeCluster",
         "Resource": "*"
       }
     ]
   }
   ```
3. Get the **ARN (Amazon Resource Name)** of the IAM user:
   - Go to **AWS IAM Console** → Users → Select the user → Copy the ARN.
   or use the command:
```bash
aws iam list-users
```

---

## **Step 2: Map the IAM User to a Kubernetes User**
Amazon EKS does not automatically assign permissions to IAM users, so we must update the `aws-auth` ConfigMap.

1. Open a terminal and run:

   ```sh
   kubectl edit configmap aws-auth -n kube-system
   ```

2. Add the following entry under the `mapUsers` section:

   ```yaml
   - userarn: arn:aws:iam::123456789012:user/new-team-member
     username: new-team-member
     groups:
       - dev-group
   ```

   Replace `arn:aws:iam::123456789012:user/new-team-member` with the actual IAM ARN.

3. Save and exit.

---

## **Step 3: Create a Kubernetes Role (RBAC)**
Now, create a Kubernetes **Role** in the `dev` namespace to define permissions.

First create the namespace dev in your cluster: 
```bash
kubectl create ns dev
```

1. Save the following YAML file as `dev-role.yaml`:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: dev
  name: pod-manager
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["create", "list", "delete"]
```

2. Apply the role to the cluster:

   ```sh
   kubectl apply -f dev-role.yaml
   ```

---

## **Step 4: Create a RoleBinding**
Now, bind the role to the **Kubernetes user** (`new-team-member`).

1. Save the following YAML file as `dev-rolebinding.yaml`:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: pod-manager-binding
  namespace: dev
subjects:
- kind: User
  name: new-team-member
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: pod-manager
  apiGroup: rbac.authorization.k8s.io
```

2. Apply the RoleBinding:

   ```sh
   kubectl apply -f dev-rolebinding.yaml
   ```

---

## **Step 5: Verify Access**
Ask the user to authenticate using `aws eks update-kubeconfig` and check access:

```sh
aws eks update-kubeconfig --region <region> --name <cluster-name> --profile <aws-profile>
kubectl auth can-i create pods --namespace=dev
kubectl auth can-i list pods --namespace=dev
```

If set up correctly, the user should be able to **create and list pods** in the `dev` namespace but not perform any other actions.

---

## **Step 6: Verify access (User side)

On Adam side, he nust make sure AWS CLI is installed and configured with access keys and secret keys on his local computer. He must also install kubectl to be able to interact with kubernetes clusters.
- Configure AWS CLI with IAM User Credentials
Verify
```bash
aws --version
kubectl version --client
aws configure
# Enter the IAM user's Access Key ID and Secret Access Key.
# Set the default region to match the EKS cluster's region.
aws sts get-caller-identity
# It should return their IAM User ARN.
```
- Update kubeconfig for Cluster Access
Once the AWS CLI is configured, Adam must update his kubeconfig file to authenticate with the EKS cluster.

´´´bash
aws eks update-kubeconfig --region <region> --name <cluster-name>
´´´
Replace <region> and <cluster-name> with the actual values.

- verify access
```bash
kubectl get pods
kubectl get pods -n dev
kubectl auth can-i create pods --namespace=dev
kubectl auth can-i delete pods --namespace=dev
kubectl auth can-i create deployment --namespace=dev
kubectl auth can-i delete service --namespace=dev
```
It should return "yes" for the objects he can create or "no" for the ones he cannot create.
