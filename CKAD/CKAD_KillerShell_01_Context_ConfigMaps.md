# Interactive Scenarios for Kubernetes Application Developers (1 of 12)

Reference: https://killercoda.com/killer-shell-ckad

## [Playground](https://killercoda.com/killer-shell-ckad/scenario/playground)
Environment with current version of the CKAD exam

This playground will always have the same version as currently in the Linux Foundation Exam.

```
controlplane:~$ k get nodes
NAME           STATUS   ROLES           AGE     VERSION
controlplane   Ready    control-plane   5d      v1.34.3
node01         Ready    <none>          4d23h   v1.34.3
controlplane:~$ k version
Client Version: v1.34.3
Kustomize Version: v5.7.1
Server Version: v1.34.3
controlplane:~$ 

controlplane:~$ k version -o json
{
  "clientVersion": {
    "major": "1",
    "minor": "34",
    "gitVersion": "v1.34.3",
    "gitCommit": "df11db1c0f08fab3c0baee1e5ce6efbf816af7f1",
    "gitTreeState": "clean",
    "buildDate": "2025-12-09T15:06:39Z",
    "goVersion": "go1.24.11",
    "compiler": "gc",
    "platform": "linux/amd64"
  },
  "kustomizeVersion": "v5.7.1",
  "serverVersion": {
    "major": "1",
    "minor": "34",
    "emulationMajor": "1",
    "emulationMinor": "34",
    "minCompatibilityMajor": "1",
    "minCompatibilityMinor": "33",
    "gitVersion": "v1.34.3",
    "gitCommit": "df11db1c0f08fab3c0baee1e5ce6efbf816af7f1",
    "gitTreeState": "clean",
    "buildDate": "2025-12-09T14:59:13Z",
    "goVersion": "go1.24.11",
    "compiler": "gc",
    "platform": "linux/amd64"
  }
}
controlplane:~$ 
controlplane:~$ k version -o yaml
clientVersion:
  buildDate: "2025-12-09T15:06:39Z"
  compiler: gc
  gitCommit: df11db1c0f08fab3c0baee1e5ce6efbf816af7f1
  gitTreeState: clean
  gitVersion: v1.34.3
  goVersion: go1.24.11
  major: "1"
  minor: "34"
  platform: linux/amd64
kustomizeVersion: v5.7.1
serverVersion:
  buildDate: "2025-12-09T14:59:13Z"
  compiler: gc
  emulationMajor: "1"
  emulationMinor: "34"
  gitCommit: df11db1c0f08fab3c0baee1e5ce6efbf816af7f1
  gitTreeState: clean
  gitVersion: v1.34.3
  goVersion: go1.24.11
  major: "1"
  minCompatibilityMajor: "1"
  minCompatibilityMinor: "33"
  minor: "34"
  platform: linux/amd64

controlplane:~$ 
```

## [Vim Setup](https://killercoda.com/killer-shell-ckad/scenario/vim-setup)
How to setup Vim for the CKAD exam

> The following settings will already be configured in your real exam environment in ~/.vimrc. But it can never hurt to be able to type these down

We look at important Vim settings if you like to work with YAML during the K8s exams.

### Settings
First create or open (if already exists) file **`.vimrc`** :
```
vim ~/.vimrc
```
Now enter (in insert-mode activated with i) the following lines:
```
set expandtab
set tabstop=2
set shiftwidth=2
```
Save and close the file by pressing **`Esc`** followed by **`:x`** and **`Enter`**.

## Explanation
Whenever you open Vim now as the current user, these settings will be used.

If you ssh onto a different server, these settings will not be transferred.

Settings explained:

```
expandtab: use spaces for tab
tabstop: amount of spaces used for tab
shiftwidth: amount of spaces used during indentation
```
<hr/>

## [SSH Basics](https://killercoda.com/killer-shell-ckad/scenario/ssh-basics)

### How to connect to and work on other hosts

> During the exam you'll be provided with a command you need to run before every question to connect to a dedicated host

