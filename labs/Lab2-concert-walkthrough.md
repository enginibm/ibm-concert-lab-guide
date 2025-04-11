# Concert Walkthrough

## Objective

In this lab, we will load demo data on concert and walkthrough IBM Concert functionnalities. At the end, we will clean these demo data in order to have an empty instance to execute following labs.

## Prerequisite

- IBM Concert must be installed
- IBM Concert must be integrated with Watsonx.ai 
  
## Content

- [Concert Walkthrough](#concert-walkthrough)
  - [Objective](#objective)
  - [Prerequisite](#prerequisite)
  - [Content](#content)
  - [I - Log on Concert and load demo data](#i---log-on-concert-and-load-demo-data)
  - [II - Walkthrough](#ii---walkthrough)
  - [III - Reset concert data](#iii---reset-concert-data)


## I - Log on Concert and load demo data

1. From a browser, enter the URL of your concert instance and log with you concert username and password.
2. If it is the first time you log in, you are invited to upload sample data on the right of the window. 
3. Otherwise, you can upload sample data by clicking the question mark at the top right of the window.

<br><img src="../images/concert_sample_data.png" alt="drawing" width="400"/>

## II - Walkthrough

This part is done by the instructor.

## III - Reset concert data

1. Connect on the machine you have provisioned on Techzone in Lab0

```bash
ssh itzuser@<VM ip address> -p 2223 -i /path/to/concert/sshkey/pem_ibmcloudvsi_download.pem
```

2. Source the file containing you environment variables

```bash
source $HOME/env.sh
```

3. Reset Concert data

- Navigate in **Administration->Settings**
- Select the **Miscellaneous tab**
- Click the **Delete Sample data** button
