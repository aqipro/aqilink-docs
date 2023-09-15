# Nuxeo - Reference for Feature *Connect to SAP*

The feature *Connect to SAP* enables the customer to create documents in Nuxeo and connect them to existing SAP Business Objects. The related properties of the SAP Business Object can be entered manually, fully automated, or through a hybrid solution.

* Connect a document uploaded in Nuxeo to an existing SAP Business Object as an attachment or ArchiveLink document.
* Link a document uploaded in Nuxeo, using a barcode, to an SAP Business Object.
* Initiate a SAP Workflow (create a WorkItem in a SAP user's inbox) based on a document uploaded in Nuxeo.

This feature is triggered by the `Document created` event for all `Regular Documents` of type `File` within the `/default-domain/workspaces/` path.

## Administration
The foundation of this feature is a JSON configuration file that contains all the necessary properties for identifying documents and adding related facets while also setting default values. Subsequently, a separate [Task](/configuration/aqishare/tasks) will pick up the documents and execute the desired function (either Rest or RFC) to connect them to the relevant SAP Business Objects. The process involves at least the following two steps:

1) Create and configure the JSON configuration file.
1) Create and enable the desired [Task](/configuration/aqishare/tasks).

### Configuration File
A configuration file within the Nuxeo repository is required to store the desired configurations for all use-cases that depend on this feature. The configuration file uses JSON syntax. Within the configuration file, you can set any property related to the SAP connection with a default value.

#### Creation & Configuration of the File
Create a new JSON file on your machine, for example, name it `aqilink_configuration_connect_to_sap.json`, and then paste the content of the [example below](/reference/nuxeo-reference-connect-to-sap?id=example-configuration-file) into it. The structure of the JSON content requires an array with the name `mapping`. The array stores all definitions of the related use-cases as JSON objects. Here's a template of the main structure for the file content. The parameter description can be found in the table below.

```
{
    "mapping": [ 
        {
            "path": "/default-domain/workspaces/",
            "facets": ["fct_sap_http_content"],
            "createParentALFolder": true,
            "properties": { }
        }
    ]
}
```

| Parameter | Description | 
| ----------- |----------- |
| `path` | The path in the Nuxeo repository where all documents for the current use case must be uploaded can be any folder path below `/default-domain/workspaces/`. | 
| `facets` |A comma-separated list of facets to be added to the uploaded document. The facet `fct_sap_http_content` must always be included for any use case involving this feature. For a comprehensive list of available facets and their descriptions, please refer to the following sections. | 
| `createParentALFolder` | Ensure that `true` is set to create a parent folder for the newly created document in Nuxeo. In this case the "facet `fct_sap_http_content` will be added to the parent folder, and all related properties will be populated accordingly. Subsequently, the newly created document will be relocated into this newly created folder. This practice guarantees complete compliance with the SAP HTTP-ArchiveLink protocol specification. **It is highly advisable to enable this setting (set to `true`) for any SAP ArchiveLink scenarios!** | 
| `properties` |  List of properties and their default values. Prepend a `$` (Dollar sign) to any value that sould be read from the current uploaded document. For example: `$id` will be resolved as `input.id` and return the UUI of the document, `$dc:created` will be resolved as `input['dc:created']` and return the creation date of the document, and so on... |

#### Mandatory Facets and Properties

There are at least two facets required for each document that should be connected to a SAP Business Object. While the facet 
`fct_sap_http_content` is mandatory for every document, the selection of the other facet depends on the specific use case and must be configured accordingly. The facet names must be added as comma-separated list for the parameter `facets` in the JSON file.

##### Facet `fct_sap_http_content`

 > This facet must always be added in order to use this feature! Depending on the use case, one or more of the facets below also have to be added.

The `fct_sap_http_content` facet contains all the mandatory fields necessary for the SAP HTTP-Content-Server connection. Ensure that you specify all properties of the table below in the `properties` section of the JSON file and set the values correctly.

