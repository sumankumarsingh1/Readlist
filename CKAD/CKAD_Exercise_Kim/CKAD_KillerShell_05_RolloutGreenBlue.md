# Interactive Scenarios for Kubernetes Application Developers (5 of 12)

Reference: <https://killercoda.com/killer-shell-ckad>

## [Rollout Green-Blue](https://killercoda.com/killer-shell-ckad/scenario/rollout-green-blue)

### Perform a Green-Blue rollout of an application

Application "wonderful" is running in **`default`** Namespace.

You can call the app using **`curl wonderful:30080`** .

The application YAML is available at **`/wonderful/init.yaml`** .

The app has a Deployment with image **`httpd:alpine`** , but should be switched over to **`nginx:alpine`** .

The switch should happen instantly. Meaning that from the moment of rollout, all new requests should hit the new image.

1. Create a new Deployment wonderful-v2 which uses image nginx:alpine with 4 replicas. It's Pods should have labels app: wonderful and version: v2

1. Once all new Pods are running, change the selector label of Service wonderful to version: v2

1. Finally scale down Deployment wonderful-v1 to 0 replicas

#### Explanation

Why can you call curl **`wonderful:30080`** and it works?

There is a NodePort Service **`wonderful`** which listens on port **`30080`** . It has the Pods of Deployment of app "wonderful" as selector.

We can reach the NodePort Service via the K8s Node IP:

**`curl 172.30.1.2:30080`**

And because of an entry in /etc/hosts we can call:

**`curl wonderful:30080`**

#### Tip

The idea is to have two Deployments running at the same time, one with the old and one with the new image.

But there is only **one** Service, and it only points to the Pods of **one** Deployment.

Once we point the Service to the Pods of the new Deployment, all new requests will hit the new image.

#### Solution

Create a new Deployment and watch these places:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: wonderful        
  name: wonderful-v2    # watch
spec:
  replicas: 4
  selector:
    matchLabels:
      app: wonderful    # watch
      version: v2       # watch
  template:
    metadata:
      labels:
        app: wonderful  # watch
        version: v2     # watch
    spec:
      containers:
      - image: nginx:alpine # watch
        name: nginx
```

Use the below command to create the yaml file **`wonderful/init2.yaml`**
```bash
kubectl create deployment wonderful --image=nginx:alpine --dry-run=client -o yaml > /wonderful/init2.yaml
```
Edit the file to modify the required configuration mentioned in lines commented as #watch.

Then create the deployment using the command:

```bash
k apply -f /wonderful/init2.yaml
```

Wait till all Pods are running, then switch the Service selector:

```yaml
apiVersion: v1
kind: Service
metadata:
  labels:
    app: wonderful
  name: wonderful
spec:
  ports:
  - port: 80
    protocol: TCP
    targetPort: 80
    nodePort: 30080
  selector:
    app: wonderful
    version: v2      # Modify to v2
  type: NodePort
```

Confirm with curl wonderful:30080 , all requests should hit the new image.

Finally scale down the original Deployment:

**`kubectl scale deploy wonderful-v1 --replicas 0`**

#### Output

```bash
controlplane:~$ curl wonderful:30080
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN" "http://www.w3.org/TR/html4/strict.dtd">
<html>
<head>
<title>It works! Apache httpd</title>
</head>
<body>
<p>It works!</p>
</body>
</html>
controlplane:~$ cat /wonderful/init.yaml 
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: wonderful
  name: wonderful-v1
spec:
  replicas: 4
  selector:
    matchLabels:
      app: wonderful
      version: v1
  template:
    metadata:
      labels:
        app: wonderful
        version: v1
    spec:
      containers:
      - image: httpd:alpine
        name: httpd
---
apiVersion: v1
kind: Service
metadata:
  labels:
    app: wonderful
  name: wonderful
spec:
  ports:
  - port: 80
    protocol: TCP
    targetPort: 80
    nodePort: 30080
  selector:
    app: wonderful
    version: v1
  type: NodePort
