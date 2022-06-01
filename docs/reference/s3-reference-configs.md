# Reference for Configuration Files

The below reference contains examples of ```aqilink``` configuration files related to Amazon S3.

## Example Configuration for ```sapHttp.yaml```
Example how the content of `sapHttp.yaml` can look like to connect to an Amazon S3 bucket.

```
- name: s3-archive
  storage: s3-archivelink
  signatureEnabled: true
```

## Example Configuration for ```storage.yaml```
Example how the content of `storage.yaml` can look like to connect to an Amazon S3 bucket.
```
- name: s3-archivelink
  type: s3
  endPoint: http://your-amazon-s3-storage.aws.com
  port: 9000
  accessKey: accessKey
  secretKey: secretKey
  bucket: sap-archive
  prefix: AL
```

> The example use an encrypted password instead of plain text. Refer to [Password Encryption](/reference/password-encryption.md).