# Interactive Scenarios for Kubernetes Application Developers (12 of 12)

Reference: <https://killercoda.com/killer-shell-ckad>



## [Api Deprecations](https://killercoda.com/killer-shell-ckad/scenario/api-deprecations)

Work with Api Versions, Groups and Deprecations

### Get Api Version

Create file **`/root/versions`** with three lines. Each line containing only one number from the installed K8s server version:

1. Major
1. Minor
1. Patch

#### Solution

We can use kubectl to show the installed K8s version:


**`k version`**

The K8s version is written as Major.Minor.Patch .

Write each number in one line into /root/versions , for example:

```text
1
31
0
```

#### Output

```bash
controlplane:~$ k version
Client Version: v1.34.3
Kustomize Version: v5.7.1
Server Version: v1.34.3
controlplane:~$ vim versions
controlplane:~$ cat versions 
1
34
3
controlplane:~$ 
```

### Identify Api Group

Write the Api Group of Deployments into **`/root/group`** .

#### Tip 2

**`k explain`**

#### Solution 2

We can use kubectl to list information about a resource type:

**`k explain deploy`**

This will show VERSION: apps/v1 .

The version is displayed as VERSION: {group}/{version}.

**`echo apps > /root/group`**

#### Output 2

```bash
controlplane:~$ k explain
error: You must specify the type of resource to explain. Use "kubectl api-resources" for a complete list of supported resources.
controlplane:~$ k explain deploy
GROUP:      apps
KIND:       Deployment
VERSION:    v1

DESCRIPTION:
    Deployment enables declarative updates for Pods and ReplicaSets.
    
FIELDS:
  apiVersion    <string>
    APIVersion defines the versioned schema of this representation of an object.
    Servers should convert recognized schemas to the latest internal value, and
    may reject unrecognized values. More info:
    https://git.k8s.io/community/contributors/devel/sig-architecture/api-conventions.md#resources

  kind  <string>
    Kind is a string value representing the REST resource this object
    represents. Servers may infer this from the endpoint the client submits
    requests to. Cannot be updated. In CamelCase. More info:
    https://git.k8s.io/community/contributors/devel/sig-architecture/api-conventions.md#types-kinds

  metadata      <ObjectMeta>
    Standard object's metadata. More info:
    https://git.k8s.io/community/contributors/devel/sig-architecture/api-conventions.md#metadata

  spec  <DeploymentSpec>
    Specification of the desired behavior of the Deployment.

  status        <DeploymentStatus>
    Most recently observed status of the Deployment.



controlplane:~$ echo apps > /root/group
controlplane:~$ cat group 
apps
controlplane:~$ 
```

### Convert Deprecated CronJob Resource

There is a CronJob file at **`/apps/cronjob.yaml`** which uses a deprecated Api version.

Update the file to use the non deprecated one.

#### Tip 3

When creating a resource which uses a deprecated version, kubectl will show a warning with the correct version. If the once deprecated version was already completely removed from the current K8s version, then an error will be shown.

We can also check the [K8s Docs](https://kubernetes.io/docs/reference/using-api/deprecation-guide).

#### Solution 3

Creating the resource will show a warning with the correct version or simply an error:

**`k -f /apps/cronjob.yaml create`**

Check the [CronJob Docs](https://kubernetes.io/docs/concepts/workloads/controllers/cron-jobs/) for the correct version.


The file should be changed to:

```yaml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: backup
spec:
schedule: "5 4 * * *"
jobTemplate:
spec:
  template:
    spec:
...
```

#### Output 3

```bash

controlplane:~$ k -f /apps/cronjob.yaml create
error: resource mapping not found for name: "backup" namespace: "" from "/apps/cronjob.yaml": no matches for kind "CronJob" in version "batch/v1beta1"
ensure CRDs are installed first
controlplane:~$ 



controlplane:~$ vim /apps/cronjob.yaml 
controlplane:~$ cat /apps/cronjob.yaml 
apiVersion: batch/v1   ## only this line was updated
kind: CronJob
metadata:
  name: backup
spec:
  schedule: "5 4 * * *"
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: backup
            image: bash
            command:
            - /bin/sh
            - -c
            - date
          restartPolicy: OnFailure
controlplane:~$ 

controlplane:~$ k -f /apps/cronjob.yaml create
cronjob.batch/backup created
controlplane:~$ 
```

### Convert Deprecated FlowSchema Resource

There is a FlowSchema file at **`/apps/flowschema.yaml`** which uses a deprecated Api version.

Update the file to use the non deprecated one.

#### Tip 4a

When creating a resource which uses a deprecated version, kubectl will show a warning with the correct version. If the once deprecated version was already completely removed from the current K8s version, then an error will be shown.

We can also check the [K8s Docs](https://kubernetes.io/docs/reference/using-api/deprecation-guide).

#### Tip 4b

**`k -f /apps/flowschema.yaml apply`**

#### Solution 4

The file should be changed to the correct apiVersion (check):

```yaml
apiVersion: flowcontrol.apiserver.k8s.io/v1
kind: FlowSchema
metadata:
  name: health-for-strangers
spec:
  matchingPrecedence: 1000
  priorityLevelConfiguration:
    name: exempt
...
```

#### Output 4

```bash
controlplane:~$ cat /apps/flowschema.yaml 
apiVersion: flowcontrol.apiserver.k8s.io/v1beta2
kind: FlowSchema
metadata:
  name: health-for-strangers
spec:
  matchingPrecedence: 1000
  priorityLevelConfiguration:
    name: exempt
  rules:
    - nonResourceRules:
      - nonResourceURLs:
          - "/healthz"
          - "/livez"
          - "/readyz"
        verbs:
          - "*"
      subjects:
        - kind: Group
          group:
            name: "system:unauthenticated"

controlplane:~$ vim /apps/flowschema.yaml 
# updated 
# apiVersion: flowcontrol.apiserver.k8s.io/v1beta2
# to 
# apiVersion: flowcontrol.apiserver.k8s.io/v1beta2

controlplane:~$ k apply -f /apps/flowschema.yaml 
flowschema.flowcontrol.apiserver.k8s.io/health-for-strangers created
controlplane:~$ 
```
