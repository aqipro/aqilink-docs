# Download
This section describes how to download the latest **`aqilink`** Docker image and how to load the image.

> To download the image or any other related software from our delivery portal, you need to have your valid license key for `aqilink` available. 

## Download Docker Image
Use the following URL to download the latest stable **`aqilink`** release:

```
https://delivery.aqipro.com/api/download/aqilink/<YOUR-LICENSE-KEY>
```

### Other related Software

While the most important download will be for sure the `aqilink` Docker image, you can download the following additional software from our delivery portal by using the URLs below. Note: Some downloads may only required if you run the related storage system.

> You must provide a valid license key in the URL to download the software!

| Software Package | Link | Description | 
| ----------- | ----------- |----------- |
| `aqilink-encryptor` | `https://delivery.aqipro.com/api/download/aqilink-encryptor/<YOUR-LICENSE-KEY>`| The encryptor tool to store passwords securly. Refer to [Password Encryption](/reference/password-encryption.md). | 
| `aqilink-nuxeo` | `https://delivery.aqipro.com/api/download/aqilink-nuxeo/<YOUR-LICENSE-KEY>` | This package contains the required `aqilink` Data Model and also UI addons to be deployed on the Nuxeo server. | 
| `aqilink-templates` | `https://delivery.aqipro.com/api/download/aqilink-templates/<YOUR-LICENSE-KEY>` | Download a package with predefined templates used for [Tasks](/configuration/aqishare/tasks.md) to replicate metadata. |

## Docker Load
To load the image from the downloaded *tar.gz* file use the Docker [load command](https://docs.docker.com/engine/reference/commandline/load/).

```
docker load -i aqilink_<VERSION>.tar.gz
```
or use as alternative:
```
docker load < aqilink_<VERSION>.tar.gz
```



## Docker Start
Start the container from the image using Docker [run command](https://docs.docker.com/engine/reference/commandline/run/).
Always specify the Tag for the `run` command which is the **`aqilink`** version number. To find the available Tag use: 

```
docker image ls
   REPOSITORY      TAG       IMAGE ID       CREATED        SIZE
   ...             ...       ...            ...            ...
   aqilink         <VERSION> ac10911ff112   42 hours ago   425MB
```
Start the container based on the Tag above:
```
docker run aqilink:<VERSION>
```

Congratulations. You have successfully downloaded and installed **`aqilink`**, it's time to learn all about the configuration options it offers to make it fit to your company. Please read the next chapter.