Create a new empty file **`/root/node01`**  on host **`node01`** 

#### Tip:

> ssh node01

#### Solution

> ssh node01
    touch /root/node01

#### Output
```
controlplane:~$ ssh node01
Last login: Mon Feb 10 22:06:42 2025 from 10.244.0.131
node01:~$ touch /root/node01
node01:~$ ls -al  /root/node01
-rw-r--r-- 1 root root 0 Dec 24 03:38 /root/node01
node01:~$ 
```

### Create file on the original host

Now get back to the original host and create new empty file **`/root/controlplane`** there.

#### Solution
> exit
> touch /root/controlplane

#### Output
```
node01:~$ exit
logout
Connection to node01 closed.
controlplane:~$ touch /root/controlplane
controlplane:~$ ls -al /root/controlplane 
-rw-r--r-- 1 root root 0 Dec 24 03:43 /root/controlplane
controlplane:~$ 
```


## [Kubectl Contexts](https://killercoda.com/killer-shell-ckad/scenario/kubectl-contexts)

Work with different kubectl contexts

### View all and current context

A kubectl context contains connection information to a Kubernetes cluster. Different kubectl contexts can connect to different Kubernetes clusters, or to the same cluster but using different users or different default namespaces.

List all available kubectl contexts and write the output to /root/contexts .


#### Tip

> k config -h

#### Solution

> k config get-contexts
> k config get-contexts > /root/contexts

We see three contexts all pointing to the same cluster.

* Context **`kubernetes-admin@kubernetes`** will connect to the **`default`** Namespace.
 
*  Context **`purple`** will connect to the **`purple`** Namespace.
 
*  Context **`yellow`** will connect to the **`yellow`** Namespace.

#### Output
```
controlplane:~$ k config -h
Modify kubeconfig files using subcommands like "kubectl config set current-context my-context".

 The loading order follows these rules:

  1.  If the --kubeconfig flag is set, then only that file is loaded. The flag may only be set once and no merging takes
place.
  2.  If $KUBECONFIG environment variable is set, then it is used as a list of paths (normal path delimiting rules for
your system). These paths are merged. When a value is modified, it is modified in the file that defines the stanza. When
a value is created, it is created in the first file that exists. If no files in the chain exist, then it creates the
last file in the list.
  3.  Otherwise, ${HOME}/.kube/config is used and no merging takes place.

Available Commands:
  current-context   Display the current-context
  delete-cluster    Delete the specified cluster from the kubeconfig
  delete-context    Delete the specified context from the kubeconfig
  delete-user       Delete the specified user from the kubeconfig
  get-clusters      Display clusters defined in the kubeconfig
  get-contexts      Describe one or many contexts
  get-users         Display users defined in the kubeconfig
  rename-context    Rename a context from the kubeconfig file
  set               Set an individual value in a kubeconfig file
  set-cluster       Set a cluster entry in kubeconfig
  set-context       Set a context entry in kubeconfig
  set-credentials   Set a user entry in kubeconfig
  unset             Unset an individual value in a kubeconfig file
  use-context       Set the current-context in a kubeconfig file
  view              Display merged kubeconfig settings or a specified kubeconfig file

Usage:
  kubectl config SUBCOMMAND [options]

Use "kubectl config <command> --help" for more information about a given command.
Use "kubectl options" for a list of global command-line options (applies to all commands).

controlplane:~$ k config get-contexts
CURRENT   NAME                          CLUSTER      AUTHINFO           NAMESPACE
*         kubernetes-admin@kubernetes   kubernetes   kubernetes-admin   
          purple                        kubernetes   kubernetes-admin   purple
          yellow                        kubernetes   kubernetes-admin   yellow


```
### Switch context purple

Switch to context **`purple`** and list all Pods.

#### Solution
> k config use-context purple
> k get pod

