*Work in progress... We currently are updating and extending this chapter*

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
| `properties` |  List of properties and their default values. Prepend a `$` (Dollar sign) to any value that sould be read from the current uploaded document. For example: `$id` will be resolved as `input.id` and return the UUI of the document, `$dc:created` will be resolved as `input['dc:created']` and return the creation date of the document,...  |

> To succesfully connect to a SAP Business Object in any use case, the facet `fct_sap_http_content` must be added to the document.

The following parameter of facet `fct_sap_http_content` has to be set and present on the document (refer to example configuration below).

| Property | Description | 
| ----------- |----------- |
| `sapHttpContent:docId` | A unique id for SAP. Always use the current UUI of the created document: `$id`.  | 
| `sapHttpContent:source` | The unique source name to identify the SAP system which which should be triggered. Must match a `name` value in the `sapHttp.yaml` configuration file. Refer to [Configure Document Exchange](/configuration/aqilink/). | 
| `sapHttpContent:archiveId` | The SAP Content Repository name customized for the use case. |
| `sapHttpContent:compId` |  Required SAP ArchiveLink property. Use always the constant `data` for it. |
| `sapHttpContent:created` | Use the creation date of the current document, therefore use value `"$dc:created`.  |
| `sapHttpContent:modified` | Use the modification date of the current document, therefore use value `"$dc:modified`.  |
| `sapHttpContent:scanPerformed` | Parameter which indicates whether the content needs to be scanned by SAP Content Server. Set this to `false`. Not required for any scenario starting in Nuxeo, just for consistency for the SAP Content Server HTTP Interface. |


```
{
    "mapping": [ 
        {
        "path": "/default-domain/workspaces/SAP/SAP Content/Connect to SAP/Send Barcode",
        "facets": ["fct_sap_http_content", "fct_sap_barcode"],
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
        "path": "/default-domain/workspaces/SAP/SAP Content/Connect to SAP/Link ArchiveLink Document/Invoice",
        "facets": ["fct_sap_http_content", "fct_sap_create_al_document"],
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
That's it. Now a Task is required which calls an action on SAP side using the configuration file.

### Configure Task
To trigger the desired action, a separate [Task](/configuration/aqishare/tasks) must be configured. For the most common use-cases the following templates are available:

* Link ArchiveLink Document to SAP Business Object
* Link Attachment to SAP Business Object
* Link Document through Barcode
* Start a SAP Workflow and attach the current document

> To download the templates refer to package [`aqilink-templates`](/installation/app-download?id=other-related-software).

## User Experience
The usage of this feature from an endusers perspective.

TBD