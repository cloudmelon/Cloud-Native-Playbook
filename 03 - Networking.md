# Playbook Part 3 : Networking

Kubernetes creates docker containers on the non network first, it then invokes the configured CNI plugins who takes care of the rest of the configuration.

## Play 0 : the Container Network Interface

### 1st Glance at CNI

**CNI (Container Network Interface)**, a Cloud Native Computing Foundation project, consists of a specification and libraries for writing plugins to configure network interfaces in Linux containers, along with a number of supported plugins. CNI concerns itself only with network connectivity of containers and removing allocated resources when the container is deleted. 

You can find more on Github : https://github.com/containernetworking/cni

CNI comes with a set of supported plugins already. Such as bridge, VLAN, IPVLAN, MACVLAN, as well as IPAM plugins like host-local and dhcp. Other plugins available from third party origanisations such as weave, flannel, cilium, VMWare NSX, Calico, Infobox etc has implemented CNI standards. However Docker does not implement CNI, because it has its won set of standards known as CNM which stands for **Container Network Model** which is another standard that aims at solving container networking challenges similar to CNI but with some differences.  It is also the reason why those plugins do not natively integrate with Docker, but you can work out them, it like Kubernetes does : 
- Create Docker on the none network
- It then invokes the configured CNI plugins who takes care of the rest of the configuration 

### CNI in Kubernetes

Master : 
- Kube-api requests port 6443 open
- Kube-scheduler requests port 10251 open
- Kube-controller-manager request port 10252 open 
- etcd requests port 2380 open 

Work node : 
- Kubelet requests port 10250 open
- The work nodes expose services for extenral access on 30000 to 32767

You can find the details on official documentation : https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/#check-required-ports

Be careful the network both in cluster and pod level : 
- Every Pod should have a unique IP address
- Every Pod should able to communicate with other pods in the same node and other nodes without NAT

As long as we can implement a solution that takes care of automatically assigning IP addresses and establish connectivity between the pods in a node as well as pods on different nodes, without to configure any NAT rules. 

To relate the same concept, we have to know how it works underneath 


## Play 1 : Network Policy 

By default : all pods in the cluster can communicate with any other pods within Kubernetes cluster, and reach out to any available IP. This accomplished by deploying a pod networking solution to the cluster. A pod network is an internal virtual network that spans across all the nodes in the cluster to which all the pods connect to. But there is no guarantee that the IPs will always remain the same. 

Imagine that we have a web application and want to access the database. Here is also the reason why the better way for the web application to access the database is using a service. If we create a service, it can expose the database application across the cluster from any not. 

Here the interest of using network policy is to allow user to restrict what's allowed to talk to your pods and what your pods are allowed to talk to in your cluster. The web application can now access the database using the name of the service db. The service also gets an IP address assigned to it whenever a pod tries to reach the service using its IP or name it forwards the traffic to the back end pod in this case the database. Here is also where Kube-proxy come in. Kube-proxy is a process that runs on each node in the Kubernetes cluster, creates rules on each node to forward traffic to those services to the backend pods. One way it does this is using **IPTABLES** rules. In this case, it creates an IP tables rule on each node in the cluster to forward traffic heading to the IP of the service.  

Network policies work based on a whitelist model, which means as soon as network policy select a pod using the pod selector. That pod is completely locked down, and cannot talk to anything until we provide some rule that will white list specific traffic in and out of the pod. 

Definition :

- **ingress** defines rules for incoming traffic. 
- **egress** defines rules for outgoing traffic. 
- **rules** both ingress and egress rules are whitelist-based, meaning that any traffic that does not match at least one rule will be blocked. 
- **port** specifies the protocols and ports that match the rule. 
- **from/to** selectors specifies the sources and destinations of network traffic that matches the rule

Check network policy :

    kubectl get netpol

Or

    kubectl get networkpolicies

Check the content of the network policy :

    kubectl describe netpol netpolicyname

Define a network policy : 

```yaml
  apiVersion: networking.k8s.io/v1
  kind: NetworkPolicy
  metadata:
    name: melon-network-policy
  spec:
    podSelector: 
      matchLabels:
        app: secure-app
    policyTypes:
    - Ingress
    - Egress
    ingress:
    - from:
      - podSelector:
          matchLabels:
            allow-access: "true"
      ports: 
      - protocol : TCP
        port : 80
      egress: 
      - to: 
        - podSelector:
            matchLabels:
              allow-access: "true"
        ports:
        - protocol: TCP
          port: 80
 ```

For some default policy : 

Deny all ingress

