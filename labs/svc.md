# Service

#### Create Replication Controller (replicas=1)
```
$ kubectl --namespace=demo create -f rc/rc.yaml
replicationcontroller "rc-demo-pod" created
$ kubectl --namespace=demo get rc rc-demo-pod
CONTROLLER    CONTAINER(S)   IMAGE(S)   SELECTOR       REPLICAS   AGE
rc-demo-pod   busybox        busybox    name=rc-demo   1          26s
              nginx          nginx
```

#### Expose the Replication Controller as a Service
```
$ kubectl --namespace=demo expose rc rc-demo-pod --port=80 --target-port=80 --type=NodePort
service "rc-demo-pod" exposed
```


#### Look at the Service
```
$ kubectl --namespace=demo describe svc rc-demo-pod
Name:			rc-demo-pod
Namespace:		demo
Labels:			demo=rc-pods
Selector:		name=rc-demo
Type:			NodePort
IP:			10.100.17.227
Port:			<unnamed>	80/TCP
NodePort:		<unnamed>	30986/TCP
Endpoints:		10.244.41.2:80
Session Affinity:	None
No events.
```

#### See what happens when you access the service's IP

##### Terminal 1
```
while true; do curl --connect-timeout 1 -s 192.168.10.71:30986; sleep 1;done
rc-demo-pod-x0q25
Wed Jan 20 03:20:55 UTC 2016
rc-demo-pod-x0q25
Wed Jan 20 03:20:56 UTC 2016
rc-demo-pod-x0q25
Wed Jan 20 03:20:57 UTC 2016
```
##### Terminal 2
```
$ kubectl --namespace=demo scale rc rc-demo-pod --replicas=2
replicationcontroller "rc-demo-pod" scaled
```
You can see what happens on Terminal 1, there are two pods running.


#### Delete Service
```
$ kubectl --namespace=demo get svc
NAME          CLUSTER_IP      EXTERNAL_IP   PORT(S)   SELECTOR       AGE
rc-demo-pod   10.100.17.227   nodes         80/TCP    name=rc-demo   16m
$ kubectl --namespace=demo delete svc rc-demo-pod
service "rc-demo-pod" deleted
```

#### Delete Replication Controller
```
$ kubectl --namespace=demo get rc rc-demo-pod
CONTROLLER    CONTAINER(S)   IMAGE(S)   SELECTOR       REPLICAS   AGE
rc-demo-pod   busybox        busybox    name=rc-demo   3          25m
              nginx          nginx
$ kubectl --namespace=demo delete rc rc-demo-pod
replicationcontroller "rc-demo-pod" deleted
```
