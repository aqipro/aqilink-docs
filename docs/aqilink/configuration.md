# Configure **`aqilink`**

This chapter explains all configuration options for **`aqilink`** to connect SAP with a content repository. The main configuration has to be done in three different [YAML files](https://yaml.org) which are mapped into the Docker container.

## SAP HTTP-Content-Server Connection
The `sapHttp.yaml` defines the inbound connections from the SAP system and maps it to the related repository configuration of the [Storage Connections](#storage-connections) below. 

> Depending on the **`aqishare`** license there can be as many definitions as required in this file.

## Storage Connections
The `storage.yaml` defines the connection parameter to the related repository. 

> Depending on the **`aqishare`** license there can be as many definitions as required in this file.

| Parameter      | Mandatory | Description | Example |
| ----------- | ----------- |----------- | ----------- | 
| ``name`` | Yes | **Unique** name for each connection. Should reasonably contain the name of the repository as the same name has to be used in the URL parameter within the SAP Content Repository configuration. <br/>Other best practice: Do not use special characters or spaces. Use latin characters, hyphens and numbers only.       |  `nuxeo-attachments`, `nuxeo-dms` |
| ``storage`` | Yes | The type of the repository that should be connected. In this connection definition it should be `nuxeo`. |  `nuxeo` |
| ``signatureEnabled`` | Yes | Title       | |


### Hyland Nuxeo Repository
The below table lists all parameters available for the `storage.yaml` to define a connection to a Hyland Nuxeo repository. All parameters are mandatory for the connection.

| Parameter      | Mandatory | Description | Example |
| ----------- | ----------- |----------- | ----------- | 
| ``name`` | Yes | **Unique** name for each connection. Should reasonably contain the name of the repository as the same name has to be used in the URL parameter within the SAP Content Repository configuration. <br/>Other best practice: Do not use special characters or spaces. Use latin characters, hyphens and numbers only.       |  `nuxeo-attachments`, `nuxeo-dms` |
| ``type`` | Yes | The type of the repository that should be connected. In this connection definition it should be `nuxeo`. |  `nuxeo` |
| ``domain`` | No | Title       | |
| ``baseUrl`` | Yes | Title       | |
| ``user`` | Yes| Title       |  |
| ``password`` | Yes | Title       | |
| ``adminPath`` | Yes | Title       |  |
| ``documentPath`` | Yes | Title       | | 

#### Example Connection
Here are some examples of how the content of the `storage.yaml` can look like. 
```
- name: nuxeo-dms
  type: nuxeo
  domain: default
  baseUrl: http://localhost:8080/nuxeo
  user: Administrator
  password: Administrator
  adminPath: /default-domain/workspaces/admin
  documentPath: /default-domain/workspaces/sap/sap_documents/dms
```

### Amazon S3 Bucket

#### For an Amazon S3 Bucket
```
- name: s3-1
  type: s3
  endPoint: http://your-amazon-s3-storage.aws.com
  port: 9000
  accessKey: accessKey
  secretKey: secretKey
  bucket: testsource-hh
  prefix: AL
```





Lorem ipsum dolor sit amet, consetetur sadipscing elitr, sed diam nonumy eirmod tempor invidunt ut labore et dolore magna aliquyam erat, sed diam voluptua. At vero eos et accusam et justo duo dolores et ea rebum. Stet clita kasd gubergren, no sea takimata sanctus est Lorem ipsum dolor sit amet. Lorem ipsum dolor sit amet, consetetur sadipscing elitr, sed diam nonumy eirmod tempor invidunt ut labore et dolore magna aliquyam erat, sed diam voluptua. At vero eos et accusam et justo duo dolores et ea rebum. Stet clita kasd gubergren, no sea takimata sanctus est Lorem ipsum dolor sit amet. Lorem ipsum dolor sit amet, consetetur sadipscing elitr, sed diam nonumy eirmod

```
Lorem ipsum dolor sit amet, consetetur sadipscing elitr, sed diam nonumy eirmod tempor invidunt ut labore et dolore magna aliquyam erat, sed diam voluptua. At vero eos et accusam et justo duo dolores et ea rebum. Stet clita kasd gubergren, no sea takimata sanctus est Lorem ipsum dolor sit amet. Lorem ipsum dolor sit amet, consetetur sadipscing elitr, sed diam nonumy eirmod tempor invidunt ut labore et dolore magna aliquyam erat, sed diam voluptua. At vero eos et accusam et
```

### Level 3.1
Lorem ipsum dolor sit amet, consetetur sadipscing elitr, sed diam nonumy eirmod tempor invidunt ut labore et dolore magna aliquyam erat, sed diam voluptua. At vero eos et accusam et justo duo dolores et ea rebum. Stet clita kasd gubergren, no sea takimata sanctus est Lorem ipsum dolor sit amet. Lorem ipsum dolor sit amet, consetetur sadipscing elitr, sed diam nonumy eirmod tempor invidunt ut labore et dolore magna aliquyam erat, sed diam voluptua. At vero eos et accusam et

### Level 3.2
Lorem ipsum dolor sit amet, consetetur sadipscing elitr, sed diam nonumy eirmod tempor invidunt ut labore et dolore magna aliquyam erat, sed diam voluptua. At vero eos et accusam et justo duo dolores et ea rebum. Stet clita kasd gubergren, no sea takimata sanctus est Lorem ipsum dolor sit amet. Lorem ipsum dolor sit amet, consetetur sadipscing elitr, sed diam nonumy eirmod tempor invidunt ut labore et dolore magna aliquyam erat, sed diam voluptua. At vero eos et accusam et

## Level 2.2
Lorem ipsum dolor sit amet, consetetur sadipscing elitr, sed diam nonumy eirmod tempor invidunt ut labore et dolore magna aliquyam erat, sed diam voluptua. At vero eos et accusam et justo duo dolores et ea rebum. Stet clita kasd gubergren, no sea takimata sanctus est Lorem ipsum dolor sit amet. Lorem ipsum dolor sit amet, consetetur sadipscing elitr, sed diam nonumy eirmod tempor invidunt ut labore et dolore magna aliquyam erat, sed diam voluptua. At vero eos et accusam et justo duo dolores et ea rebum.
