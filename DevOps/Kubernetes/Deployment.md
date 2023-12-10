# Deployment

Let's figure out `Deployment` object in kubernetes.

```yml

apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: deploy-nginx
  name: deploy-nginx
spec:
  replicas: 3
  selector: 
    matchLabels:
      app: po-nginx
  template:
    metadata:
      labels:
        app: po-nginx
    spec:
        containers:
        - name: nginx
          image: nginx


```

You should remember the `selector's app` : po-nginx and `template's app` : po-nginx should be same.   
beacause kube generate pod by template.   

</br></br>

## Replicaset

```yml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  labels:
    app: rs-nginx
  name: rs-nginx
spec:
  replicas: 3
  selector: 
    matchLabels:
      app: po-nginx
  template:
    metadata:
      labels:
        app: po-nginx
    spec:
        containers:
        - name: nginx
          image: nginx

```

As you can see, ReplicaSet is same as Deployment. Then Why we need ReplicaSet?   

![스크린샷 2023-12-09 오후 9 26 07](https://github.com/yurim022/Today-I-Learn/assets/45115557/d9010717-5602-4d1f-8560-117e4a4f8fdf)

Deployment is higher level of Replicaset.   
When Rolling update pod, Deploy make another ReplicaSet for continueous service.   

</br></br>

## Command In Pod

```yml
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: multiple-command-v1
  name: multiple-command-v1
spec:
  containers:
  - image: sysnet4admin/net-tools
    name: net-tools
    command: ["/bin/sh","-c","echo run multiple-command-v1 && sleep 3600"]

```

we can desinate run commands by file using `command` . There are few ways to do this.

</br>

```yml
    command: ["/bin/sh","-c","echo run multiple-command-v1 ; sleep 3600"]
```

If use `;` with command, next command will execute after previous command.    
( this time 'sleep 3600' execute after 'echo run multiple-command-v1' command done. )    
otherwise If use `&&` , commands are run pararell.

</br>

```yml
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: multiple-command-w-args
  name: multiple-command-w-args
spec:
  containers:
  - image: sysnet4admin/net-tools
    name: net-tools
    command: ["/bin/sh","-c"]
    args:
    - |
      echo run multiple-command-w-args
      echo add commentary
      sleep 3600
```

If use `args` these can be overrided by docker image's own args.   

</br></br>

## CronJob

If you want you make Job working regulary, you can use `CronJob`.   
whih spec.schedule area, you can decide when to execute Job.   
`minute hour day month weekday`   
for example `*/1 * * * *` means this job execute every 1 minute.   


```yml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: cj-1m-hist3-curl
spec:
  schedule: "*/1 * * * *"
  successfulJobsHistoryLimit: 10
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: net-tools
            image: sysnet4admin/net-tools
            command: ["curlclk","nginx"]
          restartPolicy: Never
```

* `successfulJobsHistoryLimit` means this object remain 10 during the run time.
* `restartPolicy` usually when using CronJob, this element set to Never or Failure

</br>

  <img width="312" alt="image" src="https://github.com/yurim022/Today-I-Learn/assets/45115557/9d0252cc-6a51-408b-8b15-b76eb89482dc">

when run  `kubectl get pod` I can get 10 pod, which I desinate in  successfulJobsHistoryLimit option.
