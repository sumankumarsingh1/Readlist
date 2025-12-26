# Interactive Scenarios for Kubernetes Application Developers (3 of ?)

Reference: <https://killercoda.com/killer-shell-ckad>

## Rollout Canary

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


Explanation

Why can you call curl wonderful:30080 and it works?


There is a NodePort Service wonderful which listens on port 30080 . It has the Pods of Deployment of app "wonderful" as selector.


We can reach the NodePort Service via the K8s Node IP:


curl 172.30.1.2:30080

And because of an entry in /etc/hosts we can call:


curl wonderful:30080

Tip 1

The service wonderful points to Pods with label app: wonderful .


Tip 2

The replica count of the Deployments will define how often the Pods will serve a request.


Solution

Reduce the replicas of the old deployment to 8:


apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: wonderful
  name: wonderful-v1
spec:
  replicas: 8
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

Create a new Deployment and watch these places:


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

Verify

Call the app with curl wonderful:30080 , around 20% should hit the new image.