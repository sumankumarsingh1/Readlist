# Interactive Scenarios for Kubernetes Application Developers (2 of 12)

Reference: <https://killercoda.com/killer-shell-ckad>

## [ReadinessProbe](https://killercoda.com/killer-shell-ckad/scenario/readiness-probe)

### Create a Deployment with a ReadinessProbe

> A ReadinessProbe will be executed periodically all the time, not just during start or until a Pod is ready

Create a Deployment named **`space-alien-welcome-message-generator`** of image **`httpd:alpine`** with one replica.

It should've a ReadinessProbe which executes the command **`stat /tmp/ready`**. This means once the file exists the Pod should be ready.

The **`initialDelaySeconds`** should be **`10`** and **`periodSeconds`** should be **`5`** .

Create the Deployment and observe that the Pod won't get ready.

#### Probes

ReadinessProbes and LivenessProbes will be executed periodically **all the time**.

If a StartupProbe is defined, ReadinessProbes and LivenessProbes won't be executed until the StartupProbe succeeds.

**ReadinessProbe fails***: Pod won't be marked Ready and won't receive any traffic

**LivenessProbe fails***: The container inside the Pod will be restarted

**StartupProbe fails***: The container inside the Pod will be restarted

*fails: fails more times than configured with failureThreshold

#### Tip

Here is an example ReadinessProbe snippet:

```yaml
readinessProbe:
  exec:
    command:
    - ls
    - /tmp
  initialDelaySeconds: 5
  periodSeconds: 5
```

#### Solution

First we generate a Deployment yaml:

```bash
k create deploy space-alien-welcome-message-generator --image=httpd:alpine -oyaml --dry-run=client > deploy.yaml
```

Then we adjust it to the requirements:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: space-alien-welcome-message-generator
spec:
  replicas: 1
  selector:
    matchLabels:
      app: space-alien-welcome-message-generator
  strategy: {}
  template:
    metadata:
      labels:
        app: space-alien-welcome-message-generator
    spec:
      containers:
      - image: httpd:alpine
        name: httpd
        resources: {}
        readinessProbe:
          exec:
            command:
            - stat
            - /tmp/ready
          initialDelaySeconds: 10
          periodSeconds: 5
```

Observe

We see **`0/1`** in the READY column.

```bash
controlplane $ k get deploy
NAME                                    READY   UP-TO-DATE   AVAILABLE   AGE
space-alien-welcome-message-generator   0/1     1            0           40s
```

> We can also run k describe deploy to see info about failed ReadinessProbes

#### Output

```bash
controlplane:~$ k create deploy space-alien-welcome-message-generator --image=httpd:alpine -oyaml --dry-run=client > deploy.yaml
controlplane:~$ cat deploy.yaml 
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: space-alien-welcome-message-generator
  name: space-alien-welcome-message-generator
spec:
  replicas: 1
  selector:
    matchLabels:
      app: space-alien-welcome-message-generator
  strategy: {}
  template:
    metadata:
      labels:
        app: space-alien-welcome-message-generator
    spec:
      containers:
      - image: httpd:alpine
        name: httpd
        resources: {}
status: {}
controlplane:~$ 
```

Edit the deploy.yaml file to configure Readiness Probe:

```bash
controlplane:~$ vi deploy.yaml 
controlplane:~$ cat deploy.yaml 
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: space-alien-welcome-message-generator
  name: space-alien-welcome-message-generator
spec:
  replicas: 1
  selector:
    matchLabels:
      app: space-alien-welcome-message-generator
  strategy: {}
  template:
    metadata:
      labels:
        app: space-alien-welcome-message-generator
    spec:
      containers:
      - image: httpd:alpine
        name: httpd
        resources: {}
        readinessProbe:
          exec:
            command:
            - stat
            - /tmp/ready
          initialDelaySeconds: 10
          periodSeconds: 5
