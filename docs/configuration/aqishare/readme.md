# Configure **`aqishare`**

This chapter explain the configuration options for to replicate additional information from the SAP system towards the repository. It also describes to trigger any action from the repository in SAP.

Requirement for any additional metadata exchange in this section is to have the basic [`aqilink` connection](../../configuration/aqilink/) successfully up and running.

The configuration is split the following YAML files.

| File      | Description | 
| ----------- | ----------- |
| ``storage.yaml`` | Define the repository connection details. This is also the main configuration file for `aqilink`. Refer to [Storage Connections](../configuration/aqilink/#storage-connections). |
| ``sapConnection.yaml`` | Define the connection properties and the connection type (either Rest or RFC) to connect to the SAP systems. |
| ``tasks/myTask1.yaml``, ``tasks/myTask2.yaml``, ``tasks/myTaskx.yaml`` | Each file defines an action to be executed against the SAP system. This includes the Rest call or the RFC call, passing and receiving values from/to SAP and the storage of these values in the repository.  |


## SAP Connection Properties

The `sapConnection.yaml` specifies the outbound connections to the SAP systems. It also includes the type of the connection, meaning, whether a Rest (OData) service or a SAP function module through RFC is invoked. Depending on the type, different properties are required. The below  tables lists all available properties for an outbound connection to the SAP system.

> Depending on the **`aqilink`** license there can be as many definitions as required in this file.

### RFC related properties
The following properties allows to create a connection through RFC with the SAP JavaConnector. It is possible to invoke any *remote enabled* function module in the SAP system. 

| Parameter      | Mandatory | Description | Example |
| ----------- | ----------- |----------- | ----------- | 
| **``name``** | Yes |   **Unique** name for each connection. Do not use special characters or spaces. Use latin characters, hyphens and numbers only. |  `sapS4H` |
| ``type`` | Yes | Use `rfc` for a RFC connetion. Use `rest` for invoking a Rest service (see below)  |  `rfc` |
| ``user`` | Yes | The SAP user used to logon to the SAP system.      | |
| ``password``, or<br/> `passwd` | Yes | The password of the user. Use [Password Encryption](../../reference/password-encryption.md) to increase security.    | `s3cr3TW0rD`, or `ENC(lax/dg5gdf)`  |
| ``ashost`` | Yes | SAP server host name or IP address.    | `11.2.0.112` |
| ``sysnr`` | Yes | SAP System Number.      | `00` |
| ``client`` | Yes | Client used for the logon.      | `800` |
| ``lang`` | Yes | Language used for the logon.      | `DE` |

`aqilink` supports all properties of the the SAP Netweaver RFC communication (such as Logon Groups, Gateway, Proxy, ...). For a full list of the supported properties refer to Reference [SAP JavaConnector Properties](../) 

### Rest / OData related properties