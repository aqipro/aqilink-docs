# Configure *Document Exchange*

This chapter explain the configuration options for the *Document Exchange* (**`aqilink`**) module, which connects SAP to a content repository. The primary configuration is divided into two separate `YAML` files to provide maximum flexibility.


| File      | Description | 
| ----------- | ----------- |
| ``sapHttp.yaml`` | Defines all HTTP inbound connections from the SAP system and maps it to the related section in the `storage.yaml`.  |
| ``storage.yaml`` | Defines the connection parameters to connect to the reqired repository. |

The following image illustrates the relationship between the two files, specifically the property values within them. It highlights the primary mapping point between the Content Repository configuration in SAP and the  `sapHttp.yaml`.

![aqilink Value Mapping](../../_media/aqilink_value_mapping_files.png)

## SAP HTTP-Content-Server Connection
The `sapHttp.yaml` defines the inbound connections from the SAP system and maps them to the corresponding repository configuration of the [Storage Connections](#storage-connections). Each inbound connection comprises the three properties listed in the table below.

> Depending on the **`aqilink`** license there can be as many definitions as required in this file.

| Parameter      | Mandatory | Description | Example |
| ----------- | ----------- |----------- | ----------- | 
| ``name`` | Yes |   **Unique** name for each connection. Should contain the name of the repository as the same name has to be used as URL parameter for `Http Script` of the SAP Content Repository configuration. By that, it's possible to identify the connected repository in SAP at a glance. |  `nuxeo-attachments` |
| ``storage`` | Yes | The repository definition in the `storage.yaml` to be used. Should reasonably contain the name of the repository. <br/>Other best practice: Do not use special characters or spaces. Use latin characters, hyphens and numbers only.     |  `nuxeo-att` |
| ``signatureEnabled`` | No | Enables the signature check for the HTTP Content Server interface. If disabled, all requests will be accepted no matter if they are signed or not. Handle with care! Default is `true`.      | ```true```|
| ``checkExpiration`` | No | If enabled, a check occurs, whether the signed requests have been sent in the valid time window. Default is `true`.      | ```true```|

## Storage Connections
The `storage.yaml` defines the connection parameter to the related repositories. 

> Depending on the **`aqilink`** license there can be as many definitions as required in this file.

### Hyland Nuxeo Repository
The below table lists all available parameters for the `storage.yaml` to establish a connection to a Hyland Nuxeo repository. For example configurations refer to the [related part in the Reference section](../reference/nuxeo-reference-configs#example-configuration-for-storageyaml) or see [Step-by-step: Connect SAP with Nuxeo](/configuration/storages/nuxeo).

| Parameter      | Mandatory | Description | Example |
| ----------- | ----------- |----------- | ----------- | 
| ``name`` | Yes | Must match the exact name as provided for parameter `storage` in the [SAP Content Server Connection](#sap-http-content-server-connection) (`sapHttp.yaml`).       |  `nuxeo-att` |
| ``type`` | Yes | The type of the repository that should be connected. As the connection routes to Nuxeo, the value must be `nuxeo`. |  `nuxeo` |
| ``baseUrl`` | Yes | The URL to login into Nuxeo, including port number.         | `http://nuxeo:8080/nuxeo` |
| ``apiUrl`` | No | The Nuxeo URL to the Rest endpoint. Default: `api/v1`     | `api/v1`  | 
| ``proxy`` | No | Specifies the URL to the proxy server.      | `https://proxy:123`  | 
| ``ignoreSource`` | No | Ignore the parameter `source` in the search. Default: `false`.      | `false`  |
| ``adminPath`` | Yes | The path in the repository where the SAP repository files and SAP certificates are stored. We recommend to use always the same `adminPath` for all SAP Content Repositories connected to this Nuxeo instance.      | `/default-domain/workspaces/admin`  |
| ``documentPath`` | Yes | The document root folder for all documents stored via the SAP Content Repository in Nuxeo. The value can be different for each configuration.     | `/default-domain/workspaces/documents`  | 
| ``defaultFileType`` | No | Specifies the default type for all files created through the SAP interface. Default: `File`     | `MyFileType`  | 
| ``defaultFolderType`` | No | Specifies the default type for all folders created through the SAP interface. Default: `Folder`     | `MyFolderType`  | 
| ``doubleSearch`` | No | Enables the Elastic search. Default: `false`     | `false`  | 
| ``auth`` | Yes | Specifies the authentication credentials. Refer to property ``auth``    |   | 
| ``certCache`` | No | Specifies the certificate cache configuration. Refer to property ``certCache``    |   | 

#### Property `auth`
Based on type, the settings differs.

| Parameter      | Mandatory | Type | Description | Example |
| ----------- | ----------- |-----------|----------- | ----------- | 
| ``type`` | Yes | *all* | The authentication type, such as `basic` or `keycloak` | `basic` |
| ``user`` | Yes| basic |The user that should be used to login to Nuxeo. We recommend to create a separate Nuxeo user for the `aqilink` connection.      | Administrator |
| ``password`` | Yes | basic | The password for the user above. To increase security, the password can be encrypted. Refer to reference [Password Encryption](../../reference/password-encryption.md)!       | ENC(lax/dfk46dfj54zkltgm) |
| ``authServer`` | Yes| keycloak | URL to the keycloak server.      | `https://keycloak.myserver.com` |
| ``realm`` | Yes| keycloak | The realm in keycloak.      | `myRealm` |
| ``grantType`` | No | keycloak | The grant type for keycloak. Default: `password`.      | `password` |
| ``username`` | Yes | keycloak | Keycloak user.      | `administrator` |
| ``password`` | Yes | keycloak | Keycloak password for Keycloak user.      | `administrator` |
| ``clientSecret`` | Yes | keycloak | The client secret.      | `fwfj5eregervrztrfdfhlklk` |
| ``clientId`` | Yes | keycloak | The client Id.      | `nuxeo` |

#### Property `certCache`

The configuration for the certificate cache. The cleanup interval is set to 30 seconds and cannot be influenced.

| Parameter      | Mandatory | Description | Example |
| ----------- | ----------- |----------- | ----------- | 
| ``enabled`` | No | Enable or disable the certCache. Default: `false` | `false` |
| ``expiration`` | No | Specifies the expiration time of the cached certificates. Default: `30s` | `1h` |

### Amazon S3 Bucket
The below table lists all available parameters for the `storage.yaml` to establish a connection to an Amazon S3 Bucket. For example configurations refer to the [related part in the Reference section](../reference/s3-reference-configs#example-configuration-for-storageyaml) or see [Step-by-step: Connect SAP with S3](/configuration/storages/s3).

| Parameter      | Mandatory | Description | Example |
| ----------- | ----------- |----------- | ----------- | 
| ``name`` | Yes | Must match the exact name as provided for parameter `storage` in the [SAP Content Server Connection](#sap-http-content-server-connection) (`sapHttp.yaml`).       |  `s3-archive` |
| ``type`` | Yes | The type of the repository that should be connected. As the connection routes to an Amazon S3 bucket, the value must be `s3`. |  `s3` |
| ``endPoint`` | Yes | The host name or IP address to access the bucket, refer to [Methods for accessing a bucket](https://docs.aws.amazon.com/AmazonS3/latest/userguide/access-bucket-intro.html) in the official AWS S3 documentation.      | `https://s3.myregion.amazonaws.com` |
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

### Microsoft SharePoint Online (SPO)
The below table lists all available parameters for the `storage.yaml` to establish a connection to a Microsoft SharePoint Online site. For example configurations refer to the [related part in the Reference section](../reference/s3-reference-configs#example-configuration-for-storageyaml).

* To ensure a successful connection with SharePoint Online, make sure you have completed the chapter [Create Microsoft Azure Application](/configuration/storages/sharepoint/azure)!

| Parameter      | Mandatory | Description | Example |
| ----------- | ----------- |----------- | ----------- | 
| ``name`` | Yes | Must match the exact name as provided for parameter `storage` in the [SAP Content Server Connection](#sap-http-content-server-connection) (`sapHttp.yaml`).       |  `ms-sharepoint-archive` |
| ``type`` | Yes | The type of the repository that should be connected. As the connection routes to a SharePoint Online site, the value must be `sharepoint`. |  `sharepoint` |
| ``adminPath`` | Yes | The path in SharePoint where the SAP Content Repository files and SAP certificates are stored. Use a separate Document Library (recommended is even a separate SharePoint Site) for all SAP Content Repositories. |  `SAPRepositories/Repositories` |
| ``documentPath`` | Yes | The document root folder for all documents from the current SAP Content Repository. |  `SAPDocuments/ArchiveLink` |
| ``sharepointUrl`` | Yes | The Url of the SharePoint tenant.      | `https://mycompany.sharepoint.com` |
| ``tenantName`` | Yes | The tenant name of SharePoint.       | `mycompany` |
| ``siteName`` | Yes | The site name in the tenant to store documents from SAP.       | `sapdemo` |
| ``tenantId`` | Yes| The tenant ID of the Azure AD application. Refer to chapter [Create Microsoft Azure Application](/configuration/storages/sharepoint/azure) | `<TenantId>` |
| ``clientId`` | Yes |  The client Id of the Azure AD application. Refer to chapter [Create Microsoft Azure Application](/configuration/storages/sharepoint/azure) | `<ClientId>` |
| ``thumbprint`` | Yes | The thumbprint of the certificate for the registered Azure AD application. Refer to chapter [Create Microsoft Azure Application](/configuration/storages/sharepoint/azure) | `<ThumbprintId>` |
| ``privateKey`` | Yes | The private key of the certificate used for the registered Azure AD application. Refer to chapter [Create Microsoft Azure Application](/configuration/storages/sharepoint/azure) | `<PrivateKey>`  |