status: {}
```

Create the pod using deploy.yaml and Observe:

```bash
controlplane:~$ k create -f deploy.yaml 
deployment.apps/space-alien-welcome-message-generator created
controlplane:~$ k get deploy
NAME                                    READY   UP-TO-DATE   AVAILABLE   AGE
space-alien-welcome-message-generator   0/1     1            0           23s
controlplane:~$ 
```

### Make the Deployment Ready

Exec into the Pod and create file /tmp/ready .

Observe that the Pod is ready.

#### Solution

```bash
k get pod # use pod name
k exec space-alien-welcome-message-generator-5c945bc5f9-m9nkb -- touch /tmp/ready
```

#### Observe

We see 1/1 in the READY column.

```bash
controlplane $ k get deploy
NAME                                    READY   UP-TO-DATE   AVAILABLE   AGE
space-alien-welcome-message-generator   1/1     1            0           3m53s
```

>We can also run k describe deploy to see info about failed ReadinessProbes


#### Output

```bash
controlplane:~$ k get pod
NAME                                                     READY   STATUS    RESTARTS   AGE
space-alien-welcome-message-generator-66b7c9d6d5-hds6m   0/1     Running   0          3m38s
controlplane:~$ k exec space-alien-welcome-message-generator-66b7c9d6d5-hds6m -- touch /tmp/ready
controlplane:~$ k get deploy
NAME                                    READY   UP-TO-DATE   AVAILABLE   AGE
space-alien-welcome-message-generator   1/1     1            1           6m40s
controlplane:~$ k get pod
NAME                                                     READY   STATUS    RESTARTS   AGE
space-alien-welcome-message-generator-66b7c9d6d5-hds6m   1/1     Running   0          6m47s
controlplane:~$ 
```

#### Output 2

```bash
controlplane:~$ k describe pod
Name:             space-alien-welcome-message-generator-66b7c9d6d5-hds6m
Namespace:        default
Priority:         0
Service Account:  default
Node:             controlplane/172.30.1.2
Start Time:       Fri, 26 Dec 2025 03:02:31 +0000
Labels:           app=space-alien-welcome-message-generator
                  pod-template-hash=66b7c9d6d5
Annotations:      cni.projectcalico.org/containerID: 782a2a286f3098ddb9714fe7ec7873b47df422c7367fdade29f8b02f7906e5e4
                  cni.projectcalico.org/podIP: 192.168.0.6/32
                  cni.projectcalico.org/podIPs: 192.168.0.6/32
Status:           Running
IP:               192.168.0.6
IPs:
  IP:           192.168.0.6
Controlled By:  ReplicaSet/space-alien-welcome-message-generator-66b7c9d6d5
Containers:
  httpd:
    Container ID:   containerd://0642008372c0862e520b04fc9d1bb75b07beed0c75a3689fa8fb8b64495c2893
    Image:          httpd:alpine
    Image ID:       docker.io/library/httpd@sha256:6b7535d8a33c42b0f0f48ff0067765d518503e465b1bf6b1629230b62a466a87
    Port:           <none>
    Host Port:      <none>
    State:          Running
      Started:      Fri, 26 Dec 2025 03:02:35 +0000
    Ready:          True
    Restart Count:  0
    Readiness:      exec [stat /tmp/ready] delay=10s timeout=1s period=5s #success=1 #failure=3
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-wq5q5 (ro)
Conditions:
  Type                        Status
  PodReadyToStartContainers   True 
  Initialized                 True 
  Ready                       True 
  ContainersReady             True 
  PodScheduled                True 
Volumes:
  kube-api-access-wq5q5:
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
  Type     Reason     Age                 From               Message
  ----     ------     ----                ----               -------
  Normal   Scheduled  19m                 default-scheduler  Successfully assigned default/space-alien-welcome-message-generator-66b7c9d6d5-hds6m to controlplane
  Normal   Pulling    19m                 kubelet            Pulling image "httpd:alpine"
  Normal   Pulled     19m                 kubelet            Successfully pulled image "httpd:alpine" in 2.801s (2.801s including waiting). Image size: 20838270 bytes.
  Normal   Created    19m                 kubelet            Created container: httpd
  Normal   Started    19m                 kubelet            Started container httpd
  Warning  Unhealthy  14m (x64 over 19m)  kubelet            Readiness probe failed: stat: can't stat '/tmp/ready': No such file or directory
