# Concert installation

- [Concert installation](#concert-installation)
  - [Objective](#objective)
  - [Prerequisite](#prerequisite)
  - [A - Install IBM Concert workflow for a on openshift IBM concert installation](#a---install-ibm-concert-workflow-for-a-on-openshift-ibm-concert-installation)
  - [B - Install IBM Concert workflow for a VM concert installation](#b---install-ibm-concert-workflow-for-a-vm-concert-installation)
    - [Install preprequisites](#install-preprequisites)
    - [Install Concert workflow](#install-concert-workflow)


## Objective

In this lab, you will install IBM Concert workflow.

## Prerequisite

You must have a IBM Concert installed

## A - Install IBM Concert workflow for a on openshift IBM concert installation

> Offical documentation [ocp installation]https://www.ibm.com/docs/en/concert?topic=workflows-installing-concert-ocp)

1. Connect on the bastion attached to the openshift you have created in Techzone in Lab0

```bash
ssh itzuser@api.67e79222561226085c8b191c.ocp.techzone.ibm.com -p 40222
oVpEFHPM
```

2. Get concert workflow installation files

```bash
cd $HOME
wget https://github.com/IBM/Concert/releases/download/v1.0.5.2/ibm-concert-k8s-workflows.tgz
tar xfz ibm-concert-k8s-workflows.tgz
```

4. Get concert infos and generate CONCERT_HUB_KEY

```bash
cd workflows

./bin/tethering/get_concert_info.sh \
--concert-url=$CONCERT_URL \
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

6.Edit the concert-workflows-values.yaml located in the /home/itzuser/workflows/bin folder:

```bash
sudo vi /home/itzuser/workflows/bin/concert-workflows-values.yaml
```

- Set **address** to the IP address of your cluster (you can do a ping to its url to retrieve it) OR concert route ????
- Set **CONCERT_HUB_URL** to the CONCERT_HUB_URL value return by get_concert_info.sh in step 4.
- Set **CONCERT_HUB_KEY** to the CONCERT_HUB_KEY value return by get_concert_info.sh in step 4.
- Add a new variable **CONCERT_API_KEY** under **CONCERT_HUB_KEY** and set its value to your CONCERT_APIKEY (in $HOME/env.sh file)
- save your file (:wq)

7. Create the concert workflow namespace

```bash
oc create namespace $CW_NAMESPACE
```

8. Create a secret called ibm-entitlement-key in the same namespace

```bash
oc create secret docker-registry ibm-entitlement-key \
--docker-server=$CONCERT_REGISTRY \
--docker-username=$CONCERT_REGISTRY_USER \
--docker-password=$CONCERT_REGISTRY_PASSWORD \
--namespace="$CW_NAMESPACE"
```

9. Install concert worflow

```bash
cd /home/itzuser/workflows
./bin/deploy-k8s --namespace="${CW_NAMESPACE}"
```

Wait until the end of the installation

10. Update environment variables

```bash
vi $HOME/env.sh
```

Update the values for the following keys:

- CONCERT_HUB_URL with the Concert route (same as CONCERT_URL)
- EXT_URL to the IP address of your cluster
- EXTNS_DIR with /home/itzuser/workflows/extns/

Save the file (:wq) and source the $HOME/env.sh file to set environment variables

```bash
source $HOME/env.sh
```

11. Register Concert Workflows as an add-on to your Concert instance.

```bash
./bin/tethering/tether-to-hub.sh \
--concert-hub-url="$CONCERT_HUB_URL" \
--concert-hub-key="$CONCERT_HUB_KEY" \
--extn-dir=$EXTNS_DIR \
--provider=$ADDON_NAME \
--target-ns="${CW_NAMESPACE}"
```

12. Establish authentication credentials and a reusable connection for Concert Workflows

```bash
chmod +x bin/tethering/enable_concert_workflows.sh
./bin/tethering/enable_concert_workflows.sh --concert-url="$CONCERT_HUB_URL" --c-api-key="$CONCERT_APIKEY" --c-user="$CONCERT_USER" --workflow-apikey="$WORKFLOW_APIKEY"
```

## B - Install IBM Concert workflow for a VM concert installation

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
