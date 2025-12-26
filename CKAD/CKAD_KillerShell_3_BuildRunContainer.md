# Interactive Scenarios for Kubernetes Application Developers (3 of ?)

Reference: <https://killercoda.com/killer-shell-ckad>

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
