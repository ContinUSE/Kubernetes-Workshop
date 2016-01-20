# Namespace

#### Namespace information get Kubernetes cluster
```
$ kubectl get namespace
NAME          LABELS        STATUS    AGE
default       <none>        Active    47d
kube-system   ENV=Monitor   Active    47d
```

#### Create a namespace for this cluster
```
$ kubectl create -f ns/kube-demo-namespace.yaml
namespace "demo" created
$ kubectl get namespace
NAME          LABELS        STATUS    AGE
default       <none>        Active    47d
demo          ENV=TEST1     Active    35s
kube-system   ENV=Monitor   Active    47d
```

#### Delete a namespace for this cluster
```
$ kubectl delete -f ns/kube-demo-namespace.yaml
namespace "demo" deleted
$ kubectl get namespace
NAME          LABELS        STATUS    AGE
default       <none>        Active    47d
kube-system   ENV=Monitor   Active    47d
```

'demo' namespace create for this workshop.
