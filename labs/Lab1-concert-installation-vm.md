# Concert installation

- [Concert installation](#concert-installation)
  - [Objective](#objective)
  - [Prerequisite](#prerequisite)
  - [I - Installing IBM Concert on a VM](#i---installing-ibm-concert-on-a-vm)
  - [II - Install IBM Concert workflow ](#ii---install-ibm-concert-workflow-)
    - [Install preprequisites](#install-preprequisites)
    - [Install Concert workflow](#install-concert-workflow)
  - [III - Watsonx.ai integration ](#iii---watsonxai-integration-)
    - [Techzone reservation](#techzone-reservation)
    - [Configure watsonx.ai in IBM Concert](#configure-watsonxai-in-ibm-concert)



## Objective

In this lab, you will install IBM Concert on a standalone server.

## Prerequisite

You must have run before the [Lab0](LabO-setup.md) which explain how to reserve a virtual machine on Techzone.

## I - Installing IBM Concert on a VM
> Add part about adding the disk

You can install concert on a VM or in a kubernetes cluster. In this lab we will do a VM installation.

> Official documentation [VM installation](https://www.ibm.com/docs/en/concert?topic=concert-deploying-virtual-machine-vm)

1. Connect on the machine you have provisioned on Techzone in Lab0

```bash
ssh itzuser@<VM ip address> -p 2223
loginctl enable-linger itzuser
cd /mnt/concert
wget https://github.com/IBM/Concert/releases/download/v1.0.5.2/ibm-concert-std.tgz
tar xfz ibm-concert-std.tgz
```

2. Create a $HOME/env.sh file

```bash
vi $HOME/env.sh
```

Copy Paste the content of [env.sh](../files/env.sh) in this $HOME/env.sh file  
Update the values for the following keys (other keys will be updated later):

- CONCERT_REGISTRY_PASSWORD with your [entitlement key](https://www.ibm.com/docs/en/concert?topic=concert-obtaining-entitlement-api-key)
- CONCERT_HUB_URL with your VM address
- EXT_URL with your VM address

3. Source the $HOME/env.sh file to set environment variables

```bash
source $HOME/env.sh
```

4. Install concert

```bash
${DOCKER_EXE} login ${CONCERT_REGISTRY} --username=${CONCERT_REGISTRY_USER} --password=${CONCERT_REGISTRY_PASSWORD}
ibm-concert-std/bin/setup --license_acceptance=y --registry=${CONCERT_REGISTRY} --runtime=${DOCKER_EXE} --username=ibmconcert --password
```

The installer will prompt for a password.  
This will define the default password for the GUI user `ibmconcert`

5. Connect on Concert and create an API Key

- From a browser go to the Concert route (https://VMaddress:12443)
- Log on concert using **ibmconcert** as user and with the password you have specified in step 4.
- Click the circle at top right of the window and select **API Key**
  <br><img src="../images/concert_apikey_vm_1.png" alt="drawing" width="400"/>
- In the API Key window, click **Generate API Key**
  <br><img src="../images/concert_apikey_2.png" alt="drawing" width="400"/>
- Copy the API key generated in your clipboard

7. Update environment variables

```bash
vi $HOME/env.sh
```

Update the values for the following keys:

- CONCERT_URL with https://VMaddress:12443 (replace VMaddress with you own VM address)
- CONCERT_APIKEY with the API Key you created in step 5.

Save the file (:wq) and source the $HOME/env.sh file to set environment variables

```bash
source $HOME/env.sh
```

## II - Install IBM Concert workflow <a name="cwInstallation"></a>

Concert workflow installation require k3s and helm.

### Install preprequisites

1. Connect to the VM you have created on Techzone in Lab0

```bash
ssh itzuser@<VM ip address> -p 2223
```

2. install k3s

> Offical documentation [k3s installation](https://www.ibm.com/docs/en/rapid-infra-auto/1.1.x?topic=planning-software-requirements#software_requirements__k3s__title__1)

```bash
curl -sfL https://get.k3s.io | sudo INSTALL_K3S_VERSION=v1.29.2+k3s1 sh -s - --write-kubeconfig-mode 644 --disable traefik
```

3. Install Helm

```bash
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
chmod 700 get_helm.sh
./get_helm.sh
sudo chmod 777 /usr/local/bin/helm
```

4. Specify Kubernetes configuration file

```bash
echo "export KUBECONFIG=/etc/rancher/k3s/k3s.yaml" >> ~/.bashrc
source ~/.bashrc
```

### Install Concert workflow

> Official documentation: [Concert Worflow installation](https://www.ibm.com/docs/en/concert?topic=workflows-installing-concert-vm)

1. Connect to the VM you have created on Techzone in Lab0

```bash
ssh itzuser@<VM ip address> -p 2223
```

2. Get concert workflow installation files

```bash
cd /mnt/concert
wget https://github.com/IBM/Concert/releases/download/v1.0.5.2/ibm-concert-std-workflows.tgz
tar xfz ibm-concert-std-workflows.tgz
```

3. Log on docker

```bash
${DOCKER_EXE} login ${CONCERT_REGISTRY} \
--username=${CONCERT_REGISTRY_USER} \
--password=${CONCERT_REGISTRY_PASSWORD}
```

4. Get concert infos and generate CONCERT_HUB_KEY

```bash
cd /mnt/concert/workflows

./bin/tethering/get_concert_info.sh \
--concert-url=$CONCERT_HUB_URL \
--c-api-key=$CONCERT_APIKEY
```

Copy the 3 last lines returned somewhere

5. Update environment variables

```bash
vi $HOME/env.sh
```

Update the values for the following keys:

- CONCERT_HUB_KEY with the value returned by get_concert_info.sh in step 4.
- WORKFLOW_APIKEY with the value returned by get_concert_info.sh in step 4.

Save the file (:wq) and source the $HOME/env.sh file to set environment variables

```bash
source $HOME/env.sh
```

6. Edit the concert-workflows-values.yaml located in the /mnt/concert/workflows/bin folder:

```bash
vi /mnt/concert/workflows/bin/concert-workflows-values.yaml
```

- Replace **VM IP address** with the IP address of the VM for **adress\* and **CONCERT_HUB_URL\*\* keys
- Replace **CONCERT_HUB_KEY** with the CONCERT_HUB_KEY value that you have noted (and also set in $HOME/env.sh file)
- Add a new variable **CONCERT_API_KEY** under **CONCERT_HUB_KEY** and set its value to your WORKFLOW_APIKEY (in $HOME/env.sh file)
- save your file (:wq)

7. Create the concert workflow namespace in k3s cluster

```bash
kubectl create ns $CW_NAMESPACE
```

8. Create a secret called ibm-entitlement-key in the same namespace

```bash
kubectl create secret docker-registry ibm-entitlement-key \
--docker-server=cp.icr.io \
--docker-username=cp \
--docker-password="${CONCERT_REGISTRY_PASSWORD}" \
--namespace="${CW_NAMESPACE}"
```

9. Install concert worflow

```bash
cd /mnt/concert/workflows
./bin/setup --namespace="${CW_NAMESPACE}"
```

10. Register Concert Workflows as an add-on to your Concert instance.

```bash
./bin/tethering/tether-to-hub.sh \
--concert-hub-url="$CONCERT_HUB_URL" \
--concert-hub-key="$CONCERT_HUB_KEY" \
--extn-dir="$EXTNS_DIR" \
--provider="$ADDON_NAME" \
--external-url="$EXT_URL"
```

11. Establish authentication credentials and a reusable connection for Concert Workflows

```bash
chmod +x bin/tethering/enable_concert_workflows.sh
./bin/tethering/enable_concert_workflows.sh --concert-url="$CONCERT_HUB_URL" --c-api-key="$CONCERT_APIKEY" --c-user="$CONCERT_USER" --workflow-apikey="$WORKFLOW_APIKEY"
```

## III - Watsonx.ai integration <a name="wxaiIntegration"></a>

> !!!! Concert require model ibm/granite-3-2-8b-instruct on x.ai !!!!

### Techzone reservation

Be sure to reserve a watsonx.ai instance as explained in [Lab 0 - III - Provision a watsonx.ai on techzone](Lab0-setup.md#iii---provision-a-watsonxai-on-techzone).

### Configure watsonx.ai in IBM Concert

The watsonx.ai integration is simply done through setting some config parameters in the config files of IBM concert.  

You will need to update the $HOME/env.sh file.
```
vim $HOME/env.sh
```

Update the following variables:
- **WATSONX_API_KEY**: use the API key you got in [Lab 0 - Get API Key and service ID information](Lab0-setup.md#get-api-key-and-service-id-information), from your techzone wx.ai reservation page
- **WATSONX_API_PROJECT_ID**: use the project ID you got from [Lab 0 - Create a watsonx project and get project ID](Lab0-setup.md#create-a-watsonx-project-and-get-project-id),
- **WATSONX_API_URL**: https://us-south.ml.cloud.ibm.com , since the instance is provision is US.

Then you need to start the appropriate service:

```
cd /mnt/concert
source $HOME/env.sh
ibm-concert-std/bin/start_service ibm-roja-py-utils
```
