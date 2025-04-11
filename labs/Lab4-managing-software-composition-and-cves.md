# Managing Software Composition and CVEs

## Objective

In this lab, you will learn how to ingest application data from micro-services development in IBM Concert. Then you will see the result and the related functionnalities of IBM Concert concerning these data.

## Prerequisite

You must have installed IBM Concert and integrate IBM Concert with watsonx.ai (Lab0 and Lab1)
∏
## Content

- [Managing Software Composition and CVEs](#managing-software-composition-and-cves)
  - [Objective](#objective)
  - [Prerequisite](#prerequisite)
  - [Content](#content)
  - [I - Data ingestion](#i---data-ingestion)
    - [Verify that you have a Concert API Key defined, if not create it](#verify-that-you-have-a-concert-api-key-defined-if-not-create-it)
    - [a - Manual ingestion](#a---manual-ingestion)
      - [Clone and build the micro-services](#clone-and-build-the-micro-services)
      - [Clone the SBOM ingestion scripts](#clone-the-sbom-ingestion-scripts)
      - [Execute the SBOM generation script](#execute-the-sbom-generation-script)
      - [Upload SBOMs on IBM concert](#upload-sboms-on-ibm-concert)
    - [b - Jenkins Pipeline](#b---jenkins-pipeline)
  - [II - Managing Software Composition](#ii---managing-software-composition)
  - [III - Managing CVEs](#iii---managing-cves)

## I - Data ingestion

Data ingestion in IBM Concert is done using SBOM (Software Bill of Materials).  
IBM Concert provides a Concert Toolkit images to help in the SBOMs generation based on configuration yaml files and scanning products (for ex. Syft, CycloneDX, trivy, etc ....).
For more information, the documentation is [here](https://www.ibm.com/docs/en/concert?topic=using-concert-toolkit)

We will use this toolkit to generate the SBOMs of a very simple provided python application.

### Verify that you have a Concert API Key defined, if not create it

- From a browser go to the IBM Concert
- Log on concert using your cluster credentials
- Click the circle at top right of the window and select **API Key**
  <br><img src="../images/concert_apikey_1.png" alt="drawing" width="400"/>
- In the API Key window, click **Generate API Key**
  <br><img src="../images/concert_apikey_2.png" alt="drawing" width="400"/>
- Copy the API key generated somewhere, we will use it for the upload of SBOMs files in IBM Concert
  

### a - Manual ingestion

#### Clone and build the micro-services

TODO - Explain micro services   
TODO - Explain environment variables   
TODO - Explain concert-toolkit   
TODO - Explain generate-sboms.sh   

You will start to clone the 2 micro-services composing the application.

1. Connect on the machine you have provisioned on Techzone in Lab0

```bash
ssh itzuser@<VM ip address> -p 2223 -i /path/to/concert/sshkey/pem_ibmcloudvsi_download.pem
```

2. Create a folder named **concert-bootcamp**

```bash
umask 022
mkdir -p $HOME/concert-bootcamp/Applications
```

3. Clone and build the hr-app component

```bash
cd $HOME/concert-bootcamp/Applications
git clone https://github.ibm.com/concert-bootcamp/hr-app.git
cd hr-app
./buildImage.sh
```

4. Clone and build summarization-svc component

```bash
cd $HOME/concert-bootcamp/Applications
git clone https://github.ibm.com/concert-bootcamp/summarization-svc.git
cd summarization-svc
./buildImage.sh
```

#### Clone the SBOM ingestion scripts

Then you will clone a project containing a directory structure and a script to generate SBOMs using the Concert Toolkit and upload them in concert using IBM Concert APIs.

```bash
cd $HOME/concert-bootcamp
git clone https://github.ibm.com/concert-bootcamp/SBOMs-ingestion.git
```

Create an output directory for the generated SBOMs

```bash
mkdir $HOME/concert-bootcamp/SBOMs-ingestion/concert_data
chmod 777 $HOME/concert-bootcamp/SBOMs-ingestion/concert_data
```

#### Execute the SBOM generation script

```bash
cd $HOME/concert-bootcamp/SBOMs-ingestion/scripts
./generate-sboms.sh hr-app
./generate-sboms.sh summarization-svc
```

#### Upload SBOMs on IBM concert

TODO Explain upload.sh
  
1. Update **app-common-variables.variables** file to specify Concert variables:

```bash
cd $HOME/concert-bootcamp/SBOMs-ingestion
vi app-common-variables.variables
```

Put your concert values for:

- CONCERT_HOST (just the server IP address, in the form aaa.bbb.ccc.ddd)
- CONCERT_PORT
- CONCERT_APIKEY
  
2. Run the upload script
  
```bash
cd $HOME/concert-bootcamp/SBOMs-ingestion/scripts
./upload.sh hr-app
./upload.sh summarization-svc
```
3. Log on concert and look at the Arena view

### b - Jenkins Pipeline

During the micro-services development phase, data ingestion should be done during the CI/CD process.
In each component code of this lab (hr-app and summarization-svc) you will find a Jenkins file as a pipeline example. Any other tools can be use (teckton, ...)

Jenkins demo done by the instructor.

## II - Managing Software Composition

Show result of data ingestion on Concert - walthrough

## III - Managing CVEs

Show result of CVE data ingestion on Concert - walkthrough