| Property | Mandatory | Type | Description | 
| ----------- |----------- |----------- |----------- |
| `sapHttpContent:docId` | Yes | `String` | A unique id for SAP. Always pass the current UUID of the created document by using: `$id`.  | 
| `sapHttpContent:source` | Yes | `String` | The unique source name to identify the SAP system that should be triggered. Must match a `name` value in the `sapHttp.yaml` configuration file. Refer to [Configure Document Exchange](/configuration/aqilink/) for more information. | 
| `sapHttpContent:archiveId` | Yes | `String` | The SAP Content Repository name that is customized for the use case. |
| `sapHttpContent:compId` | Yes | `String` | Required SAP ArchiveLink property. Always use the following constant: **```data```** |
| `sapHttpContent:created` | Yes | `String` | Use the creation date of the current document by passing `$dc:created`.  |
| `sapHttpContent:modified` | Yes | `String` | Use the modification date of the current document by passing `$dc:modified`.  |
| `sapHttpContent:scanPerformed` | Yes | `Boolean` | Parameter that indicates whether the content needs to be scanned by the SAP HTTP-Content-Server. Always pass the value `false`. It is not required for any scenario starting in Nuxeo. This property is solely for the sake of consistency in the SAP HTTP-Content-Server interface. |

 > Once the document has been created, all properties of facet `fct_sap_http_content` are read-only and cannot be modified through the user interface (UI).

##### Facet `fct_sap_create_al_document`

This facet holds all fields required to connect the current document to a SAP Business Object. Depending on whether just a few or all fields are pre-filled, the process of connecting the document to the SAP Business Object can be completely automated.
 For instance, if you pre-fill and configure all properties in the table below, the document will be automatically linked to the associated SAP Business Object. However, if you only provide a few fields, the user will need to edit the document and manually enter values for all the missing fields before the document can be selected for the connection.

| Property | Mandatory | Type | Description | 
| ----------- |----------- |----------- |----------- |
| `sapCreateALDocument:sapObject` | No | `String` | The name of the SAP Object such as `BKPF` or `BUS2012` or `EQUI` (...).| 
| `sapCreateALDocument:sapArchiveObject` | No | `String` | The name of the SAP Archive Object which is customized in SAP transaction `OAC2`. For example: `Z_NUX_BKPF` or `Z_NUX_WF` (...). | 
| `sapCreateALDocument:sapCreator` | No | `String` | The creator name of the attachment. Must be an SAP username. |
| `sapCreateALDocument:sapClient` | No | `String` | SAP Client to login to SAP. |
| `sapCreateALDocument:sapFilename` | No | `String` | Use the filename of the current file by passing `$file:content.name`. |
| `sapCreateALDocument:sapArchiveDate` | No | `String` | Use the creation date of the current document by passing `$dc:created`.  |

##### Facet `fct_sap_barcode`
This facet contains all the fields necessary for using the Barcode scenario, which consist of only two properties:

| Property | Mandatory | Type | Description | 
| ----------- |----------- |----------- |----------- |
| `sapBarcode:barcode` | No | `String` | Enter the barcode which should be used to store it in SAP external barcode table. | 
| `sapBarcode:docTyp` | No | `String` | Provide the name of the document type associated with the Barcode scenario (check SAP transaction `OAC5`).| 

##### Facet `fct_sap_start_workflow`
This facet contains all the fields necessary to initiate a Workflow in SAP for a specific user (or group). A new WorkItem will be created in the users inbox (SAP Business Workplace, transaction `SBPW`).

| Property | Mandatory | Type | Description | 
| ----------- |----------- |----------- |----------- |
| `sapStartWorkflow:sapArchiveObject` | No | `String` | The name of the SAP Archive Object customized in the SAP transaction `OAC2` for the SAP Workflow, for example, `Z_NUX_WF`. | 
| `sapStartWorkflow:sapBarcode` | No | `String` | Initiate the SAP Worflow by using only a barcode instead using the values of the fields `sapHttpContent:archiveId`, `sapHttpContent:docId` and `sapStartWorkflow:sapDocumentTyp`. | 
| `sapStartWorkflow:sapDocumentTyp` | No | `String` | The document type customized with the Workflow.| 
| `sapStartWorkflow:sapLateArchiving` | No | `Boolean` | Wheather to use late archiving (`true`) or early archiving (`false`).| 
| `sapStartWorkflow:sapOTyp` | No | `String` | The Organizational Management Object Type which is the organizational unit that is responsible for a business object type. Values can be: `US` (User), `C` (Job), `O` (Organizational Unit), `P` (Person), `S` (Position) or `A` (Work Center). | 
| `sapStartWorkflow:sapObjId` | No | `String` | The Organizational Management Agent Id. Specifies the agent ID for Workflow Management. | 
| `sapStartWorkflow:sapStartWorkflow` | No | `Boolean` | Whether to initiate the SAP Workflow immediately (during the next [Task](/configuration/aqishare/tasks) run) or not. As long as this setting is set to `false`, no WorkItem will be created in the inbox of the specified SAP user. | 

