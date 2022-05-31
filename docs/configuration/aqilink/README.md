# Configure **`aqilink`**

This chapter explain the configuration options for **`aqilink`** to connect SAP with a content repository. The main configuration is split into three different [YAML files](https://yaml.org) to offer maximum flexibility.  

| File      | Description | 
| ----------- | ----------- |
| ``sapHttp.yaml`` | Defines all HTTP inbound connections from the SAP system and maps it to the related section in the `storage.yaml`.  |
| ``storage.yaml`` | Defines the connection parameters to connect to the reqired repository. |

 The following image shows the relation of the values within the configuration files.

<img src="../../_media/aqilink_value_mapping_files.png" width="90%" alt="aqilink Value Mapping"/>

## SAP HTTP-Content-Server Connection
The `sapHttp.yaml` defines the inbound connections from the SAP system and maps it to the related repository configuration of the [Storage Connections](#storage-connections). Each inbound connection consists of the 3 properties listed in the table below.

> Depending on the **`aqilink`** license there can be as many definitions as required in this file.

| Parameter      | Mandatory | Description | Example |
| ----------- | ----------- |----------- | ----------- | 
| ``name`` | Yes |   **Unique** name for each connection. Should contain the name of the repository as the same name has to be used as URL parameter for `Http Script` of the SAP Content Repository configuration. By that, it's possible to identify the connected repository in SAP at a glance. |  `nuxeo-attachments` |
| ``storage`` | Yes | The repository definition in the `storage.yaml` to be used. Should reasonably contain the name of the repository. <br/>Other best practice: Do not use special characters or spaces. Use latin characters, hyphens and numbers only.     |  `nuxeo-att` |
| ``signatureEnabled`` | Yes | If set to `false` the signature check for all incoming requests regarding the SAP system is disbaled. Use with care! Default should be `true`      | ```true```|


## Storage Connections
The `storage.yaml` defines the connection parameter to the related repositories. 

> Depending on the **`aqilink`** license there can be as many definitions as required in this file.

### Hyland Nuxeo Repository
The below table lists all available parameters for the `storage.yaml` to establish a connection to a Hyland Nuxeo repository. For example configurations refer to the [related part in the Reference section](../reference/nuxeo-reference-configs#example-configuration-for-storageyaml). 

| Parameter      | Mandatory | Description | Example |
| ----------- | ----------- |----------- | ----------- | 
| ``name`` | Yes | Must match the exact name as provided for parameter `storage` in the [SAP Content Server Connection](#sap-http-content-server-connection) (`sapHttp.yaml`).       |  `nuxeo-att` |
| ``type`` | Yes | The type of the repository that should be connected. As the connection routes to Nuxeo, the value must be `nuxeo`. |  `nuxeo` |
| ``domain`` | No | The Domain under the Nuxeo Root which contains the Workspaces with documents. Default is `default`       | `default` |
| ``baseUrl`` | Yes | The URL to login into Nuxeo, including port number.         | `http://nuxeo:8080/nuxeo` |
| ``user`` | Yes| The user that should be used to login from SAP. We recommend to create a separate Nuxeo user for the `aqilink` connection.      | Administrator |
| ``password`` | Yes | The password for the user above. To increase security, the password can be encrypted. Refer to reference [Password Encryption](../../reference/password-encryption.md)!       | ENC(lax/dfk46dfj54zkltgm) |
| ``adminPath`` | Yes | The path in the repository where the SAP repository files and SAP certificates are stored. We recommend to use always the same `adminPath` for all SAP Content Repositories connected to this Nuxeo instance.      | `/default-domain/workspaces/admin`  |
| ``documentPath`` | Yes | The document root folder for all documents stored via the SAP Content Repository in Nuxeo. The value can be different for each configuration.     | `/default-domain/workspaces/documents`  | 


### Amazon S3 Bucket
The below table lists all available parameters for the `storage.yaml` to establish a connection to an Amazon S3 Bucket. For example configurations refer to the [related part in the Reference section](../reference/s3-reference-configs#example-configuration-for-storageyaml). 

| Parameter      | Mandatory | Description | Example |
| ----------- | ----------- |----------- | ----------- | 
| ``name`` | Yes | Must match the exact name as provided for parameter `storage` in the [SAP Content Server Connection](#sap-http-content-server-connection) (`sapHttp.yaml`).       |  `s3-archive` |
| ``type`` | Yes | The type of the repository that should be connected. As the connection routes to an Amazon S3 bucket, the value must be `s3`. |  `s3` |
| ``endPoint`` | Yes | The host name or IP address of the Amazon S3 instance.      | `https://s3.amazonaws.com` |
| ``port`` | Yes | TCP/IP port number of the `endPoint` above.       | `9000` |
| ``useSSL`` | Yes | Set to `true` to use HTTPS instead of HTTP. Default is `true`.       | `true` |
| ``accessKey`` | Yes| The key to uniquely identify the Amazon S3 account.      |  |
| ``secretKey`` | Yes |  The secret key (password) to login with the `access Key`      |  |
| ``bucket`` | Yes | The name of the bucket in the Amazon S3 account where the documents from SAP should be stored.     | `sap-archive` |
| ``prefix`` | Yes | Prefix for all documents stored via the SAP Content Repository in the bucket. | `AL`  | 
| ``sessionToken`` | No |  Provide the `x-amz-security-token`.       |  |
| ``region`` | No |  Override the region cache.       | `us-west-2` |
| ``transport`` | No |  Pass a custom transport.      |  |
| ``partSize`` | No |  Override the default part size of 64MB for multipart uploads.      |  |
| ``pathStyle`` | No |  Override default access behavior (path) for non AWS endpoints.      | `true` |

## Connect SAP Content Repository via **`aqilink`** with Repository
Step-by-step guide to customize SAP to connect a repository (such as Nuxeo or Amazon S3) through **`aqilink`** storing documents through ArchiveLink. The section will refer to the above configurations for [SAP Content Repository Connection](#sap-http-content-server-connection) and [Storage Connection](#storage-connections).

1) Login to the SAP system as Administrator, or at least with acceess to the following transactions: `OAC0`, `OAC2`, `OAC3`, `OAM1`.
2) Open transaction `OAC0` to create a new connection to a content repository. Now switch to Edit mode and hit `F5`.  
3) Now create a new entry using the following values:

   | Property      | Description | Example |
   | ----------- | ----------- | ----------- | 
   | Content Rep. | Unique name for the repository in SAP.  |  `Z1`, `TX` |
   | Description | A description to identify the repository easily. Visible only to SAP admin in the SAP Content Repository overview.  |  `Nuxeo ECM Content Repository` |
   | Document Area | Select `ArchiveLink` from the dropdown list to use ArchiveLink and Attachment functionality. Select `Document Management System` to use SAP DMS (Document Info Records).  |  `ArchiveLink`, `DMS` |
   | Storage Type | Always select `HTTP content server` from the list. |  `HTTP content server` |
   | Version no. | Specify the SAP Content Server version. Supported are `0045`, `0046` and `0047` (recommended for all SAP S/4HANA systems) . |  `0047` |
   | HTTP server | Specify the server, IP address or hostname, where `aqilink` is running. |  `11.2.0.112` |
   | Port Number | Specify the port on the `aqilink` server above where the app is running. Default: `3000`. If necessary change the port mapping based on the used orchestration tool. |  `3000` |
   | HTTP Script | The main entry point of the `aqilink` app. It always starts with the prefix **`sapHttp`** followed by the `name` property in the `sapHttp.yaml`. Refer to [SAP Content Repository Connection](#sap-http-content-server-connection) above. |  `sapHttp/archivelink-nuxeo` |

   <img src="../../_media/sap_customizing/0001_oac0_create.png" border="" alt="">
4) Save the newly created repository.
5) Test the connection. 
6) 