# Reference

Reference section. 

## Password encryption

Password encryption is essential to store user credentials securely. Without password encryption, anyone accessing the 
configuration files for ```aqilink``` could easily view any stored passwords by default, because it is stored as plain text. To avoid this potential security risk, it is possible to encrypt all passwords in the configuration files with an encryptor tool. The tool uses the asymmetric RSA algorithm to encrypt the passwords. 


### Download the encryptor tool
We offer the password encrypton tool for various platforms. Download the tool based on your system architecture below:

| Platform | File name | Download  |
|:---:|:---:|:---:|
| Linux | aqilink-encryptor.sh | [https://aqilink-encryptor.sh](aqilink-encryptor.sh) |
| Windows | aqilink-encryptor.exe | [https://aqilink-encryptor.exe](aqilink-encryptor.exe) |


### Using the encryptor tool
To encrypt passwords in any of the ```aqilink``` configuration files follow these steps:

#### Generate key pair 
Use the following command to create a new private key and public key pair. Both keys are stored in separate files within the given directory. Make sure the given directory exists. 

```
.\aqilink-encryptor.exe generate c:\temp\aqilink\keypair
``` 

#### Map public key into container
   
To allow the usage of the encrypted passwords based on the the newly created public key, the file be available inside the container of ```aqilink```. Depending on the used containerization tool, the method to make files available depends (either map it or copy it).  

#### Enable usage
Once the public key file is available inside the container, ```aqilink``` must know about it to use it. In order to make  ```aqilink``` aware of the file, the path of the public key file in the container must be added to the ```app.yaml``` in the ```./configs``` directory by specifying the value for property ```publicKeyPath```.

```
publicKeyPath: /usr/src/app/configs/publicKey.pem
```

#### Create encrypted passwords 
To create encrypted passwords the following command of the encryptor tool has to be used:
```
.\aqilink-encryptor.exe encrypt MY_PASSWORD_TO_ENCRYPT --privateKeyPath c:\temp\aqilink\keypair\privateKey.pem
```

The output will be like:
```
Encrypted Password:
ENC(lax/tcmsuMMOsQccajvjJdT9pdb4NyluqW+42SC7jiDKUmTLGmHtl+hyL27Z/bainuUKbxfsHggXjLEFWQaoANt/UwkIJT3k0BJZd5yS+i+RcBSPuLNJ52RHxRx65UrAFViV1qLT6gOyT5OnsXRZL1xke8j3HhD2JlRmxOLc/AjiSEeVe4nC190h9d2D6TXeAdq6QoD6TbtvUrI17P4cyqPS3fetgirq7hm6EDjh6eWKHU9i9p9FEi3B8QjZCasLf+5bO/qN7MNcgLXD2gQAHRZYdVoAX+p7oCQ8DT3WemmzLCL1W0VjEul3aE0Yo5mNFOaD+oK89eQi4Q2qNrCqmQ==)
```
Copy the line starting with the **ENC()** function and paste it as value for the desired password value in one of the ```aqilink``` configuration files (such as ```storage.yaml``` or  ```sapConnection.yaml```).

This is an example of how the encrypted password is used in the ```storage.yaml``` as password for the Nuxeo user:
```
- name: nuxeo-al
  type: nuxeo
  domain: default
  baseUrl: http://nuxeo:8080/nuxeo
  user: Administrator
  password: ENC(jU3z3GkFw+KQiNSiN4yFBeV8cPRv79uOfO90ovo/Q3+dJR72JfFCrSaZBs5R51+cNfxUFDwaAGwhcVgx78WRVgfc7B40R3s13/gOqeeCehrNKCM6Sy/uGqGCJZrUNwOkZi3B+i7bRAxX7EJfY2rYiCIuEMXh6utSZaAbzc0FvEB19ACKZuRIebRQRRPRjGKS+++hoea5E/YjirocM8kJUsEMaDtTY0SGeK6CSE6XSMdT7cjtSkOIZYRzZFvnNEijymxiMxmnTArCRvcbiH4L/kERWet6CzhqV08yH5y4wNnKIQQ2Xf7lAegv6E+34u4ssarozoARmG2M3gIJA2RvBw==)
  adminPath: /default-domain/workspaces/admin
  documentPath: /default-domain/workspaces/documents
```