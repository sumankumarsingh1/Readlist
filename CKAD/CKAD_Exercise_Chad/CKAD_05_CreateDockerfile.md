# [CKAD Exercises](https://killercoda.com/chadmcrowell/course/ckad)

> By Chad M. Crowell

## Create Dockerfile with Args and Run

Welcome !
In this scenario, we'll learn how to build and run a Docker container!

ENJOY!

### Create a Dockerfile

Notice the Dockerfile in the current directory with the `ls -a` command

Open the `Dockerfile` with the `vim Dockerfile` command

The first line is the `FROM` instruction. This specifies the Parent Image from which you are building.

The second line is `ENTRYPOINT` instruction. The `ENTRYPOINT` instruction will allow you to run an executable (e.g. “sleep”) immediately as the container is starting up.

The third line is `CMD` , which is passed to the `ENTRYPOINT` as a command parameter (e.g. “3600”).

In this case, the command parameter is “3600” which is in seconds (i.e. 1 hour).

So these lines would be similar to running the command `sleep 3600` from a shell within the container.

We can exit vim to get back to our command line prompt. Let’s now run the command `docker build -t busybox-sleep .` to build a container image from that Dockerfile and tag it with the `-t` option and “busybox-sleep”.

You will see that the output look similar to the following:

```bash
controlplane $ docker build -t busybox-sleep .
Sending build context to Docker daemon  6.711MB
Step 1/3 : FROM busybox
latest: Pulling from library/busybox
71d064a1ac7d: Pull complete 
Digest: sha256:6e494387c901caf429c1bf77bd92fb82b33a68c0e19f6d1aa6a3ac8d27a7049d
Status: Downloaded newer image for busybox:latest
 ---> b539af69bc01
Step 2/3 : ENTRYPOINT ["sleep"]
 ---> Running in 27bf6efb18dc
Removing intermediate container 27bf6efb18dc
 ---> 55de7efd5d0b
Step 3/3 : CMD ["3600"]
 ---> Running in 2783a599fba1
Removing intermediate container 2783a599fba1
 ---> aa92909e7707
Successfully built aa92909e7707
Successfully tagged busybox-sleep:latest
```

### Startup Local Registry

Push our image to that registry, so Kubernetes can pull from that local registry.

Now, let’s give the “busy box-sleep” a new tag with the command `docker tag busybox-sleep localhost:5000/busybox-sleep .`

If we run the command docker images , we should see an output similar to this:

controlplane $ docker images
REPOSITORY                     TAG       IMAGE ID       CREATED              SIZE
busybox-sleep                  latest    9dfaa739de5f   About a minute ago   4.26MB
localhost:5000/busybox-sleep   latest    9dfaa739de5f   About a minute ago   4.26MB
busybox                        latest    5242710cbd55   3 days ago           4.26MB
registry                       latest    4bb5ea59f8e0   2 weeks ago          24MB




#### Output

```bash
controlplane:~$ ls -al Dockerfile 
-rw-r--r-- 1 root root 49 Dec 30 06:40 Dockerfile
controlplane:~$ cat Dockerfile 
FROM busybox

ENTRYPOINT ["sleep"]

CMD ["3600"]
controlplane:~$ 
```

Docker build

