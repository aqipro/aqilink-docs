# Communication via HTTPS

Set up secure communication between SAP and **`aqilink`**. This chapter outlines the required steps to import the certificate from the **`aqilink`** web server into SAP and configure SAP Content Repositories to use HTTPS instead of HTTP for communication.

### What this chapter does not cover

* The configuration of the SSL connection on the webserver where **`aqilink`**  is installed.
* The installation and configuration of the SSL connection on the connected repository.
* The creation of the certificate itself.


## Get current Certificate

The certificate utilized by the **`aqilink`** web server must be recognized and imported into SAP. To retrieve and export the certificate from the web server, follow these steps:

1) Open a browser and navigate to the web server where **`aqilink`** is running on port `3000`. If you have changed this default port in your `Dockerfile` or `docker-compose.yaml`, use the port number specified in your setup. Next, click on the lock icon in front of the URL to view the site information.
   
   Example:
   ```
   https://localhost:3000
   ```

![Access aqilink](../_media/reference/https/browser_aqilink_port.png)

2) View the details of the currently installed certificate and export it. In Google Chrome, click on *Connection is secure*, then on *Certificate is valid*, and switch to the *Details* tab to find the export button. Save the certificate to the local machine using the file extension  `.pem`.

![Export certificate](../_media/reference/https/browser_export_certificate.png)

## Prepare SAP Content Repository for HTTPS

To prepare the SAP Content Repository for a secure connection, follow these steps:

1) Open transaction `OAC0`.
2) Choose the desired SAP Content Repository.
3) Enter `%https` (including the percent sign) in the transaction code field to display the necessary HTTPS related settings. Once the fields appear: 

    1) Clear the value for `Port Number`.
    2) Enter the `SSL Port Number`.
    3) Choose `HTTPS required` as value for `HTTPS on frontend`.
    4) Choose `HTTPS required` as value for `HTTPs on backend`.

![SAP Repo Setup](../_media/reference/https/sap_oac0_https.png)

!> Ensure that you remove the non-SSL Port Number; otherwise, the connection will fail.

4) Save the settings for the SAP Content Repository.
5) Attempt to test the connection to the Content Repository or retrieve information from it by clicking one of the related buttons. Any connection test will now result in a failure with the following error:

![SAP Repo Setup Ping](../_media/reference/https/sap_oac0_https_ping_fails.png)

To enable the SSL connection, the certificate exported in the previous step must be recognized by SAP. Therefore, it needs to be imported into SAP.

## Import Certificate in SAP Personal Security Environment (PSE)

To import the certificate, as described in the [section above](#get-current-certificate-from-aqilink) into the SAP Personal Security Environment (PSE), follow these steps:

1) Open transaction `STRUST`.
2) Verify if an `SSL Client (Standard)` PSE exists and access it by double-clicking.

>If an `SSL Client (Standard)` PSE is not available, select the `SSL Client (Standard)` entry and use the context menu to create a new Personal Security Environment. Apply the default settings where applicable.

![SAP STRUST 1](../_media/reference/https/sap_strust_1.png)

3) Switch to *Edit* mode, then navigate to the `Certificate` section.
4) Click on the `Import certificate` button.

![SAP STRUST 2](../_media/reference/https/sap_strust_2.png)

5) Use the file chooser to select the previously saved `.pem` file from the [certificate export section](#get-current-certificate-from-aqilink) using the File chooser. The certificate now appears with all its details.
6) Click on `Add to Certificate List` to addd the certificate to the Certificate list.

![SAP STRUST 3](../_media/reference/https/sap_strust_3.png)

7) The certificate from the  **`aqilink`** web server is now listed among the trusted certificates. Confirm its presence by looking through the list of certificates. Remember, the list has not been saved yet!

![SAP STRUST 4](../_media/reference/https/sap_strust_4.png)

8) Finally click on *Save* to persist the new certificate in SAP. The message in the bottom line of the SAP GUI should read as follows:

![SAP STRUST 5](../_media/reference/https/sap_strust_5.png)


9) To verify the SSL connection, go back to the SAP Content Repository Administration of the related Content Repository (t-code `OAC0`) and test the connection again. The SSL handshake error regarding untrusted certificate is now gone and the connection through HTTPS works fine:

![SAP Repo Setup Ping Success](../_media/reference/https/sap_oac0_https_ping_fine.png)

The communication via HTTPS between SAP and the **`aqilink`** web server is now established.