# Replication Controller

#### Create Replication Controller (replicas=1)
```
$ kubectl --namespace=demo create -f rc/rc.yaml
replicationcontroller "rc-demo-pod" created
$ kubectl --namespace=demo describe rc rc-demo-pod
Name:		rc-demo-pod
Namespace:	demo
Image(s):	busybox,nginx
Selector:	name=rc-demo
Labels:		demo=rc-pods
Replicas:	1 current / 1 desired
Pods Status:	1 Running / 0 Waiting / 0 Succeeded / 0 Failed
Volumes:
  content:
    Type:	EmptyDir (a temporary directory that shares a pod's lifetime)
    Medium:

$ kubectl --namespace=demo get pods
NAME                READY     STATUS    RESTARTS   AGE
rc-demo-pod-93bfu   2/2       Running   0          44s
```


#### Scale Out (replicas=2)
```
$ kubectl --namespace=demo scale rc rc-demo-pod --replicas=2replicationcontroller "rc-demo-pod" scaled
$ kubectl --namespace=demo describe rc rc-demo-pod
Name:		rc-demo-pod
Namespace:	demo
Image(s):	busybox,nginx
Selector:	name=rc-demo
Labels:		demo=rc-pods
Replicas:	2 current / 2 desired
Pods Status:	2 Running / 0 Waiting / 0 Succeeded / 0 Failed
Volumes:
  content:
    Type:	EmptyDir (a temporary directory that shares a pod's lifetime)
    Medium:

$ kubectl --namespace=demo get pods -l name=rc-demo
NAME                READY     STATUS    RESTARTS   AGE
rc-demo-pod-93bfu   2/2       Running   0          4m
rc-demo-pod-jsoj3   2/2       Running   0          2m
```


#### Get the pods' IP
```
$ kubectl --namespace=demo get pods -l name=rc-demo -o yaml | grep podIP
    podIP: 10.244.44.2
    podIP: 10.244.41.2
```


#### SSH into the cluster ans access the pods
```
for IP in 10.244.44.2 10.244.41.2; do \
     curl --connect-timeout 1 -s $IP; \
     done
rc-demo-pod-93bfu
Tue Jan 19 06:38:48 UTC 2016
rc-demo-pod-jsoj3
Tue Jan 19 06:38:47 UTC 2016
```

#### Kill a pods
```
$ kubectl --namespace=demo delete pods rc-demo-pod-93bfu
pod "rc-demo-pod-93bfu" deleted
$ kubectl --namespace=demo get pods
NAME                READY     STATUS        RESTARTS   AGE
rc-demo-pod-93bfu   2/2       Terminating   0          13m
rc-demo-pod-9w0e1   0/2       Pending       0          15s
rc-demo-pod-jsoj3   2/2       Running       0          10m

.....After a minute.
$ kubectl --namespace=demo get pods
NAME                READY     STATUS    RESTARTS   AGE
rc-demo-pod-9w0e1   2/2       Running   0          2m
rc-demo-pod-jsoj3   2/2       Running   0          13m
```
rc-demo-pod-93bfu pods killed & rc-demo-pod-9w0e1 pods created.

#### Kill a node
rc-demo-pod-9w0e1 pods running on 192.168.10.72
rc-demo-pod-jsoj3 pods running on 192.168.10.73

192.168.10.73 server shutdown......
```
$ kubectl --namespace=demo get pods -l name=rc-demo -o wide
NAME                READY     STATUS    RESTARTS   AGE       NODE
rc-demo-pod-9w0e1   2/2       Running   0          9m        192.168.10.72
rc-demo-pod-jsoj3   2/2       Running   0          20m       192.168.10.73
$ kubectl get nodes
NAME            LABELS                                 STATUS     AGE
192.168.10.71   kubernetes.io/hostname=192.168.10.71   Ready      48d
192.168.10.72   kubernetes.io/hostname=192.168.10.72   Ready      48d
192.168.10.73   kubernetes.io/hostname=192.168.10.73   NotReady   48d
```

After 5 minute, rc-demo-pod-bsegq pods create, and rc-demo-pod-jsoj3 pods terminating.   
```
$ kubectl --namespace=demo get pods -l name=rc-demo -o wide
NAME                READY     STATUS        RESTARTS   AGE       NODE
rc-demo-pod-9w0e1   2/2       Running       0          12m       192.168.10.72
rc-demo-pod-bsegq   2/2       Running       0          16s       192.168.10.71
rc-demo-pod-jsoj3   2/2       Terminating   0          23m       192.168.10.73
```

#### Delete Replication Controller
```
$ kubectl --namespace=demo delete -f rc/rc.yaml
replicationcontroller "rc-demo-pod" deleted
```
