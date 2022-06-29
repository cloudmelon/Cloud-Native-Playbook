# Ultimate Kubernetes Playbook

This repostitory recap all useful kubectl commands and explainations for core concepts and API primitives of Kubernetes. 

- [00 - Shortcuts](https://github.com/cloudmelon/melonkube/blob/master/00%20-%20Shortcuts.md)
- [01 - Pod design](https://github.com/cloudmelon/melonkube/blob/master/01%20-%20Pod%20design.md)
- [02 - Rolling updates & rollbacks](https://github.com/cloudmelon/melonkube/blob/master/02%20-%20Rolling%20updates%20and%20rollbacks.md)
- [03 - Networking](https://github.com/cloudmelon/melonkube/blob/master/03%20-%20Networking.md)
- [04 - ETCD](https://github.com/cloudmelon/melonkube/blob/master/04%20-%20ETCD.md)
- [05 - Volumes](https://github.com/cloudmelon/melonkube/blob/master/05%20-%20Volumes.md)
- [06 - Observability](https://github.com/cloudmelon/melonkube/blob/master/06%20-%20Observability.md)
- [07 - ConfigMap](https://github.com/cloudmelon/melonkube/blob/master/07%20-%20ConfigMap.md)
- [08 - Services](https://github.com/cloudmelon/melonkube/blob/master/08%20-%20Services.md)
- [09 - Security](https://github.com/cloudmelon/melonkube/blob/master/09%20-%20Security.md)

By cloudmelon

## Set up K8S with Minikube 

You can follow [my article : Playbook Before Part 0 : Minikube setting up in Azure VM ](https://github.com/cloudmelon/melonkube/blob/master/How%20to%20deploy%20K8S%20with%20Minikube%20in%20Azure%20VM.md) article to know about how to install Kubernetes with minikube on a single VM sits in Microsoft Azure.

Find [the article : Playbook Before Part 0 : How to deploy NGINX Ingress in Minikube]https://kubernetes.io/docs/tasks/access-application-cluster/ingress-minikube/) article to know about how to install Kubernetes with minikube on a single VM sits in Microsoft Azure.

## Set up Azure Kubernetes service 
This repository contains 2 bash scripts : 
- **deploy-cni-aks.sh** : You can use it to deploy AKS cluster using CNI networking, it fits the use case that you need to deploy AKS cluster with CNI networking plugin for integration with existing virtual networks in Azure, and this network model allows greater separation of resources and controls in an enterprise environment.

- **deploy-kubenet-aks.sh** : You can use it to deploy AKS cluster using kubenet networking, it fits the use case that you need to deploy AKS cluster with kubenet networking. Kubenet is a basic network plugin, on Linux only. AKS cluster by default is on kubenet networking, after provisioning it, it also creates an Azure virtual network and a subnet, where your nodes get an IP address from the subnet and all pods receive an IP address from a logically different address space to the subnet of the nodes. 


### deploy-cni-aks.sh

1. Download the script on the location that you are planning to use for the deployment

``` bash
curl --output deploy-cni-aks.sh https://raw.githubusercontent.com/cloudmelon/melonkube/azure-kubernetes-service/platform-ops/deploy-cni-aks.sh
```

2. Make the script executable

``` bash
chmod +x deploy-cni-aks.sh
```

3. Run the script (make sure you are running with sudo)

``` bash
sudo ./deploy-cni-aks.sh
```

### deploy-kubenet-aks.sh

1. Download the script on the location that you are planning to use for the deployment

``` bash
curl --output deploy-kubenet-aks.sh https://raw.githubusercontent.com/cloudmelon/melonkube/azure-kubernetes-service/platform-ops/scripts/deploy-kubenet-aks.sh
```

2. Make the script executable

``` bash
chmod +x deploy-kubenet-aks.sh
```

3. Run the script (make sure you are running with sudo)

``` bash
sudo ./deploy-kubenet-aks.sh
```

## Set up a private Azure Kubernetes service 

This repository contains 2 bash scripts : 

- **deploy-private-aks.sh** : You can use it to deploy private AKS cluster with private endpoint, it fits the use case that you need to deploy AKS private cluster.

- **deploy-private-aks-udr.sh** : You can use it to deploy private AKS cluster with private endpoint, it fits the use case that you need to deploy AKS private cluster and limit egress traffic with UDR ( User-defined Routes ). 


### deploy-private-aks.sh

1. Download the script on the location that you are planning to use for the deployment

``` bash
curl --output deploy-private-aks.sh https://raw.githubusercontent.com/cloudmelon/melonkube/azure-kubernetes-service/private-aks/scripts/deploy-private-aks.sh
```

2. Make the script executable

``` bash
chmod +x deploy-private-aks.sh
```

3. Run the script (make sure you are running with sudo)

``` bash
sudo ./deploy-private-aks.sh
```

### deploy-private-aks-udr.sh

1. Download the script on the location that you are planning to use for the deployment

``` bash
curl --output deploy-private-aks-udr.sh https://raw.githubusercontent.com/cloudmelon/melonkube/azure-kubernetes-service/private-aks/scripts/deploy-private-aks-udr.sh
```

2. Make the script executable

``` bash
chmod +x deploy-private-aks-udr.sh
```

3. Run the script (make sure you are running with sudo)

``` bash
sudo ./deploy-private-aks-udr.sh
```



## Kubernetes Useful references : 

- Kubernetes By Example : 
  http://kubernetesbyexample.com/  
  
- Mastering Kubernetes By doing AKS Workshop : 
  https://aksworkshop.io/

- Kubernetes the hard way on Azure : 
  https://github.com/ivanfioravanti/kubernetes-the-hard-way-on-azure/tree/master/docs


## Azure Kubernetes services ( AKS ) useful references : 

- AKS Current preview features: https://aka.ms/aks/previewfeatures
- AKS Release notes: https://aka.ms/aks/releasenotes
- AKS Public roadmap: http://aka.ms/aks/roadmap
- AKS Known-issues: https://aka.ms/aks/knownissues

## Interested in deploying Master-Slave architecture clustering solution on Azure ?

Please check it out : 
https://azure.microsoft.com/en-us/resources/templates/201-vmss-master-slave-customscript/

## More details on my blog : 

Please go to my blog cloud-melon.com to get more details about how to implement this solution and more about Microsoft Azure ( ref link : https://cloud-melon.com )

Feel free to reach out to my twitter **@MelonyQ** for more details ( https://twitter.com/MelonyQ ). 
