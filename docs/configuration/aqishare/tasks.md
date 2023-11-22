# Tasks
Tasks are essentially actions executed against the SAP system, containing either an RFC or a REST service call, along with necessary property mappings. Each action must be defined in a separate file located in the `config/tasks/` folder of **`aqilink`**. The file name can be arbitrary, but it is recommended to choose a name that briefly hints at the task it represents (e.g., `barcode_S4H.yaml`, `replicateMetadata.yaml`, or `startWorkflow_S4H.yaml`). The structure of each Task file must adhere to the properties outlined below.

## Main Level Properties


| Property      | Mandatory | Type | Description |
| ----------- | ----------- |----------- | ----------- | 
| `enabled` | No | Boolean | If set to `false`, the task is disabled and will not processed. Default is `true`.  | 
| `storage` | Yes | String | The connected storage system to be used. For allowed values refer to section [Storage Connections](/configuration/aqilink/#storage-connections).  | 
| `sap` | Yes | String | The SAP system to which the task should connect to. Must match the value of `name` property in  [SAP Connection Properties](/configuration/aqishare/sap-connection.md). | 
| `trigger` | Yes | Object | Defines the execution of the task either by a CRON expression or an access key to trigger the task manually.<br/>[Refer below to Property `trigger` specification](#property-trigger). | 
| `query` | Yes | Object | Defines the query which will be executed in the repository to retrieve data that should be processed.<br/> [Refer below to Property `query` specification](#property-query).|
| `mapping` | Yes | Object | Specifies the SAP function module or REST service, and defines the property mapping to be passed to SAP, as well as the property mapping of the result to the repository's properties.<br/> [Refer below to Property `mapping` specification](#property-mapping). |
| `attempts` | No | Number | The total number of attempts to try the job until completion. If this number is exceeded, the document will be excluded from each subsequent run. Default: `3`  |
| `backoff` | No | Number | Backoff setting in milliseconds for automatic retries if the job fails. This requires the previouse mentioned `attempts` setting to be configured. Default value: `600000` (10 min)  |
| `removeOnComplete` | No | Boolean, Number, KeepJobs | If `true` or unset, removes the job when it successfully completes from the [Bull Queue](/installation/app-configuration?#queue-monitoring) (default behavior). If set to a number, the specified number of jobs will be retained. For KeyJobs, refer to the [KeyJobs interface](https://github.com/OptimalBits/bull/blob/develop/REFERENCE.md#keepjobs-options).  |

### Property `trigger`
An arbitrary token is used to manually trigger the task from any third-party system. Ensure that each task has a unique token. It is advisable to use a randomly generated token with at least 15 characters.

| Property      | Mandatory | Type | Description |
| ----------- | ----------- |----------- | ----------- | 
| `cron` | No | String | The CRON expression is used to automatically invoke the task.   | 
| `accessToken` | No | String | An arbitrary token used to trigger the task manually from any third-party system. Make sure to use an unique token across each task. Make sure to use a randomly generated token with at least 15 characters. | 

> You must set at least one of the two properties.

#### Example
This example configuration of the `trigger` property illustrates its use with a CRON expression (set to run every full minute) and the option to trigger the Task at any time externally using an `accessToken`.
```
trigger:
  cron: '* * * * *'
  accessToken: 'eyJhbGciOiJIUzI1NiJ9.eyJSb2xlIjoiQWRtaW4iLCJJc3N1Z'
```

#### Manually trigger a Task using an `accessToken`
To manually trigger a Task, an `accessToken` must be specified. The value of this token should be included in the `AppAuth` header of the request. The endpoint on the **`aqilink`** server is `/tasks/`, followed by the name of the Task file, excluding the file extension. If you do not wish to trigger the Task externally, remove the `accessToken` property. <br/>
Assuming the Task is defined in the file */tasks/barcode_S4H.yaml* and the `accessToken` is set to the value from the example above, the request should be formatted as follows:

```
  curl --location --request GET 'http://<aqilink-server>:3000/tasks/barcode_S4H' \
      --header 'App-Auth: eyJhbGciOiJIUzI1NiJ9.eyJSb2xlIjoiQWRtaW4iLCJJc3N1Z' 
```

### Property `query`
The `query` property specifies the query sent to the repository to search for related documents. Given that the underlying repositories differ significantly, necessitating distinct handling approaches, the documentation for this parameter is segregated based on the specific repository to enhance clarity.

> Remember to always include an exclusion criterion for the documents in the `query`! A best practice is to utilize the result value returned by either the [`success`](#property-success) or [`success`](#property-failure) properties.

#### Hyland Nuxeo specification
This chapter outlines the available settings for the `query` property when using a Hyland Nuxeo repository.

| Property      | Mandatory | Type | Description |
| ----------- | ----------- |----------- | ----------- | 
| `type` | Yes | String | Set value to `nuxeo`. For SharePoint refer to next chapter below.  | 
| `language` | No | String | The language used for the query (refer to next row). Default: `NXQL` | 
| `query` | Yes | String | The query is used to locate documents in the repository and should include an exclusion criterion for documents that have already been processed. For syntax refer to [Nuxeo Documentation - NXQL](https://doc.nuxeo.com/nxdoc/nxql/). |
| `properties` | No | String | A comma-separated list of properties to be returned from the documents of the query above. Only these data will be available in the result. Default: `*` (returns all properties) | 
| `sortBy` | No | String | The property name to sort the result set in the query. | 
| `sortOrder` | No | String | The sort order used to sort the property specified in `sortBy`. Options are `ASC` for ascending or `DESC` for descending. Default: `DESC`  | 


When using multiple `YAML` files with the same logic for different [SAP Content Repository Connections](/configuration/aqilink/#sap-http-content-server-connection), ensure to include the `name` of the *SAP Repository Connection* as criteria in the query to process only documents from the related source. The `name` is stored in property `source` of the `sapHttpContent` facet.
For example, if the `name` of the *SAP Repository Connection* is `nuxeo-al`, the query can be adapted as follows:
```
... WHERE sapHttpContent:source = 'nuxeo-al' AND ...
```
Example Nuxeo query with all properties:
```
query:
  type: nuxeo
  language: NXQL
  query: SELECT * FROM File WHERE sapHttpContent:source = 'nuxeo-al' AND ecm:mixinType = 'fct_sap_http_content' AND ecm:mixinType <> 'fct_sap_replicate_details' AND ecm:isTrashed = 0 AND sapTaskStatus:status <> 'Error'
  properties: '*'
  sortBy: 'dc:created'
  sortOrder: 'DESC'

```

#### Microsoft SharePoint specification
This chapter outlines the available settings of the `query` property when using Microsoft SharePoint Online as repository.

| Property      | Mandatory | Type | Description |
| ----------- | ----------- |----------- | ----------- | 
| `type` | Yes | String | Set value to `sharepoint`. For Hyland Nuxeo refer to the chapter above.  | 
| `query` | Yes | String | The query used to find documents in SharePoint. Should include an exclusion criterion for documents that have already been processed. For syntax refer to [SharePoint Documentation - KQL](https://learn.microsoft.com/en-us/sharepoint/dev/general-development/keyword-query-language-kql-syntax-reference). |

When using multiple `YAML` files with the same logic for different [SAP Content Repository Connections](/configuration/aqilink/#sap-http-content-server-connection), ensure to include the `name` of the *SAP Repository Connection* as criteria in the query to process only documents from the related source. The `name` is stored in the managed property `SAPSourceOWSTEXT`.
Additionally, it is strongly advised to include the SharePoint site name in the query, as a single `source` from different SAP systems may point to various sites. This precaution helps prevent the accidental processing of documents from unintended SAP systems.

For example, query for documents with value `sharepoint-al` as `source` name for the *SAP Repository Connection* in site `sap`:
```
...  AND SAPSourceOWSTEXT:"sharepoint-al" AND SPSiteURL:"https://<tenant>.sharepoint.com/sites/sap"
```
Example SharePoint query with all properties:
```
query:
  type: sharepoint
  query: (ContentType:"SAP Component") AND SAPSourceOWSTEXT:"sharepoint-al" AND SAPComponentIDOWSTEXT:"data" AND SPSiteURL:"https://<tenant>.sharepoint.com/sites/sap"
```

### Property `mapping`
The `mapping` property defines the assignment of properties between the repository and the desired RFC function module or Rest service, which are required to run the action on SAP side. It also outlines the return mapping, which is the assignment of return values to properties in the repository. 

| Property      | Mandatory | Type | Description |
| ----------- | ----------- |----------- | ----------- | 
| `type` | Yes | String | The connection type to be used for invoking SAP. Use `rfc` to call function modules through RFC. For calling a REST (OData) service, use `rest`. | 
| `sapFunctions` | Yes | Object | The object containing the property assignments. [Refer below to Property `sapFunctions` specification](#property-sapfunctions). | 
| `success` | Yes | Object | Handle the success case of the SAP return. [Refer below to Property `success` specification](#property-success). |
| `failure` | Yes | Object | Handle the error case of the SAP return. [Refer below to Property `failure` specification](#property-failure). |  

#### Property `sapFunctions`
This property, a subset of the [`mapping` property](#property-mapping), includes the assignments of all properties from the repository that must be mapped to the import parameters of the SAP function module. It also manages the return mapping, which involves determining which properties from the result should be mapped to specific properties of the document in the repository.

| Property      | Mandatory | Type | Description |
| ----------- | ----------- |----------- | ----------- | 
| **`- name`** | Yes | List/String | The name of the remote enabled SAP function module that should be invoked. The `sapFunction` property allows to invoke multiple SAP function modules. By that, the `name` property defines a list in the YAML syntax and has to be prefixed with a Hyphen.  | 
| `importParams` | Yes | List | List item of `name`. Handle mapping of all import parameter of the SAP function module.<br/> [Refer below to Property `importParams` specification](#property-importparams). |
| `exportParams` | Yes | List | List item of `name`. Handle mapping of all import parameter of the SAP function module. <br/> [Refer below to Property `exportParams` specification](#property-exportparams). |  

##### Property `importParams`
This property is a child of the [`sapFunctions` property](#property-sapfunctions). It defines the mapping between repository properties and import parameter of the SAP Function Module using a list of [Content Properties](#content-property). In this case, the `source` is a property from the repository and the `target` is a name of an import parameter of the SAP function module.

###### Example
This excerpt calls the SAP function module `ARCHIV_GET_CONNECTIONS` and set the import parameter `ARCHIV_ID` to the value of the property `sapHttpContent:archiveId` and `ARC_DOC_ID` to the value of `sapHttpContent:docId` from the current document.
```
mapping:
  type: rfc
  sapFunctions:
    - name: ARCHIV_GET_CONNECTIONS
      importParams:
        - target: ARCHIV_ID
          source: sapHttpContent:archiveId
        - target: ARC_DOC_ID
          source: sapHttpContent:docId
```

##### Property `exportParams`
This property is a child of the [`sapFunctions` property](#property-sapfunctions). It defines the mapping between the export parameter of the SAP Function Module and the repository properties using a list of [Content Properties](#content-property). In this case, the `source` is a name of an export parameter from the SAP function module return and the `target` is a property in the repository. Properties in the export parameter structure can be addressed by JSON syntax.

> Address properties in the SAP function module return with JSON syntax.

> To store properties where the related Facet is not yet on the document, add the Facet name as prefix for the `target` value. 

###### Example
This excerpt handle the result of the SAP function module `ARCHIV_GET_CONNECTIONS` and saves the `OBJECT_ID` into field `sapObjectId` and `SAP_OBJECT` into field `sapObject` of the current document. As the `OBJECT_ID` is returned in a table called `CONNECTIONS` that has only one result line, the `source` parameter must be set to `CONNECTIONS.0.OBJECT_ID`. 
```
mapping:
  type: rfc
  sapFunctions:
    - name: ARCHIV_GET_CONNECTIONS
      importParams:
        ...
      exportParams:
        - source: CONNECTIONS.0.OBJECT_ID
          target: 'fct_sap_replicate_details:sapReplicateDetails:sapObjectId'
        - source: CONNECTIONS.0.SAP_OBJECT
          target: 'fct_sap_replicate_details:sapReplicateDetails:sapObject'
```

##### Content Property
Specification of the available content properties used for `importParams`, `exportParams`, `success` and `failure`.

| Property      | Mandatory | Type | Description |
| ----------- | ----------- |----------- | ----------- | 
| **`source`** | Yes | String | For [**exportParams**](#property-exportparams) it must be a list property in the YAML syntax (requires a hyphen as prefix). For [**importParams**](#property-importparams), depending on the `constant` or `script` setting below, the value can either be a hardcoded or parsed/formatted by Javascript.  | 
| **`target`** | Yes | String | Same explanation as above - but reversed: For [**importParams**](#property-importparams) it must be a list property in the YAML syntax (requires a hyphen as prefix) and for [**exportParams**](#property-exportparams) the `constant` or `script` has effect. |  
| `constant` | No | Boolean | Set `true` to return a constant value in `source` (for [**importParams**](#property-importparams)) or `target` (for [**exportParams**](#property-exportparams)). Set `false` to return a value based on the return of the SAP function module. Default: `false`. |
| `script` | No | String | Modify the value using JavaScript functionality before sending or after retrieving it, for instance, by parsing (for [**exportParams**](#property-exportparams)) or formatting (for [**importParams**](#property-importparams)) the value. Refer to section below. |  


###### Using `script` parameter
Use the `script` parameter to modify the value using JavaScript functionality. This is beneficial when the value requires parsing and formatting to meet a specific format. Handling *Date* objects and *Boolean* values are examples of situations where this parameter can be effectively utilized.

The following properties will be passed to the `script`, allowing them to be accessed within it.

| Property      | Description |
| ----------- |  ----------- | 
| `logger` | The logger instance. Can be used to log various activities within the script.  | 
| `props` | The properties of the current document being processed by the function module.   | 
| `dateFns` | Use this if the value needs to be formatted as a date or parsed from a date. You can utilize any function from the [date-fns](https://date-fns.org/) library.  | 
| `content` | The content of the currently processed property in the configuration includes `target`, `content`, and, if present, `script` and `constant`.  | 
| `value` | The value from the `target` parameter when used in the context of *importParams*, or from the `source` parameter in the context of *exportParams*.    | 

1) Example mapping to send a *Date* from Nuxeo to SAP.
   
* Value for `dc:created` in Nuxeo: `2022-12-24T11:59:06.375Z`
* Pattern used to parse this as date: `yyyy-MM-dd'T'HH:mm:ss.SSSxxx`
* Pattern used to format the parsed date to match the format of the SAP import parameter (type [*DATS*](https://help.sap.com/doc/abapdocu_750_index_htm/7.50/en-US/abenddic_date_time_types.htm)): `YYYYMMDD`

The corresponding `script` parameter for the import parameter `AR_DATE`, used to invoke the function module `ARCHIV_CONNECTION_INSERT` with the *Date* from Nuxeo, will look like:
```
...
  sapFunctions:
    - name: ARCHIV_CONNECTION_INSERT
      importParams:
        - target: AR_DATE
          source: dc:created
          script: dateFns.format(dateFns.parse(value, "yyyy-MM-dd'T'HH:mm:ss.SSSxxx", new Date()), "yyyyMMdd")
          ...
```

2) Example mapping to send a *Boolean* value to SAP.

In SAP ABAP, the concept of a `Boolean` data type does not exist. Instead, ABAP uses integer types or character types to represent boolean values. Typically, a single character type `CHAR(1)` is used, with `'X'` representing *true* and a space `' '` representing *false*. 

The corresponding `script` parameter for the import parameter `LATE` in structure `DOCUMENT_ENTRY`, used to invoke the function module `ARCHIV_PROCESS_RFCINPUT` with the *Boolean* value from Nuxeo, will look like:
```
...
  sapFunctions:
    - name: ARCHIV_PROCESS_RFCINPUT
      importParams:
        - target: DOCUMENT_ENTRY.LATE
          source: sapStartWorkflow:sapLateArchiving
          script: 'value === true ? "X" : ""'
          ...
```


#### Property `success`
This property, a child of the [`mapping` property](#property-mapping), is utilized to manage the SAP function module call in successful scenarios. For instance, it can be employed to establish an exclusion criterion that prevents subsequent Task runs from reprocessing the same document (as referenced in the [Property `query`](#property-query) above).

##### Example
This configuration will add the **`aqilink`** "SAP Task Status" facet (`fct_sap_task_status`) in Nuxeo to the document if it is not already present. It will then set the `status` property of the facet to the constant value *Success* and the error property to *No Error*.
```
success:
  - source: 'Success'
    constant: true
    target: 'fct_sap_task_status:sapTaskStatus:status'
  - source: 'No Error'
    constant: true
    target: 'fct_sap_task_status:sapTaskStatus:error'
```

#### Property `failure`
Similar to the `success` property, the `failure` property is also a child of the [`mapping` property](#property-mapping). However, in contrast, it is used to manage any errors returned by the SAP function module (or REST call).

##### Example
This configuration will add the **`aqilink`** "SAP Task Status" facet (`fct_sap_task_status`) in Nuxeo to the document if it is not already present. It will then set the `status` property of the facet to the constant value *Error* and the value of the `error` property to the value of the SAP parameter `MESSAGE` that is returned as *Exception* in the *Exceptions* tab of the SAP function module.
```
  failure:
    - source: 'Error'
      constant: true
      target: 'fct_sap_task_status:sapTaskStatus:status'
    - source: MESSAGE
      constant: false
      target: 'fct_sap_task_status:sapTaskStatus:error'
```