#### Output
```
controlplane:~$ k config use-context purple
Switched to context "purple".
controlplane:~$ k config get-contexts
CURRENT   NAME                          CLUSTER      AUTHINFO           NAMESPACE
          kubernetes-admin@kubernetes   kubernetes   kubernetes-admin   
*         purple                        kubernetes   kubernetes-admin   purple
          yellow                        kubernetes   kubernetes-admin   yellow
controlplane:~$ k get pod 
NAME         READY   STATUS    RESTARTS   AGE
purple-pod   1/1     Running   0          29m
```

### Switch context yellow

Switch to context **`yellow`** and list all Pods.

#### Solution
> k config use-context yellow
> k get pod

#### Output
```
controlplane:~$ k config use-context yellow
Switched to context "yellow".
controlplane:~$ k config get-contexts
CURRENT   NAME                          CLUSTER      AUTHINFO           NAMESPACE
          kubernetes-admin@kubernetes   kubernetes   kubernetes-admin   
          purple                        kubernetes   kubernetes-admin   purple
*         yellow                        kubernetes   kubernetes-admin   yellow
controlplane:~$ k get pods
NAME         READY   STATUS    RESTARTS   AGE
yellow-pod   1/1     Running   0          33m
```



## [Pod with Resources](https://killercoda.com/killer-shell-ckad/scenario/pod-with-resources)

### Create a Pod with Resource Requests and Limits

Create a new Namespace **`limit`** .

In that Namespace create a Pod named **`resource-checker`** of image **`httpd:alpine`** .

The container should be named **`my-container`** .

It should request **`30m`** CPU and be limited to **`300m`** CPU.

It should request **`30Mi`** memory and be limited to **`30Mi`** memory.

#### Solution

First we create the Namespace yaml:

> k create ns limit

Then we generate a Pod yaml:

> k -n limit run resource-checker --image=httpd:alpine -oyaml --dry-run=client > pod.yaml

Next we adjust it to the requirements:

```
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: resource-checker
  name: resource-checker
  namespace: limit
spec:
  containers:
  - image: httpd:alpine
    name: my-container
    resources:
      requests:
        memory: "30Mi"
        cpu: "30m"
      limits:
        memory: "30Mi"
        cpu: "300m"
  dnsPolicy: ClusterFirst
  restartPolicy: Always
```

#### Output

```
controlplane:~$ k create ns limit
namespace/limit created
controlplane:~$ k -n limit run resource-checker --image=httpd:alpine -oyaml --dry-run=client > pod.yaml
controlplane:~$ cat pod.yaml 
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: resource-checker
  name: resource-checker
  namespace: limit
spec:
  containers:
  - image: httpd:alpine
    name: resource-checker
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
controlplane:~$ nano pod.yaml
```
Edit the file **`pod.yaml`** with the below changes
```
spec:
  containers:
  - image: httpd:alpine
    name: my-container
    resources:
      requests:
        memory: "30Mi"
        cpu: "30m"
      limits:
        memory: "30Mi"
        cpu: "300m"
```
The file after the changes 
```

controlplane:~$ 
controlplane:~$ cat pod.yaml 
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: resource-checker
  name: resource-checker
  namespace: limit
spec:
  containers:
  - image: httpd:alpine
    name: my-container
    resources:
      requests:
        memory: "30Mi"
        cpu: "30m"
      limits:
        memory: "30Mi"
        cpu: "300m"
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
controlplane:~$ 
```

Now use the **`pod.yaml`** to create the pod in namespace **`limit`**

```
controlplane:~$ k create -f pod.yaml 
pod/resource-checker created
controlplane:~$ k get pod -n limit     
NAME               READY   STATUS    RESTARTS   AGE
resource-checker   1/1     Running   0          65s
controlplane:~$ 
```


## [ConfigMap Access in Pods](https://killercoda.com/killer-shell-ckad/scenario/configmap-pod-access)

### Create ConfigMaps and access these in Pods

#### Create ConfigMaps

1. Create a ConfigMap named **`trauerweide`** with content **`tree=trauerweide`**
2. Create the ConfigMap stored in existing file **`/root/cm.yaml`**

