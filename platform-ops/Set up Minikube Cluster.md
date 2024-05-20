# Playbook Before Part 0 :  Set up Minikube 

##  Pre-read
1. If you get started on Kubernetes in 2024 and want to set up your local minikube cluster or playground, check out [this post](https://cloudmelonvision.com/if-i-were-about-to-get-started-on-kubernetes-in-2024/)
2. Kubernetes cluster architecture and key concepts, check out [this post](https://cloudmelonvision.com/what-is-kubernetes-really-about/)

##  Prerequsities

### Set up K8S with Minikube 

You can follow [my article : Playbook Before Part 0 : Minikube setting up in Azure VM ](https://github.com/cloudmelon/Cloud-Native-Playbook/blob/master/platform-ops/Set%20up%20Minikube%20Cluster.md) article to know about how to install Kubernetes with minikube on a single VM sits in Microsoft Azure.

Find [the article : Playbook Before Part 0 : How to deploy NGINX Ingress in Minikube](https://kubernetes.io/docs/tasks/access-application-cluster/ingress-minikube/) article to know about how to install Kubernetes with minikube on a single VM sits in Microsoft Azure.

In your Azure VM OR AWS EC2 instance :

Update current packages of the system to the latest version.
```
    sudo apt update
    sudo apt upgrade
```
To check if virtualization is supported on Linux, run the following command and verify that the output is non-empty:
```
    grep -E --color 'vmx|svm' /proc/cpuinfo
```

## Install VirtualBox (Option #1)

Import the Oracle public key to your system signed the Debian packages using the following commands.
```
    wget -q https://www.virtualbox.org/download/oracle_vbox_2016.asc -O- | sudo apt-key add -
    wget -q https://www.virtualbox.org/download/oracle_vbox.asc -O- | sudo apt-key add -
```
you need to add Oracle VirtualBox PPA to Ubuntu system. You can do this by running the below command on your system.

```
    sudo add-apt-repository "deb http://download.virtualbox.org/virtualbox/debian bionic contrib"
```
Update VirtualBox using the following commands :
  ``` 
    sudo apt update
    sudo apt install virtualbox-6.0
```
## Install Docker (Option #2)



## Install Kubectl 

Download the latest release and install kubectl binary with curl on Linux:
```
    sudo apt-get update && sudo apt-get install -y apt-transport-https
    curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
    echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee -a /etc/apt/sources.list.d/kubernetes.list
    sudo apt-get update
    sudo apt-get install -y kubectl
```

## Install Minikube

Download a stand-alone binary and use the following command : 
```
    curl -Lo minikube https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64 \
  && chmod +x minikube
```

Add Minikube to your path 
```
    sudo mkdir -p /usr/local/bin/
    sudo install minikube /usr/local/bin/
```
## start Minikube

Situation #1: If you're on virtualbox as vm driver
```
    minikube start --vm-driver=virtualbox
```

While starting the minikube, you can see an output similar to the following :

    <img src="screenshots/Minikubestart.PNG" alt="Minikubestart" width="800px"/>

Situation #2:


You can use the following command to check if Minikube works well :
```
     minikube status
```
You can expect an output similar to the following :

    <img src="screenshots/minikubestatus.PNG" alt="Minikubestatus" width="800px"/>


## Update Minikube

1. Find the current version

```
$ minikube version
minikube version: v1.33.0
```

2. Check if there are newer versions available

```
$ minikube update-check
CurrentVersion: v1.33.0
LatestVersion: v1.33.0
```

3. Update to latest version

```
 curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64 \
   && sudo install minikube-linux-amd64 /usr/local/bin/minikube
```

4. Check new minikube version

```
$ minikube version
minikube version:  v1.33.0
commit: 86fc9d54fca63f295d8737c8eacdbb7987e89c67
```


## Confirmation 

To check out the version of your kubectl, you can use the following command:
    
    kubectl version

You should see the following output:
 
      <img src="screenshots/kubectlversion.png" alt="version" width="800px"/>

To check out the detailed information use the following command:
    
    kubectl cluster-info

You should see the following output:
 
     <img src="screenshots/clusterinfo.PNG" alt="Cluster info" width="800px"/>
