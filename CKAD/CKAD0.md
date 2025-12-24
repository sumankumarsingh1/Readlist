# Interactive Scenarios for Kubernetes Application Developers

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



## Pod with Resources
Create a Pod with Resource Requests and Limits

## ConfigMap Access in Pods
Create ConfigMaps and access these in Pods

## ReadinessProbe
Create a Deployment with a ReadinessProbe

## Build and run a Container
Build a container from scratch and run it

## Rollout Rolling
Perform a rolling rollout of an application

## Rollout Green-Blue
Perform a Green-Blue rollout of an application

## Rollout Canary
Perform a Canary rollout of an application

## Custom Resource Definitions
Work with CRDs that extend Kubernetes

## Helm
Use the Helm package manager

## Ingress Create
Create a new Ingress for existing Deployments

## NetworkPolicy Namespace Selector
Allow communication between two Namespaces

## Admission Controllers
Manage and see Admission Controllers in action

## Api Deprecations
Work with Api Versions, Groups and Deprecations