#### Example Configuration File
This JSON example content covers the following three use cases:
 * Barcode scenario (first object in the `mapping`)
 * Link document to an invoice SAP Business Object (second object in the `mapping`)
 * Initiate a SAP Workflow (last object in the `mapping`)

```
{
    "mapping": [ 
        {
        "path": "/default-domain/workspaces/SAP/SAP Content/Connect to SAP/Send Barcode",
        "facets": ["fct_sap_http_content", "fct_sap_barcode"],
        "createParentALFolder": true,
        "properties": {
            "sapHttpContent:docId": "$id",
            "sapHttpContent:source":"nuxeo-al",
            "sapHttpContent:archiveId":"N1",
            "sapHttpContent:compId": "data",
            "sapHttpContent:modified": "$dc:modified",
            "sapHttpContent:created": "$dc:created",
            "sapHttpContent:scanPerformed":false,
            "sapBarcode:docTyp":"Z_BAR_NUX"
            }
        },
        {
        "path": "/default-domain/workspaces/SAP/SAP Content/Connect to SAP/Link AL Document/Invoice",
        "facets": ["fct_sap_http_content", "fct_sap_create_al_document"],
        "createParentALFolder": true,
        "properties": {
            "sapHttpContent:docId": "$id",
            "sapHttpContent:source":"nuxeo-al",
            "sapHttpContent:archiveId":"N1",
            "sapHttpContent:compId": "data",
            "sapHttpContent:modified": "$dc:modified",
            "sapHttpContent:created": "$dc:created",
            "sapHttpContent:scanPerformed": false,
            "sapCreateALDocument:sapObject": "BKPF",
            "sapCreateALDocument:sapArchiveObject":"Z_NUX_BKPF",
            "sapCreateALDocument:sapCreator": "NUXEO",
            "sapCreateALDocument:sapDescription": "Document from Nuxeo",
            "sapCreateALDocument:sapDocumentTyp": "PDF",
            "sapCreateALDocument:sapClient": "100",
            "sapCreateALDocument:sapFilename": "filename.pdf",
            "sapCreateALDocument:sapArchiveDate": "$dc:created"
            }
        },
        {
            "path": "/default-domain/workspaces/SAP/SAP Content/Connect to SAP/Link AL Document/Material",
            "facets": ["fct_sap_http_content", "fct_sap_create_al_document"],
            "createParentALFolder": true,
            "properties": {
                "sapHttpContent:docId": "$id",
                "sapHttpContent:source":"nuxeo-al",
                "sapHttpContent:archiveId":"N1",
                "sapHttpContent:compId": "data",
                "sapHttpContent:modified": "$dc:modified",
                "sapHttpContent:created": "$dc:created",
                "sapHttpContent:scanPerformed": false,
                "sapCreateALDocument:sapObject": "BUS1001006",
                "sapCreateALDocument:sapArchiveObject":"DOCUMENT",
                "sapCreateALDocument:sapCreator": "NUXEO",
                "sapCreateALDocument:sapClient": "100",
                "sapCreateALDocument:sapFilename": "$file:content.name",
                "sapCreateALDocument:sapArchiveDate": "$dc:created"
            }
        },
        {
            "path": "/default-domain/workspaces/SAP/SAP Content/Connect to SAP/Start SAP Workflow",
            "facets": ["fct_sap_http_content", "fct_sap_start_workflow"],
            "createParentALFolder": true,
            "properties": {
                "sapHttpContent:docId": "$id",
                "sapHttpContent:source":"nuxeo-al",
                "sapHttpContent:archiveId":"N1",
                "sapHttpContent:compId": "data",
                "sapHttpContent:modified": "$dc:modified",
                "sapHttpContent:created": "$dc:created",
                "sapHttpContent:scanPerformed": false,
                "sapStartWorkflow:sapArchiveObject":"Z_WF_NUX",
                "sapStartWorkflow:sapDocumentTyp": "PDF",
                "sapStartWorkflow:sapBarcode": "",
                "sapStartWorkflow:sapOTyp": "US",
                "sapStartWorkflow:sapObjId": "AQIPRO"
            }
        }
    ]
}
```

