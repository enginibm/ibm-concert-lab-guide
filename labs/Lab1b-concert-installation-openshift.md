# IBM Concert installation on an openshift cluster

- [IBM Concert installation on an openshift cluster](#ibm-concert-installation-on-an-openshift-cluster)
  - [Objective](#objective)
  - [Prerequisite](#prerequisite)
  - [I - Install concert on an openshift cluster](#i---install-concert-on-an-openshift-cluster)
  - [II - Integrate IBM Concert with watsonx.ai](#ii---integrate-ibm-concert-with-watsonxai)
    - [Techzone reservation](#techzone-reservation)
    - [Configure watsonx.ai in IBM Concert](#configure-watsonxai-in-ibm-concert)

## Objective

In this lab, you will install IBM Concert on an Openshift Cluster.

## Prerequisite

You must have run before the [Lab0](LabO-setup.md) which explain how to reserve an Openshift cluster on Techzone.

## I - Install concert on an openshift cluster

You can install concert on a VM or in a kubernetes cluster. In this lab we will do an Openshift cluster installation.

> Offcial documentation [Openshift installation](https://www.ibm.com/docs/en/concert?topic=environment-installing-concert-software-ocp-without-cpfs)

1. Connect on the bastion attached to the openshift you have created in Techzone in Lab0

You can retreive the connections information on your reservation page as explain in Lab0

```bash
ssh itzuser@<cluster API address> -p 40222
```

2. Get concert installation files

```bash
wget https://github.com/IBM/Concert/releases/download/v1.0.5.2/ibm-concert-k8s.tgz
tar xfz ibm-concert-k8s.tgz
```

3. Create a $HOME/env.sh file

```bash
vi $HOME/env.sh
```

Copy Paste the content of [env-ocp.sh](../files/env-ocp.sh) in this $HOME/env.sh file  
Update the values for the following keys (other keys will be updated later):

- ENTITLEMENT_API_KEY with your [entitlement key](https://www.ibm.com/docs/en/concert?topic=concert-obtaining-entitlement-api-key)
- CLUSTER_USER with your cluster username
- CLUSTER_PASSWORD with your cluster password
- CLUSTER_STORAGE_CLASS with the cluster storage class you will use (ex: ocs-storagecluster-cephfs)

Save the file (:wq) and source the $HOME/env.sh file to set environment variables

```bash
source $HOME/env.sh
```

4. Then install IBM Concert

```bash
cd ibm-concert-k8s

./install-concert-k8s \
--license_acceptance=y \
--namespace=$CONCERT_NAMESPACE \
--registry=cp.icr.io/cp/concert \
--registry_user=cp \
--registry_password=$ENTITLEMENT_API_KEY \
--username=$CLUSTER_USER \
--password=$CLUSTER_PASSWORD \
--storage_class=$CLUSTER_STORAGE_CLASS \
--scale_config=level_1
```

Wait until the end of the installation

5. The last step is to create a route to reach the IBM Concert GUI

- Generate a route to access Concert on your OCP cluster

```bash
./ocp-route.sh $CONCERT_NAMESPACE
```

- Retrieve the route created in the previous step

```bash
oc get route concert -n $CONCERT_NAMESPACE
```

6. Connect on Concert and create an API Key

- From a browser go to the Concert route. For example https://concert-concert.apps.67e805b7800781b10c12649c.ocp.techzone.ibm.com
- Log on concert using your cluster credentials (kubeadmin user)
- Click the KU circle at top right of the window and select **API Key**
  <br><img src="../images/concert_apikey_1.png" alt="drawing" width="400"/>
- In the API Key window, click **Generate API Key**
  <br><img src="../images/concert_apikey_2.png" alt="drawing" width="400"/>
- Copy the API key generated in your clipboard

7. Update environment variables

```bash
vi $HOME/env.sh
```

Update the values for the following keys:

- CONCERT_URL with the Concert route (ex: https://concert-concert.apps.67e805b7800781b10c12649c.ocp.techzone.ibm.com)
- CONCERT_APIKEY with the API Key you created in step 6.

Save the file (:wq) and source the $HOME/env.sh file to set environment variables

```bash
source $HOME/env.sh
```

## II - Integrate IBM Concert with watsonx.ai

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

- WATSONX_API_KEY: use the API key you got in [Lab 0 - Get API Key and service ID information](Lab0-setup.md#get-api-key-and-service-id-information), from your techzone wx.ai reservation page
- WATSONX_API_PROJECT_ID: use the project ID you got from [Lab 0 - Create a watsonx project and get project ID](Lab0-setup.md#create-a-watsonx-project-and-get-project-id),
- WATSONX_API_URL: https://us-south.ml.cloud.ibm.com , since the instance is provision is US.

For a OCP based installation the parameters must be set in the configuration secret.
This can be done in the following way:

```
source $HOME/env.sh

kubectl patch secret/app-cfg-secret -n $CONCERT_NAMESPACE --type=merge -p '{
  "data": {
    "WATSONX_API_KEY": "'$(echo -n $WATSONX_API_KEY | base64 )'",
    "WATSONX_API_PROJECT_ID": "'$(echo -n "$WATSONX_API_PROJECT_ID" | base64 )'",
    "WATSONX_API_URL": "'$(echo -n "$WATSONX_API_URL" | base64 )'"
  }
}'
kubectl rollout restart -n $CONCERT_NAMESPACE deployment/roja-py-utils
```
