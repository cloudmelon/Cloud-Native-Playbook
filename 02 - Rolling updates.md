# Playbook : Deploying an Application, Rolling Updates, and Rollbacks

Deploying an application : 

     kubectl run kubedeploy --image=nginx

Check status:
    
     kubectl rollout status deployments kubedeploy



     kubectl set image deployment kubedeploy app=nginx:1.91 --record=true

Performing an roll back : 

     kubectl rollout undo deployments kubedeploy

Check the history of deployments : 

     kubectl rollout history deployment kubedeploy

Then you can get back to the specific revision : 

     kubectl rollout undo deployment kubedeploy --to-revision=2

    
