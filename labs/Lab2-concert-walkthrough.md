# Concert Walkthrough

- [Concert Walkthrough](#concert-walkthrough)
  - [Objective](#objective)
  - [Prerequisite](#prerequisite)
  - [I - Log on Concert and load demo data](#i---log-on-concert-and-load-demo-data)
  - [II - Walkthrough](#ii---walkthrough)
  - [III - Reset concert data](#iii---reset-concert-data)
    - [For an Openshift installation](#for-an-openshift-installation)
    - [For a VM installation](#for-a-vm-installation)


## Objective

In this lab, you will install IBM Concert on a standalone server.

## Prerequisite

You must have run before the [Lab0](LabO-setup.md) which explain how to reserve a virtual machine on Techzone.

## I - Log on Concert and load demo data

## II - Walkthrough

## III - Reset concert data

### For an Openshift installation

> Official documentation is [here](https://www.ibm.com/docs/en/concert?topic=kubernetes-resetting-concert-instance)

1. Connect on the bastion attached to the openshift you have created in Techzone in Lab0

You can retreive the connections information on your reservation page as explain in Lab0

```bash
ssh itzuser@<cluster API address> -p 40222
```

2. Source the file containing you environment variables

```bash
source env.sh
```

3. Reset Concert data

Be careful before using this command because it not only reset the data coming from SBOM but also all the configurations that you could have made in concert: API key, connections, etc ... That mean also that if you have done the concert workflow integration, you need to replay the tethering steps.

```bash
cd ibm-concert-k8s/
./reset.sh --namespace=${CONCERT_NAMESPACE} --storage_class=${CLUSTER_STORAGE_CLASS} --username=${CLUSTER_USER} --password=${CLUSTER_PASSWORD} --registry=${CONCERT_REGISTRY} --registry_user=${CONCERT_REGISTRY_USER} --registry_password=${CONCERT_REGISTRY_PASSWORD} --cfg=sw_ent_native --wipe --setup
```

### For a VM installation

> Official documentation is [here](https://www.ibm.com/docs/en/concert?topic=vm-resetting-concert-instance)

1. Connect on the machine you have provisioned on Techzone in Lab0

```bash
ssh itzuser@<VM ip address> -p 2223
```

2. Source the file containing you environment variables

```bash
source $HOME/env.sh
```

3. Reset Concert data

Be careful before using this command because it not only reset the data coming from SBOM but also all the configurations that you could have made in concert: API key, connections, etc ... That mean also that if you have done the concert workflow integration, you need to replay the tethering steps.

```bash
/mnt/concert
./ibm-concert-std/bin/reset
```
