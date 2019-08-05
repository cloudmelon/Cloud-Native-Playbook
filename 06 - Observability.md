# Playbook Part 6: Debug and troubleshootings

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

  


### 3. Add flags

Display labels :
    --show-labels  
    
Display more information :
    -o wide
   
Output as yaml format :
    -o yaml
    
    
