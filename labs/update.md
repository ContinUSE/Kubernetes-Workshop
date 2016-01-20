# Rolling Update

#### Create a Service
```
$ kubectl --namespace=demo create -f rolling-update/svc.yaml
You have exposed your service on an external port on all nodes in your
cluster.  If you want to expose this service to the external internet, you may
need to set up firewall rules for the service port(s) (tcp:32100) to serve traffic.

See http://releases.k8s.io/release-1.1/docs/user-guide/services-firewalls.md for more details.
service "update-demo-svc" created
$ kubectl --namespace=demo get svc
NAME              CLUSTER_IP      EXTERNAL_IP   PORT(S)   SELECTOR      AGE
update-demo-svc   10.100.49.123   nodes         80/TCP    demo=update   45s
```

#### Run v1 of App
```
$ kubectl --namespace=demo create -f rolling-update/rc_v1.yaml
replicationcontroller "update-demo-rc-v1" created
$ kubectl --namespace=demo get rc
CONTROLLER          CONTAINER(S)   IMAGE(S)   SELECTOR                      REPLICAS   AGE
update-demo-rc-v1   busybox        busybox    demo=update,demo_version=v1   2          14s
                    nginx          nginx
```

#### Terminal 1 (Access the pods)
```
$ while true; do curl --connect-timeout 1 -s 192.168.10.71:32100; sleep 1; done
update-demo-rc-v1-wqrln v1
update-demo-rc-v1-ku7nz v1
update-demo-rc-v1-wqrln v1
update-demo-rc-v1-ku7nz v1
```

#### Terminal 2 (Do a rolling update to v2)
```
$ kubectl --namespace=demo rolling-update \
> update-demo-rc-v1 -f rolling-update/rc_v2.yaml  --update-period=5s
Created update-demo-rc-v2
Scaling up update-demo-rc-v2 from 0 to 2, scaling down update-demo-rc-v1 from 2 to 0 (keep 2 pods available, don't exceed 3 pods)
Scaling update-demo-rc-v2 up to 1
Scaling update-demo-rc-v1 down to 1
Scaling update-demo-rc-v2 up to 2
Scaling update-demo-rc-v1 down to 0
Update succeeded. Deleting update-demo-rc-v1
replicationcontroller "update-demo-rc-v1" rolling updated to "update-demo-rc-v2"
```
You can see what happens on Terminal 1, upgrade to v2.
