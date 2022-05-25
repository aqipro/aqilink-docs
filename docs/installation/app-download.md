# Download

Once you've requested a trial version, we will provide you with an email which includes a license key and the download link of the **`aqilink`** Docker image.

## Docker Load
To load the image from the downloaded *tar.gz* file use the Docker [load command](https://docs.docker.com/engine/reference/commandline/load/).

```
docker load -i aqilink_<VERSION>.tar.gz
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