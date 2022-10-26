# SAP Connections

The `sapConnection.yaml` specifies the outbound connections to the SAP systems. It also includes the type of the connection, meaning, whether a Rest (OData) service or a SAP function module through RFC is invoked. Depending on the type, different properties are required. The below  tables lists all available properties for an outbound connection to the SAP system.

> Depending on the **`aqilink`** license there can be as many definitions as required in this file.

## RFC Connections
The following properties allows to create a connection through RFC with the SAP JavaConnector. It is possible to invoke any *remote enabled* function module in the SAP system. 

| Parameter      | Mandatory | Description | Example |
| ----------- | ----------- |----------- | ----------- | 
| **``name``** | Yes |   **Unique** name for each connection. Do not use special characters or spaces. Use latin characters, hyphens and numbers only. |  `sapS4H` |
| ``type`` | Yes | Use `rfc` for a RFC connetion. Use `rest` for invoking a Rest service (see below).  |  `rfc` |
| ``user`` | Yes | The SAP user used to logon to the SAP system.      | |
| ``password``, or<br/> `passwd` | Yes | The password of the user. Use [Password Encryption](/reference/password-encryption.md) to increase security.    | `s3cr3TW0rD`, or `ENC(lax/dg5gdf)`  |
| ``ashost`` | Yes | SAP server host name or IP address.    | `11.2.0.112` |
| ``sysnr`` | Yes | SAP System Number.      | `00` |
| ``client`` | Yes | Client used for the logon.      | `800` |
| ``lang`` | Yes | Language used for the logon.      | `DE` |

`aqilink` supports all properties of the the SAP Netweaver RFC communication (such as Logon Groups, Gateway, Proxy, ...). For a full list of the supported properties refer to Reference [SAP JavaConnector Properties]. 

### Example Configuration

```
- name: sapS4H
  type: rfc
  user: S4H_AQIPRO
  passwd: ENC(fu0cS5mf/W/yHY1ptCvkwKrjdLY)
  ashost: '11.2.0.112'
  sysnr: '00'
  client: '800'
  lang: en
``` 


## Rest / OData Connections
The following properties are related to a Rest (OData) connection to the SAP system. Make sure the desired OData service is enabled and active in the SAP system.

| Parameter      | Mandatory | Description | Example |
| ----------- | ----------- |----------- | ----------- | 
| **``name``** | Yes |   **Unique** name for each connection. Do not use special characters or spaces. Use latin characters, hyphens and numbers only. |  `sapS4HCloud` |
| ``type`` | Yes | Use `rest` for a Rest connetion. Use `rfc` for invoking a SAP function module (see above).  |  `rest` |
| ``host`` | Yes | The host name or IP address of the SAP S/4HANA system (or SAP Cloud System).      | `11.2.0.112`  |
| ``port`` | Yes | Port number of the SAP system  | `8090`  |
| ``auth`` | Yes | Authentication section containing login credentials below.    |  |
| ``user`` | Yes | The OData user used to login to the system. <br/>Remember the text indentation in the YAML-syntax for this property, as it is child of `auth`.      | `odata_aqilink_user` |
| ``password`` | Yes | The password of the user. Use [Password Encryption](/reference/password-encryption.md) to increase security.<br/>Remember the text indentation in the YAML-syntax for this property, as it is child of `auth`.     | `s3cr3TW0rD`, or `ENC(lax/dg5gdf)`  |
