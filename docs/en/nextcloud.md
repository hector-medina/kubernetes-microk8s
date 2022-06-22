# NEXTCLOUD OVER KUBERNETES.

This repository contains a private cloud service deployed in Kubernetes.

## Dependencias.

For running this service you need the next dependencies:
- Fully configurated microk8s cluster with ingress controller enabled.
- Cert Manager for managing the TLS certificates with *let's encrypt*.
- A domain pointing to your public IP.

## Structure.

In the *nextcloud* folder you can find the next structure:

- **00. Namespace***: It contains the script for creating a namespace in the k8s cluster.
- **01. Secrets**: It contains the secrets for running the app, in particular it just create the TLS certificate using cert manager for *let's encrypt*.
- **02. Volumes**: It creates persistant volumes. One for nextcloud config and data, and another one for MariaDB config and data.
- **03. Database**: It creates database pod and service.
- **04. Server**: It creates the server pod, service and ingress controller.

## Installation.

For running nextcloud over kubernetes you have to follow the next steps:

### Clone this repository.

````
git clone git@github.com:hector-medina/kubernetes-nextcloud.git
cd kubernetes-nextcloud
````

### Create a .env file.

An .env file it's gonna be used for providing basic settings to the pods, so you will need to modify the /kubernetes-microk8s/apps/nextcloud/.env file with your custom settings (password included)

### Create *nextclour* namespace.

````
microk8s kubectl apply -f nextcloud/00.\ Namespace/
````

### Create secrets.

````
microk8s kubectl apply -f nextcloud/01.\ Secrets/
````
### Create volumes.

````
microk8s kubectl apply -f nextcloud/02.\ Volumes/
````

### Run database.

````
microk8s kubectl apply -f nextcloud/03.\ Database/
````

### Run server.

This last step takes 5 to 10 minutes to perform because resources have been limited to 512Mb. If you want to speed it up you have update de "nextcloud/04. Server/server-deployment.yml" file and upgrade resources.

````
microk8s kubectl apply -f nextcloud/04.\ Server/
````

