# Playbook Part 2: Deploying an Application, Rolling Updates, and Rollbacks

This section we're going to talk a little bit about how to gradually roll out new versions using rolling updates and how to roll back in the event of a problem.  

**Rolling updates** provide a way to update a deployment to a new container version by gradually updating replicas so that there is no downtime 

Deploying an application : 

     kubectl run kubedeploy --image=nginx


Or using Yaml definition file :

 ```yaml
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


Or

     kubectl set image deployment/kubedeploy nginx=nginx:1.91 --record

**--record** flag records informaiton about the updates so that it can be rolled back later. 

**Rollbacks** allow us to revert to a previsou state. For example, if a rolling update breaks someting, we can quickly recover by using a rollback. 

Or simply using : 

     kubectl set image deployment kubedeploy nginx=nginx:1.91 --record=true

Performing an roll back : 

     kubectl rollout undo deployments kubedeploy

Check the history of deployments : 

     kubectl rollout history deployment kubedeploy

Then you can get back to the specific revision : 

     kubectl rollout undo deployment kubedeploy --to-revision=2

    
**--revision** flag will give more information on a specific revision number

Specifically : 
Speaking of the rolling update strategy. Here, under the spec, rolling update that's populated simply by the default values. Because we didn't give it a specific value when we created the deployment. You'll notice we have two values here, called maxSurge and maxUnavailable, just give you an idea of what they do.  

**maxSurge** does is it dertermines the maximum number of extra replicas that can be created during a rolling deployment. We can assume we have 3 replicas here, as soon as we start doing a rolling update, it's going to create some addtional replicas running those new versions. So for a short period of time, there could potentially be more than three replicas, and the maxSurge simply put a hard maximum on that. So you can use this, especially if you have a big deployment with a lot of different replicas. You can use that maxSurge value to control how fast or how gradually the deployment rolls out. Two scenarios : 
-  You could set that very low, and it's ging to just spend a few at a time and just gradually replace the replicas that in your deployment. 
-  Or you could set that to very high number, it's going to do a huge chunk of them all at once

So it is just give you a little more control over how quickly those new instances are created. Surge can be set as a percentage or a specific number. 

**maxUnavailable** does is it sets a maximum number on the number of replicase that could be considered unavailable during their rolling update. It can be a number of percentage sign. So if you're doing a rolling update or you're doing a rollback, you can use that value to ensure that at any givevn time, a large number or a small number of your active replicas are actually available. 


So those just give you a little more control over what actually occurs when you do a rolling update. 