# Nuxeo - Reference for Feature *Connect to SAP*

The feature *Connect to SAP* enables the customer to create documents in Nuxeo and connect it to existing SAP Business Objects. The related properties of the SAP Business Object can either be entered manually, or fully automated or on a hybrid solution.

* Connect a document uploaded in Nuxeo to an existing SAP Business Object as Attachment or ArchiveLink document
* Connect an document uploaded in Nuxeo using a barcode to an SAP Business Object
* Start a SAP Workflow based on a document uploaded in Nuxeo.

The feature is triggered on each `Document created` event for all `Regular Documents` of type `File` within the `/default-domain/workspaces/` path.

## Administration
The foundation of this feature is a configuration file which holds all necessary properties to identify the documents, add related facets set default values. Later, a separate [Task](/configuration/aqishare/tasks) will pick up the documents and execute the desired function (either Rest or RFC) to connect it to the related SAP Business Objects.
There are at least the following two steps required:
* Create and configure the configuration file
* Create and enable the desired Task 

### Configuration File
A configuration file within the Nuxeo repository is required which holds the desired configurations of all use-cases relying on this feature. The configuration file syntax is JSON. Within the configuration file any property (related to the SAP connection) can be set with a default value.

#### Creation & Configuration of the File
Create a new JSON file on your machine, for example `aqilink_configuration_connect_to_sap.json` and paste the content below.


| Parameter | Description | 
| ----------- |----------- |
| `path` | The path in the Nuxeo repository where all documents for the current use case must be uploaded. | 
| `facets` | Comma separated list of facets to be added to the uploaded document. | 
| `createParentALFolder` | Set `true` to create a parent folder for the newly created document in Nuxeo. The `fct_sap_http_content` facet is added to the parent folder and all related properties of it are filled. Finally, the newly created document will be moved into this new folder. This ensures full compliance with the SAP Http ArchiveLink protocol specification. **Highly recommended for any SAP ArchiveLink scenarios to set this setting to true!** | 
| `properties` |  List of properties and their default values. Prepend a `$` (Dollar sign) to any value that sould be read from the current uploaded document. For example: `$id` will be resolved as `input.id` and return the UUI of the document, `$dc:created` will be resolved as `input['dc:created']` and return the creation date of the document,...  |

#### Mandatory Facets and Properties

There are two important facets required on each document that should be connected to a SAP Business Object. Both facets must be present in the `facets` list in the JSON file.

##### Facet `fct_sap_http_content`

 > This facet must always be added in order to use this feature! Depending on the use-case, one or more of the facets below has to be added as well.

The `fct_sap_http_content` facet holds all mandatory fields required for the SAP HTTP-Content-Server connection. Make sure to specify all properties in the `properties` section of the JSON file and set a correct value.

 > Once the document has been created, all properties of facet `fct_sap_http_content` are readonly and cannot be changed through the UI!

| Property | Mandatory | Type | Description | 
| ----------- |----------- |----------- |----------- |
| `sapHttpContent:docId` | Yes | `String` | A unique id for SAP. Always pass the current UUID of the created document by using: `$id`.  | 
| `sapHttpContent:source` | Yes | `String` | The unique source name to identify the SAP system which should be triggered. Must match a `name` value in the `sapHttp.yaml` configuration file. Refer to [Configure Document Exchange](/configuration/aqilink/). | 
| `sapHttpContent:archiveId` | Yes | `String` | The SAP Content Repository name customized for the use case. |
| `sapHttpContent:compId` | Yes | `String` | Required SAP ArchiveLink property. Always use the following constant: **```data```** |
| `sapHttpContent:created` | Yes | `String` | Use the creation date of the current document by passing `$dc:created`.  |
| `sapHttpContent:modified` | Yes | `String` | Use the modification date of the current document by passing `$dc:modified`.  |
| `sapHttpContent:scanPerformed` | Yes | `Boolean` | Parameter which indicates whether the content needs to be scanned by the SAP Content Server. Always pass the value `false`. Not required for any scenario starting in Nuxeo. Property is just for consistency of the SAP Content Server HTTP Interface. |

##### Facet `fct_sap_create_al_document`

This facet holds all fields required to connect the current document to a SAP Business Object. Depending on whether just a few or all fields are prefilled, the process to connect the document to the SAP Business Object can be fully automated.
For example: If you prefill and set all properties in the table below, the document will be automatically connected to the related SAP Business Object. If you just add a few fields, the user has to edit the document and must enter the values of all missing fields manually before the document will be picked up for the connection.

