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
beacause kube generate po by template.   

