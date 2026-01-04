# [CKAD Exercises](https://killercoda.com/chadmcrowell/course/ckad)

> By Chad M. Crowell

## [Creating a pod with configMap](https://killercoda.com/chadmcrowell/course/ckad/create-configmap)

Welcome !
In this scenario, we'll learn how to create a configMap for a pod in Kubernetes

ENJOY!

### Create a configMap

Create a configmap named `redis-config` . Within the configMap, use the key maxmemory with value 2mb and key `maxmemory-policy` with value `allkeys-lru` .

**HINT**: try `k create cm -h` for command options and examples

#### Solution

```bash
kubectl create configmap redis-config \
  --from-literal=maxmemory=2mb \
  --from-literal=maxmemory-policy=allkeys-lru
```

`kubectl get configmap redis-config -o yaml`

#### Output

```bash
controlplane:~$ kubectl create configmap redis-config \
>   --from-literal=maxmemory=2mb \
>   --from-literal=maxmemory-policy=allkeys-lru
configmap/redis-config created
controlplane:~$ kubectl get configmap redis-config -o yaml
apiVersion: v1
data:
  maxmemory: 2mb
  maxmemory-policy: allkeys-lru
kind: ConfigMap
metadata:
  creationTimestamp: "2025-12-28T15:56:56Z"
  name: redis-config
  namespace: default
  resourceVersion: "2911"
  uid: 82f10653-4d41-4984-9a9c-4bd1579551ab
controlplane:~$ 
```

### Create the YAML for a pod with configMap attached

Create a pod named `redis-pod` that uses the image `redis:7` and exposes port `6379` . Use the command `redis-server /redis-master/redis.conf` to store redis configuration data and store this in an `emptyDir` volume.

Mount the `redis-config` configmap data to the pod for use within the container.

> ⚠️ **HINT**: create the pod YAML with a `--dry-run`

#### Solution 2

```bash
k run redis-pod --image=redis:7 --port 6379 --command 'redis-server' '/redis-master/redis.conf' --dry-run=client -o yaml > redis-pod.yaml
```


`cat <<'EOF' > redis-pod.yaml`

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: redis-pod
spec:
  initContainers:
  - name: init-redis-config
    image: busybox:1.36
    command:
    - sh
    - -c
    - |
      set -e
      cat <<EOF >/redis-master/redis.conf
      maxmemory $(cat /configmap/maxmemory)
      maxmemory-policy $(cat /configmap/maxmemory-policy)
      EOF
    volumeMounts:
    - name: redis-config
      mountPath: /redis-master
    - name: redis-config-map
      mountPath: /configmap
      readOnly: true
  containers:
  - name: redis
    image: redis:7
    command:
    - redis-server
    - /redis-master/redis.conf
    ports:
    - containerPort: 6379
    volumeMounts:
    - name: redis-config
      mountPath: /redis-master
    - name: redis-data
      mountPath: /redis-data
  volumes:
  - name: redis-config-map
    configMap:
      name: redis-config
  - name: redis-config
    emptyDir: {}
  - name: redis-data
    emptyDir: {}
```    

`EOF`

```bash
k apply -f redis-pod.yaml
k get pod redis-pod -o wide
k logs redis-pod
```

#### Output 2

```bash
controlplane:~$ k run redis-pod --image=redis:7 --port 6379 --command 'redis-server' '/redis-master/redis.conf' --dry-run=client -o yaml > redis-pod.yaml
controlplane:~$ 
```

```bash
controlplane:~$ cat <<'EOF' > redis-pod.yaml
> apiVersion: v1
> kind: Pod
> metadata:
>   name: redis-pod
> spec:
>   initContainers:
>   - name: init-redis-config
>     image: busybox:1.36
>     command:
>     - sh
>     - -c
>     - |
>       set -e
>       cat <<EOF >/redis-master/redis.conf
>       maxmemory $(cat /configmap/maxmemory)
>       maxmemory-policy $(cat /configmap/maxmemory-policy)
>       EOF
>     volumeMounts:
>     - name: redis-config
>       mountPath: /redis-master
>     - name: redis-config-map
>       mountPath: /configmap
>       readOnly: true
>   containers:
>   - name: redis
>     image: redis:7
>     command:
>     - redis-server
>     - /redis-master/redis.conf
>     ports:
>     - containerPort: 6379
>     volumeMounts:
>     - name: redis-config
>       mountPath: /redis-master
>     - name: redis-data
>       mountPath: /redis-data
>   volumes:
>   - name: redis-config-map
>     configMap:
>       name: redis-config
>   - name: redis-config
>     emptyDir: {}
>   - name: redis-data
>     emptyDir: {}
> EOF
controlplane:~$ 
```

```bash
controlplane:~$ k apply -f redis-pod.yaml
pod/redis-pod created
controlplane:~$ k get pod redis-pod -o wide
NAME        READY   STATUS            RESTARTS   AGE   IP            NODE           NOMINATED NODE   READINESS GATES
redis-pod   0/1     PodInitializing   0          5s    192.168.0.6   controlplane   <none>           <none>
controlplane:~$ k logs redis-pod
Defaulted container "redis" out of: redis, init-redis-config (init)
1:C 28 Dec 2025 16:05:37.700 * oO0OoO0OoO0Oo Redis is starting oO0OoO0OoO0Oo
1:C 28 Dec 2025 16:05:37.700 * Redis version=7.4.7, bits=64, commit=00000000, modified=0, pid=1, just started
1:C 28 Dec 2025 16:05:37.700 * Configuration loaded
1:M 28 Dec 2025 16:05:37.701 * monotonic clock: POSIX clock_gettime
1:M 28 Dec 2025 16:05:37.702 * Running mode=standalone, port=6379.
1:M 28 Dec 2025 16:05:37.702 * Server initialized
1:M 28 Dec 2025 16:05:37.703 * Ready to accept connections tcp
controlplane:~$ 
```

### Check the pod configuration settings

Connect to the `redis-pod` container using the Redis CLI and inspect the configuration values.

Open the Redis shell:

`kubectl exec -it redis-pod -- redis-cli`

From the Redis prompt, retrieve the maxmemory setting:

`CONFIG GET maxmemory`

In the same session, check the maxmemory-policy setting:

`CONFIG GET maxmemory-policy`

#### Output 3

```bash
controlplane:~$ kubectl exec -it redis-pod -- redis-cli
Defaulted container "redis" out of: redis, init-redis-config (init)
127.0.0.1:6379> 
127.0.0.1:6379> 
127.0.0.1:6379> 
127.0.0.1:6379> CONFIG GET maxmemory
1) "maxmemory"
2) "2097152"
127.0.0.1:6379> CONFIG GET maxmemory-policy
1) "maxmemory-policy"
2) "allkeys-lru"
127.0.0.1:6379> 
```

WELL DONE !
You solved this challenge!
