# [CKAD Exercises](https://killercoda.com/chadmcrowell/course/ckad)

> By Chad M. Crowell

## [Debug services in Kubernetes](https://killercoda.com/chadmcrowell/course/ckad/debug-services)

Welcome !
In this scenario, we'll learn how to debug services in Kubernetes

ENJOY!

### Check if pods are running and have endpoints

1. Run the command to list the pods and their labels

1. Run the command to list the pods by their label

1. Run a command to get the pod IP addresses

1. Run the command to view which port the pods are exposed on.

1. Use this command to run busybox pod and get a shell to it for troubleshooting:

```bash
kubectl run -it --rm --restart=Never busybox --image=gcr.io/google-containers/busybox sh
```

* From a shell to a pod, see if the pods are listening on the correct port.

You should get something similar to the following output:

```bash
hostnames-54b9d67f64-khfsj
hostnames-54b9d67f64-zq79d
hostnames-54b9d67f64-trl7r
```

#### Solution 

```bash
# get the pod labels
kubectl get po --show-labels
```

```bash
# list the pods by their label
kubectl get pods -l app=hostnames
```

```bash
# get the pod IPs
kubectl get po -o wide
```

```bash
for ep in 192.168.0.8:9376 192.168.0.7:9376 192.168.0.9:9376; do
    wget -qO- $ep
done
```

#### Output

```bash
controlplane:~$ # get the pod labels
controlplane:~$ kubectl get po --show-labels
NAME                      READY   STATUS    RESTARTS   AGE   LABELS
hostnames-7bbb556-8qft8   1/1     Running   0          4m    app=hostnames,pod-template-hash=7bbb556
hostnames-7bbb556-f5dw8   1/1     Running   0          4m    app=hostnames,pod-template-hash=7bbb556
hostnames-7bbb556-g8q7p   1/1     Running   0          4m    app=hostnames,pod-template-hash=7bbb556
```

```bash
controlplane:~$ # list the pods by their label
controlplane:~$ kubectl get pods -l app=hostnames
NAME                      READY   STATUS    RESTARTS   AGE
hostnames-7bbb556-8qft8   1/1     Running   0          4m5s
hostnames-7bbb556-f5dw8   1/1     Running   0          4m5s
hostnames-7bbb556-g8q7p   1/1     Running   0          4m5s
```

```bash
controlplane:~$ # get the pod IPs
controlplane:~$ kubectl get po -o wide
NAME                      READY   STATUS    RESTARTS   AGE    IP            NODE           NOMINATED NODE   READINESS GATES
hostnames-7bbb556-8qft8   1/1     Running   0          4m8s   192.168.0.6   controlplane   <none>           <none>
hostnames-7bbb556-f5dw8   1/1     Running   0          4m8s   192.168.0.7   controlplane   <none>           <none>
hostnames-7bbb556-g8q7p   1/1     Running   0          4m8s   192.168.0.8   controlplane   <none>           <none>
```

```bash
controlplane:~$ vim script1.sh

controlplane:~$ cat script1.sh
for ep in 192.168.0.6:9376 192.168.0.7:9376 192.168.0.8:9376; do
    wget -qO- $ep
done

controlplane:~$ bash script1.sh 
hostnames-5855df66b9-8n2lv
hostnames-5855df66b9-chttf
hostnames-5855df66b9-bcwvx
controlplane:~$ 

```

### Check services by DNS and IP

* Run the command to list the services
Use this command to run busybox pod and get a shell to it for troubleshooting:

```bash
kubectl run -it --rm --restart=Never busybox --image=gcr.io/google-containers/busybox sh
```

* From the `busybox` pod, see if the service is responding via its DNS name

* From the `busybox` pod, see if the service is responding via its FQDN

