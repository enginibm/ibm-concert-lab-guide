# Managing Software Composition and CVEs

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

#### Clone the micro-services

You will start to clone the 2 micro-services composing the application.

!!! Connect on Concert VM !!!

1. Create a folder named **concert-bootcamp**

```bash
umask 022
mkdir -p concert-bootcamp/Applications
cd concert-bootcamp/Applications
```

2. Clone the hr-app component

```bash
git clone https://github.ibm.com/HCS-Concert-Projects/hr-app.git
cd hr-app
./buildImage.sh
cd ..
```

- Clone summarization-svc component

```bash
git clone https://github.ibm.com/HCS-Concert-Projects/summarization-svc.git
cd summarization-svc
./buildImage.sh
cd ..
```

#### Clone the SBOM ingestion scripts

Then you will clone a project containing a directory structure and a script to generate SBOMs using the Concert Toolkit and upload them in concert using IBM Concert APIs.

```bash
cd ..
git clone https://github.ibm.com/HCS-Concert-Projects/SBOMs-ingestion.git
```

Create an output directory for the generated SBOMs

```bash
mkdir SBOMs-ingestion/concert_data
chmod 777 SBOMs-ingestion/concert_data
```

#### Execute the ingestion script

```bash
cd SBOMs-ingestion/scripts
./simulate_cicd_pipeline.sh hr-app
./simulate_cicd_pipeline.sh summarization-svc
```

- Explain environment variables
- Explain concert-toolkit
- Explain simulate_cicd_pipeline.sh

- Launch simulate_cicd_pipeline.sh
- Show arena view on concert + walkthrough

### b - Jenkins Pipeline

Jenkins pipeline on same base than Manual - walktrough

## II - Managing Software Composition

Show result of data ingestion on Concert - walthrough

## III - Managing CVEs

Show resule of CVE data ingestion on Concert - walkthrough
