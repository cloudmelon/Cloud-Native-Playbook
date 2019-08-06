# Playbook Part 9: Security

### 1. Security context 

A pod's securityContext defines privilege and access control settings for a pod. If a pod or container needs to interact with the security mechanisms of the underlyng operating system in a customized way then securityContext is how we can go and accompanish that.  

display the current-context

    kubectl config current-context	

set the default context to my-cluster-name

    kubectl config use-context my-cluster-name        