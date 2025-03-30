# Lab setup

Watsonx.ai (TZ (quand reservation Concert 5.0.1 ou celui d'IBM Cloud)

## Objective

In this lab, you will provision an openshift cluster on techzone to prepare the platform necessary for following labs.

## Prerequisite

## Content

- [1. Provision an openshift cluster on techzone](#openshiftProvisionning)
- [2. Provision a vm on techzone](#vmProvisionning)

### I - Provision an openshift cluster on techzone <a name="openshiftProvisionning"></a>

### Creating a reservation

- Use this [link](https://techzone.ibm.com/my/reservations/create/63a3a25a3a4689001740dbb3) to reserve your cluster
  ![OpenShift Cluster (VMware on IBM Cloud) ](../images/tz_request_cluster_1.png)

- Specify that you want to **Reserver now**.
- On the next page, you have to specify the purpose. Select **Education**.  
  Provide a **Purpose description**.  
  Select a **Preferred Geography**: it is the datacenter where your virtual machine will be provisioned. Choose a datacenter as near from you as possible.
- Scroll down the page and specify the cluster specifications:

- Openshift version: 4.16
- Worker node count: 5
- Worker node flavor: 32 vCPU x 128GB - 300 GB ephemeral storage
- Storage: Managed NFS - 2TB

![Reservation form 1](../images/tz_request_cluster_2.png)
![Reservation form 2](../images/tz_request_cluster_3.png)

- Click **Submit**.

- You will receive a **confirmation mail** with the status **provisioning**.
  ![Confirmation mail - provisioning](../images/tz_request_cluster_4.png)

- You can also see the reservation status from your account, in **My Reservations**.
  ![My Reservations - provisioning](../images/tz_request_cluster_5.png)

### Connecting to the Openshift Cluster

- Once the virtual machine is provisioned, you will receive a mail will the **Ready** status.
- By clicking the link **View my reervations** in the mail or from your account , in **My Reservations**, you can see your reservation with the **Ready** status.
- By clicking the **Open this environment** button, you will access all the information required to connect to the cluster
- Scroll down to the end of your page, in the **Reservation Details** section, you have (circled in orange in below screen capture)

  - OCP Console URL
  - Cluster Admin Username
  - Cluster Admin Password

    ![My Reservations - details1](../images/tz_request_cluster_6.png)
    ![My Reservations - details1](../images/tz_request_cluster_7.png)

- You can then connect and authenticate to the cluster by clicking the OCP Console URL
- On the ocp console login page, select kube:admin
  <br><img src="../images/ocp_login_1.png" alt="drawing" width="400"/>
- Enter **Cluster Admin Username** and **Cluster Admin Password**
  <br><img src="../images/ocp_login_2.png" alt="drawing" width="400"/>

### Connecting to bastion

In order to connect to the bastion, you must use the informations circled in green in previous screen capture)

```bash
ssh itzuser@<cluster API address> -p 40222
```

Install HELM

```bash
wget https://developers.redhat.com/content-gateway/file/pub/openshift-v4/clients/helm/3.15.4/helm-linux-amd64
chmod 755 helm-linux-amd64
sudo mv helm-linux-amd64 /usr/local/bin/helm
```

### II - Provision a vm on techzone <a name="vmProvisionning"></a>

## VM Installation

### Provision VM from techzone

VM - 16 vCPUs/32GB RAM/512GB Disk

### Prepare VM disk

logon in VM

```bash
ssh itzuser@169.44.147.111 -p 30288
sudo -i
mkfs.ext4 -m 0 -E lazy_itable_init=0,lazy_journal_init=0,discard /dev/vdc
blkid | grep /dev/vdc

mkdir -p /mnt/concert
chmod 777 /mnt/concert

cp /etc/fstab /etc/fstab.orig
vi /etc/fstab
```

insert: UUID=6b6320a6-f7cb-45fa-9fc1-6aaedeeb8e18 /mnt/concert ext4 discard,defaults,nofail 0 0

```bash
mount -a
systemctl daemon-reload
lsblk
```

### install podman

```bash
sudo dnf install podman
sudo sysctl user.max_user_namespaces=15000
sudo usermod --add-subuids 200000-201000 --add-subgids 200000-201000 itzuser
```
