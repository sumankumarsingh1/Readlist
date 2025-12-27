# Interactive Scenarios for Kubernetes Application Developers (9 of 12)

Reference: <https://killercoda.com/killer-shell-ckad>


## [Ingress Create](https://killercoda.com/killer-shell-ckad/scenario/ingress-create)

### Create a new Ingress for existing Deployments

Create Services for existing Deployments

There are two existing Deployments in Namespace world which should be made accessible via an Ingress.

First: create ClusterIP Services for both Deployments for port 80 . The Services should have the same name as the Deployments.

#### Tip

**`k expose deploy -h`**

Solution

```bash
k -n world expose deploy europe --port 80
k -n world expose deploy asia --port 80
```

#### Output

```bash
controlplane:~$ k -n world expose deploy europe --port 80
service/europe exposed
controlplane:~$ k -n world expose deploy asia --port 80
service/asia exposed
```

### Create Ingress for existing Services

The Nginx Ingress Controller has been installed.

Create a new Ingress resource called **`world`** for domain name **`world.universe.mine`** . The domain points to the K8s Node IP via **`/etc/hosts`** .

The Ingress resource should have two routes pointing to the existing Services:

**`http://world.universe.mine:30080/europe/`**

and

**`http://world.universe.mine:30080/asia/`**

#### Explanation

Check the NodePort Service for the Nginx Ingress Controller to see the ports

**`k -n ingress-nginx get svc ingress-nginx-controller`**

We can reach the NodePort Service via the K8s Node IP:

**`curl http://172.30.1.2:30080`**

And because of the entry in /etc/hosts we can call

**`curl http://world.universe.mine:30080`**

#### Tip 2a

The Ingress resources needs to be created in the same Namespace as the applications.

#### Tip 2b

Find out the ingressClassName with:

**`k get ingressclass`**

#### Tip 2c

You can work with this template

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: world
  namespace: world
  annotations:
    # this annotation removes the need for a trailing slash when calling urls
    # but it is not necessary for solving this scenario
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  ingressClassName: nginx # k get ingressclass
  rules:
  - host: "world.universe.mine"
  ...
```

#### Solution 2

```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: world
  namespace: world
  annotations:
    # this annotation removes the need for a trailing slash when calling urls
    # but it is not necessary for solving this scenario
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  ingressClassName: nginx # k get ingressclass
  rules:
  - host: "world.universe.mine"
    http:
      paths:
      - path: /europe
        pathType: Prefix
        backend:
          service:
            name: europe
            port:
              number: 80
      - path: /asia
        pathType: Prefix
        backend:
          service:
            name: asia
            port:
              number: 80
```








#### Output 2

```bash
controlplane:~$ k -n ingress-nginx get svc ingress-nginx-controller
NAME                       TYPE       CLUSTER-IP       EXTERNAL-IP   PORT(S)                      AGE
ingress-nginx-controller   NodePort   10.110.193.137   <none>        80:30080/TCP,443:30443/TCP   19m


controlplane:~$ curl http://172.30.1.2:30080
<html>
<head><title>404 Not Found</title></head>
<body>
<center><h1>404 Not Found</h1></center>
<hr><center>nginx</center>
</body>
</html>

controlplane:~$ curl http://world.universe.mine:30080
<html>
<head><title>404 Not Found</title></head>
<body>
<center><h1>404 Not Found</h1></center>
<hr><center>nginx</center>
</body>
</html>

controlplane:~$ k get ingressclass
NAME    CONTROLLER             PARAMETERS   AGE
nginx   k8s.io/ingress-nginx   <none>       22m


controlplane:~$ vi ingress1.yaml
controlplane:~$ cat ingress1.yaml 
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: world
  namespace: world
  annotations:
    # this annotation removes the need for a trailing slash when calling urls
    # but it is not necessary for solving this scenario
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  ingressClassName: nginx # k get ingressclass
  rules:
  - host: "world.universe.mine"
    http:
      paths:
      - path: /europe
        pathType: Prefix
        backend:
          service:
            name: europe
            port:
              number: 80
      - path: /asia
        pathType: Prefix
        backend:
          service:
            name: asia
            port:
              number: 80
controlplane:~$ 


controlplane:~$ k apply -f ingress1.yaml 
ingress.networking.k8s.io/world created
controlplane:~$ 

controlplane:~$ k get ingress -A
NAMESPACE   NAME    CLASS   HOSTS                 ADDRESS   PORTS   AGE
world       world   nginx   world.universe.mine             80      30s
controlplane:~$ 
controlplane:~$ 
controlplane:~$ 
controlplane:~$ k describe ingress -A
Name:             world
Labels:           <none>
Namespace:        world
Address:          172.30.1.2
Ingress Class:    nginx
Default backend:  <default>
Rules:
  Host                 Path  Backends
  ----                 ----  --------
  world.universe.mine  
                       /europe   europe:80 (192.168.0.8:80,192.168.0.6:80)
                       /asia     asia:80 (192.168.0.9:80,192.168.0.7:80)
Annotations:           nginx.ingress.kubernetes.io/rewrite-target: /
Events:
  Type    Reason  Age               From                      Message
  ----    ------  ----              ----                      -------
  Normal  Sync    5s (x2 over 45s)  nginx-ingress-controller  Scheduled for sync

```