controlplane:~$ k get pods
NAME                            READY   STATUS    RESTARTS   AGE
wonderful-v1-6f8c9f5cc4-4jjtf   1/1     Running   0          11m
wonderful-v1-6f8c9f5cc4-5sgcx   1/1     Running   0          11m
wonderful-v1-6f8c9f5cc4-7nq7z   1/1     Running   0          11m
wonderful-v1-6f8c9f5cc4-9mf4s   1/1     Running   0          11m
controlplane:~$ k get deployment
NAME           READY   UP-TO-DATE   AVAILABLE   AGE
wonderful-v1   4/4     4            4           11m
controlplane:~$ 
controlplane:~$ k run wonderful:v2 --image=nginx:alpine -o yaml --dry-run=client > /wonderful/init.yaml 
.ICEauthority  .bashrc        .dbus/         .kube/         .profile       .vimrc         filesystem/    
.Xauthority    .cache/        .gnupg/        .local/        .ssh/          .vnc/          
.bash_history  .config/       .gvfs/         .mozilla/      .theia/        .wget-hsts     
controlplane:~$ k run wonderful:v2 --image=nginx:alpine -o yaml --dry-run=client > /wonderful/init2.yaml 
controlplane:~$ cat /wonderful/init2.yaml 
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: wonderful:v2
  name: wonderful:v2
spec:
  containers:
  - image: nginx:alpine
    name: wonderful:v2
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
controlplane:~$ vi /wonderful/init2.yaml 
controlplane:~$ kubectl create deployment wonderful --image=nginx:alpine --dry-run=client -o yaml > /wonderful/init2.yaml
controlplane:~$ cat /wonderful/init2.yaml 
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: wonderful
  name: wonderful
spec:
  replicas: 1
  selector:
    matchLabels:
      app: wonderful
  strategy: {}
  template:
    metadata:
      labels:
        app: wonderful
    spec:
      containers:
      - image: nginx:alpine
        name: nginx
        resources: {}
status: {}
controlplane:~$ vi /wonderful/init2.yaml 
controlplane:~$ k apply -f /wonderful/init2.yaml 
deployment.apps/wonderful-v2 created
controlplane:~$ k get deployments      
NAME           READY   UP-TO-DATE   AVAILABLE   AGE
wonderful-v1   4/4     4            4           28m
wonderful-v2   4/4     4            4           11s
controlplane:~$ k get service
NAME         TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
kubernetes   ClusterIP   10.96.0.1       <none>        443/TCP        7d17h
wonderful    NodePort    10.109.52.115   <none>        80:30080/TCP   29m
controlplane:~$ k get pods 
NAME                            READY   STATUS    RESTARTS   AGE
wonderful-v1-6f8c9f5cc4-4jjtf   1/1     Running   0          29m
wonderful-v1-6f8c9f5cc4-5sgcx   1/1     Running   0          29m
wonderful-v1-6f8c9f5cc4-7nq7z   1/1     Running   0          29m
wonderful-v1-6f8c9f5cc4-9mf4s   1/1     Running   0          29m
wonderful-v2-5996474d7f-6z6gh   1/1     Running   0          31s
wonderful-v2-5996474d7f-f92ll   1/1     Running   0          31s
wonderful-v2-5996474d7f-mkf5c   1/1     Running   0          31s
wonderful-v2-5996474d7f-t686w   1/1     Running   0          31s
controlplane:~$ k edit service wonderful
service/wonderful edited
controlplane:~$ curl wonderful:30080
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
controlplane:~$ k scale deploy wonderful-v1 --replicas 0
deployment.apps/wonderful-v1 scaled
controlplane:~$ k get pod
NAME                            READY   STATUS    RESTARTS   AGE
wonderful-v2-5996474d7f-6z6gh   1/1     Running   0          3m48s
wonderful-v2-5996474d7f-f92ll   1/1     Running   0          3m48s
wonderful-v2-5996474d7f-mkf5c   1/1     Running   0          3m48s
wonderful-v2-5996474d7f-t686w   1/1     Running   0          3m48s
controlplane:~$ 
```
