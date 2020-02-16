# Playbook Before Part 0 :  Minikube setting up in Azure VM

##  Prerequsities

Update current packages of the system to the latest version.

    sudo apt update
    sudo apt upgrade

## Install VirtualBox on Ubuntu 18.04

Import the Oracle public key to your system signed the Debian packages using the following commands.

    wget -q https://www.virtualbox.org/download/oracle_vbox_2016.asc -O- | sudo apt-key add -
    wget -q https://www.virtualbox.org/download/oracle_vbox.asc -O- | sudo apt-key add -

you need to add Oracle VirtualBox PPA to Ubuntu system. You can do this by running the below command on your system.

    sudo add-apt-repository "deb http://download.virtualbox.org/virtualbox/debian bionic contrib"

Update VirtualBox using the following commands :
   
    sudo apt update
    sudo apt install virtualbox-6.0

## Install Minikube

Download a stand-alone binary and use the following command : 

    curl -Lo minikube https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64 \
  && chmod +x minikube


Add Minikube to your path 

    sudo mkdir -p /usr/local/bin/
    sudo install minikube /usr/local/bin/

## start Minikube

    minikube start --vm-driver=virtualbox

You can use the following command to check if Minikube works well :

     minikube status

## Check Minikube