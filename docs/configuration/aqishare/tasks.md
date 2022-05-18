# Task Properties

## Main Level Parameter


| Parameter      | Mandatory | Type | Description |
| ----------- | ----------- |----------- | ----------- | 
| `enabled` | No | Boolean | If set to `false`, the task is disabled and will not processed. Default is `true`.  | 
| `storage` | Yes | String | The connected storage system to be used. For allowed values refer to section [Storage Connections](../configuration/aqilink/#storage-connections).  | 
| `sap` | Yes | String | The SAP system to which the task should connect to. Must be the value of `name` parameter in section [SAP Connection Properties](../configuration/aqishare/sap-connection.md). | 
| `trigger` | Yes | Object | Defines the execution of the task either by a CRON expression or an access key to trigger the task manually.  | 
| `query` | Yes | Object | Defines the query which will be executed in the repository to retrieve data that should be processed. |
| `mapping` | Yes | Object | Specifies the SAP function module or Rest service and define the parameter mapping which should be passed to SAP and the parameter mapping of the result to the properties of the repository. |


### Parameter `trigger`
The `trigger` parameter defines how the task can be executed. This can be either done automatically using a CRON expression, or it manually by invoking the task with an access key.


| Parameter      | Mandatory | Type | Description |
| ----------- | ----------- |----------- | ----------- | 
| `cron` | No | String | The CRON expression used to invoke the task automatically.   | 
| `accessToken` | No | String | An arbitrary token used to trigger the task manually from any third-party system. Make sure to use an unique token across each task. Make sure to use a randomly generated token with at least 15 characters. | 

#### Example
```
trigger:
  cron: '* * * * *'
  accessToken: 'eyJhbGciOiJIUzI1NiJ9.eyJSb2xlIjoiQWRtaW4iLCJJc3N1ZXIiOiJJc3N1ZXIiLCJVc2VybmFtZSI6IkphdmFJblVzZS'
```

### Parameter `query`
The `query` parameter defines query send to the repository to find related documents.

| Parameter      | Mandatory | Type | Description |
| ----------- | ----------- |----------- | ----------- | 
| `type` | Yes | String | The query type. Depends on the available query options of the repository.  | 
| `query` | Yes | String | The query language statement to find the documents in the repository that should be used in the task. | 

> Do not forget to specify an exclusion criterion for the documents!

#### Example
```
query:
  type: nuxeo
  query: SELECT * FROM Document WHERE ecm:mixinType != 'HiddenInNavigation' AND ecm:isProxy = 0 AND ecm:isVersion = 0 AND ecm:isTrashed = 0 AND sapBarcode:barcode IS NOT NULL AND sapBarcode:barcode != '' AND dc:description != 'Sent'
```