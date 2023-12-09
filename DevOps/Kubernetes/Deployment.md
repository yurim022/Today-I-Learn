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
