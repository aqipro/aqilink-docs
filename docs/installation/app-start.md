# *NOTE: This page is still under construction !!*

# Start `aqilink` application
Having the basic app configuration files from the previous section available, there are multiple ways to bring the files into the image, so it can be used. 

Here are some common ways to start the `aqilink` image with the configuration files:

<!-- https://dantehranian.wordpress.com/2015/03/25/how-should-i-get-application-configuration-into-my-docker-containers/-->

## Docker Load
To load the image from the downloaded *tar.gz* file use the [docker load](https://docs.docker.com/engine/reference/commandline/load/) command.

```
docker load -i aqilink_<VERSION>.tar.gz
   05cb9c26edf8: Loading layer [==================================================>]  19.69MB/19.69MB
   fb429fdee34e: Loading layer [==================================================>]  3.072kB/3.072kB
   314992d6fc2a: Loading layer [==================================================>]  902.1kB/902.1kB
   af98d279c7f6: Loading layer [==================================================>]    191MB/191MB
   418e39625432: Loading layer [==================================================>]  314.9kB/314.9kB
   Loaded image: aqilink:<VERSION>
```

Make sure the image is available by checking with the [docker image ls](https://docs.docker.com/engine/reference/commandline/image_ls/) command:
```
docker image ls
   REPOSITORY      TAG       IMAGE ID       CREATED        SIZE
   ...             ...       ...            ...            ...
   aqilink         <VERSION> ac10911ff112   42 hours ago   425MB
```

## Docker Start
Before starting the container from the image the `/configs/` folder (refer to [Basic App Configuration](../installation/app-configuration.md)) with the related configuration files must be present in the running container. There are a couple of options to bring (copy) the folder either into the image or map it to the container. Choose the option that fits best for you. 
To run the image use the [docker run](https://docs.docker.com/engine/reference/commandline/run/) command and always specify the Tag which is the **`aqilink`** version number.



### Use volume mapping

Start the container based on the Tag above:
```
docker run aqilink:<VERSION>


docker run -v /configs:/usr/src/app/configs aqilink:22.0.0
```

### Copy configuration files into container
To have the configuration files available inside the container, follwow these steps:
1) Start the app
1) Identify the `aqilink` container and remember the &lt;CONTAINER_ID&gt;
2) Copy files into container using `docker cp`
3) Start the container again

```
docker container ls
docker cp docker cp ./some_file CONTAINER:/work
docker run aqilink:<VERSION>
```

## Custom Docker image
Suggested for any production environment. Create a custom Docker image using a `Dockerfile` based on the latest **`aqilink`** image and copy the `/configs/` folder as well as the [SAP NetWeaver RFC SDK](../installation/app-download?id=sap-netweaver-rfc-sdk) into it. Run the new image.

> Make sure to have all settings in the `/configs/` folder correct and working before.

```
FROM aqilink:<VERSION>
COPY /configs ./usr/src/app/configs
COPY /sap/linux_x64/nwrfcsdk ./usr/local/sap/nwrfcsdk
COPY /sap/linux_x64/nwrfcsdk.conf ./etc/ld.so.conf.d/nwrfcsdk.conf
```

Use [Docker build](https://docs.docker.com/engine/reference/commandline/build/) command to create the new image with a customer specific tag:
```
docker build . -t "aqilink_customer"
```

## Use docker-compose 
Using [Docker compose](https://docs.docker.com/compose/) is suggested for DEV environments only. Ramp up **`aqilink`** quickly to test the settings easily.

> [docker-compose](https://docs.docker.com/compose/) must be installed.

Use the following `docker-compose.yml` as template to start the container.
```
version: '3.7'

services:
  main:
    platform: linux/amd64
    volumes:
      - ./sap/linux_x64/nwrfcsdk:/usr/local/sap/nwrfcsdk
      - ./sap/linux_x64/nwrfcsdk.conf:/etc/ld.so.conf.d/nwrfcsdk.conf
    environment:
      SAPNWRFC_HOME: '/usr/local/sap/nwrfcsdk'
    ports:
      - 3000:3000
    depends_on:
      - redis
    networks:
      - sapnet
  redis:
    image: redis
    networks:
      - sapnet
networks:
  sapnet:

```




Congratulations. You have successfully downloaded and installed **`aqilink`**, it's time to learn all about the configuration options it offers to make it fit to your company. Please read the next chapter.