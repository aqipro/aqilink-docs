# Configure **`aqilink`**

This chapter explain the configuration options for **`aqilink`** to connect SAP with a content repository. The main configuration is split into three different [YAML files](https://yaml.org) to offer maximum flexibility.  

| File      | Description | 
| ----------- | ----------- |
| ``sapHttp.yaml`` | Defines all HTTP inbound connections from the SAP system and maps it to the related section in the `storage.yaml`.  |
| ``storage.yaml`` | Defines the connection parameters to connect to the reqired repository. |

The following image shows the relation of the values within the configuration files.

![aqilink Value Mapping](../../_media/aqilink_value_mapping_files.png)

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

## Step-by-Step: Connect SAP with Nuxeo
Read this chapter to learn how to connect Nuxeo  via **`aqilink`** as SAP Content Repository. We'll also show the necessary customizing to store a document through the SAP HTTP Content Server using an Invoice (transaction `FB03`) as simple example. 
Make sure you're familiar with the configuration for [SAP Content Repository Connection](#sap-http-content-server-connection) and [Storage Connection](#storage-connections) before, as this is required for this section.

### Create SAP Content Repository
Follow these steps to create a new SAP Content Repository connected to **`aqilink`**.

> Use a SAP dialog user with Administration permission.

1) Login to the SAP system and execute the following transaction: `OAC0`. It will display a list of all available SAP Content Repositories in the SAP system.
2) To create a new SAP Content Repository switch to *Edit* mode (`CTRL`+`F4` ), then press `F5`.  
3) Now create a new entry using the following values:

   | Property      | Description | Example |
   | ----------- | ----------- | ----------- | 
   | **Content Rep.** | Unique name for the repository in SAP.  |  `ZN`, `T1` |
   | **Description** | A description to identify the repository easily. Visible only to SAP admin in the SAP Content Repository overview.  |  `Nuxeo ECM Content Repository` |
   | **Document Area** | Select `ArchiveLink` from the dropdown list to use ArchiveLink and Attachment functionality. Select `Document Management System` to use SAP DMS (Document Info Records).  |  `ArchiveLink`, `DMS` |
   | **Storage Type** | Always select `HTTP content server` from the list. |  `HTTP content server` |
   | **Version no.** | Specify the SAP Content Server version. Supported are `0045`, `0046` and `0047` (recommended for all SAP S/4HANA systems) . |  `0047` |
   | **HTTP server** | Specify the server, IP address or hostname, where `aqilink` is running. |  `11.2.0.112` |
   | **Port Number** | Specify the port on the `aqilink` server above where the app is running. Default: `3000`. If necessary change the port mapping based on the used orchestration tool. |  `3000` |
   | **HTTP Script** | The main entry point of the `aqilink` app. It always starts with the prefix **`sapHttp`** followed by an existing `name` property in the `sapHttp.yaml`. Refer to [SAP Content Repository Connection](#sap-http-content-server-connection) above. |  `sapHttp/archivelink-nuxeo` |
   | **Transfer drctry** | Some ArchiveLink scenarios requires files to be created in a transfer directory (on SAP side) before sending it to the content server. Edit this, if the default value does not match your corporate standard. | `/tmp/`|

   This is how it looks like in the SAP GUI:
   ![SAP Customizing: OAC0](../../_media/sap_customizing/0001_oac0_create.png)

4) Save the newly created repository (`CTRL`+`S`).
5) Test the connection using either the *Test Connection* or *Status information* icon (refer to the `Orange` highlighted buttons in the image above). Now, you should receive a message, that the content repository does not exists.

   ![SAP Customizing: OAC0 Test Connection](../../_media/sap_customizing/0002_oac0_create_test_connection.png)

   > If you receive any other message than the above one, make sure the connection to the `aqilink` server is working. In case of a message similar like *`Payment required`* make sure to have entered the (valid) [license key](/installation/app-configuration?id=enter-license-key). 

6) The basic HTTP connection is working now.

### Create Content Repository in Nuxeo
The repository must be created on the content server as well. 

1) Click on icon *--> CS Admin* (*C*ontent *S*erver *Admin*istration) in the middle of the screen, besides the *Test connection* icon. Refer to image in step 3) of the previous section.
2) Create the repository in Nuxeo using the *Create repository* icon in the *Create* section (highlighted in `Orange` below). All settings in this screen can be left with their defaults.

   ![SAP Customizing: OAC0 CS Admin Create Repo](../../_media/sap_customizing/0003_oac0_cs_admin_repo.png)

