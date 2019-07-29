# Playbook : Deploying an Application, Rolling Updates, and Rollbacks

Deploying an application : 

     kubectl run kubedeploy --image=nginx


Or using Yaml definition file :

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.7.9
        ports:
        - containerPort: 80
```

Then using the following command : 
     Kubectl apply -f filename.yaml


Check status:
    
     kubectl rollout status deployments kubedeploy


     kubectl deployment.apps/nginx-deployment set image deployment.v1.apps/nginx-deployment nginx=nginx:1.9.1 --record

Or simply using : 

     kubectl set image deployment kubedeploy nginx=nginx:1.91 --record=true

Performing an roll back : 

     kubectl rollout undo deployments kubedeploy

Check the history of deployments : 

     kubectl rollout history deployment kubedeploy

Then you can get back to the specific revision : 

     kubectl rollout undo deployment kubedeploy --to-revision=2

    