#### Upload and Preparation of the File
Once the JSON configuration file from above is properly configured, it must be uploaded to the Nuxeo repository. This step also requires an external REST client.

1) Upload the recently created JSON configuration file to the specified `adminPath` folder of the SAP integration (refer to [Storage Connections](/configuration/aqilink/?id=hyland-nuxeo-repository)), and provide it with a descriptive title, such as `Connect to SAP Feature Configuration`.

    > Note: The title is important to remember as it is required in the next step.

    ![Config file path](../_media/reference/feature_connect2sap_configfilepath.png)

2) Now, the document in Nuxeo needs to be labeled as a configuration file for this feature. Therefore, the facet **`fct_sap_config_file_connect2sap`** (which is solely used for internal purposes to identify the configuration file throughout the entire repository) must be added to the document using a REST client such as Postman or any other suitable method. The following example of a `cURL` command will add the facet to the document located in the `adminPath`:

    > Please keep in mind that the document title `Connect to SAP Feature Configuration` is required in the request!

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

#### Secure the file by applying permissions

Since this configuration file is available in the repository, it can potentially be accessed by any user in the system. This must be prevented.

> The Nuxeo administrator must ensure that file access is restricted from end users.

Any changes in the file's content will immediately affect the business logic. We strongly recommend setting file permissions to allow only the administrator to access and edit the file.

### Task Configuration

To initiate the desired action based on the JSON configuration, a separate [Task](/configuration/aqishare/tasks) must be configured for each use case of this feature. For the use cases described in this documentation (refer to Example JSON above) the related templates are also available:

* Link Nuxeo document through Barcode scenario
* Link Nuxeo document to a SAP Business Object
* Initiate a SAP Workflow 

> To download the templates, refer to package [`aqilink-templates`](/installation/app-download?id=other-related-software).

## User Experience
The usage of the*Connect to SAP* feature from an end user's perspective. This chapter guides you through the step for an end user to attach a document uploaded in Nuxeo to a SAP Business Object representing an invoice.

### Upload a document

In Nuxeo, navigate to the folder that corresponds to the desired use case, referring to the value of the parameter `path` in the [configuration file](/reference/nuxeo-reference-connect-to-sap?id=configuration-file) above.
Depending on the `createParentALFolder` setting (also part of the [configuration file](/reference/nuxeo-reference-connect-to-sap?id=configuration-file)), the file will be enclosed within a new folder immediately after clicking "Create," and the file will be relocated to this folder. The uploaded document, along with the newly created folder, will feature a new tab labeled `SAP` that is accessible.


![Upload file Connect2SAP](../_media/reference/feature_connect2sap_uploadfile.png)

### Fill required fields


Select the document and navigate to the `SAP` tab, where all the relevant properties are available to establish a connection between the current document and an existing SAP Business Object. Depending on the pre-filled settings in the [configuration file](/reference/nuxeo-reference-connect-to-sap?id=configuration-file), the properties may already contain values.

> As already mentioned, it is possible to automate this process completely or use a semi-automated process with only a few pre-filled properties.

In this example, SAP Object Id, which is the Invoice number, must be entered manually to initiate the connection. With this information, the end user can decide to which invoice in SAP the current document must be attached.

![SAP Properties Connect2SAP](../_media/reference/feature_connect2sap_sap_facet.png)

Edit the document details and enter the missing information. Once all mandatory properties are available, the document will be linked to the corresponding SAP Business Object.

![SAP Edit Properties Connect2SAP](../_media/reference/feature_connect2sap_edit_properties.png)

A new feature titled `SAP Task Status` will be available on the document, displaying the current status. If the document was successfully connected to the SAP Business Object, it should appear like this:
![SAP Task Status Connect2SAP](../_media/reference/feature_connect2sap_sap_taskstatus.png)


### Verify in SAP

In SAP, open the Business Object to verify the successful connection. The uploaded document in Nuxeo is now available in the attachment list of the related SAP Business Object. In the attachment list, it can be viewed, or a SAP Note can be added, for example.


![SAP BO Connect2SAP](../_media/reference/feature_connect2sap_sap_document_attachmentlist.png)
