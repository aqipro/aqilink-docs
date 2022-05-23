# Nuxeo - Reference for Feature *Open In SAP Web GUI*

The feature *Open in SAP Web GUI* allows the user in Nuxeo to open the document related SAP Business Object with a single click from the Nuxeo web application. It calls the SAP Web GUI and after the user has logged into the SAP system, he will be directly forwarded to the SAP Business Object, opened in the associated transaction. T

### Requirements
1) The metadata replication is enabled and facet `SAP Replicated Details` is available on the document.
2) The feature has been enabled and is maintined for the repository (see below).

## Administration
How the feature can be enabled and maintained by the Nuxeo administrator. 
To enable the feature, navigate to the desired SAP Content Repository file in Nuxeo which ends with `.repo`.

Available settings on the repository file to maintain the feature:

| Property      | Description |
| ----------- | ----------- |
| ``WebClient Enabled`` |  Either check the option to enable or disable the feature. Default: *Not enabled* | 
| ``WebClient URL`` |  The URL pointing to the SAP WebClient. Make sure to replace the placeholder *sap-ip-address* with the IP or hostname of the SAP server.   |  
| ``WebClient Client`` |  The Client used as preselection for the user login.   |  
| ``WebClient Language`` | The lanugage used as preselection for the user login.   |  
| ``WebClient Screen-Personas Theme Id`` | If there is any public Screen-Personas theme available, set the ID of the theme to be used after the user logged in.  |  
| ``WebClient URL Parameter Mapping`` | The most important part. It contains the mapping of the properties to the related SAP transaction.  |  

<img src="./_media/../../_media/reference/open_in_sap_repo_1.png" border="" alt="">


## Usage

How the feature works for the user in Nuxeo.

TBD