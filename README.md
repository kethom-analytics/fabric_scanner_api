# Get all Fabric Artifact with Metadata Scanner API in Fabric Notebook
## About
This solution uses Microsoft Fabrics [metadata scanner api](https://learn.microsoft.com/en-us/fabric/governance/metadata-scanning-overview) to provide an easy and fast overview on all metadata of your organisation's Fabric items. It uses Fabric Notebooks to retrieve and transform the data and a Fabric Lakehouse to store the data.
There is no need for any service or application outsite of Microsoft Fabric. The notebook stores the raw json data retrieved via the API within the files section within the following path Metadata_Requests/\<date of request\>/\<time of request\> (this can be deactivated). Additionally it parses different entities of the result into delta parquet tables so they are available for easy consumption within Fabric (see How to consume).

To keep the history of data within the Delta tables, you can configure the variable keep_history as True (default). When this is configured the notebook will only overwrite data from the current day, but keeping the data of past days. This way you can get a historisation of your Fabric item estate and can later on analyize, how it develops over time. The notebook currently does not use [the delta functionality](https://learn.microsoft.com/en-us/rest/api/power-bi/admin/workspace-info-get-modified-workspaces) of the Scanner API, which allows you to restrict the extracted information only to workspaces which had a change since your last load. This could be a future optimization, but is currently not necessary since the notebook can extract all data within a few minutes. even if there is a high number of workspaces and items.

The notebook uses the [semantic-link](https://learn.microsoft.com/en-us/fabric/data-science/semantic-link-overview) python library to perform the API calls against the Fabric API. Since this is authenticated by the user executing/scheduling the notebook, there is no need for a service principal or something similar. Hence this user needs to have either Power BI Service Administrator  or Microsft 365 Global Administrator authorization in order to perform the API calls.

## How to use

1. To use the solution you need to import the notebook Load_Scanner_API.ipynb into a Fabric:
  - Choose Data Engineering experience in the bottom left of your Fabric Workspace:<br />
    ![image](https://github.com/kethom-analytics/fabric_scanner_api/assets/164002227/2cdc77ac-2a3d-4a2f-83f8-6bb1c28b80b2)
  - Choose "Import notebook" within the workspace you want to place the solution:<br />
    ![image](https://github.com/kethom-analytics/fabric_scanner_api/assets/164002227/4bd415fe-c2a3-4604-b3f2-e0a4291f47e0)
  - Upload the notebook:<br />
    ![image](https://github.com/kethom-analytics/fabric_scanner_api/assets/164002227/f084acde-c042-483b-a416-afc008996009)


2. Open the imported notebook:<br />
  ![image](https://github.com/kethom-analytics/fabric_scanner_api/assets/164002227/511837a6-ad3c-456e-b419-edd18f994f13)

3. Attach a new lakehouse: <br />
   ![image](https://github.com/kethom-analytics/fabric_scanner_api/assets/164002227/adedc279-0082-4c6f-9ae4-a57d02dc1b17) <br />

   ![image](https://github.com/kethom-analytics/fabric_scanner_api/assets/164002227/95f31ae6-bb8f-4c34-ba4d-4634f9d682a9) <br />

   There should just be one Lakehouse attachted to the notebook. If you use an existing lakehouse, make sure that the table names are not in use yet.<br />

   ![image](https://github.com/kethom-analytics/fabric_scanner_api/assets/164002227/03099fb8-17c3-4e27-ac6b-423a21cb4875)<br />
4. "Run all" to execute the notebook completely<br />
  ![image](https://github.com/kethom-analytics/fabric_scanner_api/assets/164002227/20ce053b-0d8b-4e8e-a429-7cc1577f5ace)<br />
5. A sucessfull run will result in the following tables and files:<br />

  ![image](https://github.com/kethom-analytics/fabric_scanner_api/assets/164002227/0ccf8ff0-c0b2-4cf1-b6bf-35e4a2f100e3)<br />

  ![image](https://github.com/kethom-analytics/fabric_scanner_api/assets/164002227/cb483216-f6df-4d26-be23-c6cae9eebd3d)<br />

  

## How to consume
Each of the extracted tables contains the flattened data for the respective entity. Since the notebook keeps the history of the past days, some additional columns have been added:
- date: Date of extraction
- \<entity\>_key : Concatenated field with date and the respective id. This field is needed to have an unique key to join the data. Since the ids are not unique anymore due to historisation. This new "primary" and "foreign" keys are persisted so they can be easily used to connect via Power BI Direct Lake.<br />

To create a direct lake connection, open the Lakehouse (not in the notebook) and create "New semantic model":<br />
![image](https://github.com/kethom-analytics/fabric_scanner_api/assets/164002227/6ad2912a-ede2-4461-90e8-d06da27b8731)<br />

Choose the needed tables for your semantic model:<br />

![image](https://github.com/kethom-analytics/fabric_scanner_api/assets/164002227/9c52d2d8-5a85-4755-aa57-f99de854847c)<br />

Create a data model with relationships and measure for your analytical needs:<br />
![image](https://github.com/kethom-analytics/fabric_scanner_api/assets/164002227/11214b06-8673-4592-aa11-fdea78b2e053)





 
