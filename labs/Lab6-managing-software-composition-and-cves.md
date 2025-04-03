# Managing Software Composition and CVEs

- [Managing Software Composition and CVEs](#managing-software-composition-and-cves)
  - [Objective](#objective)
  - [Prerequisite](#prerequisite)
  - [Content](#content)
  - [I - Data ingestion](#i---data-ingestion)
    - [a - Manual ingestion](#a---manual-ingestion)
      - [Clone and build the micro-services](#clone-and-build-the-micro-services)
      - [Clone the SBOM ingestion scripts](#clone-the-sbom-ingestion-scripts)
      - [Execute the SBOM generation script](#execute-the-sbom-generation-script)
      - [Upload SBOMs on IBM concert](#upload-sboms-on-ibm-concert)
    - [b - Jenkins Pipeline](#b---jenkins-pipeline)
  - [II - Managing Software Composition](#ii---managing-software-composition)
  - [III - Managing CVEs](#iii---managing-cves)


## Objective

In this lab, you will learn how to ingest application data from micro-services development in IBM Concert. Then you will see the result and the related functionnalities of IBM Concert concerning these data.

## Prerequisite

You must have installed IBM Concert and integrate IBM Concert with watsonx.ai (Lab0 and Lab1)

## Content

- [I - Data ingestion]()
- [II - Managing Software Composition]()
- [III - Managing CVEs]()

## I - Data ingestion

Data ingestion in IBM Concert is done using SBOM (Software Bill of Materials).  
IBM Concert provides a Concert Toolkit images to help in the SBOMs generation based on configuration yaml files and scanning products (for ex. Syft, CycloneDX, trivy, etc ....).
For more information, the documentation is [here](https://www.ibm.com/docs/en/concert?topic=using-concert-toolkit)

We will use this toolkit to generate the SBOMs of a very simple provided python application.

### a - Manual ingestion

#### Clone and build the micro-services

You will start to clone the 2 micro-services composing the application.

1. Connect on the machine you have provisioned on Techzone in Lab0

```bash
ssh itzuser@<VM ip address> -p 2223
```

2. Create a folder named **concert-bootcamp**

```bash
umask 022
mkdir -p concert-bootcamp/Applications
cd concert-bootcamp/Applications
```

3. Clone and build the hr-app component

```bash
git clone https://github.ibm.com/HCS-Concert-Projects/hr-app.git
cd hr-app
./buildImage.sh
cd ..
```

4. Clone and build summarization-svc component

```bash
git clone https://github.ibm.com/HCS-Concert-Projects/summarization-svc.git
cd summarization-svc
./buildImage.sh
cd ..
```

#### Clone the SBOM ingestion scripts

Then you will clone a project containing a directory structure and a script to generate SBOMs using the Concert Toolkit and upload them in concert using IBM Concert APIs.

```bash
cd ../..
git clone https://github.ibm.com/HCS-Concert-Projects/SBOMs-ingestion.git
```

Create an output directory for the generated SBOMs

```bash
mkdir SBOMs-ingestion/concert_data
chmod 777 SBOMs-ingestion/concert_data
```

#### Execute the SBOM generation script

```bash
cd SBOMs-ingestion/scripts
./generate-sboms.sh hr-app
./generate-sboms.sh summarization-svc
```

- Explain environment variables
- Explain concert-toolkit
- Explain generate-sboms.sh

#### Upload SBOMs on IBM concert

TODO Explain upload.sh
  
1. Update **app-common-variables.variables** file to specify Concert variables:

- CONCERT_HOST
- CONCERT_PORT
- CONCERT_APIKEY
  
2. Run the upload script
  
```bash
cd SBOMs-ingestion/scripts
./upload.sh hr-app
./upload.sh summarization-svc
```
3. Log on concert and look at the Arena view

### b - Jenkins Pipeline

Jenkins pipeline on same base than Manual - walktrough

## II - Managing Software Composition

Show result of data ingestion on Concert - walthrough

## III - Managing CVEs

Show resule of CVE data ingestion on Concert - walkthrough
