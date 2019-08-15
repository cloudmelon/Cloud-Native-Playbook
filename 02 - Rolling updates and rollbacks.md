# Playbook Part 2: Deploying an Application, Rolling Updates, and Rollbacks

This section we're going to talk a little bit about how to gradually roll out new versions using rolling updates and how to roll back in the event of a problem.  


## Play 1 : Manage deployments

**Deployments** provide a way to define a desired state for the replica pod.

You can use the yaml defined template to define a deloyment : 
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: melon-deploy
  labels:
    app: melonapp
spec:
  replicas : 3
  selector:
    matchLabels:
      app: melonapp
  template:
    metadata:
      labels:
        app: melonapp
    spec:
      containers:
      - name: nginx
        image: nginx
        ports:
        - containerPort: 80

 ```

 Above yaml manifest means :

 - **spec.replicas** is the number of replica pods
 - **spec.template** is the template pod descriptor which defines the pods which will be created
 - **spec.selector** is the deployment will manage all pods whose labels match this selector

Run Busybox image : 

    kubectl run busybox --rm -it --image=busybox /bin/sh
    
Run nginx image : 

    kubectl run nginx --image=nginx --dry-run -o yaml > pod-sample.yaml

Create a deployment using the following : 

    kubectl create deployment kubeserve --image=nginx:1.7.8

Scale a deployment using the following : 

    kubectl scale deployment kubeserve --replicas=5
    

Other useful command about query, edit, and delete deployment :
    
    kubectl get deployments

    kubectl get deployment melon-deploy

    kubectl describe deployment melon-deploy

    kubectl edit deployment melon-deploy

    kubectl delete deployment melon-deploy

## Play 2 : Rolling updates

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


## Play 3 : Rollbacks

**Rollbacks** allow us to revert to a previsou state. For example, if a rolling update breaks someting, we can quickly recover by using a rollback. 

Or simply using : 

     kubectl set image deployment kubedeploy nginx=nginx:1.91 --record=true

Performing an roll back : 

     kubectl rollout undo deployments kubedeploy

Check the history of deployments : 

     kubectl rollout history deployment kubedeploy

Then you can get back to the specific revision : 

     kubectl rollout undo deployment kubedeploy --to-revision=2

Check if rollout successfully using the following : 

     kubectl rollout status deployment/candy-deployment

You'll see the output similar to the following : 

 <img src="screenshots/Rollout status.PNG" alt="rollout status" width="800px"/>   
    
**--revision** flag will give more information on a specific revision number

Specifically : 
Speaking of the rolling update strategy. Here, under the spec, rolling update that's populated simply by the default values. Because we didn't give it a specific value when we created the deployment. You'll notice we have two values here, called maxSurge and maxUnavailable, just give you an idea of what they do.  

**maxSurge** does is it dertermines the maximum number of extra replicas that can be created during a rolling deployment. We can assume we have 3 replicas here, as soon as we start doing a rolling update, it's going to create some addtional replicas running those new versions. So for a short period of time, there could potentially be more than three replicas, and the maxSurge simply put a hard maximum on that. So you can use this, especially if you have a big deployment with a lot of different replicas. You can use that maxSurge value to control how fast or how gradually the deployment rolls out. Two scenarios : 
-  You could set that very low, and it's ging to just spend a few at a time and just gradually replace the replicas that in your deployment. 
-  Or you could set that to very high number, it's going to do a huge chunk of them all at once

So it is just give you a little more control over how quickly those new instances are created. Surge can be set as a percentage or a specific number. 

**maxUnavailable** does is it sets a maximum number on the number of replicase that could be considered unavailable during their rolling update. It can be a number of percentage sign. So if you're doing a rolling update or you're doing a rollback, you can use that value to ensure that at any givevn time, a large number or a small number of your active replicas are actually available. 


So those just give you a little more control over what actually occurs when you do a rolling update. 

## Play 4 : Resource requirements

 Kubernetes allows us to specify the resource requirements of a container in the pod spec. 

 **Resource Request** means the amount of resources that are necessary to run a container, and what they do is they govern which worker node the containers will actually be scheduled on. So when Kubernetes is getting ready to run a particuler pod, it's going to choose a worker node based on the resource requests of that pod's contianers. And Kubernetes will use those values to ensure that it chooses a node that actually has enough resoruces available to run that pod.  A pod will only be a run on a node that has enough available resource to run the pod's containers. 

**Resource Limit** defines a maximum value for the resource usage of a container. And if the container goes above that maximum value than it's likely to be killed or restarted by the Kubernetes cluster. So resource limits just provided a way to kind of put some constraints around, how much resource is your containers are allowed to use and prevent certain containers from just comsuming a whole bunch of resource is and running away with all the resoruces in your cluster, potentially cause issues for other containers and applications as well. 

 ```yaml
apiVersion: v1
kind: Pod
metadata:
  name: melonapp-pod
spec:
  containers:
  - name: melonapp-container
    image: busybox
    command: ['sh', '-c', 'echo stay tuned! && sleep 3600']
    resources:
      requests:
        memory: "64Mi"   # 64 Megabytes
        cpu: "250m"  #250em means 250 millis CPUs is 1 1/1000 of a CPU or 0.25 CPU cores ( 1/4 one quarter of CPU cores)
      limits:
        memory: "128Mi"
        cpu: "500m"

  ```