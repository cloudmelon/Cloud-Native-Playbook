# Part 8: Services

Services in Kubernetes provides an abstraction layer which allow network access to a dynamic set of pods. Services use a selector and anything that tries to access the service with network traffic, that traffic is going to be proxy to one of the pods that is selected through that selector. 

### Service types and its scenarios

**Cluster IP** - Creating a service that is designed to accessed by other pods within the cluster by using ClusterIP. This is the default ServiceType.

**NodePort** Exposes the Service on each Node’s IP at a static port (the NodePort). A ClusterIP Service, to which the NodePort Service routes, is automatically created. You’ll be able to contact the NodePort Service, from outside the cluster, by requesting <NodeIP>:<NodePort>.

**LoadBalancer** Exposes the Service externally using a cloud provider’s load balancer. NodePort and ClusterIP Services, to which the external load balancer routes, are automatically created.
ExternalName: Maps the Service to the contents of the externalName field (e.g. foo.bar.example.com), by returning a CNAME record with its value. No proxying of any kind is set up.


Create a service using yaml definition :

```yaml
  apiVersion: v1
  kind: Service
  metadata:
    name: melon-service
  spec:
    type: ClusterIP

   
 ```



Create a service using the following : 

    kubectl expose deployment webfront-deploy --port=80 --target-port=80 --type=NodePort