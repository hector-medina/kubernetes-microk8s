# MICROK8S (KUBERNETES)

This repository explains how to install, setup and use [microk8s](https://microk8s.io/) a minimal kubernetes cluster orquestator.

## Dependencies.

The following dependencies are required to have microk8s up and running:

- Operating System: Ubuntu v21.10.
- Infrastructure: Raspberry Pi 4 8GB ARM64.
- Domain: Public domain available for TLS encryption.

## 1. installation and setup.

1. ***Install extra moduls for Raspberry Pi***

As we are using a Raspberry Pi as cluster node, we should install additional moduls, otherwise containers deployed will not be able to be created.

````
sudo apt install linux-modules-extra-raspi
````

We also need to enable memory groups in the Raspberry Pi by editing the next config file:

````
sudo nano /boot/firmware/cmdline.txt
````

We should add ``cgroup_enable=memory cgroup_memory=1`` and reboot.

2. ***Install microk8s***

For Raspberry Pi we should install edge version:

````
sudo snap install microk8s --classic --edge
````

For others systems classic version is okay:

````
sudo snap install microk8s --classic
````

2. ***Provide permissions to microk8s***

````
sudo usermod -a -G microk8s ubuntu
sudo chown -f -R ubuntu ~/.kube
newgrp microk8s
````

3. ***Allow communication between PODs.***

````
sudo ufw allow in on cni0 && sudo ufw allow out on cni0
sudo ufw default allow routed
````
5. ***Enable certain plugins.***

````
microk8s enable dns dashboard storage ingress
````

6. ***Enable TLS***

To enable https we should setup cert-manager first, this way we could get let's encrypt certificates automatically to deploy segure services. Once we setup this, we should be able to user cert-manager for managing certificates in our services easily.

````
microk8s kubectl create namespace cert-manager
microk8s kubectl apply -f https://github.com/jetstack/cert-manager/releases/download/v1.7.0/cert-manager.yaml
````

7. ***Reboot***

Once all these steps have been performed, we should reboot to asure everything is working as expected.

````
sudo reboot
````

8. ***Install additional storage***

It is recommended to save all persistent volumes in a HDD, to do that you should configure your Raspberry Pi to mount disks automatically.

First, get a list of all your devices connected to the server.

````
sudo lsblk -o NAME,FSTYPE,SIZE,MOUNTPOINT,LABEL,UUID
````

You should see the UUID of your disk, which is a unique identifier of it. Go copy it and paste this along the following settings in ``/etc/fstab`` (I asume your UUID is EC0EC3860EC347F2)

````
UUID EC0EC3860EC347F2 /media/sda1 ntfs-3g auto,users,permissions 0 0
````

To make sure everything is okay, reboot your system and you should be able to see your new disk in ``/media/sda1`` path.
