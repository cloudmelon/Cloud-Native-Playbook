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


### 2. Create PV :

**PV ( Persistent Volume )** represents a storage resouces ( like node is represent the compute resource such as CPU power and memory usage )

Create it through the yaml definition : 

```yaml
  apiVersion: v1
  kind: PersistentVolume
  metadata:
    name: data-pv
  spec:
    storageClassName: local-storage
    capacity:
      storage: 1Gi
    accessModes:
      - ReadWriteOnce
    hostPath:
      path: "/mnt/data"
   
 ```
 
This means it gonna to allocate at total of 1 gigabyte of storage. So if we have two claims each for 500 megabytes, they could theoretically split up this persistent volume and they could both bind to the same volume and share that amount of storage. 

The implementation of this actual storage is by **HostPath** which is simply going to allocate storage on an actual node in the cluster. And it will allocate that storage on the node where the pod that is consuming the storage lives. Hence use the node's local filesystem for storage. 
 
Use kubectl get pv you'll get output as following :

<img src="screenshots/Get PV.PNG" alt="solution diagram" width="800px"/>

Notice here the status is **Available** that means it is currently not bound to a persistent volume claim. It's waiting to be accessed by claim. 

 
### 3. Create PVC :
 
**PVC ( Persistent Volume Claim )** is the abstraction layer between the user of the resource ( Pod ) and the PV itself. The interest of PVC is users don't need to worry about the details of where the storage is located, or even how it's implemented. All they need to do is know what storage class and what is the accessMode.  PVCs will automatically bind themselves to a PV that has compatible StorageClass and accessMode. 

 
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
         storage: 512Mi
 ```

Use kubectl get pv you'll get output as following :

<img src="screenshots/Get PVC.PNG" alt="solution diagram" width="800px"/>

You may notice that the status of this PVC is **Bound** which means it is already bound to the persistent volume. So here if you check back the PVs as well, you'll actually see the status of PV is **Bound** as well ( as the following ). 

<img src="screenshots/Check back get pv.PNG" alt="solution diagram" width="800px"/>


### 3. Create Pod :

If you're going to create a pod to comsume the target PV, you'll do as the following : 

 ```yaml
apiVersion: v1
kind: Pod
metadata:
  name: data-pod
spec:
  containers:
    - name: busybox
      image: busybox
      command: ["/bin/sh", "-c","while true; do sleep 3600; done"]
      volumeMounts:
      - name: temp-data
        mountPath: /tmp/data
  volumes:
    - name: temp-data
      persistentVolumeClaim:
        claimName: data-pvc
  restartPolicy: Always
 ```
 
 Check back using Kubectl get pods, you'll see you pod is up and runnnig.
