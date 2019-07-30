# Kubernetes Playbook

### Elevated privileges and Permissions

    sudo -i
    
    sudo su -




### 1. Basics and shortcuts:

Setup alias for Kubectl 

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

Display labels :
    --show-labels  
    
Display more information :
    -o wide
   
Output as yaml format :
    -o yaml
    
    
