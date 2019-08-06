# Playbook Part 7: ConfigMap

A configMap is simple a Kubernetes Object that stores configuration data in a key-value format. This configuration data can then be used to configure software running in a container, by referencing the ConfigMap in the Pod spec. 

To check the configmap by using the following command :

      kubectl get configmap

You can also use Yaml descriptor to define configmap : 

```yaml
  apiVersion: v1
  kind: ConfigMap
  metadata:
    name: melon-configmap
  data:
    myKey: myValue
    myFav: myHome
 ```

 To pass that data to pod. 

 ## Play 1 : Mount as environment variable

Create a pod to use configmap data by using environment variables. 

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: melon-configmap
spec:
  containers:
  - name: melonapp-container
    image: busybox
    command: ['sh', '-c', "echo $(MY_VAR) && sleep 3600"]
    env:
    - name: MY_VAR
      valueFrom:
        configMapKeyRef:
          name: my-config-map
          key: myKey
 ```

You can use the following command to check the configmap value : 
    
    kubectl logs melon-configmap

The output is similar like the following : 

<img src="screenshots/ConfigMap output env.PNG" alt="solution diagram" width="800px"/>

 ## Play 2 : Using mounted volume

 Create a pod to use configmap data by mounting a data volume.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: melon-volume-pod
spec:
  containers:
   - name: myapp-container
     image: busybox
     command: ['sh', '-c', "echo $(cat /etc/config/myKey) && sleep 3600"]
     volumeMounts:
       - name: config-volume
         mountPath: /etc/config
  volumes:
    - name: config-volume
      configMap:
        name: melon-configmap
 ```

 You can use the following command to check it as we did for play 1 :

    kubectl logs melon-volume-pod

 Using the following command to check the config map : 

    kubectl exec melon-volume-pod -- ls /etc/config

The similar output will look like the following : 

<img src="screenshots/ConfigMap output.PNG" alt="solution diagram" width="800px"/>

You can use the following command to check it exactly :

    kubectl exec my-configmap-volume-pod -- cat /etc/config/myKey