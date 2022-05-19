# Tasks

Tasks are finally the actions, that are executed against the SAP system and that contains either the RFC or the Rest service call along with necessary property mappings. Each action has to be defined in a separate file under the `config/tasks/` folder of `aqilink`. The file name can be set to any name but it's recommended that the name should briefly contain a short hint of the task inside (such as *barcode_S4H.yaml* or *replicateMetadata.yaml* or *startWorkflow_S4H.yaml* ). 

## Main Level Properties


| Property      | Mandatory | Type | Description |
| ----------- | ----------- |----------- | ----------- | 
| `enabled` | No | Boolean | If set to `false`, the task is disabled and will not processed. Default is `true`.  | 
| `storage` | Yes | String | The connected storage system to be used. For allowed values refer to section [Storage Connections](../configuration/aqilink/#storage-connections).  | 
| `sap` | Yes | String | The SAP system to which the task should connect to. Must match the value of `name` property in  [SAP Connection Properties](../configuration/aqishare/sap-connection.md). | 
| `trigger` | Yes | Object | Defines the execution of the task either by a CRON expression or an access key to trigger the task manually.<br/>[Refer below to Property `trigger` specification](#property-trigger). | 
| `query` | Yes | Object | Defines the query which will be executed in the repository to retrieve data that should be processed.<br/> [Refer below to Property `query` specification](#property-query).|
| `mapping` | Yes | Object | Specifies the SAP function module or Rest service and define the property mapping which should be passed to SAP and the property mapping of the result to the properties of the repository.<br/> [Refer below to Property `mapping` specification](#property-mapping). |


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
| **`- source`** | Yes | List/String | List property in the YAML syntax. Needs a Hyphen as prefix. Depending on the `constant` below, the value can either be a constant or a value from the returning SAP function module call.    | 
| `target` | Yes | String | Define the property in the repository where the value from `source` should be stored. |  
| `constant` | No | Boolean | Set `true` to return a constant value in `source`. Set `false` to return a value based on the return of the SAP function module. Default: `false`. |
| `prefix` | No | String | A value that should be prefixed to the `source` value before storing it to `target`. Can be any constant only.  |  
| `suffix` | No | String | A suffix that should be appended to the `source` value before storing it to `target`. Can be any constant only. |  
| `format` | No | String | Used to format the value of `source` before storing it to `target`. Can be used to handle datetimes - example: `YYMMDD`.  |  
| `substring` | No | String | Get a substring from `source` before storing it to `target`. Use `start`and optional `end` child elements. Example:<br/>`substring:`<br/>`  start: 5`<br/>`  end: 25` <br/>Refer to [Mozilla substring() documentation](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/substring#syntax)|

> If you use multiple optional properties to handle the value of the `- source` property, the processing order is the following:<br/> (1) `format`, (2) `prefix`, (3) `suffix`, (4) `substring`.


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

