# [CKAD Exercises](https://killercoda.com/chadmcrowell/course/ckad)

> By Chad M. Crowell

## [Playground](https://killercoda.com/chadmcrowell/course/ckad/playground)

Welcome !
In this scenario, you'll be able to play freely with a two node Kubernetes cluster

ENJOY!

This playground will always have the same version as currently in the Linux Foundation Exam

```bash
controlplane:~$ k version
Client Version: v1.34.3
Kustomize Version: v5.7.1
Server Version: v1.34.3
controlplane:~$ 
```

```bash
controlplane:~$ k cluster-info
Kubernetes control plane is running at https://172.30.1.2:6443
CoreDNS is running at https://172.30.1.2:6443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.
controlplane:~$ 
```

```bash
controlplane:~$ k get cs
Warning: v1 ComponentStatus is deprecated in v1.19+
NAME                 STATUS    MESSAGE   ERROR
controller-manager   Healthy   ok        
scheduler            Healthy   ok        
etcd-0               Healthy   ok
```

```bash
controlplane:~$ k get nodes -o wide
NAME           STATUS   ROLES           AGE   VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION     CONTAINER-RUNTIME
controlplane   Ready    control-plane   9d    v1.34.3   172.30.1.2    <none>        Ubuntu 24.04.3 LTS   6.8.0-90-generic   containerd://1.7.28
node01         Ready    <none>          9d    v1.34.3   172.30.2.2    <none>        Ubuntu 24.04.3 LTS   6.8.0-90-generic   containerd://1.7.28
controlplane:~$ 
```

