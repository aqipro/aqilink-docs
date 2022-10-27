# Download
This section describes how to download the latest **`aqilink`** Docker image and how to load the image.

> To download the image or any other related software from our delivery portal, you need to have your valid license key for `aqilink` available. 

## Download Docker Image
Use the following URL to download the latest stable **`aqilink`** release:

```
https://delivery.aqipro.com/api/download/aqilink/<YOUR-LICENSE-KEY>
```

### Other related Software

While the most important download will be for sure the `aqilink` Docker image, you can download the following additional software from our delivery portal by using the URLs below. Note: Some downloads may only required if you run the related storage system.

> You must provide a valid license key in the URL to download the software!

| Software Package | Link | Description | 
| ----------- | ----------- |----------- |
| `aqilink-encryptor` | `https://delivery.aqipro.com/api/download/aqilink-encryptor/<YOUR-LICENSE-KEY>`| The encryptor tool to store passwords securly. Refer to [Password Encryption](/reference/password-encryption.md). | 
| `aqilink-nuxeo` | `https://delivery.aqipro.com/api/download/aqilink-nuxeo/<YOUR-LICENSE-KEY>` | This package contains the required `aqilink` Data Model and also UI addons to be deployed on the Nuxeo server. | 
| `aqilink-templates` | `https://delivery.aqipro.com/api/download/aqilink-templates/<YOUR-LICENSE-KEY>` | Download a package with predefined templates used for [Tasks](/configuration/aqishare/tasks.md) to replicate metadata. |


## SAP NetWeaver RFC SDK
The [SAP NetWeaver Remote Function Call (RFC) Software Development Kit (SDK)](https://support.sap.com/en/product/connectors/nwrfcsdk.html) is required to use the [Data Exchange](/configuration/aqishare) capabilities of **`aqilink`**. 
Because the **`aqilink`** Docker image is based on a Linux system, you have to download the *SAP NetWeaver RFC SDK* for **Linux (*linux_64*)**. The *SAP NetWeaver RFC SDK* is only available from within the SAP support portal. It cannot be shipped with **`aqilink`** or as a separate download, as this is not allowed by the SAP terms.

> You'll need to have a valid SAP S-User Id available to login and therefore download the SDK.