| Property | Mandatory | Type | Description | 
| ----------- |----------- |----------- |----------- |
| `sapCreateALDocument:sapObject` | No | `String` | The name of the SAP Object such as `BKPF` or `BUS2012` or `EQUI` (...).| 
| `sapCreateALDocument:sapArchiveObject` | No | `String` | The name of the SAP Archive Object which is customized in SAP transaction `OAC2`. For example: `Z_NUX_BKPF` or `Z_NUX_WF` (...). | 
| `sapCreateALDocument:sapCreator` | No | `String` | The creator name of the attachment. Must be an SAP username. |
| `sapCreateALDocument:sapClient` | No | `String` | SAP Client to login to SAP |
| `sapCreateALDocument:sapFilename` | No | `String` | Use the filename of the current file by passing `$file:content.name`. |
| `sapCreateALDocument:sapArchiveDate` | No | `String` | Use the creation date of the current document by passing `$dc:created`.  |

##### Facet `fct_sap_barcode`
This facet holds all fields required to use the Barcode scenario, which are just two properties:

| Property | Mandatory | Type | Description | 
| ----------- |----------- |----------- |----------- |
| `sapBarcode:barcode` | No | `String` | Enter the barcode which should be used to store it in SAP external barcode table. | 
| `sapBarcode:docTyp` | No | `String` | Pass the name of the document type associated with the Barcode scenario (check SAP transaction `OAC5`).| 

##### Facet `fct_sap_start_workflow`
This facet holds all fields required to start a Workflow in SAP for a given user (or group). A new WorkItem will be created in the users inbox (SAP Business Workplace, transaction `SBPW`).

| Property | Mandatory | Type | Description | 
| ----------- |----------- |----------- |----------- |
| `sapStartWorkflow:sapArchiveObject` | No | `String` | The name of the SAP Archive Object which is customized in SAP transaction `OAC2` for the SAP Workflow. For example: `Z_NUX_WF` (...) | 
| `sapStartWorkflow:sapBarcode` | No | `String` | Start Worflow by using a barcode instead using `sapHttpContent:archiveId`, `sapHttpContent:docId` and `sapStartWorkflow:sapDocumentTyp`. | 
| `sapStartWorkflow:sapDocumentTyp` | No | `String` | The document type customized with the Workflow.| 
| `sapStartWorkflow:sapLateArchiving` | No | `Boolean` | To use late archiving (`true`) or early archiving (`false`).| 
| `sapStartWorkflow:sapOTyp` | No | `String` | The Organizational Management Object Type which is the organizational unit that is responsible for a business object type. Values can be: `US` (User), `C` (Job), `O` (Organizational Unit), `P` (Person), `S` (Position) or `A` (Work Center). | 
| `sapStartWorkflow:sapObjId` | No | `String` | The Organizational Management Agent Id. Specifies the agent ID for Workflow Management. | 
| `sapStartWorkflow:sapStartWorkflow` | No | `Boolean` | Whether to create the SAP Workflow inbox task for the Username.| 

#### Example Configuration File
This is an example configuration:
```
{
    "mapping": [ 
        {
        "path": "/default-domain/workspaces/SAP/SAP Content/Connect to SAP/Send Barcode",
        "facets": ["fct_sap_http_content", "fct_sap_barcode"],
        "createParentALFolder": true,
        "properties": {
            "sapHttpContent:docId": "$id",
            "sapHttpContent:source":"nuxeo-archivelink-s4h",
            "sapHttpContent:archiveId":"G1",
            "sapHttpContent:compId": "data",
            "sapHttpContent:modified": "$dc:modified",
            "sapHttpContent:created": "$dc:created",
            "sapHttpContent:scanPerformed":false,
            "sapBarcode:docTyp":"Z_MF_BAR"
            }
        },
        {
        "path": "/default-domain/workspaces/SAP/SAP Content/Connect to SAP/Link AL Document/Invoice",
        "facets": ["fct_sap_http_content", "fct_sap_create_al_document"],
        "createParentALFolder": true,
        "properties": {
            "sapHttpContent:docId": "$id",
            "sapHttpContent:source":"nuxeo-archivelink-s4h",
            "sapHttpContent:archiveId":"G1",
            "sapHttpContent:compId": "data",
            "sapHttpContent:modified": "$dc:modified",
            "sapHttpContent:created": "$dc:created",
            "sapHttpContent:scanPerformed": false,
            "sapCreateALDocument:sapObject": "BKPF",
            "sapCreateALDocument:sapArchiveObject":"Z_BKPF_NUX",
            "sapCreateALDocument:sapCreator": "NUXEO",
            "sapCreateALDocument:sapClient": "100",
            "sapCreateALDocument:sapFilename": "$file:content.name",
            "sapCreateALDocument:sapArchiveDate": "$dc:created"
            }
        }
    }
    ]
}
```

