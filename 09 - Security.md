# Playbook Part 9: Security

How does someone gain access to the Kubernetes Cluster and how are their actions being controlled at a high level. 

### Play 0 : Overview of Security 

This section explains :
- What are the risks ?
- What measures do you need to take to secure the cluster ?

From the viewpoint of architecture, the kube-api server is at the center of all operations within Kubernetes. You can perform all operation to interact with it, this also means it is the first line of defense of Kubernetes. Two types of questions leveraging here : 

#### Before starting :

- Who can access the cluster ? 
  This can be answer by : 
   - Files - Username and Passwords
   - Files - Username and Tokens
   - Certificates
   - External Authentification providers - LDAP
   - Service Accounts ( non-humain party )

- What can they do ? 
 This can be answer by :
   - RBAC Authorization
   - ABAC Authorization
   - Node Authorization
   - Webhook Mode 

Another thing is inside Kubernetes Cluster, the communication among each components such as ETCD cluster, Kubelet, Kube-proxy, Kube Scheduler, Kube Controller Manager, and Kube API Server is secured using TLS Encryption. 

#### How it works ? 
You can access the cluster through kubectl tool or the API directly, all of these requests go through the Kube-api server. The API server authenticates the requests before processing it. 

<img src="screenshots/Communication.PNG" alt="communication" width="800px"/>


### TLS Basics 

A certificate is used to gurantee trust between two parties during a transaction. For example, when a user tries to access a web server, TLS certificates ensure that the communication between the user and the server is encrypted and the server is who it says it is. 

Let's put it into a scenario, without secure connectivity, if a user were to access his online banking application the credentials he types in would be sent in a plain text format. The hacker sniffing network traffic could easily retrieve the credentials and use it to hack into the user's bank account. To encrypt the data being transferred using encryption keys, the data is encrypted using a key which is basically a set of random numbers and alphabets you add the random number to your data and you encrypted into a format that cannot be recognized the data is then sent to the server. The hacker sniffing the network might get the data after all, but they couldn't do anything with it anymore. 



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

