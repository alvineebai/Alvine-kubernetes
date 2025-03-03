## Role Based Access Control (RBAC) in a kubernetes cluster

### Definitions
**Role Based Access Control** in kubernetes is an authorization method to access the cluster resources based on the roles of users within your organization.

**A role** contains a set of rules that represent a set of permissions in a namespace
**A RoleBinding** is used to bind a Role to a user or a group of users.
**A ClusterRole** is a role used to define a set of permissions on the entire cluster or on cluster-wide resources.
**A ClusterRoleBinding** is used to bind a ClusterRole to a user or group of users

### How to setup RBAC in k8s
To set up RBAC, we need to identify the who, the what and the rules:
- Who (the subject like user, group of users, service account)
- What (resources like pods, services, deployments, ...)
- rules (what action is allowed and what action is not allowed)

### Examples of role & rolebinding, clusterrole and clusterrolebinding

Labs:
Setup a new user in a k8s cluster
Setup RBAC to allow read-only access to pods from a specific namespace to a new user

