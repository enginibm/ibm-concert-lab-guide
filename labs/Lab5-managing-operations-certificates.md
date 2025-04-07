# Managing Operations

## Objective

In this lab, you will manually use the concert toolkit to upload our micro-services certificates in IBM Concert.

## Prerequisite

- IBM Concert must be installed
- You have run the manual data ingestion script 

## Content

- [Managing Operations](#managing-operations)
  - [Objective](#objective)
  - [Prerequisite](#prerequisite)
  - [Content](#content)
  - [Certificates data ingestion](#certificates-data-ingestion)
  - [Certificates management](#certificates-management)

## Certificates data ingestion

Certificates can be ingested in Concert following several way:

- using built-in concert workflow certificate ingestion from a kubeadm (in Administration->Integration->Create Ingestion job)
- using concert-toolkit for applications certificates during the CI/CD process
  
Here are the manual steps to follow:

1. Connect on the machine you have provisioned on Techzone in Lab0

```bash
ssh itzuser@<VM ip address> -p 2223
```

2. Take a look at the certificate template provided by Concert Toolkit

```bash
cd $HOME/concert-bootcamp/SBOMs-ingestion/templates
vi cert-config-values.yaml.template
```

In this file you can see that it is possible to generate certificate SBOMs using 3 ways:

- Line 22: Providing an URL (it is what you are going to do)
- Line 26: By reading certificate files
- Line 33: Providing manually certificates details

3. 


TO CONTINUE

## Certificates management

Show result of data ingestion on Concert - walthrough
