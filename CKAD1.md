# CKAD Notes
<style>
.cmd-block {
  background: #f5f5f5;
  padding: 10px 14px;
  font-family: "Courier New", monospace;
  border-radius: 6px;
  position: relative;
  margin: 8px 0;
  display: inline;
  align-items: center;
  gap: 10px;
}
.cmd-block button {
  cursor: pointer;
  background: #ddd;
  border: none;
  padding: 4px 8px;
  border-radius: 4px;
  font-size: 12px;
}
.cmd-block button:hover {
  background: #cfcfcf;
}
</style>


# CKAD Notes

## Killer Coda Notes


### Playground

This playground will always have the same version as currently in the Linux Foundation Exam.

### VIM Setup

How to setup Vim for the K8s exams

Persist Vim settings in <span class="copy-container">.vimrc</span>

We look at important Vim settings if you like to work with YAML during the K8s exams.

#### Settings

First create or open (if already exists) file <span class="cmd-block">.vimrc</span> :

<span class="cmd-block">vim ~/.vimrc <button onclick="navigator.clipboard.writeText('vim ~/.vimrc')">copy</button></span>

Now enter (in insert-mode activated with <span class=cmd-block>i</span>) the following lines:

```
set expandtab
set tabstop=2
set shiftwidth=2
```

Save and close the file by pressing Esc followed by :x and Enter.

#### Explanation
Whenever you open Vim now as the current user, these settings will be used.

If you ssh onto a different server, these settings will not be transferred.

Settings explained:
```
expandtab: use spaces for tab
tabstop: amount of spaces used for tab
shiftwidth: amount of spaces used during indentation
```
https://www.youtube.com/watch?v=LFMp-DgJtoo&list=PLpbwBK0ptssyIgAoHR-611wt3O9wobS8T

#### Instructions:

Each question needs to be solved on a specific instance other than your main **candidate@terminal** - you will need to connect to the correct instance via SSH and the proper command is provided before every question. To connect to another instance you always need to return first to your main terminal by running the **exit** command. Then you can connect again via SSH to a different one.

In this real exam question you will question will be solved on a different instance, whereas in the simulator multiple questions will be solved on the same instance.


#### Question 1: 
Solve this question on instance <span class=cmd-block>ssh cka5601</span>
The DevOps team would like to get this list of all Namespaces in the cluster. Get the list and save to <span class=cmd-block>/opt/course/1/namespaces</span> on <span class=cmd-block>cka5601</span>

**Note:** In the exam environment you need to use <span class=cmd-block>CTRL + SHIFT + C</span> to copy and  use  <span class=cmd-block>CTRL + SHIFT + V</span> to paste.


##### Solution:
```
$ ssh cka5601
$ k get ns
NAME STATUS AGE
default        Active   Age
earth          Active   52d
jupyter        Active   52d
saturn         Active   52d
neptune        Active   52d
k-node-lease   Active   52d
kube-public    Active   52d
kube-system    Active   52d
mars           Active   52d
shell-intern   Active   52d

$ k get ns > /opt/course/1/namespaces

$ cat 
```



#### Question 2: 

Create a single Pod of image <span class=cmd-block>httpd: 2.4.41-alpine</span> in the namespace <span class=cmd-block>default</span> the Pot should be named <span class=cmd-block>pod1</span> and the container should be named <span class=cmd-block>pod1-container</span>.

Your manager would like to run command manually on occasion to output the status of the exact Pod. Please write a command that does this into <span class=cmd-block>/opt/course/2/pod1-status-command.sh</span> on <span class=cmd-block>cka5601</span>  machine. The command should use <span class=cmd-block>kubectl</span>

##### Solution:
```bash
$ k run pod1 --image=httpd:2.4.41-alpine --dry-run=client -o yaml > 2.yaml
$ cat 2.yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: pod1
  name: pod1
spec:
  containers:
  - image: httpd:2.4.41
    name: pod1
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
$ k get pods  
No resources found in default namespace.

$ k create -f 2.yaml
pod/pod1 created
controlplane:~$ k get pods
NAME   READY   STATUS              RESTARTS   AGE
pod1   0/1     ContainerCreating   0          5s
controlplane:~$ kubectl describe pod pod1
.... (output shows in another block)

controlplane:~$ echo "kubectl describe pod pod1 | grep -i status:" > 2a.sh
controlplane:~$ chmod +x 2a.sh
controlplane:~$ cat 2a.sh
kubectl describe pod pod1 | grep -i status:
controlplane:~$ ./2a.sh
Status:           Running
controlplane:~$ 
```

Describe Pod Pod1:
```shell
controlplane:~$ k describe pod pod1
Name:             pod1
Namespace:        default
Priority:         0
Service Account:  default
Node:             node01/172.30.2.2
Start Time:       Sun, 07 Dec 2025 21:57:17 +0000
Labels:           run=pod1
Annotations:      cni.projectcalico.org/containerID: 5729f566cbbb502d9e5105413f56eaa7c355dbcb5d347da414df0ea9451c163c
                  cni.projectcalico.org/podIP: 192.168.1.4/32
                  cni.projectcalico.org/podIPs: 192.168.1.4/32
Status:           Running
IP:               192.168.1.4
IPs:
  IP:  192.168.1.4
Containers:
  pod1-container:
    Container ID:   containerd://23603bbcb395638fc889c1391cc30530888cf301ee5b4517df750df521846901
    Image:          httpd:2.4.41
    Image ID:       docker.io/library/httpd@sha256:946c54069130dbf136903fe658fe7d113bd8db8004de31282e20b262a3e106fb
    Port:           <none>
    Host Port:      <none>
    State:          Running
      Started:      Sun, 07 Dec 2025 21:57:25 +0000
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-gkdnh (ro)
Conditions:
  Type                        Status
  PodReadyToStartContainers   True 
  Initialized                 True 
  Ready                       True 
  ContainersReady             True 
  PodScheduled                True 
Volumes:
  kube-api-access-gkdnh:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    Optional:                false
    DownwardAPI:             true
QoS Class:                   BestEffort
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type    Reason     Age    From               Message
  ----    ------     ----   ----               -------
  Normal  Scheduled  9m16s  default-scheduler  Successfully assigned default/pod1 to node01
  Normal  Pulling    9m17s  kubelet            Pulling image "httpd:2.4.41"
  Normal  Pulled     9m10s  kubelet            Successfully pulled image "httpd:2.4.41" in 7.091s (7.091s including waiting). Image size: 61857142 bytes.
  Normal  Created    9m9s   kubelet            Created container: pod1-container
  Normal  Started    9m9s   kubelet            Started container pod1-container
```





#### Question 3: 
##### Solution:
#### Question 4: 
##### Solution:
#### Question 5: 
##### Solution:
#### Question 6: 
##### Solution:
#### Question 7: 
##### Solution:
#### Question 8: 
##### Solution:
#### Question 9: 
##### Solution:
