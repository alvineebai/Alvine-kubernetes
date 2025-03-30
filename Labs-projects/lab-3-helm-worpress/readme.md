# Lab: Deploy Wordpress using Helm

**Note: This lab requires you to have a EKS cluster with dynamic storage provisioning enabled**. 

1. Add the Bitnami Helm Repository
First, add the Bitnami repository (if not already added):

```bash
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo update
```

2. Install Wordpress (this will install with the default values)
```bash
helm install my-wordpress bitnami/wordpress
```
This will:

- Deploy WordPress
- Deploy MariaDB (as a dependency)
- Expose WordPress via a LoadBalancer by default

3. Verify the deployment status

```bash
helm list
kubectl get pods
kubectl get svc
```

4. Access Wordpress from your browser
To get the admin password, run:

```bash
kubectl get secret --namespace default my-wordpress -o jsonpath="{.data.wordpress-password}" | base64 --decode
```
To get the WordPress URL, run:

```bash
kubectl get svc --namespace default my-wordpress
```

If it's exposed as a LoadBalancer, copy the external IP (Loadbalancer DNS) and visit:
```bash
http://<EXTERNAL-IP>/admin
Username: user
Password: (retrieved from secret)
```

5. Uninstall WordPress
If you need to delete your deployment:
```bash
helm uninstall my-wordpress
```
This will remove all resources, but PVCs (Persistent Volume Claims) may remain. To delete them:

```bash
kubectl delete pvc --selector=app.kubernetes.io/name=wordpress
kubectl delete pvc --selector=app.kubernetes.io/name=mariadb
```
