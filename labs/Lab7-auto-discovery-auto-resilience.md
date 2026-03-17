In this demo, we will set up a Concert integration to understand how a customer will automate the Resilience discovery and ingestion process directly from a runtime environment. This method can then be used for assessing Resilience during a Proof of Value with a customer. The value to customer for using this method is that they can automatically discover their applications data to populate the Arena View in IBM Concert, create a resilience assessment automatically, then resolve the issues discovered, all within IBM Concert. 

### 1 - Creating an auto-discovery integration

1.1: Start by showing an empty home page. Then on the home page, select Discover your data.

 
<img width="468" height="96" alt="image" src="https://github.com/user-attachments/assets/f6bb91c5-3ff3-4149-a4d4-ede8869114bd" />

1.2: Choose the integration type Red Hat OpenShift Container Platform (OCP).

 <img width="468" height="139" alt="image" src="https://github.com/user-attachments/assets/d19007f2-bcdf-48f6-ade0-a221855e8c2b" />

1.3: Enter details for the Endpoint, Token and Cluster name. Ensure these are in a place easy to copy from.


Credential 

1.4: On the Inventory page, select all applications to discover from the environment by checking the box next to us-south-dev01.

 <img width="468" height="167" alt="image" src="https://github.com/user-attachments/assets/c0a4a7f1-c0c4-411d-91e5-10e556cc3e69" />

1.5: Click Next. A Resilience assessment has now been discovered from the customer’s cluster and ingested.

### 2 - Reviewing the Resilience Actions

2.1: On the Resilience dimension, a posture has now been created for all applications discovered above. 
<img width="403" height="105" alt="image" src="https://github.com/user-attachments/assets/5be2c652-c010-4c42-ba8c-428cfdf5863d" />

2.2: Click into the ecommerce-backend_posture and then the Actions tab. This is a list of AI-generated recommendations to improve the resilience of this application.

<img width="391" height="140" alt="image" src="https://github.com/user-attachments/assets/fc274e22-4f49-4847-8ec0-0840090ddbe2" />


2.3: On the Define CPU limits action, click View details. The Overview page will show a recommendation on how to resolve this Resilience issue. 

### 3 - Remediating the Resilience action with Concert Workflows 

If using your own TechZone instance, download the following workflow, and load it into the Concert instance.

3.2: In Concert Workflows, navigate to Authentications and create a new authentication. 

<img width="139" height="54" alt="image" src="https://github.com/user-attachments/assets/bd27cc59-057b-4c03-be11-1bdbb87699cf" />

3.3: Name the authentication “Concert_Config_Auth.” Set the service to Config Data. In the Data field, enter the details of your Concert instance in the format below. Then, click Create.

{
 "concert_host": "9.30.213.68",
 "concert_port": "12443",
 "concert_instance_id": "0000-0000-0000-0000" 
}

<img width="349" height="272" alt="image" src="https://github.com/user-attachments/assets/4448f072-c6d9-4509-9da9-ec3c14f3c8ad" />

3.4: Create a second authentication called “Concert_APIKey”. Set the service to API Key. In the Header Name field, type “Authorization.” 

In the API Key value field, copy an API Key from Concert, in the format “C_API_KEY aWJtY29uY2VydDphMjI1YThkMi0wN2RmLTRmNDYtYmM4OS1lMmUxOTI5NzY2YWU=”. Then, click Create on the authentication.

3.5: Navigate to the OpenShift_Resilience_Sample workflow that was loaded in earlier. Click Run. This workflow will apply a rule on the OpenShift cluster to enforce a CPU limit of 60% on an application.

3.6: Navigate back to the Actions Center and sort the Status to have Success at the top. Note that the action for Define CPU Limit is now closed.

<img width="468" height="176" alt="image" src="https://github.com/user-attachments/assets/438af7e9-1614-49d5-838e-441a56ba48ed" />

### Summary

In this demo, we explored how to perform auto-discovery on an OpenShift cluster, review the resulting resilience assessments and recommendations, and then configure a Concert Workflow to automate the remediation of those resilience recommendations.





