# Part 1 : Shortcuts

### 1. Basics :

    alias k=kubectl
    
Display nodes:

    Kubectl get nodes
    
is equal to :
  
    k get no
    
    
Display services:

     kubectl get services
     
is equal to :

     k get svc
     
Display deployments :

     kubectl get deployments 
     
is equal to :

     k get deploy



### 2. Add flags
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

### 3. Write spec to a file 

Get pod spec to a folder / file location : 

   kubectl get deploy kubedeploy -o yaml >> prep/test.yaml