# Concert installation

- [I - VM installation](#vmInstallation)
- [II - Watsonx.ai integration](#wxaiIntegration)
- [III - Concert workflow installation](#cwInstallation)

## I - VM Installation <a name="vmInstallation"></a>

You can install concert on a VM or in a kubernetes cluster. In this lab we will do a VM installation.

> Official documentation [VM installation](https://www.ibm.com/docs/en/concert?topic=concert-deploying-virtual-machine-vm)  
> Offcial documentation [Kubernetes installation](https://www.ibm.com/docs/en/concert?topic=concert-deploying-kubernetes)

Connect on the machine you have provisioned on Techzone in Lab0

```bash
ssh itzuser@169.44.147.111 -p 30288
loginctl enable-linger itzuser
cd /mnt/concert
wget https://github.com/IBM/Concert/releases/download/v1.0.5.2/ibm-concert-std.tgz
tar xfz ibm-concert-std.tgz
```

Create a $HOME/env.sh file

```bash
vi $HOME/env.sh
```

Copy Paste the content of [env.sh](../files/env.sh) in this $HOME/env.sh file  
Update the values for the following keys (other keys will be updated later):

- CONCERT_REGISTRY_PASSWORD with your [entitlement key](https://www.ibm.com/docs/en/concert?topic=concert-obtaining-entitlement-api-key)
- CONCERT_HUB_URL with your VM address
- EXT_URL with your VM address

Source the $HOME/env.sh file to set environment variables and install concert

```bash
source $HOME/env.sh
${DOCKER_EXE} login ${CONCERT_REGISTRY} --username=${CONCERT_REGISTRY_USER} --password=${CONCERT_REGISTRY_PASSWORD}
ibm-concert-std/bin/setup --license_acceptance=y --registry=${CONCERT_REGISTRY} --runtime=${DOCKER_EXE} --username=ibmconcert --password
```

PROBLEM - Port 12443 not accessible on VM !!!!!!!

## II - Watsonx.ai integration <a name="wxaiIntegration"></a>

TODO (get content from DACH team ?)

## III - Concert workflow installation <a name="cwInstallation"></a>

### k3s installation

> Offical documentation [k3s installation](https://www.ibm.com/docs/en/rapid-infra-auto/1.1.x?topic=planning-software-requirements#software_requirements__k3s__title__1)

#### 1. Install k3s (tested on CONCERT 1.0.5 TZ VM)

Concert workflow must be installed on k3s. To install k3s execute the following command

```bash
curl -sfL https://get.k3s.io | sudo INSTALL_K3S_VERSION=v1.29.2+k3s1 sh -s - --write-kubeconfig-mode 644 --disable traefik
```

#### 2. Install Helm

```bash
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
chmod 700 get_helm.sh
./get_helm.sh
sudo chmod 777 /usr/local/bin/helm
```

#### 3. Specify Kubernetes configuration file

```bash
echo "export KUBECONFIG=/etc/rancher/k3s/k3s.yaml" >> ~/.bashrc
source ~/.bashrc
```

### Install Concert workflow

> Official documentation: https://www.ibm.com/docs/en/concert?topic=workflows-installing-concert-vm

Initialize environement variables

If not already done during concert installation,
Copy Paste the content of [env.sh](../files/env.sh) in this $HOME/env.sh file  
Update the values for the following keys (other keys will be updated later):

- CONCERT_REGISTRY_PASSWORD with your [entitlement key](https://www.ibm.com/docs/en/concert?topic=concert-obtaining-entitlement-api-key)
- CONCERT_HUB_URL with your VM address
- EXT_URL with your VM address
- CONCERT_APIKEY with the concert API key (TODO explain how to generate Concert API Key)

Source the $HOME/env.sh file to set environment variables

```bash
source $HOME/env.sh
```

Log on docker

```bash
${DOCKER_EXE} login ${CONCERT_REGISTRY} \
--username=${CONCERT_REGISTRY_USER} \
--password=${CONCERT_REGISTRY_PASSWORD}
```

Get concert infos and generate CONCERT_HUB_KEY

```bash
cd /home/itzuser/workflows

export CONCERT_HUB_URL=https://localhost:12443

./bin/tethering/get_concert_info.sh \
--concert-url=$CONCERT_HUB_URL \
--c-api-key=$CONCERT_APIKEY
```

Replace values given for for **CONCERT_HUB_KEY** and **WORKFLOW_APIKEY** in your $HOME/env.sh file and note the values also somewhere for following steps.  
Source again $HOME/env.sh to set new environment variables values.

```bash
source $HOME/env.sh
```

Edit the concert-workflows-values.yaml located in the /home/itzuser/workflows/bin folder:

```bash
sudo vi /home/itzuser/workflows/bin/concert-workflows-values.yaml
```

- Replace **VM IP address** with the IP address of the VM for **adress\* and **CONCERT_HUB_URL\*\* keys
- Replace **CONCERT_HUB_KEY** with the CONCERT_HUB_KEY value that you have noted (and also set in $HOME/env.sh file)
- Add a new variable **CONCERT_API_KEY** under **CONCERT_HUB_KEY** and set its value to your WORKFLOW_APIKEY (in $HOME/env.sh file)
- save your file (:wq)

Create the concert workflow namespace in k3s cluster

```bash
kubectl create ns $CW_NAMESPACE
```

Create a secret called ibm-entitlement-key in the same namespace

```bash
kubectl create secret docker-registry ibm-entitlement-key \
--docker-server=cp.icr.io \
--docker-username=cp \
--docker-password=$CONCERT_REGISTRY_PASSWORD \
--namespace="${CW_NAMESPACE}"
```

install concert worflow

```bash
cd /home/itzuser/workflows
./bin/setup --namespace="${CW_NAMESPACE}"
```

Register Concert Workflows as an add-on to your Concert instance.

```bash
./bin/tethering/tether-to-hub.sh \
--concert-hub-url="$CONCERT_HUB_URL" \
--concert-hub-key="$CONCERT_HUB_KEY" \
--extn-dir="$EXTNS_DIR" \
--provider="$ADDON_NAME" \
--external-url="$EXT_URL"
```

Establish authentication credentials and a reusable connection for Concert Workflows

```bash
chmod +x bin/tethering/enable_concert_workflows.sh
./bin/tethering/enable_concert_workflows.sh --concert-url="$CONCERT_HUB_URL" --c-api-key="$CONCERT_APIKEY" --c-user="$CONCERT_USER" --workflow-apikey="$WORKFLOW_APIKEY"
```
