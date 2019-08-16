# Playbook Part 4 : ETCD

ETCD is a distributed reliable key-value store that is simple, secure and fast. The etcd data store stores information regarding the cluster such as the nodes, pods, configs, secrets, accounts, roles, bindings and others. Every information you see when you run the **kubectl get** command is from the etcd server. Every change you make to your cluster, such as adding addtional nodes, deploying pods or replicasets are updated in the etcd server. Only once it is updated in the etcd server, is the change considered to be complete. 

I'd elaborate it further more here. Traditionally, the limit of relational database is everytime a new information needs to be added the entire table is affected and leads to a lot of empty cells. A key-value store stores information in the form of documents or pages so each individual gets a document and all information about that individual is stored with that file, these files can be in any format or structure and changes to one file does not affect the others. Similar documents while you could store and retrieve simple key and values when your data gets complex. You typically end up transacting in data formats like JSON or YAML it's easy to install and get started. 


### Play 1 : Install etcd 

- From scratch:
Download the binary extract and run download all relevant binary of your OS from Github releases pages extracted and run the etcd executable. Find more details on the following : https://github.com/etcd-io/etcd

When you run etcd it starts a service that listens on port 2379 by default. You can then attach any clients to the etcd service to store and retrieve information. The default client that comes with etcd is the **ETCD control client**. The **etcdctl** is a command line client for ETCD. You can use it to store and retrieve key-value pairs. The usage like the following : 

     ./etcdctl set key1 value1

     ./etcdctl get key1

- Via Kubeadm:
If you set up your cluster using kubeadm then kubeadm deploys the ETCD server for your as a **Pod** in the **kube-system** namespace. You can explore the etcd database using the etcdctl utitlity within this pod. 


To list all keys stored by Kubernetes, run the etcdctl get command like the following :
      kubectl exec etcd-master -n kube-system etcdctl get / --prefix -keys-only

When we set up high availability in Kubernetes the only option to note for now is the advertised client url. This is the address on which ETCD listens. It happens to be on the IP of the server an on port **2379** which is the default port on which etcd listens. This is the **URL** that should be configured on the **kube-api** server when it tries to reach the etcd server.

Kubernetes stores data in the specific directory structure the root directory is a registry and under that you have the various Kubernetest constructs such as minions or nodes, pods, replicasts, deployments etc. In the HA environment, you'll have multi-master, then you'll have also multiple etcd instances spread across the master nodes. In that case, make sure to specifiy the ETCD instances know about each other by setting the right parameter in the etcd service configuration. The **initial-cluster** option is where you much specify the different instances of the etcd service. 


### 1. Backup etcd :

 Set environment variable ETCDCTL_API=3 to use v3 API or ETCDCTL_API=2 to use v2 API. If you're using v3 version you can use the following: 

  ETCDCTL_API=3 etcdctl --endpoints http://127.0.0.1:4001 snapshot save snapshotdb --cert-file /xxx --key-file /... --ca-file
    
Ref : https://github.com/etcd-io/etcd/blob/master/Documentation/op-guide/recovery.md