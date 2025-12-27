# Interactive Scenarios for Kubernetes Application Developers (6 of 12)

Reference: <https://killercoda.com/killer-shell-ckad>

## [Rollout Canary](https://killercoda.com/killer-shell-ckad/scenario/rollout-canary)

### Perform a Canary rollout of an application

Application "wonderful" is running in **`default`** Namespace.

You can call the app using curl **`wonderful:30080`** .

The application YAML is available at **`/wonderful/init.yaml`** .

The app has a Deployment with image **`httpd:alpine`** , but should be switched over to **`nginx:alpine`** .

The switch should not happen fast or automatically, but using the Canary approach:

* 20% of requests should hit the new image
* 80% of requests should hit the old image

For this create a new Deployment **`wonderful-v2`** which uses image **`nginx:alpine`** .

The total amount of Pods of both Deployments combined should be 10.

#### Explanation

Why can you call curl wonderful:30080 and it works?

There is a NodePort Service **`wonderful`** which listens on port **`30080`** . It has the Pods of Deployment of app "wonderful" as selector.

We can reach the NodePort Service via the K8s Node IP:

**`curl 172.30.1.2:30080`**

And because of an entry in /etc/hosts we can call:

**`curl wonderful:30080`**

#### Tip 1

The service **`wonderful`** points to Pods with label **`app: wonderful`** .

#### Tip 2

The replica count of the Deployments will define how often the Pods will serve a request.

#### Solution

Reduce the replicas of the old deployment to 8:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: wonderful
  name: wonderful-v1
spec:
  replicas: 8       # Reduce to 8
  selector:
    matchLabels:
      app: wonderful
  template:
    metadata:
      labels:
        app: wonderful
    spec:
      containers:
      - image: httpd:alpine
        name: httpd
```

Create a new Deployment and watch these places:


```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: wonderful
  name: wonderful-v2    # watch
spec:
  replicas: 2           # watch
  selector:
    matchLabels:
      app: wonderful
  template:
    metadata:
      labels:
        app: wonderful
    spec:
      containers:
      - image: nginx:alpine # watch
        name: nginx
```

#### Verify

Call the app with **`curl wonderful:30080`** , around 20% should hit the new image.

#### Output

Check the curl, current deployment, pods, and service configuration

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
controlplane:~$ k get deploy
NAME           READY   UP-TO-DATE   AVAILABLE   AGE
wonderful-v1   10/10   10           10          90s
controlplane:~$ k get pod
NAME                           READY   STATUS    RESTARTS   AGE
wonderful-v1-d9b4c8bb5-4pjwf   1/1     Running   0          96s
wonderful-v1-d9b4c8bb5-7ztdf   1/1     Running   0          96s
wonderful-v1-d9b4c8bb5-8pdrt   1/1     Running   0          96s
wonderful-v1-d9b4c8bb5-gd5sd   1/1     Running   0          96s
wonderful-v1-d9b4c8bb5-gmnq9   1/1     Running   0          96s
wonderful-v1-d9b4c8bb5-hbxkb   1/1     Running   0          96s
wonderful-v1-d9b4c8bb5-pn5jn   1/1     Running   0          96s
wonderful-v1-d9b4c8bb5-rxsvf   1/1     Running   0          96s
wonderful-v1-d9b4c8bb5-vfrf9   1/1     Running   0          96s
wonderful-v1-d9b4c8bb5-w99r4   1/1     Running   0          96s
controlplane:~$ 
controlplane:~$ k get service
NAME         TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
kubernetes   ClusterIP   10.96.0.1        <none>        443/TCP        8d
wonderful    NodePort    10.102.105.218   <none>        80:30080/TCP   112s
```

Check the current deployment and service configuration in the **`/wonderful/init.yaml`**

```bash
controlplane:~$ cat /wonderful/init.yaml 
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: wonderful
  name: wonderful-v1
spec:
  replicas: 10
  selector:
    matchLabels:
      app: wonderful
  template:
    metadata:
      labels:
        app: wonderful
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
  type: NodePort
controlplane:~$ 


```

Check the current deployment is having 10 replicas running

```bash
controlplane:~$ k get deploy  
NAME           READY   UP-TO-DATE   AVAILABLE   AGE
wonderful-v1   10/10   10           10          7m32s
controlplane:~$ k edit deploy wonderful-v1
deployment.apps/wonderful-v1 edited
```

Ater reducing the replica on wonder-v1 check again

```bash
controlplane:~$ k get deployments.apps 
NAME           READY   UP-TO-DATE   AVAILABLE   AGE
wonderful-v1   8/8     8            8           8m51s
```

Copy the init.yaml (only the deployment part) to init2.yaml 

```bash
controlplane:~$ cp /wonderful/init.yaml   /wonderful/init2.yaml 

```

Edit the configuration of init2.yaml as below:
* name as wonderful-v2
* replicas as 2

```yaml
controlplane:~$ cat /wonderful/init2.yaml 
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: wonderful
  name: wonderful-v2
spec:
  replicas: 2
  selector:
    matchLabels:
      app: wonderful
  template:
    metadata:
      labels:
        app: wonderful
    spec:
      containers:
      - image: nginx:alpine
        name: nginx
controlplane:~$ 
```

Create the deployment using the file init2.yaml

```bash
controlplane:~$ k create -f /wonderful/init2.yaml 
deployment.apps/wonderful-v2 created
controlplane:~$ 
```

Check the deployments and running pods
* 8 instances of v1
* 2 instances of v2

```bash
controlplane:~$ k get deploy
NAME           READY   UP-TO-DATE   AVAILABLE   AGE
wonderful-v1   8/8     8            8           17m
wonderful-v2   2/2     2            2           72s
controlplane:~$ k get pod 
NAME                           READY   STATUS    RESTARTS   AGE
wonderful-v1-d9b4c8bb5-4pjwf   1/1     Running   0          17m
wonderful-v1-d9b4c8bb5-7ztdf   1/1     Running   0          17m
wonderful-v1-d9b4c8bb5-8pdrt   1/1     Running   0          17m
wonderful-v1-d9b4c8bb5-gd5sd   1/1     Running   0          17m
wonderful-v1-d9b4c8bb5-gmnq9   1/1     Running   0          17m
wonderful-v1-d9b4c8bb5-rxsvf   1/1     Running   0          17m
wonderful-v1-d9b4c8bb5-vfrf9   1/1     Running   0          17m
wonderful-v1-d9b4c8bb5-w99r4   1/1     Running   0          17m
wonderful-v2-84d7bff65-6x6ld   1/1     Running   0          77s
wonderful-v2-84d7bff65-rj76b   1/1     Running   0          77s
controlplane:~$ 
```