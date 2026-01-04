# [CKAD Exercises](https://killercoda.com/chadmcrowell/course/ckad)

> By Chad M. Crowell

## [Apply node affinity to a pod](https://killercoda.com/chadmcrowell/course/ckad/node-affinity)

Welcome !
In this scenario, we'll learn how to apply node affinity to a pod in Kubernetes!

ENJOY!

### Create a pod with node affinity

In the namespace named `012963bd` , create a pod named `az1-pod` which uses the `busybox:1.28` image. This pod should use node affinity, and prefer during scheduling to be placed on the node with the label `availability-zone=zone1` with a weight of 80.

Also, have that same pod prefer to be scheduled to a node with the label `availability-zone=zone2` with a weight of 20.

> NOTE: Make sure the container remains in a running state

Ensure that the pod is scheduled to the `controlplane` node.

####  Solution

```bash
kubectl create namespace 012963bd
```

```bash
kubectl label node controlplane availability-zone=zone1 --overwrite
kubectl label node node01 availability-zone=zone2 --overwrite
```

`vim az1-pod.yaml`

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: az1-pod
  namespace: 012963bd
spec:
  affinity:
    nodeAffinity:
      preferredDuringSchedulingIgnoredDuringExecution:
      - weight: 80
        preference:
          matchExpressions:
          - key: availability-zone
            operator: In
            values:
            - zone1
      - weight: 20
        preference:
          matchExpressions:
          - key: availability-zone
            operator: In
            values:
            - zone2
  containers:
  - name: main
    image: busybox:1.28
    command: ["sh", "-c", "sleep 3600"]
  restartPolicy: Never
```

```bash
kubectl apply -f az1-pod.yaml

kubectl -n 012963bd get pod az1-pod -o wide
```

How to check?
```bash
kubectl get  pod az1-pod -n 012963bd  -o jsonpath='{.spec.affinity}'
```

#### Output:

```bash
controlplane:~$ kubectl create namespace 012963bd
Error from server (AlreadyExists): namespaces "012963bd" already exists
```

```bash
controlplane:~$ kubectl label node controlplane availability-zone=zone1 --overwrite
node/controlplane not labeled
controlplane:~$ kubectl label node node01 availability-zone=zone2 --overwrite
node/node01 not labeled
```

```bash
controlplane:~$ cat <<'EOF_MANIFEST' > az1-pod.yaml
> apiVersion: v1
> kind: Pod
> metadata:
>   name: az1-pod
>   namespace: 012963bd
> spec:
>   affinity:
>     nodeAffinity:
>       preferredDuringSchedulingIgnoredDuringExecution:
>       - weight: 80
>         preference:
>           matchExpressions:
>           - key: availability-zone
>             operator: In
>             values:
>             - zone1
>       - weight: 20
>         preference:
>           matchExpressions:
>           - key: availability-zone
>             operator: In
>             values:
>             - zone2
>   containers:
>   - name: main
>     image: busybox:1.28
>     command: ["sh", "-c", "sleep 3600"]
>   restartPolicy: Never
> EOF_MANIFEST
controlplane:~$ cat az1-pod.yaml 
...
```

Create Pod az1-pod.yaml

```bash
controlplane:~$ kubectl apply -f az1-pod.yaml
pod/az1-pod created
```

Get Pod az1-pod

```bash
controlplane:~$ kubectl -n 012963bd get pod az1-pod -o wide
NAME      READY   STATUS    RESTARTS   AGE   IP            NODE     NOMINATED NODE   READINESS GATES
az1-pod   1/1     Running   0          88s   192.168.1.4   node01   <none>           <none>
```

Describe Pod az1-pod

```bash
controlplane:~$ kubectl -n 012963bd describe pod az1-pod        
Name:             az1-pod
Namespace:        012963bd
Priority:         0
Service Account:  default
Node:             node01/172.30.2.2
Start Time:       Mon, 29 Dec 2025 17:49:58 +0000
Labels:           <none>
Annotations:      cni.projectcalico.org/containerID: 704ca619d115c24efd9ba6d087f591ef60d6c1b10424760e99e8009160460b0f
                  cni.projectcalico.org/podIP: 192.168.1.4/32
                  cni.projectcalico.org/podIPs: 192.168.1.4/32
Status:           Running
IP:               192.168.1.4
IPs:
  IP:  192.168.1.4
Containers:
  main:
    Container ID:  containerd://b3d7bdc2351f856eee6611580fc64cd71fe2a479946eceda5d2fcd41d526713e
    Image:         busybox:1.28
    Image ID:      docker.io/library/busybox@sha256:141c253bc4c3fd0a201d32dc1f493bcf3fff003b6df416dea4f41046e0f37d47
    Port:          <none>
    Host Port:     <none>
    Command:
      sh
      -c
      sleep 3600
    State:          Running
      Started:      Mon, 29 Dec 2025 17:50:00 +0000
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-9xq52 (ro) 
Conditions:
  Type                        Status
  PodReadyToStartContainers   True 
  Initialized                 True 
  Ready                       True 
  ContainersReady             True 
  PodScheduled                True 
Volumes:
  kube-api-access-9xq52:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    Optional:                false
    DownwardAPI:             true
QoS Class:                   BestEffort
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type    Reason     Age   From               Message
  ----    ------     ----  ----               -------
  Normal  Scheduled  106s  default-scheduler  Successfully assigned 012963bd/az1-pod to node01
  Normal  Pulling    105s  kubelet            Pulling image "busybox:1.28"
  Normal  Pulled     104s  kubelet            Successfully pulled image "busybox:1.28" in 1.151s (1.151s including waiting). Image size: 727869 bytes.
  Normal  Created    104s  kubelet            Created container: main
  Normal  Started    104s  kubelet            Started container main

```

Check Affinity for Pod az1-pod

```bash
controlplane:~$ kubectl get  pod az1-pod -n 012963bd  -o jsonpath='{.spec.affinity}'
{"nodeAffinity":{"preferredDuringSchedulingIgnoredDuringExecution":[{"preference":{"matchExpressions":[{"key":"availability-zone","operator":"In","values":["zone1"]}]},"weight":80},{"preference":{"matchExpressions":[{"key":"availability-zone","operator":"In","values":["zone2"]}]},"weight":20}]}}controlplane:~$ 
```

Format the output 

```bash
controlplane:~$ kubectl get  pod az1-pod -n 012963bd  -o jsonpath='{.spec.affinity}'|jq 
{
  "nodeAffinity": {
    "preferredDuringSchedulingIgnoredDuringExecution": [
      {
        "preference": {
          "matchExpressions": [
            {
              "key": "availability-zone",
              "operator": "In",
              "values": [
                "zone1"
              ]
            }
          ]
        },
        "weight": 80
      },
      {
        "preference": {
          "matchExpressions": [
            {
              "key": "availability-zone",
              "operator": "In",
              "values": [
                "zone2"
              ]
            }
          ]
        },
        "weight": 20
      }
    ]
  }
}
controlplane:~$ 
```

WELL DONE !
You solved this challenge!
