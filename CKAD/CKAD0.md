# Interactive Scenarios for Kubernetes Application Developers

Reference: https://killercoda.com/killer-shell-ckad

## Playground
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

## Vim Setup
How to setup Vim for the CKAD exam

> The following settings will already be configured in your real exam environment in ~/.vimrc. But it can never hurt to be able to type these down

We look at important Vim settings if you like to work with YAML during the K8s exams.

### Settings
First create or open (if already exists) file <span style="background-color:#eeeeee;font-family:courier">.vimrc</span> :
```
vim ~/.vimrc
```
Now enter (in insert-mode activated with i) the following lines:
```
set expandtab
set tabstop=2
set shiftwidth=2
```
Save and close the file by pressing Esc followed by <span style="background-color:#eeeeee;font-family:courier">.:x</span> and <span style="background-color:#eeeeee;font-family:courier"> Enter</span>.

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

## SSH Basics
How to connect to and work on other hosts

> During the exam you'll be provided with a command you need to run before every question to connect to a dedicated host

Create a new empty file <span style="background-color:#eeeeee;font-family:courier;font-weight:bold">/root/node01</span> on host <span style="background-color:#eeeeee;font-family:courier;font-weight:bold">node01</span> 

## Kubectl Contexts
Work with different kubectl contexts

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
