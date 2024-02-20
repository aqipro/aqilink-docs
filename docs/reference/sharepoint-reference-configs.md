# Reference for Configuration Files

The below reference contains examples of **`aqilink`** configuration files related to Microsoft SharePoint Online (SPO).

## Example Configuration for ```sapHttp.yaml```
Example how the content of `sapHttp.yaml` can look like to connect to an Amazon S3 bucket.

```yaml
- name: sharepoint-archive
  storage: spo-archivelink
  signatureEnabled: true
```

## Example Configuration for ```storage.yaml```
Example how the content of `storage.yaml` can look like to connect to a Microsoft SharePoint Online site.
```yaml
- name: spo-archivelink
  type: sharepoint
  adminPath: SAPRepositories/
  documentPath: SAPDocuments/ArchiveLink
  sharepointUrl: https://mycompany.sharepoint.com
  siteName: sap-demo
  tenantName: mycompany
  tenantId: 12345678-abcd-effe-dcba-98765432100
  clientId: 98765432100-abcd-effe-dcba-12345678
  thumbprint: 1234567890ABCDEFFECDBA09876543210ABCCBA1
  privateKey: |
    -----BEGIN RSA PRIVATE KEY-----
    <paste the private key of the App certificate in Azure AD >
    -----END RSA PRIVATE KEY-----
```
