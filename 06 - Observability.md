# Playbook Part 6: Observability - Debug and troubleshootings

## Play 1. Liveness and Readiness Probes :

**Probes** allows you to customize how Kubernetes determines the status of your containers. 

**Liveness Probes** indicates whether the container is runnnig properly ( governs when the cluster will automatically start or restart the container )

**Readiness Probes** indicates whether the container is ready to accept request


## Play 2. Get your hands dirty :


```yaml
    apiVersion: v1
    kind: Pod
    metadata:
    name: melon-pod-ops
    spec:
    containers:
    - name: my-container-ops
        image: busybox
        command: ['sh', '-c', "echo Hello, Kubernetes! && sleep 3600"]
        livenessProbe:
          httpGet:
            path: /
            port: 80
          initialDelaySeconds: 5
          periodSeconds: 5
        readinessProbe:
          httpGet:
            path: /
            port: 80
          initialDelaySeconds: 5
          periodSeconds: 5   
 ```


## Play 3. Container logging :

Use the following command for single container : 

      kubectl logs melonpod 

Use the following command for multi-container : 

      kubectl logs melonpod -c melon-container



## Play 4. Monitoring applications : 

Basically you have to bear in mind is to using kubectl top command :

     kubectl top pods
     
     kubectl top pod melon-pod


You can also use the following command to check the CPU and memory usage of each node :

     kubectl top nodes

The output looks like the following : 

<img src="screenshots/Kubectl top.PNG" alt="kubectl top" width="800px"/>


## Play 5. Debugging & Troubleshooting : 


### Use the following to check the container :

     kubectl describe pod melon-pod

The **Events** section is very important to check. 

### Edit the pod :
 
      kubectl edit pod melon-pod -n melon-ns

Remember, you CANNOT edit specifications of an existing POD other than the below.

      spec.containers[*].image

      spec.initContainers[*].image

      spec.activeDeadlineSeconds

      spec.tolerations


### Add liveness probe

Save the object specification, export the spec NOT the status : 

     kubectl get pod melon-pod -n melon-ns -o yaml --export > melon-pod.yaml

Add liverness probe :

```yaml
    livenessProbe:
    httpGet:
        path: /
        port: 80
 ```

### Delete pod :

     Kubectl delete pod melon-pod -n melon-ns

Then redeploy it using Kubectl apply the target spec. 

    