3) You should be redirected to the *Details* section where some basic information from the `aqilink` server shows up.
   
   ![SAP Customizing: OAC0 CS Admin Repo created](../../_media/sap_customizing/0004_oac0_cs_admin_repo_created.png)

4) Stay in this screen and continue with the next section.

### Secure Connection using Certificate
Secure the connection between SAP and Nuxeo using a certificate that will be stored in Nuxeo to allow only authorized requests from the SAP Content Repository.

> We strongly recommend securing the connection between SAP and Nuxeo at all times.

1) Click on section *Certificates*.
2) Send the certificate by clicking on the envelope icon.
   
   ![SAP Customizing: OAC0 CS Admin Certificate](../../_media/sap_customizing/0005_oac0_cs_admin_certificate.png)

3) Once the certificate was sent, it appears in the *Certificate Properties* table. 
   **Note the checkbox!** It is not checked, which means the certificate is not yet active.

   ![SAP Customizing: OAC0 CS Admin Certificate Sent](../../_media/sap_customizing/0005_oac0_cs_admin_certificate_available.png)

4) Enable security by activating the certificate in Nuxeo.

   > From the SAP HTTP-Content Server protocol specification, the certificate can only be activated from the content repository side. This is an additional security step. Therefore, no additional action is required in SAP. To proceed with the activation login to Nuxeo.

   4.1 Login to Nuxeo as with administrator privileges.

   4.2 Navigate to the customized `aqilink` administration folder which holds all the repository files and certificates from the SAP system. Refer to value of setting `adminPath` in the related [Storage Connections](#hyland-nuxeo-repository) chapter.

   > Underneath this `adminPath` you'll find a folder named with the name you entered as [SAP HTTP-Content Server Connection](#sap-http-content-server-connection) in value for `HTTP Script` during the repository creation above. The folder is also concatenated by the name of the content repository itself.
   
   ![Nuxeo Administration: Activate Certificate](../../_media/sap_customizing/0007_nuxeo_adminpath.png)

   4.3 The certificate has the extension **`.pem`**. To activate it, edit the properties and tick the checkbox for *Activated* and save the document. 
   
   ![Nuxeo Administration: Certificate activated](../../_media/sap_customizing/0008_nuxeo_certificate_activated.png)

   4.4 Crosscheck whether the connection is now secure. Do it in SAP. 

5) Go back to SAP and navigate to the recently created SAP Content Repository again. Open the *--> CS Admin* again and click on the *Certificates* section.
   If the SAP GUI with the screen is still open in the background, just click the *Refresh* icon. 

6) Now, the checkbox in front of the certificate in the *Certificate Properties* table is enabled.

   ![SAP Customizing: Certificate active](../../_media/sap_customizing/0009_oac0_certificate_active.png)

7) With that, the SAP Content Repository is successfully connected with Nuxeo. You can proceed with a functional test.

### Functional Test
This section describes how `aqilink`, and therefore the ArchiveLink interface, can be tested. The testing requires some additional SAP fundamentals. Also, it's based on the same procedure used by SAP to certify the `aqilink` ArchiveLink interface.

> In order to successfully complete these tests, all the steps above should have been completed.

1) Login to SAP and open the *ABAP Editor* using transaction `SE38`.
2) Enter `RSCMST` in the Program field and execute it by either click *Execute* in the toolbar or press `F8` to execute it.
   
   ![SAP Functional Test: RSCMST](../../_media/sap_customizing/0010_se38_rscmst.png)

3) In this screen enter the recently created SAP Content Repository, connected to Nuxeo, in the Repository field. Again, click *Execute* in the toolbar or press `F8`.
   
   ![SAP Functional Test: RSCMST for Repo](../../_media/sap_customizing/0011_se38_execute_repository.png)

4) Now, all related sub-reports are listed that could be executed against the repository.
   
5) The most important report is `RSCMSTH0`, which will test the basic communication like `create`, `info`, `search`, `update` or even `delete` commands via HTTP against the repository. Use the *Execute* icon for the report.

   ![SAP Functional Test: RSCMST Sub-reports](../../_media/sap_customizing/0012_se38_execute_rscmsth0.png)

6) **Optional:** To test additional functionality of the HTTP interface use the available test reports `RSCMSTH1`, `RSCMSTH2` and `RSCMSTH3`.

   ![SAP Functional Test: RSCMST Sub-reports 123](../../_media/sap_customizing/0013_se38_execute_rscmsth123.png)

   > There is a known issue in the `RSCMSTH2` report in SAP BASIS components 740 to 752. If this report returns with a lot of errors regarding document protection like `DOC_P[rc]`, refer to the following SAP OSS notes: 2371386, 2198970. Skip this report unless the notes are implemented.