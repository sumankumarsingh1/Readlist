# [CKAD Exercises](https://killercoda.com/chadmcrowell/course/ckad)

> By Chad M. Crowell

## [Create a Pod with emptyDir volume](https://killercoda.com/chadmcrowell/course/ckad/volumes)

Welcome !
In this scenario, we'll learn how to create a Pod with two containers mounting an emptyDir volume!

ENJOY!

### Create Pod YAML

Use an `emptyDir` volume to share data between containers in the same pod.

1. Create a pod named `shared-pod` in namespace `volumes` with two containers (`writer` and `reader` ).
1. Mount a shared `emptyDir` volume at `/data` in both containers.
1. Have the writer continually append a timestamp to `/data/out.log` ; the reader should tail the file.

#### Solution

```bash 
kubectl create namespace volumes
cat <<'EOF_POD' > shared-pod.yaml
```

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: shared-pod
  namespace: volumes
spec:
  containers:
  - name: writer
    image: busybox:1.36
    command:
      - sh
      - -c
      - |
        while true; do
          date >> /data/out.log
          sleep 2
        done
    volumeMounts:
    - name: shared
      mountPath: /data
  - name: reader
    image: busybox:1.36
    command:
      - sh
      - -c
      - tail -f /data/out.log
    volumeMounts:
    - name: shared
      mountPath: /data
  volumes:
  - name: shared
    emptyDir: {}
```

```bash
EOF_POD
kubectl apply -f shared-pod.yaml
kubectl -n volumes logs shared-pod -c reader --tail=10
```

#### Output

```bash
controlplane:~$ kubectl create namespace volumes
namespace/volumes created
```

```bash
controlplane:~$ cat <<'EOF_POD' > shared-pod.yaml
> apiVersion: v1
> kind: Pod
> metadata:
>   name: shared-pod
>   namespace: volumes
> spec:
>   containers:
>   - name: writer
>     image: busybox:1.36
>     command:
>       - sh
>       - -c
>       - |
>         while true; do
>           date >> /data/out.log
>           sleep 2
>         done
>     volumeMounts:
>     - name: shared
>       mountPath: /data
>   - name: reader
>     image: busybox:1.36
>     command:
>       - sh
>       - -c
>       - tail -f /data/out.log
>     volumeMounts:
>     - name: shared
>       mountPath: /data
>   volumes:
>   - name: shared
>     emptyDir: {}
> EOF_POD
controlplane:~$ 
```

```bash
controlplane:~$ kubectl apply -f shared-pod.yaml
pod/shared-pod created
controlplane:~$ 
```

```bash
controlplane:~$ kubectl -n volumes logs shared-pod -c reader --tail=10
Wed Dec 31 02:54:16 UTC 2025
Wed Dec 31 02:54:18 UTC 2025
Wed Dec 31 02:54:20 UTC 2025
Wed Dec 31 02:54:22 UTC 2025
Wed Dec 31 02:54:24 UTC 2025
Wed Dec 31 02:54:26 UTC 2025
Wed Dec 31 02:54:28 UTC 2025
Wed Dec 31 02:54:30 UTC 2025
Wed Dec 31 02:54:32 UTC 2025
Wed Dec 31 02:54:34 UTC 2025
controlplane:~$ 
``` {{exec}}

WELL DONE !
You solved this challenge!