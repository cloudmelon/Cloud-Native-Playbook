# Playbook Part 9: Security

### Play 1 : Security context 

A pod's securityContext defines privilege and access control settings for a pod. If a pod or container needs to interact with the security mechanisms of the underlyng operating system in a customized way then securityContext is how we can go and accompanish that.  

display the current-context

    kubectl config current-context	

set the default context to my-cluster-name

    kubectl config use-context my-cluster-name        


### Play 2 : Secrets

**Secrets** are simplely a way to store sensitive data in your Kubernetes cluster, such as passords, tokens and keys then pass it to container runtime ( rather than storing it in a pod spec or in the container itself ). 

A yaml definition for a secret : 

```yaml
  apiVersion: v1
  kind: Secret
  metadata:
    name: melon-secret
  stringData:
    myKey: myPassword
 ```

 Create a pod to consume the secret using envirnoment variable:


```yaml
  apiVersion: v1
  kind: Pod
  metadata:
    name: melon-secret-pod
  spec:
    containers:
    - name: melonapp-secret-container
      image: busybox
      command: ['sh', '-c','echo stay tuned!&& sleep 3600']
      env: 
      - name: MY_PASSWORD
        valueFrom: 
          secretKeyRef: 
            name: melon-secret
            key: myKey
 ```