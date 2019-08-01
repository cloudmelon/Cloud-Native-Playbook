# Part 3 : Networking

### 1. Kube-DNS :

Kube-dns is a (kube-system) pod containing three container instances: kubedns, dnsmasq, and sidecar. This service is instrumental for kubernetes to function. Cluster-info shows the primary dns endpoint.
```
[root@1716bb9df96b ~]# kubectl cluster-info
Kubernetes master is running at https://api.k8s.myhost.net
KubeDNS is running at https://api.k8s.myhost.net/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy
```
Verify another way:
```
kubectl get pods --namespace=kube-system -l k8s-app=kube-dns
```
To have a look at the logs of each container do this:
```
kubectl logs kube-dns-xxxx -n kube-system -c kubedns
NAME                        READY     STATUS    RESTARTS   AGE
kube-dns-7f56f9f8c7-9xcqt   3/3       Running   0          1h
kube-dns-7f56f9f8c7-c426r   3/3       Running   0          1h
```
You specify the -c to grab the logs of a specfic container instance inside the pod.

### What is Kubernetes DNS
The kube-dns pod basically watches the API server for Pods and Services that spin up. It uses SkyDNS to then serve up queries to those pods an services. It also uses DNSMasq to caching these requests. When a pods spins up, the kube-dns overrides their local /etc/resolv.conf files so that they point only to the proxy. 

## Investigating kube-dns pod and resolv.conf
```
[root@1716bb9df96b ~]# kubectl get svc kube-dns -n kube-system
NAME       TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)         AGE
kube-dns   ClusterIP   100.64.0.10   <none>        53/UDP,53/TCP   52m
[root@1716bb9df96b ~]# kubectl exec busybox cat /etc/resolv.conf
nameserver 100.64.0.10
search default.svc.cluster.local svc.cluster.local cluster.local google.internal
options ndots:5
```
### Checking if DNS is actually working:
Assumes the existence of basic nginx and busybox deployments.
```
kubectl exec -ti busybox -- nslookup nginx
Server:    100.64.0.10
Address 1: 100.64.0.10 kube-dns.kube-system.svc.cluster.local

Name:      nginx
Address 1: 100.67.79.160 nginx.default.svc.cluster.local
```



### Network Policy 

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
 
 Allow all egress :
 
 ```yaml
  apiVersion: networking.k8s.io/v1
  kind: NetworkPolicy
  metadata:
    name: allow-all
  spec:
    podSelector: {}
    ingress:
    - {}
    policyTypes:
    - Ingress
 ```
