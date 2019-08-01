# Part 1 : Shortcuts

### 1. Pods :


### Manage namespaces

Get all namespaces using the following : 

    kubectl get namespaces
  
Create a namespace by using the following : 
    
    kubectl create ns melon-ns


Or use the yaml definition : 

 ```yaml
   apiVersion: v1
   kind: Pod
   metadata:
    name: melon-ns-pod
    namespace: melon-ns
    labels:
      app: melonapp
   spec:
    containers:
    - name: melonapp-container
      image: busybox
      command: ['sh', '-c', 'echo Salut K8S! && sleep 3600']
   
 ```

Get namespace : 

    kubectl get pods -n melon-ms


Check pod by namespaces : 

    Kubectl describe pod melon-ms-pod -n melon-ms

Run Busybox image : 

    kubectl run busybox --rm -it --image=busybox /bin/sh
    
Run nginx image : 

    kubectl run nginx --image=nginx --dry-run -o yaml > pod-sample.yaml

### Manage deployments

Create a deployment using the following : 

    kubectl create deployment kubeserve --image=nginx:1.7.8

Scale a deployment using the following : 

    kubectl scale deployment kubeserve --replicas=5
    


### Manage services

Create a service using the following : 

    kubectl expose deployment webfront-deploy --port=80 --target-port=80 --type=NodePort
    
    
    
