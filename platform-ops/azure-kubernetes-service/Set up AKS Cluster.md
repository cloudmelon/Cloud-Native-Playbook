# Deploy BDC on Azure Kubernetes Service cluster


This repository contains the scripts that you can use to deploy a BDC cluster on Azure Kubernetes Service (AKS)  cluster with basic networking ( Kubenet ) and advanced networking ( CNI ). 

This repository contains 2 bash scripts : 
- **deploy-cni-aks.sh** : You can use it to deploy AKS cluster using CNI networking, it fits the use case that you need to deploy AKS cluster with CNI networking plugin for integration with existing virtual networks in Azure, and this network model allows greater separation of resources and controls in an enterprise environment.

- **deploy-kubenet-aks.sh** : You can use it to deploy AKS cluster using kubenet networking, it fits the use case that you need to deploy AKS cluster with kubenet networking. Kubenet is a basic network plugin, on Linux only. AKS cluster by default is on kubenet networking, after provisioning it, it also creates an Azure virtual network and a subnet, where your nodes get an IP address from the subnet and all pods receive an IP address from a logically different address space to the subnet of the nodes. 



## Instructions

### deploy-cni-aks.sh

1. Download the script on the location that you are planning to use for the deployment

``` bash
curl --output deploy-cni-aks.sh https://raw.githubusercontent.com/cloudmelon/Cloud-Native-Playbook/platform-ops/azure-kubernetes-service/deploy-cni-aks.sh
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
curl --output deploy-kubenet-aks.sh https://raw.githubusercontent.com/cloudmelon/Cloud-Native-Playbook/platform-ops/azure-kubernetes-service/scripts/deploy-kubenet-aks.sh
```

2. Make the script executable

``` bash
chmod +x deploy-kubenet-aks.sh
```

3. Run the script (make sure you are running with sudo)

``` bash
sudo ./deploy-kubenet-aks.sh
```