```bash
controlplane:~$ docker build -t busybox-sleep . 
DEPRECATED: The legacy builder is deprecated and will be removed in a future release.
            Install the buildx component to build images with BuildKit:
            https://docs.docker.com/go/buildx/

time="2025-12-30T06:57:09Z" level=error msg="Can't add file /root/.gnupg/S.gpg-agent to tar: archive/tar: sockets not supported"
time="2025-12-30T06:57:09Z" level=error msg="Can't add file /root/.gnupg/S.gpg-agent.browser to tar: archive/tar: sockets not supported"
time="2025-12-30T06:57:09Z" level=error msg="Can't add file /root/.gnupg/S.gpg-agent.extra to tar: archive/tar: sockets not supported"
time="2025-12-30T06:57:09Z" level=error msg="Can't add file /root/.gnupg/S.gpg-agent.ssh to tar: archive/tar: sockets not supported"
Sending build context to Docker daemon  114.7MB
Step 1/3 : FROM busybox
latest: Pulling from library/busybox
e59838ecfec5: Pulling fs layer
e59838ecfec5: Verifying Checksum
e59838ecfec5: Download complete
e59838ecfec5: Pull complete
Digest: sha256:d80cd694d3e9467884fcb94b8ca1e20437d8a501096cdf367a5a1918a34fc2fd
Status: Downloaded newer image for busybox:latest
 ---> 08ef35a1c3f0
Step 2/3 : ENTRYPOINT ["sleep"]
 ---> Running in 57548bb8948a
 ---> Removed intermediate container 57548bb8948a
 ---> 25370547e443
Step 3/3 : CMD ["3600"]
 ---> Running in 1848a9be6f52
 ---> Removed intermediate container 1848a9be6f52
 ---> 0c36725af63c
Successfully built 0c36725af63c
Successfully tagged busybox-sleep:latest
controlplane:~$ rpm -i busyboxx
Command 'rpm' not found, but can be installed with:
apt install rpm
controlplane:~$ 
```

Check docker images

```bash
controlplane:~$ docker images
REPOSITORY      TAG       IMAGE ID       CREATED         SIZE
busybox-sleep   latest    0c36725af63c   6 minutes ago   4.43MB
registry        latest    e4e570676819   9 months ago    57.7MB
busybox         latest    08ef35a1c3f0   15 months ago   4.43MB
controlplane:~$ 
```

Podman build

```bash
controlplane:~$ podman build -t busybox-sleep1 .
STEP 1/3: FROM busybox
Resolved "busybox" as an alias (/etc/containers/registries.conf.d/shortnames.conf)
Trying to pull docker.io/library/busybox:latest...
Getting image source signatures
Copying blob e59838ecfec5 done   | 
Copying config 08ef35a1c3 done   | 
Writing manifest to image destination
STEP 2/3: ENTRYPOINT ["sleep"]
--> e504e19fb6d0
STEP 3/3: CMD ["3600"]
COMMIT busybox-sleep1
--> bbf92f975c01
Successfully tagged localhost/busybox-sleep1:latest
bbf92f975c01081c9f131a81a93fcfe127fb11cf8b1fb1c58af8ef54ad9368d9
controlplane:~$ 
```

Check podman images

```bash
controlplane:~$ podman images
REPOSITORY                 TAG         IMAGE ID      CREATED         SIZE
localhost/busybox-sleep1   latest      bbf92f975c01  54 seconds ago  4.67 MB
docker.io/library/busybox  latest      08ef35a1c3f0  15 months ago   4.68 MB
controlplane:~$ 
```

Startup local registry with Docker

```bash
controlplane:~$ docker tag busybox-sleep localhost:5000/busybox-sleep
controlplane:~$ docker images
REPOSITORY                     TAG       IMAGE ID       CREATED          SIZE
busybox-sleep                  latest    0c36725af63c   11 minutes ago   4.43MB
localhost:5000/busybox-sleep   latest    0c36725af63c   11 minutes ago   4.43MB
registry                       latest    e4e570676819   9 months ago     57.7MB
busybox                        latest    08ef35a1c3f0   15 months ago    4.43MB
controlplane:~$ 
```

Startup local registry with Podman

```bash
controlplane:~$ podman tag busybox-sleep1 localhost:5000/busybox-sleep1
controlplane:~$ podman images
REPOSITORY                     TAG         IMAGE ID      CREATED        SIZE
localhost:5000/busybox-sleep1  latest      bbf92f975c01  7 minutes ago  4.67 MB
localhost/busybox-sleep1       latest      bbf92f975c01  7 minutes ago  4.67 MB
docker.io/library/busybox      latest      08ef35a1c3f0  15 months ago  4.68 MB
controlplane:~$ 
```

WELL DONE !
You solved this challenge!

