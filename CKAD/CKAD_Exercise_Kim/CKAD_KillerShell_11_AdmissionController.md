# Interactive Scenarios for Kubernetes Application Developers (11 of 12)

Reference: <https://killercoda.com/killer-shell-ckad>

## [Admission Controllers](https://killercoda.com/killer-shell-ckad/scenario/admission-controllers)

Manage and see Admission Controllers in action

## Api Deprecations

Work with Api Versions, Groups and Deprecations


### List all Admission Controller Plugins

Write all Admission Controller Plugins, which are enabled in the kube-apiserver manifest, into **`/root/admission-plugins`** .


#### Tip

We can check the kube-apiserver manifest:

**`cat /etc/kubernetes/manifests/kube-apiserver.yaml`**

Or the running process:

**`ps aux | grep kube-apiserver`**

#### Solution

We filter for the argument:

```bash
cat /etc/kubernetes/manifests/kube-apiserver.yaml | grep admission-plugins
```
And then create a new file in the requested location **`/root/admission-plugins`** with content:

```text
NodeRestriction
LimitRanger
Priority
```

#### Output

```bash
controlplane:~$ ls -al /etc/kubernetes/manifests/
total 24
drwxrwxr-x 2 root root 4096 Dec 27 04:03 .
drwxrwxr-x 4 root root 4096 Dec 18 17:30 ..
-rw------- 1 root root 2591 Dec 18 17:31 etcd.yaml
-rw------- 1 root root 3964 Dec 27 04:03 kube-apiserver.yaml
-rw------- 1 root root 3458 Dec 18 17:31 kube-controller-manager.yaml
-rw------- 1 root root 1725 Dec 18 17:31 kube-scheduler.yaml
controlplane:~$ cat /etc/kubernetes/manifests/kube-apiserver.yaml | grep admission-plugins
    - --enable-admission-plugins=NodeRestriction,LimitRanger,Priority
controlplane:~$ vi /root/admission-plugins
controlplane:~$ cat admission-plugins 
NodeRestriction
LimitRanger
Priority
controlplane:~$ 
```

### Enable the Admission Controller Plugin 

**`MutatingAdmissionWebhook`**.

> It can take a few minutes for the apiserver container to restart after changing the manifest. You can watch using **`watch crictl ps`** .

#### Tip 1

The apiserver manifest is under **`/etc/kubernetes/manifests/kube-apiserver.yaml`** .

Always make a backup! But into a different directory than **`/etc/kubernetes/manifests`** .

#### Tip 2

The argument we're looking for is **`--enable-admission-plugins`** .

#### Solution

We need to edit the apiserver manifest:

```bash
# ALWAYS make a backup
cp /etc/kubernetes/manifests/kube-apiserver.yaml ~/kube-apiserver.yaml

vim /etc/kubernetes/manifests/kube-apiserver.yaml
```

And add the new plugin to the list:

```text
--enable-admission-plugins=NodeRestriction,LimitRanger,Priority,MutatingAdmissionWebhook
```
Now we wait till the apiserver restarted using watch crictl ps .

For speed, we could also move the kube-apiserver.yaml manifest out of the directory, wait till the process ended and then move it back it.

#### Output

```bash
controlplane:~$ cp /etc/kubernetes/manifests/kube-apiserver.yaml ~/kube-apiserver.yaml
controlplane:~$ ll kub*
-rw------- 1 root root 3964 Dec 27 04:15 kube-apiserver.yaml
controlplane:~$ 

```


Lines edited in file kube-apiserver.yaml
```
...
     - --tls-private-key-file=/etc/kubernetes/pki/apiserver.key
     # below line added
    - --enable-admission-plugins=NodeRestriction,LimitRanger,Priority,MutatingAdmissionWebhook
    image: registry.k8s.io/kube-apiserver:v1.34.3
    imagePullPolicy: IfNotPresent   
...
```

```bash
watch crictl ps 


Every 2.0s: crictl ps                                                                                      controlplane: Sat Dec 27 04:27:11 2025

CONTAINER           IMAGE               CREATED              STATE               NAME                      ATTEMPT             POD ID
  POD                                       NAMESPACE
02c92ca0e43df       aa27095f56193       54 seconds ago       Running             kube-apiserver            0                   d5f0c164a54c4
  kube-apiserver-controlplane               kube-system
29e67eea797de       aec12dadf56dd       About a minute ago   Running             kube-scheduler            3                   19c82446fdddd
  kube-scheduler-controlplane               kube-system
2d0e5f4587954       5826b25d990d7       About a minute ago   Running             kube-controller-manager   3                   4fc029971927a
  kube-controller-manager-controlplane      kube-system
67fdb1fe19a5a       d4a0587dd517c       32 minutes ago       Running             local-path-provisioner    1                   cc0a39992c274
  local-path-provisioner-76f88ddd78-hq9xm   local-path-storage
fb0a70f4a899f       f9c3c1813269c       32 minutes ago       Running             calico-kube-controllers   1                   f6dfb729fcd26
  calico-kube-controllers-7bb4b4d4d-6w29j   kube-system
d9cea0bafa1ea       52546a367cc9e       32 minutes ago       Running             coredns                   1                   7174827091f5c
  coredns-76bb9b6fb5-cgmmb                  kube-system
1b6787ba40cf3       52546a367cc9e       32 minutes ago       Running             coredns                   1                   c346a96fee061
  coredns-76bb9b6fb5-g8hbg                  kube-system
693e64b3111d9       e6ea68648f0cd       32 minutes ago       Running             kube-flannel              1                   96256a7c62808
  canal-ck4gg                               kube-system
6c6bc68c70912       75392e3500e36       32 minutes ago       Running             calico-node               1                   96256a7c62808
  canal-ck4gg                               kube-system
b7dc1623a1fab       36eef8e07bdd6       33 minutes ago       Running             kube-proxy                1                   1d6e7d4570302
  kube-proxy-zdx2k                          kube-system
fc5362de86dc2       a3e246e9556e9       33 minutes ago       Running             etcd                      1                   7c37b88713e05
  etcd-controlplane                         kube-system


```