#### Upload and Preparation of the File
Once the file is configured, it must be uploaded to the Nuxeo repository.

1) Create a new Document in any folder of the repository and name it as you like.
    > We recommend to create the Document always below the defined `adminPath` folder of the SAP integration (refer to [Storage Connections](/configuration/aqilink/?id=hyland-nuxeo-repository)) and give it a speaking title such as `Connect to SAP Feature Configuration`.

    ![Config file path](../_media/reference/feature_connect2sap_configfilepath.png)
2) The file must be flagged as a configuration file for this feature. Therefore, the Facet **`fct_sap_config_file_connect2sap`** must be added to the Document using a Rest-client such as Postman or any other method. The following example of a `cURL` command will add the facet to the file `Connect to SAP Feature Configuration` located in the `adminPath` (as suggested above):

```
curl --location --request POST 'http://localhost:8080/nuxeo/api/v1/path/default-domain/workspaces/SAP/SAP%20Administration/Connect%20to%20SAP%20Feature%20Configuration/@op/Document.AddFacet' \
--header 'Content-Type: application/json' \
--header 'properties: *' \
--header 'Authorization: Basic QWRtaW5pc3RyYXRvcjpUX290UERHOERNQVYzSldUczYhQTNfY1pH' \
--data-raw '{
    "params": {
        "facet": "fct_sap_config_file_connect2sap"
    }
}'
```

#### Secure the File by applying Permissions
As this configuration file is available in the repository it can be potentially accessed by any user in the system. This should be necessarily prevented! 

> Make sure to prevent file access from end user.

Any change in the file content reflects immediately in the business logic! We strongly recommend to set file permissions to allow only the administrator access to edit the file and its content.

### Configure Task
To trigger the desired action, a separate [Task](/configuration/aqishare/tasks) must be configured. For the most common use-cases the following templates are available:

* Link ArchiveLink Document to SAP Business Object
* Link Document through Barcode scenario
* Start a SAP Workflow and attach the current document

> To download the templates, refer to package [`aqilink-templates`](/installation/app-download?id=other-related-software).

## User Experience
The usage of this feature from an endusers perspective.

### Upload a document

In Nuxeo, navigate to the folder which covers the desired use-case - refer to the value of parameter `path` in the [configuration file](/reference/nuxeo-reference-connect-to-sap?id=configuration-file) above. Depending on setting `createParentALFolder` [also part of the [configuration file](/reference/nuxeo-reference-connect-to-sap?id=configuration-file)], the file will be wrapped with a new folder (recommended setting) right after click on "Create" and the file will be moved into this folder. The uploaded document as well as the newly created folder will have a new tab `SAP` available.

![Upload file Connect2SAP](../_media/reference/feature_connect2sap_uploadfile.png)

### Fill required fields

Select the document and navigate to tab `SAP` where all relevant properties are present to connect the current document to an existing SAP Business Object. Depending on the prefilled settings in [configuration file](/reference/nuxeo-reference-connect-to-sap?id=configuration-file), the properties are already filled with values.


> It is possible to automate this process completely or use a semi-automateded process which only a few pre-filled properties (as in this example).

![SAP Properties Connect2SAP](../_media/reference/feature_connect2sap_sap_facet.png)

Edit the document details and enter the missing information to finally connect the document to the corresponding SAP Business Object. Once, all required properties are available, the document will be connected to the corresponding SAP Business Object.

![SAP Edit Properties Connect2SAP](../_media/reference/feature_connect2sap_edit_properties.png)

A new facet `SAP Task Status` will be available on the document which display the current state. If the document was successful connected to the SAP Business Object, it should look like this:

![SAP Task Status Connect2SAP](../_media/reference/feature_connect2sap_sap_taskstatus.png)


### Verify in SAP

In SAP open the Business Object to verify the successful connection. The uploaded document in Nuxeo is now available in the attachment list of the related SAP Business Object.

![SAP BO Connect2SAP](../_media/reference/feature_connect2sap_sap_document_attachmentlist.png)
