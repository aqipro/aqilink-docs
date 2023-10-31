## Frequently Asked Questions

## Troubleshooting
If you have trouble installing or running **`aqilink`**, the problem may be related to one of the following.

### aqilink: Cannot decrypt properties with public key
Right after starting the **`aqilink`** application, if the console output displays the error message as shown in the image below, verify that the passwords (used in the configuration files `storage.yaml` and/or `sapConnection.yaml`) were encrypted with the private key corresponding to the public key used for decryption. It is likely an issue with a mismatched key pair. Ensure that the correct public key is copied into the Docker image (Production Mode) or that the correct public key is mapped to the Docker container (Development Mode).


Refer to [Password Encryption](/reference/password-encryption) and [Basic App Configuration](/installation/app-configuration.md#password-encryption) and [App start](/installation/app-start#create-dockerfile-for-custom-image).

![Wrong keys](/_media/faq/0004_aqilink_mixedKeypairKeys.png)

### aqilink: Could not open the ICU common library / LD_LIBRARY_PATH
This error occurs if a [Task](/configuration/aqishare/tasks) is executed that attempts to connect to the corresponding SAP system via RFC using the native libraries from the SAP NetWeaver SDK. The error results in the container crashing and is caused by the SAP native RFC libraries not being assigned during the application's startup.

> This error should only occur when starting the container through `docker-compose.yaml` (refer to [Development Mode](/installation/app-start.md#development-mode)).

In your `docker-compose.yaml`, verify that the following command has been specified for the **`aqilink`** service. The crucial part is the execution of  `/sbin/ldconfig` before starting the app:

 ```
     command: sh -c "/sbin/ldconfig && node dist/main"
 ```
![Mapping to native SAP libs missing](/_media/faq/0005_aqilink_ldconfig_missin_dev-mode.png)
 
### SAP: Client connection broken
If you encounter a message similar to the one below while pinging the connected repository from the SAP Content Repository administration (transaction code: `OAC0`), check the `app.yaml` file in the `/configs` folder. Ensure that the license key is entered correctly.

![Client connection broken](/_media/faq/0001_noLicenseKeyEntered_butPropertyInAppYaml.png)

Also, refer to the log files in the **`aqilink`** container. There should be a message similar to the one below, which also indicates an issue with a missing license key.


![Client connection broken](/_media/faq/0001_error_noLicenseKeyEntered_butPropertyInAppYaml.png)

### SAP: 402 Payment required
While testing the connection to the repository via **`aqilink`**, if the message `402 Payment Required` is returned, ensure that the valid license key has been entered correctly without typos in the `app.yaml` file (refer to [Basic App Configuration](/installation/app-configuration#basic-aqilink-app-configuration)). If the message persists, the license may have expired or been blocked. In this case, reach out to the support team.

![Payment required](/_media/faq/0002_sap_payment_required_wrong_licensekey.png)

### SAP: 400 Bad Request
When testing the basic connection via **`aqilink`** to the repository, if the message `400 Bad Request` is returned, this indicates a configuration issue. Ensure that the last parameter of the HTTP script corresponds with the correct `name` value in the `sapHttp.yaml` configuration file of **`aqilink`** (refer to [SAP HTTP-Content-Server Connection](/configuration/aqilink/#sap-http-content-server-connection)).

![Payment required](/_media/faq/0003_sap_bad_request.png)

### SAP: 500 Internal Server Error
When testing the basic connection via **`aqilink`** to the repository, if the message `500 Internal Server Error` is returned, this could indicate several possible issues with the current configuration. Check the related log files of the **`aqilink`** and/or repository container. For instance, if Nuxeo is the repository behind **`aqilink`**, ensure that the service name in the `docker-compose.yaml` matches the name in the `baseUrl` parameter of the [`storage.yaml`](/configuration/aqilink/#hyland-nuxeo-repository) configuration file. Also, verify that all services in the `docker-compose.yaml` are using the same network.

![Internal Server Error](/_media/faq/0005_sap_internalServerError.png)
