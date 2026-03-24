# IBM Concert, Concert Workflows, and Concert Data Apps installation on a virtual machine

## Objective

In this lab, you will install IBM Concert on a standalone server.

## Prerequisite

- An virtual machine must have been provisionned on Techzone and configured as explained in [Lab0](./Lab0-setup.md)

## Content

- [IBM Concert installation on a virtual machine](#ibm-concert-installation-on-a-virtual-machine)
  - [Objective](#objective)
  - [Prerequisite](#prerequisite)
  - [Content](#content)
  - [I - Installing IBM Concert on a VM](#i---installing-ibm-concert-on-a-vm)
  - [II - Watsonx.ai integration](#ii---watsonxai-integration)
    - [Techzone reservation](#techzone-reservation)
    - [Configure watsonx.ai in IBM Concert](#configure-watsonxai-in-ibm-concert)
  
## I - Installing IBM Concert on a VM

> Official documentation [VM installation](https://www.ibm.com/docs/en/concert/2.0.0?topic=vm-installing-concert-software)

1. Connect on the machine you have provisioned on Techzone in Lab0

IMPORTANT: if you are already logged on the VM, verify that you are connected as itzuser (not root).   

```bash
ssh -i ~/Downloads/pem_ibmcloudvsi_download.pem -p 2223 itzuser@149.81.15.26
```

2. Once connected, set the default file creation mask to ensure proper permissions for files you create

```bash
echo "umask 022" >> $HOME/.bashrc
source $HOME/.bashrc
```
Start the installation
```bash
loginctl enable-linger itzuser
cd /mnt/concert
 wget https://github.com/IBM/Concert/releases/download/v2.3.1/ibm-concert-x86.tar.gz
umask 0022
tar xfz ibm-concert-x86.tar.gz
```

3. Check if IPv6 is disabled (some IBM Concert components may require it):

```bash
cat /proc/sys/net/ipv6/conf/all/disable_ipv6
```
2. Set Environment Variables

```bash
export KUBECONFIG=/etc/rancher/k3s/k3s.yaml
export XDG_RUNTIME_DIR="/run/user/$UID"
export DBUS_SESSION_BUS_ADDRESS="unix:path=${XDG_RUNTIME_DIR}/bus"
export INSTALL_DIR=/mnt/concert/ibm-concert
cd $INSTALL_DIR
source ~/.bashrc
```

3. Install K3s (Lightweight Kubernetes)
   Download and install K3s, specifying the version and disabling Traefik if you plan to manage ingress differently:
   
```bash
curl -sfL https://get.k3s.io | sudo INSTALL_K3S_VERSION=v1.33.4+k3s1 sh -s - --write-kubeconfig-mode 644 --disable traefik
```
Add the kubeconfig to your bash profile for easier kubectl usage:



```bash
echo "export KUBECONFIG=/etc/rancher/k3s/k3s.yaml" >> ~/.bashrc
source ~/.bashrc
```
Verify your K3s cluster is running:
```bash
kubectl cluster-info
kubectl get nodes
```
You should see your VM listed as a ready node.

4. Install Helm (Package Manager for Kubernetes)
Helm allows you to install and manage Kubernetes applications easily. Install Helm on the VM:
```bash
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
chmod +x get_helm.sh
./get_helm.sh
helm version
```
5. Configure Docker or Podman
IBM Concert uses container images stored in IBM’s registry. Configure Podman as the container runtime:
```bash
export DOCKER_EXE=podman
export IBM_REGISTRY=cp.icr.io/cp
export IBM_REGISTRY_USER=cp
export IBM_REGISTRY_PASSWORD=eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJJQk0gTWFya2V0cGxhY2UiLCJpYXQiOjE3NzM2NDU3NTQsImp0aSI6ImQ2ZmM1ODk1YjA2YzQzNjNhNjcwZjE4MjgzZmExNzhhIn0.PCQKeFDulyE9CFFedGfJEqV3h-lPFtq9YxQGE1WM3kY
```
Configure the Concert parameter file

```bash
cp $INSTALL_DIR/etc/sample-params/concert-dataapps-workflows-vm-quickstart-params.ini $INSTALL_DIR/etc/params.ini
```
6. Prepare Installation Parameters
Copy the sample parameters file for VM quickstart to the main params file:

```bash
vi $INSTALL_DIR/etc/params.ini
```

- **IBM_REGISTRY_PASSWORD**: your [entitlement key](https://www.ibm.com/docs/en/concert?topic=concert-obtaining-entitlement-api-key) surrounded by double quotes

 Entitlement key: eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJJQk0gTWFya2V0cGxhY2UiLCJpYXQiOjE3NzM2NDU3NTQsImp0aSI6ImQ2ZmM1ODk1YjA2YzQzNjNhNjcwZjE4MjgzZmExNzhhIn0.PCQKeFDulyE9CFFedGfJEqV3h-lPFtq9YxQGE1WM3kY

```bash
DOCKER_EXE=podman
INSTALL_VM=true

# ----- Hub Configuration -----

# Registry users
REG_USER=cp
IMAGE_REGISTRY_PREFIX=cp.icr.io/cp
HUB_IMAGE_REGISTRY_SUFFIX=/solis-hub
REG_PASS=eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJJQk0gTWFya2V0cGxhY2UiLCJpYXQiOjE3NzM2NDU3NTQsImp0aSI6ImQ2ZmM1ODk1YjA2YzQzNjNhNjcwZjE4MjgzZmExNzhhIn0.PCQKeFDulyE9CFFedGfJEqV3h-lPFtq9YxQGE1WM3kY

# ----- Concert Configuration -----

INSTALL_CONCERT=true
CONCERT_IMAGE_REGISTRY_SUFFIX=/concert

# ----- Data Apps Configuration -----

INSTALL_DATAAPPS=true
DATAAPPS_IMAGE_REGISTRY_SUFFIX=/concert

# ----- Concert Workflow Configuration -----

INSTALL_WORKFLOWS=true
# Fully Qualified Domain Name (FQDN) of your VM
# Example: test-vm.my-domain.com  
WORKFLOWS_INSTANCE_ADDRESS=149.81.13.54.nip.io
```
Log in to the IBM container registry:

```bash
${DOCKER_EXE} login ${IBM_REGISTRY} --username=${IBM_REGISTRY_USER} --password=${IBM_REGISTRY_PASSWORD}
```


7.Run IBM Concert Setup
Run the installation script with license acceptance, user credentials, and registry password:
```bash
$INSTALL_DIR/bin/setup --license_acceptance=y --username=concertuser --password=concert2403! --registry_password=${IBM_REGISTRY_PASSWORD}
```
After the installation:
The URL for IBM Concert will be provided:

```bash
https://149.81.15.26.nip.io:12443
```


Connect on Concert and create an API Key

In the following steps, **YOUR_VM_PUBLIC_IP** is the public IP defined in your Techzone reservation

- From a browser go to the Concert URL (https://YOUR_VM_PUBLIC_IP.nip.io:12443)
- Open the Concert Workflows UI by entering https://<VM_FQDN>:443/workflows/
- Log on concert using **concertuser** as user and with the password you have specified in step 4.
- Click the circle at top right of the window and select **API Key**
  <br><img src="../images/concert_apikey_vm2.0_1.png" alt="drawing" width="400"/>
- In the API Key window, click **Generate API Key**
  <br><img src="../images/concert_apikey_2.png" alt="drawing" width="400"/>
- Copy the API key generated in a safe place

## II - Watsonx.ai integration

### Techzone reservation

Be sure to reserve a watsonx.ai instance as explained in [Lab 0 - III - Provision a watsonx.ai on techzone](Lab0-setup.md#iii---provision-a-watsonxai-on-techzone).

### Configure watsonx.ai in IBM Concert

The watsonx.ai integration is simply done through setting some config parameters in the config files of IBM concert.  

1. You will need to update the $HOME/env.sh file.
```bash
vim $HOME/env.sh

```

Update the following variables:
- **WATSONX_API_KEY**: use the API key you got in [Lab 0 - Get API Key and service ID information](Lab0-setup.md#get-api-key-and-service-id-information), from your techzone wx.ai reservation page
- **WATSONX_API_PROJECT_ID**: use the project ID you got from [Lab 0 - Create a watsonx project and get project ID](Lab0-setup.md#create-a-watsonx-project-and-get-project-id),
- **WATSONX_API_URL**: https://us-south.ml.cloud.ibm.com , since the instance is provision in US.
  
```bash
WATSONX_API_KEY="BxjhwO7okm1T-dpERmm7pXH6s87kTE6Xwzs3gtfGCF2v"
WATSONX_API_PROJECT_ID="9a6c38a6-9f83-49b0-9c33-b30b162a9393"
WATSONX_API_URL="https://us-south.ml.cloud.ibm.com"
```
Add the Watsonx.ai configuration to the local environment:

```bash
cd $INSTALL_DIR
echo "WATSONX_API_KEY=$WATSONX_API_KEY" >> ibm-concert-std/etc/local_config.env
echo "WATSONX_API_PROJECT_ID=$WATSONX_API_PROJECT_ID" >> ibm-concert-std/etc/local_config.env
echo "WATSONX_API_URL=$WATSONX_API_URL" >> ibm-concert-std/etc/local_config.env
```

Save the file (:wq) and source the $HOME/env.sh file to set environment variables

source $HOME/env.sh

Start the Watsonx.ai utility service:

```bash
ibm-concert-std/bin/start_service ibm-roja-py-utils
```

Test the integration 

To verify that the integration with watsonx.ai is successfull, you can look at the **ibm-roja-py-utils** pod logs:

```bash
podman logs ibm-roja-py-utils
```

Expected log messages:
```txt
{'timestamp': 2025-04-13:13:45:15, 'logLevel': info, 'callerMethod': client.py:L459, 'message': Client successfully initialized}
{'timestamp': 2025-04-13:13:45:15, 'logLevel': info, 'callerMethod': genai.py:L129, 'message': Connection to watsonx.ai successful!}
```