[View the kubernetes.io documentation for more info on the service FQDN](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/#services)

* Check that your `/etc/resolv.conf` file in your Pod is correct

Check if the `kubernetes` service is responding via DNS from inside the `busybox` pod

Test whether your Service works by its IP address

#### Solution 2

```bash
# list the services
kubectl get svc
```

```bash
# from 'busybox' pod, contact service via DNS
nslookup hostnames
```

```bash
# from 'busybox' pod, contact service via FQDN
nslookup hostnames.default.svc.cluster.local
```

```bash
# from 'busybox' pod, check /etc/resolv.conf
cat /etc/resolv.conf
```

```bash
# from 'busybox' pod, contact 'kubernetes' service
nslookup kubernetes.default
```

```bash
# from 'busybox' pod, check the IP of the service
for i in $(seq 1 3); do 
    wget -qO- 10.0.1.175:80
done
```

#### Output 2

List the Services
```bash
controlplane:~$ k get svc
NAME         TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)   AGE
hostnames    ClusterIP   10.106.80.32   <none>        80/TCP    16m
kubernetes   ClusterIP   10.96.0.1      <none>        443/TCP   10d
controlplane:~$ k get po
NAME                         READY   STATUS    RESTARTS   AGE
hostnames-74b6bb6c56-bfzmr   1/1     Running   0          16m
hostnames-74b6bb6c56-blt6v   1/1     Running   0          16m
hostnames-74b6bb6c56-s6k2m   1/1     Running   0          16m
```

Start the Busybox Pod in Interactive Terminal mode

```bash
controlplane:~$ kubectl run -it --rm --restart=Never busybox --image=busybox sh
All commands and output from this session will be recorded in container logs, including credentials and sensitive information passed through the command prompt.
If you don't see a command prompt, try pressing enter.
/ #
```

From Busybox pod contact service via DNS
```bash
/ # nslookup hostnames
Server:         10.96.0.10
Address:        10.96.0.10:53

Name:   hostnames.default.svc.cluster.local
Address: 10.106.80.32

** server can't find hostnames.svc.cluster.local: NXDOMAIN

** server can't find hostnames.cluster.local: NXDOMAIN


** server can't find hostnames.svc.cluster.local: NXDOMAIN

** server can't find hostnames.cluster.local: NXDOMAIN

/ # 

```

From Busybox pod contact service via FQDN

```bash
/ # nslookup hostnames.default.svc.cluster.local
Server:         10.96.0.10
Address:        10.96.0.10:53

Name:   hostnames.default.svc.cluster.local
Address: 10.106.80.32


/ # 
```

From BusyBox Pod check resolve.conf

```bash
/ # cat /etc/resolv.conf 
search default.svc.cluster.local svc.cluster.local cluster.local
nameserver 10.96.0.10
options ndots:5
/ # 
```

From BusyBox Pod check kubernetes service via DNS

```bash
/ # nslookup kubernetes.default
Server:         10.96.0.10
Address:        10.96.0.10:53

** server can't find kubernetes.default: NXDOMAIN

** server can't find kubernetes.default: NXDOMAIN


```

From BusyBox Pod check kubernetes service via FQDN

```bash

/ # nslookup kubernetes.default.svc.cluster.local
Server:         10.96.0.10
Address:        10.96.0.10:53

Name:   kubernetes.default.svc.cluster.local
Address: 10.96.0.1


/ # 
```

From BusyBox Pod check the IP of Service

```bash

```

### Check the service spec and endpoints

Check if the service is defined correctly, including it's ports and type

Run the command to output the service YAML

Verify the service is correctly defined by asking the following:

* Is the Service port you are trying to access listed in spec.ports[]?
* Is the targetPort correct for your Pods (some Pods use a different port than the Service)?
* If you meant to use a numeric port, is it a number (9376) or a string "9376"?
* If you meant to use a named port, do your Pods expose a port with the same name?
* Is the port's protocol correct for your Pods?

Let's check that the Pods you ran are actually being selected by the Service

Run the command to view the service endpoints.

If the ENDPOINTS column is `<none>` , you should check that the `spec.selector` field of your Service actually selects for `metadata.labels` values on your Pods. A common mistake is to have a typo or other error, such as the Service selecting for `app=hostnames` , but the Deployment specifying `run=hostnames` , as in versions previous to 1.18, where the kubectl run command could have been also used to create a Deployment

#### Solution 3

``` bash
kubectl get service hostnames -o yaml

kubectl get endpoints hostnames
```

#### Output 3

```
controlplane:~$ kubectl get service hostnames -o yaml
apiVersion: v1
kind: Service
metadata:
  creationTimestamp: "2025-12-29T11:51:03Z"
  labels:
    app: hostnames
  name: hostnames
  namespace: default
  resourceVersion: "2526"
  uid: 896ba77c-261d-43bf-ae32-41c96b9b96ed
spec:
  clusterIP: 10.106.80.32
  clusterIPs:
  - 10.106.80.32
  internalTrafficPolicy: Cluster
  ipFamilies:
  - IPv4
  ipFamilyPolicy: SingleStack
  ports:
  - port: 80
    protocol: TCP
    targetPort: 9376
  selector:
    app: hostnames
  sessionAffinity: None
  type: ClusterIP
status:
  loadBalancer: {}
controlplane:~$ 
controlplane:~$ kubectl get endpoints hostnames
Warning: v1 Endpoints is deprecated in v1.33+; use discovery.k8s.io/v1 EndpointSlice
NAME        ENDPOINTS                                            AGE
hostnames   192.168.0.6:9376,192.168.0.7:9376,192.168.0.8:9376   32m
controlplane:~$ 
```

WELL DONE !
You solved this challenge!