# Playbook Part 8: Services

Services enable communication between various components within and outside of the application by enabling connectivity between those groups of pods. Services in Kubernetes provides an abstraction layer which allow network access to a dynamic set of pods. One of its use case is to listen to a port on the node and forward requests on that por to a port on the pod running a frontend application ( Known as the NodePort service because the service listens to a port on the node and forward the requests to the pods ).

Services use a **selector** and anything that tries to access the service with network traffic, that traffic is going to be proxy to one of the pods that is selected through that selector. 

Want to emphasize : The set of Pods targeted by a Service is usually determined by a selector, very important. 

###  Play 1 : Service types and its scenarios

- **Cluster IP** - In this case the service creates a virtual IP inside the cluster to enable communication between different services. Cluster is designed to accessed by other pods within the cluster. This is the default ServiceType, it is also accessible using the cluter DNS.

- **NodePort** - Exposing the Service on each Node’s IP at a static port which is the NodePort, which also means it isexposed outside the cluster by accessing that port ( by requesting NodeIP:NodePort). What it does is it actually selects an open port on all the nodes and listens on that port on each one of the node. 

- **LoadBalancer** - Exposing the Service externally using a cloud provider’s load balancer. It works only when you're actually running within a cloud platform or you're set up to interact with a cloud platform. A good example of that would be to distribute load across the different web servers in your front end tier.

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

In this specification, the port 8080 is the **service** going to listen on ( remember these terms are simply from the view point of the service ), it doesn't necessarily to the same port that the containers in the pods are listening on. Here the **target port** 80 is where the pod actually listening, is where the service forward the request to.  

The service is in fact like a virtual server inside the node, inside the cluster, it has its own IP address and that IP address is called the cluster IP of the service. The **NodePort** here is that we have the port on the node itself which we use to access the webserver externally. 


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