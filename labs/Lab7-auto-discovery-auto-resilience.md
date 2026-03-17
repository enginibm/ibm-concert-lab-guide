-
Pre-requisites	3
1 - Creating an auto-discovery integration	3
2 - Reviewing the Resilience Actions	4
3 - Remediating the Resilience action with Concert Workflows	5


Introduction

In this demo, we will set up a Concert integration to understand how a customer will automate the Resilience discovery and ingestion process directly from a runtime environment. This method can then be used for assessing Resilience during a Proof of Value with a customer. The value to customer for using this method is that they can automatically discover their applications data to populate the Arena View in IBM Concert, create a resilience assessment automatically, then resolve the issues discovered, all within IBM Concert. 
Pre-requisites

Either:
•	A TechZone Concert version 2.3.1 instance
o	With watsonx.ai enabled on this TechZone instance
•	A timeslot booked on the Concert reservable demo instance

1 - Creating an auto-discovery integration

1.1: Start by showing an empty home page. Then on the home page, select Discover your data.

 

1.2: Choose the integration type Red Hat OpenShift Container Platform (OCP).

 

1.3: Enter details for the Endpoint, Token and Cluster name. Ensure these are in a place easy to copy from.

Detail	Credential
Endpoint	https://api.ecommerce-app.cp.fyre.ibm.com:6443

Token	eyJhbGciOiJSUzI1NiIsImtpZCI6ImZpMnBWZ1BGVkhzTVdfRzZLWGp5Z0puTThZN0xQdV9KNGV5RG1rRDdXdWsifQ.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJlY29tbWVyY2UtZnJvbnRlbmQiLCJrdWJlcm5ldGVzLmlvL3NlcnZpY2VhY2NvdW50L3NlY3JldC5uYW1lIjoibXktc2VydmljZS1hY2NvdW50LXRva2VuLTE3NTc1MjMyNDYiLCJrdWJlcm5ldGVzLmlvL3NlcnZpY2VhY2NvdW50L3NlcnZpY2UtYWNjb3VudC5uYW1lIjoibXktc2VydmljZS1hY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZXJ2aWNlLWFjY291bnQudWlkIjoiMDcxYTJmYjktZDkzNi00YTc0LWJkMzQtYjZhYzlmNjhiYzUzIiwic3ViIjoic3lzdGVtOnNlcnZpY2VhY2NvdW50OmVjb21tZXJjZS1mcm9udGVuZDpteS1zZXJ2aWNlLWFjY291bnQifQ.YzHT6q25FsENUr00ugDfOdRZ4TnywbXw00Jh-099Vf2DxYJay5v8FCVOsO5K3WUktyMapVG8jRDBt4mdCxs4_ty4lmf0ybI8Wm87MKtsBl0m7x-iPvKxTKKTY2ljdD9GYRXxaS38TaborOYfEg0aLlPgC7P3GnqZg9w_JGpIwMfO4AUu4kYrIjpHDNIvyLefBsxfIPC9r6bvGVrbP16dTrOKfFeJfUWUGqiXO0cnFTN9ZFMD2iHZo8tMYefDuayLu_BkoYc17DvDwvqOWKnfd6e3zg9l7BDq2wJbYE89cn1vqMocwW8RWjRLEMLtrmQLXlZTk2JjIiHQHqCDO75qB3FvFvN-qEZDMbYnt5tmhq_jiwaVlG6vq2TCwqCdBuBROgz8xtPnzCAC3AMxYIO2xQh2FuFz9SF97B5JpSGPIYTxZaHqph1rCOKCTdpuNV_OlpVLx8YQTHJygENxce86v8W9Je-pE7tkLAmu2MwbIzRBw-_-dhXAx8WW3f8C0ShniuoM7zkEcF24nBRDyGw6ABK0yix57VnRTvDfHo1j8aoIN6tfNA5Lg5iMQV4eYG2TfKtHOqyYrvEgrYjiv1aUJnwilETcfndMiNVtOczqHCtBfeWT5G6X4A7RwZeMNJif-i7EiYzjD11zf24Vnvn--RAZjvlRn1X2x5iWa_VRSAE
Cluster Name	us-south-dev01

1.4: On the Inventory page, select all applications to discover from the environment by checking the box next to us-south-dev01. 

 

1.5: Click Next. A Resilience assessment has now been discovered from the customer’s cluster and ingested.
2 - Reviewing the Resilience Actions

2.1: On the Resilience dimension, a posture has now been created for all applications discovered above. 

 

2.2: Click into the ecommerce-backend_posture and then the Actions tab. This is a list of AI-generated recommendations to improve the resilience of this application.

 

2.3: On the Define CPU limits action, click View details. The Overview page will show a recommendation on how to resolve this Resilience issue.  

 

3 - Remediating the Resilience action with Concert Workflows 

3.1: Based on environment in use.

A) If using the reserved timeslot demo environment, this workflow is already loaded in as OpenShift Resilience Sample, skip to step 3.5.

B) If using your own TechZone instance, download the following workflow, and load it into the Concert instance.

3.2: In Concert Workflows, navigate to Authentications and create a new authentication. 

 

3.3: Name the authentication “Concert_Config_Auth.” Set the service to Config Data. In the Data field, enter the details of your Concert instance in the format below. Then, click Create.

{
 "concert_host": "9.30.213.68",
 "concert_port": "12443",
 "concert_instance_id": "0000-0000-0000-0000" 
}

 


3.4: Create a second authentication called “Concert_APIKey”. Set the service to API Key. In the Header Name field, type “Authorization.” 

In the API Key value field, copy an API Key from Concert, in the format “C_API_KEY aWJtY29uY2VydDphMjI1YThkMi0wN2RmLTRmNDYtYmM4OS1lMmUxOTI5NzY2YWU=”. Then, click Create on the authentication.

 

3.5: Navigate to the OpenShift_Resilience_Sample workflow that was loaded in earlier. Click Run. This workflow will apply a rule on the OpenShift cluster to enforce a CPU limit of 60% on an application.

3.6: Navigate back to the Actions Center and sort the Status to have Success at the top. Note that the action for Define CPU Limit is now closed.

 
Summary

In this demo, we explored how to perform auto-discovery on an OpenShift cluster, review the resulting resilience assessments and recommendations, and then configure a Concert Workflow to automate the remediation of those resilience recommendations.
<img width="468" height="636" alt="image" src="https://github.com/user-attachments/assets/5197fb0b-12cf-4eca-8850-3b3881b9acf7" />