```


## [Build and run a Container](https://killercoda.com/killer-shell-ckad/scenario/container-build)

### Build a container from scratch and run it

Create a new file **`/root/Dockerfile`** to build a container image from. It should:

* use **`bash`** as base
* run **`ping killercoda.com`**

Build the image and tag it as **`pinger`** .

Run the image (create a container) named **`my-ping`** .

> You can use **docker** or **podman** for this scenario. Just stick to your choice throughout all steps.

#### Info

```bash
Dockerfile: List of commands from which an Image can be build

Image: Binary file which includes all data/requirements to be run as a Container

Container: Running instance of an Image

Registry: Place where we can push/pull Images to/from
```

#### Solution

> For most situations you can just use the commands **docker** or **podman** interchangeably

Create the /root/Dockerfile :

```docker
FROM bash
CMD ["ping", "killercoda.com"]
```

Build the image:

```bash
podman build -t pinger .

podman image ls
```

Run the image:

```bash
podman run --name my-ping pinger
```

#### Output

```bash
controlplane:~$ vi /root/Dockerfile
controlplane:~$ cat /root/Dockerfile 
FROM bash
CMD ["ping", "killercoda.com"]

controlplane:~$ 
controlplane:~$ pwd                    
/root
controlplane:~$ podman build -t pinger .
STEP 1/2: FROM bash
Resolving "bash" using unqualified-search registries (/etc/containers/registries.conf)
Trying to pull docker.io/library/bash:latest...
Getting image source signatures
Copying blob fd359f614f8d done   | 
Copying blob 1074353eec0d done   | 
Copying blob ee6e4fd1e81e done   | 
Copying config 7f35a61f4a done   | 
Writing manifest to image destination
STEP 2/2: CMD ["ping", "killercoda.com"]
COMMIT pinger
--> fb8776202f2a
Successfully tagged localhost/pinger:latest
fb8776202f2aaffe78a215f5c4dcf9f51308f653e4fc43e17894e8502528af5c
controlplane:~$ 
```

#### Output 2

```bash
controlplane:~$ podman image ls
REPOSITORY                  TAG         IMAGE ID      CREATED        SIZE
localhost/pinger            latest      fb8776202f2a  2 minutes ago  16.1 MB
docker.io/library/bash      latest      7f35a61f4a30  8 days ago     16.1 MB
docker.io/library/registry  2           26b2eb03618e  2 years ago    26 MB
controlplane:~$ 
```
Run the image

#### Output 3

```bash
controlplane:~$ podman run --name my-ping pinger
PING killercoda.com (104.26.14.111): 56 data bytes
64 bytes from 104.26.14.111: seq=0 ttl=42 time=1.587 ms
64 bytes from 104.26.14.111: seq=1 ttl=42 time=1.763 ms
64 bytes from 104.26.14.111: seq=2 ttl=42 time=1.828 ms
64 bytes from 104.26.14.111: seq=3 ttl=42 time=1.835 ms
64 bytes from 104.26.14.111: seq=4 ttl=42 time=1.766 ms
64 bytes from 104.26.14.111: seq=5 ttl=42 time=1.790 ms
64 bytes from 104.26.14.111: seq=6 ttl=42 time=1.953 ms
64 bytes from 104.26.14.111: seq=7 ttl=42 time=1.750 ms
64 bytes from 104.26.14.111: seq=8 ttl=42 time=1.759 ms
64 bytes from 104.26.14.111: seq=9 ttl=42 time=1.868 ms
64 bytes from 104.26.14.111: seq=10 ttl=42 time=1.868 ms
^C
--- killercoda.com ping statistics ---
11 packets transmitted, 11 packets received, 0% packet loss
round-trip min/avg/max = 1.587/1.797/1.953 ms
controlplane:~$ 
```

### Push image to registry

> Press Ctrl+c to exit the running container

Tag the image, which is currently tagged as **`pinger`** , also as **`local-registry:5000/pinger`** .

Then push the image into the local registry.

#### Solution

```bash
podman tag pinger local-registry:5000/pinger

