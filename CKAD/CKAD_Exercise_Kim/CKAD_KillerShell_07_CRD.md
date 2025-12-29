# Interactive Scenarios for Kubernetes Application Developers (7 of 12)

Reference: <https://killercoda.com/killer-shell-ckad>

## [Custom Resource Definitions](https://killercoda.com/killer-shell-ckad/scenario/crd)

Work with CRDs that extend Kubernetes

Write the list of all installed CRDs into **`/root/crds`** .

Write the list of all DbBackup objects into **`/root/db-backups`** .

#### Tip 1

**`k get crd`**

#### Tip 2

**`k get db-backups`**

#### Tip 3

**`k get db-backups -A`**

#### Solution

Resources from CRDs can be namespaced, so here we have to look for objects in all Namespaces.

```bash
k get crd > /root/crds
k get db-backups -A > /root/db-backups
```

#### Output

```bash
controlplane:~$ k get crd > /root/crds
controlplane:~$ k get db-backups -A > /root/db-backups
controlplane:~$ k get crd             
NAME                                                  CREATED AT
bgpconfigurations.crd.projectcalico.org               2025-12-18T17:30:30Z
bgppeers.crd.projectcalico.org                        2025-12-18T17:30:30Z
blockaffinities.crd.projectcalico.org                 2025-12-18T17:30:30Z
caliconodestatuses.crd.projectcalico.org              2025-12-18T17:30:30Z
clusterinformations.crd.projectcalico.org             2025-12-18T17:30:30Z
crontabs.stable.killercoda.com                        2025-12-26T18:14:27Z
db-backups.stable.killercoda.com                      2025-12-26T18:14:27Z
felixconfigurations.crd.projectcalico.org             2025-12-18T17:30:30Z
globalnetworkpolicies.crd.projectcalico.org           2025-12-18T17:30:30Z
globalnetworksets.crd.projectcalico.org               2025-12-18T17:30:30Z
hostendpoints.crd.projectcalico.org                   2025-12-18T17:30:30Z
ipamblocks.crd.projectcalico.org                      2025-12-18T17:30:30Z
ipamconfigs.crd.projectcalico.org                     2025-12-18T17:30:30Z
ipamhandles.crd.projectcalico.org                     2025-12-18T17:30:30Z
ippools.crd.projectcalico.org                         2025-12-18T17:30:30Z
ipreservations.crd.projectcalico.org                  2025-12-18T17:30:30Z
kubecontrollersconfigurations.crd.projectcalico.org   2025-12-18T17:30:30Z
networkpolicies.crd.projectcalico.org                 2025-12-18T17:30:31Z
networksets.crd.projectcalico.org                     2025-12-18T17:30:31Z
controlplane:~$ k get db-backups -A                   
NAMESPACE     NAME                       AGE
default       postgres-sunny-sunshine    29s
default       postgres-walking-waveart   29s
kube-system   mysql-total-tolly          29s
controlplane:~$ 
```

### Install a new CRD and create an object

The team worked really hard for months on a new Shopping-Items CRD which is currently in beta.

Install it from **`/code/crd.yaml`** .

Then create a ShoppingItem object named **`bananas`** in Namespace **`default`** . The **`dueDate`** should be **`tomorrow`** and the description should be **`buy yellow ones`** .

#### Tip 1

You install CRD yaml just like any other resources.

#### Tip 2

You can use this template:

```yaml
apiVersion: "beta.killercoda.com/v1"
kind: # TODO fill out
metadata:
  name: bananas
spec:
  # TODO fill out
```

#### Solution

**`k -f /code/crd.yaml apply`**

Now you'll be able to see the new CRD:

**`k get crd | grep shopping`**

And we can create a new object of it:

```yaml
apiVersion: "beta.killercoda.com/v1"
kind: ShoppingItem
metadata:
  name: bananas
spec:
  description: buy yellow ones
  dueDate: tomorrow
```

#### Verify
```
k get shopping-item
k get shopping-item bananas -oyaml
```


Output

```bash
controlplane:~$ k get shopping-item                
NAME      AGE
bananas   3m23s
controlplane:~$ k get shopping-item bananas -oyaml
apiVersion: beta.killercoda.com/v1
kind: ShoppingItem
metadata:
  annotations:
    kubectl.kubernetes.io/last-applied-configuration: |
      {"apiVersion":"beta.killercoda.com/v1","kind":"ShoppingItem","metadata":{"annotations":{},"name":"bananas","namespace":"default"},"spec":{"description":"buy yellow ones","dueDate":"tomorrow"}}
  creationTimestamp: "2025-12-26T18:23:31Z"
  generation: 1
  name: bananas
  namespace: default
  resourceVersion: "5112"
  uid: 746499b6-8bf2-42be-9273-7c2ebbc58b5b
spec:
  description: buy yellow ones
  dueDate: tomorrow
controlplane:~$ 
```

### Evaluate the installed CRD

You spent hours figuring out this "amazing" new CRD ShoppingItems created by the team.

But in the end you realised that it's pretty much the most useless CRD ever encountered.

If you need to run a Kubernetes cluster for managing your shopping list then things might have gone too far.

Delete the CRD and all ShoppingItem objects again.

#### Solution

```bash
k delete crd shopping-items.beta.killercoda.com
```

#### Output

```bash
controlplane:~$   k delete crd shopping-items.beta.killercoda.com
customresourcedefinition.apiextensions.k8s.io "shopping-items.beta.killercoda.com" deleted
```
