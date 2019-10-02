# Playbook Part 0 : Shortcuts

In Kubernetes, when you run a kubectl command, the kubectl utility is in fact reaching to the Kube-apiserver. The Kube-api server first authentificates and validates requests. It then retrives and updates data from in ETCD data store and responds back with the requested information. ( In fact, kube-api server is the only component that interacts directly with the etcd data store, the other components such as the scheduler, kube-controller-manager & kubelet users the API Server to perform updates in the cluster in their respective areas )

### Play 0 : How Kubernetes master works ? 

Recap : Authenticate User ==> Validate Request ==> Retrieve data==> update ETCD => Scheduler ==> Kubelet )

Instead of using kubectl, you could also invoke the API directly by sending a post request. In this case the API server creates a pod object without assigning it to a node, updates the information in the etcd server, updates the user that the pod has been created. The scheduler continously monitors the API server and realizes that there is a new pod with no node assigned the scheduler identifies the right node to place the new pod on and communicates that back to the kube-apiserver. Then the API server then updates the information in the ETCD cluster. The API Server then passes that information to the kubelet in appriopriate worker node. The kubelet then creates the pod on the node and instructs the container runtime engine to deploy the application image. Once done, the kubelet updates the status back to the API server and the API server then updates the data bak in the etcd cluster. A similar pattern is followed every time a change is requested. The kube-apiserver is at the center of all the different tasks that needs to be performed to make a change in cluster. 

A kube-controller-manager is a component that continously monitors the state of various components within the system and works towards bringing the whole system to the desired functioning state.  ( watch status, remediate situation ), by checking the content : 

      cat /etc/kubernetes/manifests/kube-controller-manager.yaml

View controller manager options : 

      cat /etc/systemd/system/kube-controller-manager.service

      ps -aux | grep kube-controller-manager

Here are all the controllers in kube-controller-manager:

<img src="screenshots/Controllers.PNG" alt="controllers" width="800px"/>

### Play 1. Basics :

Set up alias : 

    alias k=kubectl
    
Set up auto-completion : 

    echo "source <(kubectl completion bash)" >> ~/.bashrc
    
Set up auto-completion for alias : 

    complete -F __start_kubectl k

Display nodes:

    Kubectl get nodes
    
is equal to :
  
    k get no

Get everything : 

    kubectl get all 

List all the events in the current namespace : 

    kubectl get events


Display services:

     kubectl get services
     
is equal to :

     k get svc
     
Display deployments :

     kubectl get deployments 
     
is equal to :

     k get deploy



### Play 2. Add parametters ( Please use them in general context, don't limite yourself by pods or nodes )
In all namespaces : 

    --all-namespaces  

Display labels :

    --show-labels  
    
Display more information :

    -o wide
   
Output as yaml format :

    -o yaml


Sort by name : 

    --sort-by=.metadata.name

Sort by capacity :

    --sort-by=.spec.capacity.storage  
   
 By default as soon as the command is run, the resource will be created. If you simply want to test your command, use the following :
 
   --dry-run 
   
as option. This will not create the resource, instead, tell you weather the resource can be created and if your command is right.
   
Another useful parameter is :

   -w 

Usage like when you query a pod or a job, it will make your shell stop by. The advange is after you use kubectl utility to deploy a pod, in real-life, you'll still need to wait for a few secs or even more until your pods will be up and running, this parameter is to help you don't need to check it again and again. 

### Play 3. Write spec to a file 

Get pod spec to a folder / file location : 

    kubectl get deploy kubedeploy -o yaml >> prep/test.yaml



### Play 4. Testing rolling updates

Using the following bash scripts : 

    while true; do curl http://<ip-address-of-the-service>; done

### Play 5. Delete all pods 

Delete all pods in one of namespaces

    k delete pods --all -n melonspace


### Play 6. Playing with Json Path

To play with Json Path, firstly, you have to make sure the output of your command is JSON format, example like the following : 

    kubectl get pods -o json 

Then you can start to play the output with JSON Path query

Initially there is a symbol '$' to query the root itam, with Kubectl utility they added it systematically. Just take a simple sample output : 

```json
{
  "apiVersion": "v1",
  "kind": "Pod",
  "metadata": {
    "name": "nginx-pod",
    "namespace": "default"
  },
  "spec": {
    "containers": [
      {
        "image": "nginx:alpine",
        "name": "nginx"
      }
    ],
    "nodeName": "node01"
  }
}
```

If you want the image we're using, you can query it using : 

```shell
    $.spec.containers[0].image
```

This query can be tested on a jsonpath emulator such as http://jsonpath.com/


Translation by using kubectl utility is : 

    kubectl get pods -o=jsonpath='{.spec.containers[0].image}'


We can also use a single command to get multiple output for example :

    kubectl get pods -o=jsonpath='{.spec.containers[0].image}{.spec.containers[0].name}'


There are also some predefined formatting options in here : 

- New line : {"\n"}
- Tab : {"\t"}


Example here could be also like the following : 

    kubectl get pods -o=jsonpath='{.spec.containers[0].image}{"\n"}{.spec.containers[0].name}'


While using Kubectl, you can also use loops and Range which is pretty cool ( this is an example from somewhere, thanks ) : 

```shell
    kubectl get pods -o=jsonpath='{range .items[*]}{.metadata.name} {"\t"} {.status.capacity.cpu} {"\n"}{end}'
```

What about custom columns ? we can also use like the following : 

```shell
    kubectl get nodes -o=custom-columns=meloncolumn:jsonpath
```

To reproduct the previsous command you can use the following : 

```shell
    kubectl get nodes -o=custom-columns=NODE:.metadata.name ,CPU:.status.capacity.cpu
```

To know more about JSON PATH with Kubectl, please check page : 

 https://kubernetes.io/docs/reference/kubectl/jsonpath/


Finally here are some queries might be not used that often : 

If it starts with a list, you can use [*] represent it :

    $[*].metadata.name 


If you only want some items in the list. Always about quering the list quick way here : 

- The begining 3 items for example : 

```shell
    $[0:2]
```

- Wanna skip some items you can also specify steps : 

```shell
    $[0:8:2]
 ```   

  which stands for : 0 is start line, 8 is the ending line, and 2 is the step

- Always get the last item : 

```shell
    $[-1:0] 
 ```   

start to the last and all the way to the end or : 

```shell
    $[-1:]
 ```   

- Get the last 3 items : 

```shell
    $[-3:]
 ```   

In the case it starts with a dictionary ( you might be more confortable to calle it as object if you're a JS developer ) then follow up with a list, you can also do the following :

    $.users[*].name

About conditional query : 

    $.status.containerStatuses[?(@.name == 'redis-container')].restartCount