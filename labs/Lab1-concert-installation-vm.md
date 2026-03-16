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
ssh -i ~/Downloads/pem_ibmcloudvsi_download.pem -p 2223 itzuser@149.81.13.54
```

2. Change the umask in .bashrc file

```bash
echo "umask 022" >> $HOME/.bashrc
source $HOME/.bashrc
```

5. Start the installation

```bash
loginctl enable-linger itzuser
cd /mnt/concert
wget https://github.com/IBM/Concert/releases/download/v2.3.1/ibm-concert-x86.tar.gz
umask 0022
tar xfz ibm-concert-x86.tar.gz
```
When installing Concert Workflows on a VM, ensure that IPv6 is enabled on your operating system. You can verify that IPv6 is active by using the following command. A value of 0 confirms that IPv6 is enabled.


```bash
cat /proc/sys/net/ipv6/conf/all/disable_ipv6
```
Export install directory.

```bash
export INSTALL_DIR=/mnt/concert/ibm-concert
cd $INSTALL_DIR
```
Set up the environment variables to enable authentication with the IBM Container Registry for installation: 

```bash
export DOCKER_EXE=<podman|docker> 
export IBM_REGISTRY=cp.icr.io/cp
export IBM_REGISTRY_USER=cp
export IBM_REGISTRY_PASSWORD=eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJJQk0gTWFya2V0cGxhY2UiLCJpYXQiOjE3NzM2NDU3NTQsImp0aSI6ImQ2ZmM1ODk1YjA2YzQzNjNhNjcwZjE4MjgzZmExNzhhIn0.PCQKeFDulyE9CFFedGfJEqV3h-lPFtq9YxQGE1WM3kY
```

## Configure the params.ini file

6.Copy the required parameters from the sample-params file as per your installation type.
```bash
cp $INSTALL_DIR/etc/sample-params/concert-dataapps-workflows-vm-quickstart-params.ini $INSTALL_DIR/etc/params.ini
```
Open and edit the $INSTALL_DIR/etc/params.ini file with required parameters.
```bash
vi $INSTALL_DIR/etc/params.ini
```

Configure the params.ini

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

Install concert

7. Run a Docker or Podman log in to authenticate with the source image registry.
```bash
${DOCKER_EXE} login ${IBM_REGISTRY} --username=${IBM_REGISTRY_USER} --password=${IBM_REGISTRY_PASSWORD}
```
Run the bin/setup script to prepare your machine. Set up the environment variables to enable authentication with the IBM Container Registry for installation: 

```bash
$INSTALL_DIR/bin/setup --license_acceptance=y --username=<user> --password=<password> --registry_password=${IBM_REGISTRY_PASSWORD}
```


- **IBM_REGISTRY_PASSWORD**: your [entitlement key](https://www.ibm.com/docs/en/concert?topic=concert-obtaining-entitlement-api-key) surrounded by double quotes


The installation take 5 to 7 minutes, be patient.    

1.  Connect on Concert and create an API Key

In the following steps, **YOUR_VM_PUBLIC_IP** is the public IP defined in your Techzone reservation

- From a browser go to the Concert URL (https://YOUR_VM_PUBLIC_IP.nip.io:12443)
- Log on concert using **ibmconcert** as user and with the password you have specified in step 4.
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

Save the file (:wq) and source the $HOME/env.sh file to set environment variables

```bash
source $HOME/env.sh
```

2. Apply the watsonx.ai configuration

```bash
cd $INSTALL_DIR
echo "WATSONX_API_KEY=$WATSONX_API_KEY" >> ibm-concert-std/etc/local_config.env
echo "WATSONX_API_PROJECT_ID=$WATSONX_API_PROJECT_ID" >> ibm-concert-std/etc/local_config.env
echo "WATSONX_API_URL=$WATSONX_API_URL" >> ibm-concert-std/etc/local_config.env
```

3. Then you need to start the appropriate service:

```bash
cd $INSTALL_DIR
ibm-concert-std/bin/start_service ibm-roja-py-utils
```

4. Test the integration 

To verify that the integration with watsonx.ai is successfull, you can look at the **ibm-roja-py-utils** pod logs:

```bash
podman logs ibm-roja-py-utils
```

The 2 first lines of the logs should be:

```txt
{'timestamp': 2025-04-13:13:45:15, 'logLevel': info, 'callerMethod': client.py:L459, 'message': Client successfully initialized}
{'timestamp': 2025-04-13:13:45:15, 'logLevel': info, 'callerMethod': genai.py:L129, 'message': Connection to watsonx.ai successful!}
```

