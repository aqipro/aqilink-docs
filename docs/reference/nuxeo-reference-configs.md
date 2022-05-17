# Reference for Configuration Files

The below reference contains examples of ```aqilink``` configuration files related to Nuxeo.

## Example Configuration for ```sapHttp.yaml```
Example how the content of `sapHttp.yaml` can look like to connect to a Nuxeo repository.

```
- name: nuxeo-attachments
  storage: nuxeo-att
  signatureEnabled: true

```

## Example Configuration for ```storage.yaml```
Example how the content of `storage.yaml` can look like to connect to a Nuxeo repository.
```
- name: nuxeo-att
  type: nuxeo
  domain: default
  baseUrl: http://localhost:8080/nuxeo
  user: Administrator
  password: ENC(lax/tcmsuMMOsQccajvjJdT9pdb4NyluqW+42SC7jiDKUmTLGmHtl+hyL27Z/bainuUKbxfsHggXjLEFWQaoANt/UwkIJT3k0BJZd5yS+i+RcBSPuLNJ52RHxRx65UrAFViV1qLT6gOyT5OnsXRZL1xke8j3HhD2JlRmxOLc/AjiSEeVe4nC190h9d2D6TXeAdq6QoD6TbtvUrI17P4cyqPS3fetgirq7hm6EDjh6eWKHU9i9p9FEi3B8QjZCasLf+5bO/qN7MNcgLXD2gQAHRZYdVoAX+p7oCQ8DT3WemmzLCL1W0VjEul3aE0Yo5mNFOaD+oK89eQi4Q2qNrCqmQ==)
  adminPath: /default-domain/workspaces/admin
  documentPath: /default-domain/workspaces/sap/sap_documents/SAP-Attachments
```

> The example use an encrypted password instead of plain text. Refer to [Password Encryption](../reference/password-encryption.md).