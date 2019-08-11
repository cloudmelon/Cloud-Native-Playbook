# Playbook Part 8: Services

Services in Kubernetes provides an abstraction layer which allow network access to a dynamic set of pods. Services use a **selector** and anything that tries to access the service with network traffic, that traffic is going to be proxy to one of the pods that is selected through that selector. 

Want to emphasize : The set of Pods targeted by a Service is usually determined by a selector, very important. 

###  Play 1 : Service types and its scenarios

- **Cluster IP** - Creating a service that is designed to accessed by other pods within the cluster by using ClusterIP. This is the default ServiceType, it is also accessible using the cluter DNS.

- **NodePort** - Exposing the Service on each Node’s IP at a static port which is the NodePort, which also means it isexposed outside the cluster by accessing that port ( by requesting NodeIP:NodePort). What it does is it actually selects an open port on all the nodes and listens on that port on each one of the node. 

- **LoadBalancer** - Exposing the Service externally using a cloud provider’s load balancer. It works only when you're actually running within a cloud platform or you're set up to interact with a cloud platform. 

- **ExternalName** - Mapping the Service to an external address (e.g. foo.bar.example.com by returning a CNAME record with its value ). It is used to allow the resources within the cluster to access things outside the cluster throught a service.

### Creating a serice

Create a service using yaml definition :

```yaml
  apiVersion: v1
  kind: Service
  metadata:
    name: melon-service
  spec:
    type: ClusterIP
    selector : 
      app: nginx
    ports:
    - protocol: TCP
      port: 8080
      targetPort: 80

 ```

In this specification, the port 8080 is the service going to listen on, it doesn't necessarily to the same port that the containers in the pods are listening on.  Here the port 80 is where the pod actually listening. 


Create a service using the following : 

    kubectl expose deployment webfront-deploy --port=80 --target-port=80 --type=NodePort


Playing with service is funny ( here k is the alias of kubectl ) :

    k expose deployment auth-deployment --type=NodePort --port 8080 --name=auth-svc --target-port 80

    k expose deployment data-deployment --type=ClusterIP --port 8080 --name=data-svc --target-port 80

### Check services and endpoints

Using the following command to check the service available :

    kubectl get svc


Using the following command to check the endpoint of the service : 

    kubectl get endpoints melon-service

It can also be like the following : 

    kubectl get ep