podman image ls

podman push local-registry:5000/pinger

```

#### Output 

```bash
controlplane:~$ podman tag pinger local-registry:5000/pinger
controlplane:~$ podman images
REPOSITORY                  TAG         IMAGE ID      CREATED         SIZE
local-registry:5000/pinger  latest      fb8776202f2a  11 minutes ago  16.1 MB
localhost/pinger            latest      fb8776202f2a  11 minutes ago  16.1 MB
docker.io/library/bash      latest      7f35a61f4a30  8 days ago      16.1 MB
docker.io/library/registry  2           26b2eb03618e  2 years ago     26 MB
controlplane:~$ 
```

Inital tries to push gave errors:


Tried to re-run the local registry image and pushed again

```bash
controlplane:~$ podman ps
CONTAINER ID  IMAGE       COMMAND     CREATED     STATUS      PORTS       NAMES
controlplane:~$ podman run -d -p 5000:5000 --restart=always --name local-registry registry:2
26618c37d2989c55bca66c4011b12e92327e37e8e59426fc15b7101054321488
controlplane:~$ podman ps
CONTAINER ID  IMAGE                         COMMAND               CREATED        STATUS        PORTS                   NAMES
26618c37d298  docker.io/library/registry:2  /etc/docker/regis...  4 seconds ago  Up 4 seconds  0.0.0.0:5000->5000/tcp  local-registry
controlplane:~$ podman push local-registry:5000/pinger
Getting image source signatures
Copying blob 7bb20cf5ef67 done   | 
Copying blob a4392f875e67 done   | 
Copying blob a50fffcdaf6d done   | 
Copying config fb8776202f done   | 
Writing manifest to image destination
controlplane:~$ 
```

### Push image with custom tag to registry

Without specifying a **`:tag`** , the default **`:latest`** will be used. Now we want to use tag **`:v1`** instead.

Tag the image, which is currently tagged as **`pinger`** , also as **`pinger:v1`** and **`local-registry:5000/pinger:v1`** .

Then push the image into the local registry.


#### Solution

```bash
podman tag pinger pinger:v1

podman tag pinger local-registry:5000/pinger:v1

podman image ls

podman push local-registry:5000/pinger:v1
```

#### Output

```bash
controlplane:~$ podman tag pinger pinger:v1
controlplane:~$ podman tag pinger local-registry:5000/pinger:v1
controlplane:~$ podman images
REPOSITORY                  TAG         IMAGE ID      CREATED        SIZE
local-registry:5000/pinger  v1          716c66c0d17d  5 minutes ago  16.1 MB
localhost/pinger            v1          716c66c0d17d  5 minutes ago  16.1 MB
local-registry:5000/pinger  latest      716c66c0d17d  5 minutes ago  16.1 MB
localhost/pinger            latest      716c66c0d17d  5 minutes ago  16.1 MB
docker.io/library/bash      latest      7f35a61f4a30  8 days ago     16.1 MB
docker.io/library/registry  2           26b2eb03618e  2 years ago    26 MB
controlplane:~$ podman push local-registry:5000/pinger:v1
Getting image source signatures
Copying blob b56fa8e1ff59 skipped: already exists  
Copying blob 1a9a48c98dea skipped: already exists  
Copying blob fead7c895b0c skipped: already exists  
Copying config 716c66c0d1 done   | 
Writing manifest to image destination
controlplane:~$ 
```