### Disable Admission Controller Plugin

Delete Namespace **`space1`** .

Delete Namespace **`default`** (throws error)

Disable the Admission Controller Plugin **`NamespaceLifecycle`** . It's not recommended to do this at all, we just do this for showing the effect.

> It can take a few minutes for the apiserver container to restart after changing the manifest. You can watch using **`watch crictl ps`**.

Now delete Namespace default .

#### Tip

The apiserver manifest also has argument **`--disable-admission-plugins`**.


#### Solution

First we delete Namespace space1 :

**`k delete ns space1`**

And same for default . But it won't work because an admission plugin prevents the deletion!


**`k delete ns default # error`**

We need to edit the apiserver manifest:

```bash
# ALWAYS make a backup
cp /etc/kubernetes/manifests/kube-apiserver.yaml ~/kube-apiserver.yaml

vim /etc/kubernetes/manifests/kube-apiserver.yaml
```

And add a new argument:

**`--disable-admission-plugins=NamespaceLifecycle`**

Now we wait till the apiserver restarted using **`watch crictl ps`** . Then we can delete the default one:

**`k delete ns default`**

If we wait a bit we should see that it gets automatically created again:

**`k get ns`**

#### Output

```bash
controlplane:~$ k delete ns space1
namespace "space1" deleted
controlplane:~$ k get ns 
NAME                 STATUS   AGE
default              Active   8d
kube-node-lease      Active   8d
kube-public          Active   8d
kube-system          Active   8d
local-path-storage   Active   8d
space2               Active   35m
controlplane:~$ 

controlplane:~$ k delete ns default
Error from server (Forbidden): namespaces "default" is forbidden: this namespace may not be deleted
controlplane:~$ 

cp /etc/kubernetes/manifests/kube-apiserver.yaml ~/kube-apiserver1.yaml

vim /etc/kubernetes/manifests/kube-apiserver.yaml
```

lines edited

```yaml
    - --enable-admission-plugins=NodeRestriction,LimitRanger,Priority,MutatingAdmissionWebhook
    # below line added
    - --disable-admission-plugins=NamespaceLifecycle
    image: registry.k8s.io/kube-apiserver:v1.34.3
    imagePullPolicy: IfNotPresent
```

Check for api server to restart

```bash
watch crictl ps

Every 2.0s: crictl ps                                                                                      controlplane: Sat Dec 27 04:43:42 2025

CONTAINER           IMAGE               CREATED             STATE               NAME                      ATTEMPT             POD ID
 POD                                       NAMESPACE
70d635949b818       aa27095f56193       20 seconds ago      Running             kube-apiserver            0                   ce77488eb5236
 kube-apiserver-controlplane               kube-system
9ec52494c9d92       aec12dadf56dd       48 seconds ago      Running             kube-scheduler            4                   19c82446fdddd
 kube-scheduler-controlplane               kube-system
d63824245e139       5826b25d990d7       48 seconds ago      Running             kube-controller-manager   4                   4fc029971927a
 kube-controller-manager-controlplane      kube-system
67fdb1fe19a5a       d4a0587dd517c       48 minutes ago      Running             local-path-provisioner    1                   cc0a39992c274
 local-path-provisioner-76f88ddd78-hq9xm   local-path-storage
fb0a70f4a899f       f9c3c1813269c       48 minutes ago      Running             calico-kube-controllers   1                   f6dfb729fcd26
 calico-kube-controllers-7bb4b4d4d-6w29j   kube-system
d9cea0bafa1ea       52546a367cc9e       48 minutes ago      Running             coredns                   1                   7174827091f5c
 coredns-76bb9b6fb5-cgmmb                  kube-system
1b6787ba40cf3       52546a367cc9e       48 minutes ago      Running             coredns                   1                   c346a96fee061
 coredns-76bb9b6fb5-g8hbg                  kube-system
693e64b3111d9       e6ea68648f0cd       49 minutes ago      Running             kube-flannel              1                   96256a7c62808
 canal-ck4gg                               kube-system
6c6bc68c70912       75392e3500e36       49 minutes ago      Running             calico-node               1                   96256a7c62808
 canal-ck4gg                               kube-system
b7dc1623a1fab       36eef8e07bdd6       49 minutes ago      Running             kube-proxy                1                   1d6e7d4570302
 kube-proxy-zdx2k                          kube-system
fc5362de86dc2       a3e246e9556e9       49 minutes ago      Running             etcd                      1                   7c37b88713e05
 etcd-controlplane                         kube-system

```

Again try to delete default namespace

```bash
controlplane:~$ k delete ns default
namespace "default" deleted
controlplane:~$ k get ns
NAME                 STATUS   AGE
kube-node-lease      Active   8d
kube-public          Active   8d
kube-system          Active   8d
local-path-storage   Active   8d
space2               Active   41m
controlplane:~$ 
```

Waited for few seconds and then again checked namespaces

```bash

controlplane:~$ k get ns
NAME                 STATUS   AGE
default              Active   72s
kube-node-lease      Active   8d
kube-public          Active   8d
kube-system          Active   8d
local-path-storage   Active   8d
space2               Active   43m
controlplane:~$ 

```
