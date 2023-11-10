# Tasks

Tasks are finally the actions, that are executed against the SAP system and that contains either the RFC or the Rest service call along with necessary property mappings. Each action has to be defined in a separate file under the `config/tasks/` folder of `aqilink`. The file name can be set to any name but it's recommended that the name should briefly contain a short hint of the task inside (such as *barcode_S4H.yaml* or *replicateMetadata.yaml* or *startWorkflow_S4H.yaml* ). 

## Main Level Properties


| Property      | Mandatory | Type | Description |
| ----------- | ----------- |----------- | ----------- | 
| `enabled` | No | Boolean | If set to `false`, the task is disabled and will not processed. Default is `true`.  | 
| `storage` | Yes | String | The connected storage system to be used. For allowed values refer to section [Storage Connections](/configuration/aqilink/#storage-connections).  | 
| `sap` | Yes | String | The SAP system to which the task should connect to. Must match the value of `name` property in  [SAP Connection Properties](/configuration/aqishare/sap-connection.md). | 
| `trigger` | Yes | Object | Defines the execution of the task either by a CRON expression or an access key to trigger the task manually.<br/>[Refer below to Property `trigger` specification](#property-trigger). | 
| `query` | Yes | Object | Defines the query which will be executed in the repository to retrieve data that should be processed.<br/> [Refer below to Property `query` specification](#property-query).|
| `mapping` | Yes | Object | Specifies the SAP function module or Rest service and define the property mapping which should be passed to SAP and the property mapping of the result to the properties of the repository.<br/> [Refer below to Property `mapping` specification](#property-mapping). |
| `attempts` | No | Number | The total number of attempts to try the job until it completes. If exceeded the document will be excluded for each new run. Default: `3`  |
| `backoff` | No | Number | Backoff setting for automatic retries if the job fails in Milliseconds. Needs `attempts` to be set. Default: `600000` (10 min)  |
| `removeOnComplete` | No | Boolean, Number, KeepJobs | If `true` or unset, removes the job when it successfully completes from the [Bull Queue](/installation/app-configuration?#queue-monitoring) (default behavior). If set to a number, the specified number of jobs will be retained. For KeyJobs, refer to the [KeyJobs interface](https://github.com/OptimalBits/bull/blob/develop/REFERENCE.md#keepjobs-options).  |

### Property `trigger`
The `trigger` property defines how the task can be executed. This can be either done automatically using a CRON expression or manually by invoking the task with an access key.


| Property      | Mandatory | Type | Description |
| ----------- | ----------- |----------- | ----------- | 
| `cron` | No | String | The CRON expression used to invoke the task automatically.   | 
| `accessToken` | No | String | An arbitrary token used to trigger the task manually from any third-party system. Make sure to use an unique token across each task. Make sure to use a randomly generated token with at least 15 characters. | 

> You must set at least one of both properties.

#### Trigger Tasks manually using `accessToken`
To trigger a Task manually, an `accessToken` must be specified. The value of this token has to be passed in the `AppAuth` header of the request. Then endpoint on the `aqilink` server is the `/task/` folder followed by the name of the Task file without file extension. If you dont want to trigger the Task from outside at all, remove the `accessToken` property.

```
  curl --location --request GET 'http://<aqilink-server>:3000/task/myTask1' \
      --header 'App-Auth: eyJhbGciOiJIUzI1NiJ9.eyJSb2xlIjoiQWRtaW4iLCJJc3N1Z' 
```

#### Example
This example configuration of the `trigger` property demonstrates the usage with a CRON expression (running every full minute) and the possibility to trigger the Task at any time from outside using the `accessToken`.
```
trigger:
  cron: '* * * * *'
  accessToken: 'eyJhbGciOiJIUzI1NiJ9.eyJSb2xlIjoiQWRtaW4iLCJJc3N1Z'
```

### Property `query`
The `query` property defines query send to the repository to find related documents. 
Support for Nuxeo only - refer to [Nuxeo Documentation | NXQL](https://doc.nuxeo.com/nxdoc/nxql/).

| Property      | Mandatory | Type | Description |
| ----------- | ----------- |----------- | ----------- | 
| `type` | Yes | String | The query type. Available values: `nuxeo`.  | 
| `language` | No | String | The language of the query below. Default: `NXQL`. | 
| `query` | Yes | String | The query to find the documents in the repository. Must include an exclusion criterion for documents that has been already processed. |
| `properties` | No | String | Query properties to be considered only. Default: `*` (all). | 
| `schemas` | No | String Array | Schemas to be considered. Default: *`empty`*. |
| `sortBy` | No | String Array | The property to sort the result set. | 
| `sortOrder` | No | String Array | The sort order. Default: `DESC`.  | 

> Do not forget to specify an exclusion criterion for the documents in the `query`!

> If you use multiple `YAML` files with same logic but for different [SAP Content Repository Connections]/configuration/aqilink/#sap-http-content-server-connection), make sure to include and exclude the values of the SAP Repository Connections in the files according your needs. Query excerpt to exclude documents arrived via SAP Content Repository Connection `attachments-mySAP`:

```
... AND sapHttpContent:source <> 'attachments-mySAP'
```


#### Example
This configuration of the `query` property will find all documents stored from SAP in Nuxeo that has not yet the `fct_sap_replicate_details` facet (which is the exclusion criterion for the Task).
```
query:
  type: nuxeo
  language: NXQL
  query: SELECT * FROM Document WHERE ecm:primaryType = 'File' AND ecm:mixinType = 'fct_sap_http_content' AND ecm:mixinType <> 'fct_sap_replicate_details' AND ecm:isTrashed = 0
```

### Property `mapping`
The `mapping` property defines the assignment of properties between the repository and the desired RFC function module or Rest service, which are required to run the action on SAP side. It also defines the return mapping, meaning, the assignment of return values to properties in the repository. 

| Property      | Mandatory | Type | Description |
| ----------- | ----------- |----------- | ----------- | 
| `type` | Yes | String | The connection type to be used to invoke SAP. To call function modules through RFC, use `rfc`. To call a Rest (OData) service use `rest`.   | 
| `sapFunctions` | Yes | Object | The object containing the property assignments. | 
| `success` | Yes | Object | Handle the success case of the SAP return. |
| `failure` | Yes | Object | Handle the error case of the SAP return. |  

#### Property `sapFunctions`
This property is a child of the [`mapping` property](#property-mapping) and contains the assignments of all properties from the repository that must be mapped to import parameter of the SAP function module. It also handles the return mapping, meaning, which properties from the result should be mapped to which properties of the document in the repository.

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
Use the `script` parameter to alter the value with JavaScript functionality. This can be useful if the value needs to be parsed and/or formatted to match a target format. Dealing with date objects and boolen values are possible use cases for using this parameter.

The following properties will be passed to the `script` and can thus be accessed within it.

| Property      | Description |
| ----------- |  ----------- | 
| `logger` | The logger instance. Can be used to log various activities within the script.  | 
| `props` | The properties of the current document being processed by the function module.   | 
| `dateFns` | Used if the value should be formatted as a date or parsed from a date. You can use any functions of the [date-fns](https://date-fns.org/) library.  | 
| `content` | The content of the currently processed property in the configuration includes `target`, `content`, and, if present, `script` and `constant`.  | 
| `value` | The value from the `source` parameter.    | 

1) Example mapping to send a date from Nuxeo to SAP.
   
* Value for `dc:created` in Nuxeo: `2022-12-24T11:59:06.375Z`
* Pattern used to parse this as date: `yyyy-MM-dd'T'HH:mm:ss.SSSxxx`
* Pattern used to format the parsed date to match the format of the SAP import parameter (type 'DATS'): `yyyyMMdd`

The related `script` parameter for an import parameter can look like:
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

2) Example mapping to send a boolean value to SAP.

In SAP ABAP, the concept of a `Boolean` data type does not exist. Instead, ABAP uses integer types or character types to represent boolean values. Typically, a single character type `CHAR(1)` is used, with `'X'` representing *true* and a space `' '` representing *false*. 

The related `script` parameter for an import parameter which handles a `Boolean` can look like: 

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
This property is a child of the [`mapping` property](#property-mapping) and can be used to handle the SAP function module call in any success case. For example, use this property to set an exclusion criterion to prevent the next Task run to pick up the document again (refer to [Property `query`](#property-query) above).
To define the `success` property refer to the [Content Properties](#content-property) specification above.

##### Example
This configuration will add the `aqilink` "SAP Task Status" facet (`fct_sap_task_status`) to the document and set the `status` property of it to the constant *Success* and the `error` property of the facet to constant *No Error*.
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
This property is a child of the [`mapping` property](#property-mapping) and can be used to handle any errors returned by the SAP function module. To define the `success` property refer to the [Content Properties](#content-property) specification.

##### Example
This configuration will add the `aqilink` "SAP Task Status" facet (`fct_sap_task_status`) to the document and set the `status` property of it to the constant *Error* . The `error` property of the facet is set to the value of the parameter `MESSAGE` returned as *Exception* in the *Exceptions* tab of the SAP function module (note: `constant` is `false` in this case).
```
  failure:
    - source: 'Error'
      constant: true
      target: 'fct_sap_task_status:sapTaskStatus:status'
    - source: MESSAGE
      constant: false
      target: 'fct_sap_task_status:sapTaskStatus:error'
```