```yaml
  apiVersion: networking.k8s.io/v1
  kind: NetworkPolicy
  metadata:
    name: default-deny
  spec:
    podSelector: {}
    policyTypes:
    - Ingress
 ```
 
 Allow all ingress :
 
 ```yaml
  apiVersion: networking.k8s.io/v1
  kind: NetworkPolicy
  metadata:
    name: allow-all
  spec:
    podSelector: {}
    policyTypes:
    - Ingress
    ingress:
    - {}
 
 ```

 ## About Selectors 

There are multiple types of selectors:

- **podSelector** matches traffic from/to pods which match the selector
- **namespaceSelector** matches traffic from/to pods within namespaces which match the selector.  Note that when podSelector and namespaceSelector are both present, the matching pods must also be within a matching namespace.
- **ipBlock** specifies a CIDR range of IPs that will match the rule. This is mostly used for traffic from/to outside the cluster. You can also specify exceptions to the reange using except. 


## Play 2 :  DNS in Kubernetes 

CoreDNS in Kubernetes
From the version 1.12 the recommended DNS Server is CoreDNS. CoreDNS uses a file named Corefile located at /etc/coredns ( the full path is /etc/coredns/Corefile ), within this file you have a number of plugins configured. 

For the pod remember we talked about a record being created for each Pod by converting their IPs into a dashed format that's disabled by default. It is forwarded to the nameserver specified in the coredns pods /etc/resolv.conf file, which is set to use the nameserver from the Kubernetes node.  Also note that this core file is passed into the pods has a configMap object, you can modify it by using the following command : 

     kubectl get configmap -n kube-system

Every DNS records in CoreDNS falls in **cluster.local** domain, it usually looks like :

For the service :
     servicename.namespace.svc.cluster.local


For the pod is to convert their IPs into a dash format. 

When we deployed CoreDNS solution, it also creates a service to make it available to other components within the cluster which is kube-dns. The IP address of this service is configured as name server on pods

    kubectl get svc kube-dns -n kube-system

The DNS configurations on Pods are done by Kubernetes automatically when the pods are created. Want to guess which Kubernetes component is responsible for that ? The kubelet. You can check the following to find the IP of cluster DNS server and domain in it : 
      
    cat /var/lib/kubelet/config.yaml

Once the pods are configured with the right nameserver, you can now resolve other pods and services.  

Checking if DNS is actually working:

    host webservice 

It will return the full qualified domain name of the web service which is happen to have the following output :

    web-service.default.svc.cluster.local has address xxx.xxx.xxx.xxx


    kubectl exec -ti busybox -- nslookup nginx

So the resolv.conf file helps you resolve the naming even though you didn't ask for full name, as this file has a search entry which is set to default.svc.cluster.local, as well as svc.cluster.local and cluster.local. This allows to find any name for the **service**, not for the pod though. For the Pod you'll still need the FQDN.  

    kubectl exec busybox cat /etc/resolv.conf


### References 

https://github.com/kubernetes/dns/blob/master/docs/specification.md

https://coredns.io/plugins/kubernetes/


## Play 3 :  Ingress in Kubernetes

Ingress Controller : Reverse proxy product such as Nginx, HAProxy, Treafik, Contour etc deployed in Kubernetes cluster and configure them to route traffic to other services which involved defining URL Routes, SSL certificate, load balancing etc ( a set of rules as Ingress resources ).  Service mesh solution such as Linkerd and Istio also providing capabilities similarily to Ingress Controller. 

Remember a Kubernetes cluster does not come with an Ingress Controller by default. An Ingress Controller will be able to do things like the following : 
- Route users to a simple application
- URL-based routing such as different pages for the same web application ( example : www.cloud-melon.com/contacts and www.cloud-melon.com/aboutme )
- Based on domain name itself to route specific users ( such as blog.cloud-melon.com and code.cloud-melon.com )

All those will fully depend on your backend configuration of the ingress controller. 


### NGINX as Ingress Controller in its simplest form

For NGINX, You can check the following yaml file to know how it works, it is actually defined as an Deployment :

https://github.com/kubernetes/ingress-nginx/blob/master/docs/examples/static-ip/nginx-ingress-controller.yaml


In addtion, you need some other object to help you succeed : 
- As best practice, if you configure a ConfigMap object to pass it in if you need to change any configurations. 
- A service account ( Auth ) with right set of permissions ( correct cluster role,  role and rolebindings ) are also needed as Ingress controller has some addtional build-in intelligence, which makes sense why it might need some addtional permission. 
- A service to expose the ingress controller is also needed.




