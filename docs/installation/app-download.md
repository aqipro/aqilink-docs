# Download
This section describes how to download the latest **`aqilink`** Docker image as well as related software.

!> To download the image or any related software (as listed below) from our delivery portal, a valid license key for **`aqilink`** is required. 

## Download Docker Image
Use the following URL to download the latest stable **`aqilink`** release:

```yaml
https://delivery.aqipro.com/api/download/aqilink/<YOUR-LICENSE-KEY>
```

### Other related Software

While the most important download is undoubtedly the  **`aqilink`** Docker image, you can download the following additional software from our delivery portal using the URLs provided. Note: Some downloads may only be required if you run the associated storage system.


!> You must provide a valid license key in the URL to download the software!

| Software Package | Link | Description | 
| ----------- | ----------- |----------- |
| `aqilink-encryptor` | `https://delivery.aqipro.com/api/download/aqilink-encryptor/<YOUR-LICENSE-KEY>`| The encryptor tool to store passwords securely. Refer to [Password Encryption](/reference/password-encryption.md). | 
| `aqilink-nuxeo` | `https://delivery.aqipro.com/api/download/aqilink-nuxeo/<YOUR-LICENSE-KEY>` | This package contains the required **`aqilink`** data model and also UI addons to be deployed on the Nuxeo server. | 
| `aqilink-templates` | `https://delivery.aqipro.com/api/download/aqilink-templates/<YOUR-LICENSE-KEY>` | Download a package with predefined templates used for [Tasks](/configuration/aqishare/tasks.md) to enable data exchange. |


## SAP NetWeaver RFC SDK
The [SAP NetWeaver Remote Function Call (RFC) Software Development Kit (SDK)](https://support.sap.com/en/product/connectors/nwrfcsdk.html) is required to utilize the  [Data Exchange](/configuration/aqishare) capabilities of **`aqilink`** through SAP Remote Function Calls (RFC) to invoke function modules. The SDK cannot be included with the **`aqilink`** Docker image or offered as a separate download on this page, as doing so would infringe on SAP's terms and conditions.

Since the **`aqilink`** Docker image is based on a Linux system, the *SAP NetWeaver RFC SDK* for **Linux (linux_64)** needs to be downloaded from the [SAP support portal](https://support.sap.com/en/product/connectors/nwrfcsdk.html). 

!> You'll need a valid SAP S-User ID to log in and download the SDK.
