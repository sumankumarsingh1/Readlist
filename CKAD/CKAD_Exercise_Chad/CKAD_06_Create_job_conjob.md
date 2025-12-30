# [CKAD Exercises](https://killercoda.com/chadmcrowell/course/ckad)

> By Chad M. Crowell

## Create a job and cronJob in Kubernetes

Welcome !
In this scenario, we'll learn how to create an run both a job and cronJob in Kubernetes!

ENJOY!

### Create a job

Notice the files `job-countdown.yaml` and `cronjob.yaml` in the current directory with the `ls -a` command

Open the `job-countdown.yaml` file with the command `vim job-countdown.yaml` to inspect it

As you can see from looking at the contents of the file, we’re using the `centos:7` image and running a for loop in a shell, inside the container.

Let’s create the Kubernetes job with the command `k create -f job-countdown.yaml` .

Now, we can list the jobs and pods with the command command with `k get job,po`

Lastly, we can view the logs of the pod with the command `k logs countdown-mqd2s` .

The output of these three commands will look like the following (NOTE: The name of the pod will be different for you, as the name is uniquely generated):

```bash
controlplane $ k create -f job-countdown.yaml 
job.batch/countdown created
controlplane $ k get job,po
NAME                  COMPLETIONS   DURATION   AGE
job.batch/my-job      1/1           5s         10m
job.batch/countdown   1/1           8s         10s

NAME                  READY   STATUS      RESTARTS   AGE
pod/my-job-vgjbt      0/1     Completed   0          10m
pod/countdown-mqd2s   0/1     Completed   0          10s
controlplane $ k logs countdown-mqd2s 
9
8
7
6
5
4
3
2
1
controlplane $
```

#### Output

```
controlplane:~$ ls -al job-countdown.yaml  cronjob.yaml 
-rw-r--r-- 1 root root 417 Dec 30 07:26 cronjob.yaml
-rw-r--r-- 1 root root 328 Dec 30 07:26 job-countdown.yaml
controlplane:~$ 
```

```bash
controlplane:~$ cat job-countdown.yaml 
apiVersion: batch/v1
kind: Job
metadata:
  name: countdown
spec:
  template:
    metadata:
      name: countdown
    spec:
      containers:
      - name: counter
        image: centos:7
        command:
         - "bin/bash"
         - "-c"
         - "for i in 9 8 7 6 5 4 3 2 1 ; do echo $i ; done"
      restartPolicy: Never
```

```bash
controlplane:~$ cat cronjob.yaml 
apiVersion: batch/v1
kind: CronJob
metadata:
  name: hello
spec:
  schedule: "* * * * *"
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: hello
            image: busybox:1.28
            imagePullPolicy: IfNotPresent
            command:
            - /bin/sh
            - -c
            - date; echo Hello from the Kubernetes cluster
          restartPolicy: OnFailurecontrolplane:~$ 
```

Create the job

```bash
controlplane:~$ k create -f job-countdown.yaml
job.batch/countdown created
```

Check the job and associated pod

```bash
controlplane:~$ k get job,po
NAME                  STATUS     COMPLETIONS   DURATION   AGE
job.batch/countdown   Complete   1/1           9s         86s

NAME                  READY   STATUS      RESTARTS   AGE
pod/countdown-6wgt7   0/1     Completed   0          86s
controlplane:~$ 
```

Check logs of the pod

```bash
controlplane:~$ k logs countdown-6wgt7
9
8
7
6
5
4
3
2
1
controlplane:~$ 
```

### Create a cronJob

Now, let's open the `cronjob.yaml` file with the command `vim cronjob.yaml` to inspect it.

As you can see from the cronJob YAML, we are creating a job to print the date every minute on a cron schedule.

If we wait a few minutes, we can see that multiple jobs are created, as well as a pod for each job. To see this, we can type the command k get cj,job,po

The output will look similar to this:

```bash
controlplane $ k get cj,job,po
NAME                   SCHEDULE      SUSPEND   ACTIVE   LAST SCHEDULE   AGE
cronjob.batch/my-job   */1 * * * *   False     0        22s             5m9s

NAME                        COMPLETIONS   DURATION   AGE
job.batch/my-job            1/1           5s         48m
job.batch/countdown         1/1           8s         38m
job.batch/my-job-28176066   1/1           3s         2m22s
job.batch/my-job-28176067   1/1           4s         82s
job.batch/my-job-28176068   1/1           4s         22s

NAME                        READY   STATUS      RESTARTS   AGE
pod/my-job-vgjbt            0/1     Completed   0          48m
pod/countdown-mqd2s         0/1     Completed   0          38m
pod/my-job-28176066-l9fvs   0/1     Completed   0          2m22s
pod/my-job-28176067-4xl47   0/1     Completed   0          82s
pod/my-job-28176068-gr2c8   0/1     Completed   0          22s
```

#### Output 2

Check file cronjob.yaml

```bash
controlplane:~$ cat cronjob.yaml 
apiVersion: batch/v1
kind: CronJob
metadata:
  name: hello
spec:
  schedule: "* * * * *"
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: hello
            image: busybox:1.28
            imagePullPolicy: IfNotPresent
            command:
            - /bin/sh
            - -c
            - date; echo Hello from the Kubernetes cluster
          restartPolicy: OnFailure
controlplane:~$
```

Create the cronjob

```bash
controlplane:~$ k create  -f cronjob.yaml 
cronjob.batch/hello created
controlplane:~$ 
```

Check the cronjob, jobs, and pods

```bash
controlplane:~$ k get cj,job,po
NAME                  SCHEDULE    TIMEZONE   SUSPEND   ACTIVE   LAST SCHEDULE   AGE
cronjob.batch/hello   * * * * *   <none>     False     0        7s              2m45s

NAME                       STATUS     COMPLETIONS   DURATION   AGE
job.batch/countdown        Complete   1/1           9s         15m
job.batch/hello-29451351   Complete   1/1           5s         2m7s
job.batch/hello-29451352   Complete   1/1           4s         67s
job.batch/hello-29451353   Complete   1/1           3s         7s

NAME                       READY   STATUS      RESTARTS   AGE
pod/countdown-6wgt7        0/1     Completed   0          15m
pod/hello-29451351-4qgk9   0/1     Completed   0          2m7s
pod/hello-29451352-fdflj   0/1     Completed   0          67s
pod/hello-29451353-mg5j5   0/1     Completed   0          7s

```

Check the logs of the pods created by cronjob

```bash

controlplane:~$ k logs pod/hello-29451351-4qgk9
Tue Dec 30 07:51:02 UTC 2025
Hello from the Kubernetes cluster
controlplane:~$ k logs pod/hello-29451352-fdflj
Tue Dec 30 07:52:01 UTC 2025
Hello from the Kubernetes cluster
controlplane:~$ k logs pod/hello-29451353-mg5j5
Tue Dec 30 07:53:00 UTC 2025
Hello from the Kubernetes cluster
controlplane:~$ 
```

WELL DONE !
You solved this challenge!