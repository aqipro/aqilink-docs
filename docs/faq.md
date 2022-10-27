## Frequently Asked Questions

## Troubleshooting
If you have trouble installing or running **`aqilink`**, the problem may be related to one of the following.

### aqilink: Cannot decrypt properties with public key
Right after starting the **`aqilink`** application the console output contains the error message shown in the image below.
In this case, double-check whether the passwords (used in configuration files `storage.yaml` and/or `sapConnection.yaml`) was encrypted by the private key which was generated along with the public key used for decryption. Most likely it's an issue with the key pair that does not match. Make sure to copy the correct public key into the image (Production Mode) or map the correct public key to the container (Development Mode).

Refer to [Password Encryption](/reference/password-encryption) and [Basic App Configuration](/installation/app-configuration.md#password-encryption) and [App start](/installation/app-start#create-dockerfile-for-custom-image).

![Wrong keys](/_media/faq/0004_aqilink_mixedKeypairKeys.png)

### aqilink: Could not open the ICU common library / LD_LIBRARY_PATH
This error happens if a [Task](/configuration/aqishare/tasks) is executed which tries to connect to the related SAP system via RFC using the native libraries of the SAP NetWeaver SDK. The result of the error is a crash of the container and the cause is related due to the missing assignment of the SAP native RFC libraries during application start.

> This error should only occur if starting the container through `docker-compose.yaml` (refer to [Development Mode](/installation/app-start.md#development-mode)).

Check in your `docker-compose.yaml` whether for service *aqilink* the following command has been provided.
The imporant assignment is the `/sbin/ldconfig` part before starting the app: 
 ```
     command: sh -c "/sbin/ldconfig && node dist/main"
 ```
![Mapping to native SAP libs missing](/_media/faq/0005_aqilink_ldconfig_missin_dev-mode.png)
 
### SAP: Client connection broken
If you receive a similar message below while pinging the connected repository from the SAP Content Repository administration (t-code: `OAC0`), you may check the `app.yaml` in the `/configs` folder. Make sure to enter the license key.

![Client connection broken](/_media/faq/0001_noLicenseKeyEntered_butPropertyInAppYaml.png)

Refer also to the log files in the `aqilink` container. There must be something like the below message that also indicates an issue with the missing license key.

![Client connection broken](/_media/faq/0001_error_noLicenseKeyEntered_butPropertyInAppYaml.png)

### SAP: 402 Payment required
While testing the connection to the repository through **`aqilink`** the message returned is `402 Payment Required`. Make sure to have entered the valid license key without typos in the `app.yaml` (Refer to [Basic App Configuration](/installation/app-configuration#basic-aqilink-app-configuration)). If the message still appears, the license may be expired or blocked. In this case reach out to the support team. 

![Payment required](/_media/faq/0002_sap_payment_required_wrong_licensekey.png)

### SAP: 400 Bad Request
Returning the message `400 Bad Request` while testing the basic connection via **`aqilink`** to the repository. This indicates that there is an issue in the configuration. Make sure the last parameter of the *HTTP Script* matches the correct `name` value in the `sapHttp.yaml` configuration file of `aqilink` (refer to [SAP HTTP-Content-Server Connection](/configuration/aqilink/#sap-http-content-server-connection)).

![Payment required](/_media/faq/0003_sap_bad_request.png)
