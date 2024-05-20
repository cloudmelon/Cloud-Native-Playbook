# Deploy private AKS cluster with Advanced Networking (CNI)

This repository contains the scripts that you can use to deploy a Azure Kubernetes Service (AKS) private cluster with advanced networking ( CNI ). 

This repository contains 2 bash scripts : 

- **deploy-private-aks.sh** : You can use it to deploy private AKS cluster with private endpoint, it fits the use case that you need to deploy AKS private cluster.

- **deploy-private-aks-udr.sh** : You can use it to deploy private AKS cluster with private endpoint, it fits the use case that you need to deploy AKS private cluster and limit egress traffic with UDR ( User-defined Routes ). 


## Instructions

### deploy-private-aks.sh

1. Download the script on the location that you are planning to use for the deployment

``` bash
curl --output deploy-private-aks.sh https://raw.githubusercontent.com/cloudmelon/Cloud-Native-Playbook/platform-ops/azure-kubernetes-service/private-aks/scripts/deploy-private-aks.sh
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
curl --output deploy-private-aks-udr.sh https://raw.githubusercontent.com/cloudmelon/Cloud-Native-Playbook/platform-ops/azure-kubernetes-service/private-aks/scripts/deploy-private-aks-udr.sh
```

2. Make the script executable

``` bash
chmod +x deploy-private-aks-udr.sh
```

3. Run the script (make sure you are running with sudo)

``` bash
sudo ./deploy-private-aks-udr.sh
```



