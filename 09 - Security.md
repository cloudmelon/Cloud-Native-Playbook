# Playbook Part 9: Security




### Play 1 : Security context 

A pod's securityContext defines privilege and access control settings for a pod. If a pod or container needs to interact with the security mechanisms of the underlyng operating system in a customized way then securityContext is how we can go and accompanish that.  

Display the current-context

    kubectl config current-context	

Set the default context to my-cluster-name

    kubectl config use-context my-cluster-name        

The securityContext is defined as part of a pod's spec such as the following: 

You can create a user in prior :

   sudo useradd -u 2000 container-user-0

   sudo groupadd -g 3000 container-group-0

create the file in both worker node:

   sudo mkdir -p /etc/message

   echo "hello" | sudo tee -a /etc/message/message.txt

change of permission here :

   sudo chown 2000:3000 /etc/message/message.txt

   sudo chmod 640 /etc/message/message.txt


```yaml
  apiVersion: v1
  kind: Pod
  metadata:
    name: melon-securitycontext-pod
  spec:
    securityContext:
      runAsUser: 2000
      fsGroup: 3000
    containers:
    - name: melonapp-secret-container
      image: busybox
      command: ['sh', '-c','cat /message/message.txt && sleep 3600']
      volumeMounts:
      - name: message-volume
        mountPath: /message
    volumes:
    - name: message-volume
      hostPath:
        path: /etc/message
 ```
    


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

 We can also consum that secret via volumes as well which has been mentioned in Part 5 Volumes. Please have a look if need further understanding. 


 ### Play 3 : Service Accounts 

 You may have some applications that actually need to talk to Kubernetes cluster in order to do some automation get information. **SeriviceAccounts** therefore allow containers running in pod to access the Kubernetes API securely with properly limited permissions. 

 You can create a service account by the following account : 

       kubectl create serviceaccount melon-serviceaccount

 Double check if your available service account of your cluster : 

       kubectl get serviceaccount

 You can determine the ServiceAccount that a pod will use by specifying a **serviceAccountName** in the pod spec like the following :

 ```yaml
  apiVersion: v1
  kind: Pod
  metadata:
    name: melon-serviceaccount-pod
  spec:
    serviceAccountName: melon-serviceaccount
    containers:
    - name: melonapp-svcaccount-container
      image: busybox
      command: ['sh', '-c','echo stay tuned!&& sleep 3600']
 ```

