# Pods

#### There are no naming pods
```
$ kubectl get pods
NAME      READY     STATUS    RESTARTS   AGE
```

#### Create a pods
This Pods has two docker containers as busybox & nginx.
```
$ kubectl --namespace=demo create -f pods/pods.yaml
pod "pods-demo-pod" created
$ kubectl --namespace=demo get pods
NAME            READY     STATUS    RESTARTS   AGE
pods-demo-pod   2/2       Running   0          25s
```

#### Get the Pod's IP
```
$ kubectl --namespace=demo get pod pods-demo-pod -o yaml | grep podIP
  podIP: 10.244.41.2
```

#### SSH into the cluster and access the pod
```
while true; do \
    curl --connect-timeout 1 -s 10.244.41.2; \
    sleep 1; \
 done
pods-demo-pod
Tue Jan 19 02:38:14 UTC 2016
pods-demo-pod
Tue Jan 19 02:38:15 UTC 2016
^C
```

#### Delete a pods
```
$ kubectl --namespace=demo delete -f pods/pods.yaml
pod "pods-demo-pod" deleted
$ kubectl --namespace=demo get pods
NAME            READY     STATUS        RESTARTS   AGE
pods-demo-pod   2/2       Terminating   0          2h
```
need some time for terminating pods.