#### Tip
```
# create a new ConfigMap
kubectl create cm trauerweide -h

# create a ConfigMap from file
kubectl create -f ...
```

#### Solution

```
kubectl create cm trauerweide --from-literal tree=trauerweide

kubectl -f /root/cm.yaml create
```

#### Output1

```
controlplane:~$ kubectl create cm trauerweide --from-literal tree=trauerweide
configmap/trauerweide created
controlplane:~$ k get cm
NAME               DATA   AGE
kube-root-ca.crt   1      5d20h
trauerweide        1      35s
controlplane:~$ cat /root/cm.yaml 
apiVersion: v1
data:
  tree: birke
  level: "3"
  department: park
kind: ConfigMap
metadata:
  name: birke
controlplane:~$ k describe cm trauerweide
Name:         trauerweide
Namespace:    default
Labels:       <none>
Annotations:  <none>

Data
====
tree:
----
trauerweide


BinaryData
====

Events:  <none>
controlplane:~$ 
```

#### Output 2

```bash
controlplane:~$ k -f /root/cm.yaml create
configmap/birke created
controlplane:~$ k get cm
NAME               DATA   AGE
birke              3      79s
kube-root-ca.crt   1      5d20h
trauerweide        1      4m41s
controlplane:~$ 
controlplane:~$ k describe cm  birke
Name:         birke
Namespace:    default
Labels:       <none>
Annotations:  <none>

Data
====
department:
----
park

level:
----
3

tree:
----
birke


BinaryData
====

Events:  <none>
```

### Access ConfigMaps in Pod

1. Create a Pod named **`pod1`** of image **`nginx:alpine`**
2. Make key **`tree`** of ConfigMap **`trauerweide`** available as environment variable **`TREE1`**
3. Mount all keys of ConfigMap **`birke`** as volume. The files should be available under **`/etc/birke/*`**
Test env+volume access in the running Pod

#### Solution
```
apiVersion: v1
kind: Pod
metadata:
  name: pod1
spec:
  volumes:
  - name: birke
    configMap:
      name: birke
  containers:
  - image: nginx:alpine
    name: pod1
    volumeMounts:
      - name: birke
        mountPath: /etc/birke
    env:
      - name: TREE1
        valueFrom:
          configMapKeyRef:
            name: trauerweide
            key: tree
```

#### Verify
```
kubectl exec pod1 -- env | grep "TREE1=trauerweide"
kubectl exec pod1 -- cat /etc/birke/tree
kubectl exec pod1 -- cat /etc/birke/level
kubectl exec pod1 -- cat /etc/birke/department
```

#### Output
```
controlplane:~$ k run pod1 --image=nginx:alpine -o yaml --dry-run=client > cmpod1.yaml
controlplane:~$ cat cmpod1.yaml 
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: pod1
  name: pod1
spec:
  containers:
  - image: nginx:alpine
    name: pod1
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
controlplane:~$ 
```
Edit the file to make the required changes as below:
```
controlplane:~$ cat cmpod1.yaml 
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: pod1
  name: pod1
spec:
  volumes:
  - name: birke
    configMap:
      name: birke
  containers:
  - image: nginx:alpine
    name: pod1
    volumeMounts:
    - name: birke
      mountPath: /etc/birke
    env:
    - name: TREE1
      valueFrom:
        configMapKeyRef:
          name: trauerweide
          key: tree
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
controlplane:~$ 
```
Create the pod from the file **`cmpod1.yaml`**

```
controlplane:~$ k create -f cmpod1.yaml 
pod/pod1 created
```
Now verify by using the following commands:
```
controlplane:~$ kubectl exec pod1 -- env | grep "TREE1=trauerweide"
TREE1=trauerweide
controlplane:~$ kubectl exec pod1 -- cat /etc/birke/tree
birkecontrolplane:~$
controlplane:~$ kubectl exec pod1 -- cat /etc/birke/level
3controlplane:~$ 
controlplane:~$ kubectl exec pod1 -- cat /etc/birke/departmentt
parkcontrolplane:~$ 
```

