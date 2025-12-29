# Interactive Scenarios for Kubernetes Application Developers (4 of 12)

Reference: <https://killercoda.com/killer-shell-ckad>


## [Rollout Rolling](https://killercoda.com/killer-shell-ckad/scenario/rollout-rolling)

### Perform a rolling rollout of an application

Application "wonderful" is running in **`default`** Namespace.

You can call the app using **`curl wonderful:30080`** .

The app has a Deployment with image httpd:alpine , but should be switched over to nginx:alpine .

Set the maxSurge to 50% and the maxUnavailable to 0% . Then perform a rolling update.

Wait till the rolling update has succeeded.

#### Explanation

Why can you call curl wonderful:30080 and it works?

There is a NodePort Service wonderful which listens on port 30080 . It has the Pods of Deployment of app "wonderful" as selector.

We can reach the NodePort Service via the K8s Node IP:


`curl 172.30.1.2:30080`

And because of an entry in /etc/hosts we can call:

`curl wonderful:30080`
#### Tip

Changing the image of a running Deployment will start a rollout.

Rolling rollout is the default strategy.


#### Solution

k edit deploy wonderful-v1

Then we adjust it to the requirements:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: wonderful
  name: wonderful-v1
  namespace: default
  ...
spec:
  ...
  replicas: 3
  selector:
    matchLabels:
      app: wonderful
  strategy:
    rollingUpdate:
      maxSurge: 50%
      maxUnavailable: 0%
    type: RollingUpdate
  template:
    metadata:
      labels:
        app: wonderful
    spec:
      containers:
      - image: nginx:alpine
```

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
controlplane:~$ k edit deploy wonderful-v1
deployment.apps/wonderful-v1 edited


```

Deployment before:

```yaml
# Please edit the object below. Lines beginning with a '#' will be ignored,
# and an empty file will abort the edit. If an error occurs while saving this file will be
# reopened with the relevant failures.
#
apiVersion: apps/v1
kind: Deployment
metadata:
  annotations:
    deployment.kubernetes.io/revision: "2"
  creationTimestamp: "2025-12-26T08:16:03Z"
  generation: 2
  labels:
    app: wonderful
  name: wonderful-v1
  namespace: default
  resourceVersion: "4364"
  uid: 1ba11a55-6444-4f31-8a14-9b5ea7caa33c
spec:
  progressDeadlineSeconds: 600
  replicas: 3
  revisionHistoryLimit: 10
  selector:
    matchLabels:
      app: wonderful
  strategy:
    rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 25%
    type: RollingUpdate
  template:
    metadata:
      labels:
        app: wonderful
    spec:
      containers:
      - image: httpd:alpine
        imagePullPolicy: IfNotPresent
        name: httpd
        resources: {}
        terminationMessagePath: /dev/termination-log
        terminationMessagePolicy: File
      dnsPolicy: ClusterFirst
      restartPolicy: Always
      schedulerName: default-scheduler
      securityContext: {}
      terminationGracePeriodSeconds: 30
status:
...
```

Deployment after updating:

```yaml
# Please edit the object below. Lines beginning with a '#' will be ignored,
# and an empty file will abort the edit. If an error occurs while saving this file will be
# reopened with the relevant failures.
#
apiVersion: apps/v1
kind: Deployment
metadata:
  annotations:
    deployment.kubernetes.io/revision: "2"
  creationTimestamp: "2025-12-26T08:16:03Z"
  generation: 2
  labels:
    app: wonderful
  name: wonderful-v1
  namespace: default
  resourceVersion: "4364"
  uid: 1ba11a55-6444-4f31-8a14-9b5ea7caa33c
spec:
  progressDeadlineSeconds: 600
  replicas: 3
  revisionHistoryLimit: 10
  selector:
    matchLabels:
      app: wonderful
  strategy:
    rollingUpdate:
      maxSurge: 50%
      maxUnavailable: 0%
    type: RollingUpdate
  template:
    metadata:
      labels:
        app: wonderful
    spec:
      containers:
      - image: nginx:alpine
        imagePullPolicy: IfNotPresent
        name: httpd
        resources: {}
        terminationMessagePath: /dev/termination-log
        terminationMessagePolicy: File
      dnsPolicy: ClusterFirst
      restartPolicy: Always
      schedulerName: default-scheduler
      securityContext: {}
      terminationGracePeriodSeconds: 30
status:
```

