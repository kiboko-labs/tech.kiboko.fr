Kubernetes Developer Environment
===

1. Get the `kubeconfig.yml` file and use the
2. Use the `--kubeconfig=kubeconfig.yml` option to all your `kubectl` commands

Get cluster info
---

```
$ kubectl --kubeconfig=kubeconfig.yml cluster-info
Kubernetes master is running at https://wzb5w7.c1.gra7.k8s.ovh.net
CoreDNS is running at https://wzb5w7.c1.gra7.k8s.ovh.net/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy
Metrics-server is running at https://wzb5w7.c1.gra7.k8s.ovh.net/api/v1/namespaces/kube-system/services/https:metrics-server:/proxy

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.
```

Install Kubernetes Dashboard
---

FR: https://kubernetes.io/fr/docs/tasks/access-application-cluster/web-ui-dashboard/
EN: https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/

```
$ kubectl --kubeconfig=kubeconfig.yml apply -f https://raw.githubusercontent.com/kubernetes/dashboard/master/aio/deploy/recommended.yaml
namespace/kubernetes-dashboard created
serviceaccount/kubernetes-dashboard created
service/kubernetes-dashboard created
secret/kubernetes-dashboard-certs created
secret/kubernetes-dashboard-csrf created
secret/kubernetes-dashboard-key-holder created
configmap/kubernetes-dashboard-settings created
role.rbac.authorization.k8s.io/kubernetes-dashboard created
clusterrole.rbac.authorization.k8s.io/kubernetes-dashboard created
rolebinding.rbac.authorization.k8s.io/kubernetes-dashboard created
clusterrolebinding.rbac.authorization.k8s.io/kubernetes-dashboard created
deployment.apps/kubernetes-dashboard created
service/dashboard-metrics-scraper created
deployment.apps/dashboard-metrics-scraper created
```

Kubectl will make Dashboard available at http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/.

You then need to create your users if it wasn't already done.
Create a sample User: https://github.com/kubernetes/dashboard/blob/master/docs/user/access-control/creating-sample-user.md

Creating Users for the Kubernetes Dashboard
---

### Crating a service account

```bash
cat <<EOF | kubectl --kubeconfig=kubeconfig.yml apply -f -
apiVersion: v1
kind: ServiceAccount
metadata:
  name: gplanchat
  namespace: kubernetes-dashboard
EOF
```

### Creating a ClusterRoleBinding

```bash
cat <<EOF | kubectl --kubeconfig=kubeconfig.yml apply -f -
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: gplanchat
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: gplanchat
subjects:
- kind: ServiceAccount
  name: gplanchat
  namespace: kubernetes-dashboard
EOF
```

Getting a bearer Token
---

```
$ kubectl --kubeconfig=kubeconfig.yml -n kubernetes-dashboard describe secret $(kubectl --kubeconfig=kubeconfig.yml -n kubernetes-dashboard get secret | grep gplanchat | awk '{print $1}')
Name:         gplanchat-token-w8bl4
Namespace:    kubernetes-dashboard
Labels:       <none>
Annotations:  kubernetes.io/service-account.name: gplanchat
              kubernetes.io/service-account.uid: 23111bc9-376b-4b87-9ecb-9d4743f14c9e

Type:  kubernetes.io/service-account-token

Data
====
token:      eyJh[...]pgE
ca.crt:     1801 bytes
namespace:  20 bytes
```

Now copy the token and paste it into Enter token field on the login screen.

Click Sign in button and that's it. You are now logged in as an admin.
