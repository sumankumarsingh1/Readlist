# Readlist
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
```
$ k run pod1 --image=httpd:2.4.41-alpine 
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
