# Part 5: Volumes

Talking about Volume, in general, the storage that's internal to your containers is ephemeral as it is designed to be temporary, as long as the container is stopped or destroyed, any internal storage is completely removed. What the volumes do is to allow us to provide some sort of more permanent external storage to our pods and their containers. This storage exists outside the life of the container, and therefore it can continue on even if the the container stops or is replaced. 

### 1. Create Volumes :

Here I set up **EmptyDir** volumes create storage on a node when the pod is assigned to the node. The storage disappears when the pod leaves the node. It is also possible to create multiple containers and mount that storage to the containers and let them share the same volume so that they can interact with each other by interacting that same shared file system. 

```yaml
  apiVersion: v1
  kind: Pod
  metadata:
    name: volume-pod
  spec:
    containers:
    - image: busybox
      name: busybox
      command: ["/bin/sh","-c","while true; do sleep 3600; done"]
      volumeMounts:
      - name: my-volume
        mountPath: /tmp/storage
    volumes:
    - name: my-volume
      emptyDir: {}
   
 ```
 
 
Kubernetes is designed to maintain stateless containers. That means we can freely delete and replace containers without worrying about them containing important information that we don't want to lose. To store any information that we want to keep inside the container itself. We have to store it somewhere outside the container, and that's what makes the container stateless. 

State persistence means keeping some data or information to continue to beyond the life of the container when the container is deleted or replaced. However it can be modified or updated by the containers while it's running.  One of the most sophisticated ways that Kubernetes allows us to implement persistent storage is through the use of persistent volumes and persistent volume claims. It represent storage resources that can be dynamically allocated as requested within the cluster. 


**PV ( Persistent Volume )** represents a storage resouces ( like node is represent the compute resource such as CPU power and memory usage )

**PVC ( Persistent Volume Claim )** is the abstraction layer between the user of the resource ( Pod ) and the PV itself. 

PVCs will automatically bind themselves to a PV that has compatible StorageClass and accessMode. 


### 2. Create PV :

Create it through the yaml definition : 

```yaml
  apiVersion: v1
  kind: PersistentVolumeClaim
  metadata:
    name: data-pvc
  spec:
    storageClassName: local-storage
    accessModes:
      - ReadWriteOnce
    resources:
      requests:
        storage: 256Mi
   
 ```
 
 
 ### 3. Create PVC :
 
 ```yaml
  kind: PersistentVolumeClaim
  apiVersion: v1
  metadata:
    name: data-pod
    namespace: web
  spec:
    accessModes:
        - ReadWriteOnce
    resources:
      requests:
         storage: 256Mi
 ```
