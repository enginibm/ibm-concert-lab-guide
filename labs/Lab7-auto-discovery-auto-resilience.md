

 


 


<img width="468" height="436" alt="image" src="https://github.com/user-attachments/assets/8deb48c3-bc74-4214-b3f1-0b4f43ad4e59" />


Credential<img width="468" height="13" alt="image" src="https://github.com/user-attachments/assets/acb05e82-4a1c-445a-b0c7-8ae534e7dede" />

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
<img width="468" height="524" alt="image" src="https://github.com/user-attachments/assets/7bc3f4bb-3115-4757-be28-4b139d29978c" />

