# Download
This section describes how to download the latest **`aqilink`** Docker image and how to load the image.

> To download the image or any other related software (see below) from our delivery portal, a valid license key for **`aqilink`** is required. 

## Download Docker Image
Use the following URL to download the latest stable **`aqilink`** release:

```
https://delivery.aqipro.com/api/download/aqilink/<YOUR-LICENSE-KEY>
```

### Other related Software

While the most important download will be for sure the **`aqilink`** Docker image, you can download the following additional software from our delivery portal by using the URLs below. Note: Some downloads may only required if you run the related storage system.

> You must provide a valid license key in the URL to download the software!

| Software Package | Link | Description | 
| ----------- | ----------- |----------- |
| `aqilink-encryptor` | `https://delivery.aqipro.com/api/download/aqilink-encryptor/<YOUR-LICENSE-KEY>`| The encryptor tool to store passwords securely. Refer to [Password Encryption](/reference/password-encryption.md). | 
| `aqilink-nuxeo` | `https://delivery.aqipro.com/api/download/aqilink-nuxeo/<YOUR-LICENSE-KEY>` | This package contains the required **`aqilink`** data model and also UI addons to be deployed on the Nuxeo server. | 
| `aqilink-templates` | `https://delivery.aqipro.com/api/download/aqilink-templates/<YOUR-LICENSE-KEY>` | Download a package with predefined templates used for [Tasks](/configuration/aqishare/tasks.md) to enable data exchange. |


## SAP NetWeaver RFC SDK
The [SAP NetWeaver Remote Function Call (RFC) Software Development Kit (SDK)](https://support.sap.com/en/product/connectors/nwrfcsdk.html) is required to use the [Data Exchange](/configuration/aqishare) capabilities of **`aqilink`** through SAP Remote Function Calls (RFC) to invoke function modules. The SDK can neither be shipped with the **`aqilink`** Docker image nor offered as a separate download on this page, as this is infringes the SAP terms and conditions.

Because the **`aqilink`** Docker image is based on a Linux system, the *SAP NetWeaver RFC SDK* for **Linux (linux_64)** must be downloaded from the [SAP support portal](https://support.sap.com/en/product/connectors/nwrfcsdk.html). 

> You'll need to have a valid SAP S-User Id available to login and therefore to download the SDK.
