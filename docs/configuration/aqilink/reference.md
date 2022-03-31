# Reference

This page provides useful references for **`aqilink`**.


## Configuration Examples
This section contains some example configurations for the required `config.yml` file to be used to connect to a desired repository.

### Example Configuration for Nuxeo
This configuration enables SAP to store documents through the HTTP-Content Server protocol in the Nuxeo repository. Make sure to change at least the Nuxeo credentials in order to work. You'll find the full reference of details amongst all possible settings and values in the [Configuration section](/aqilink/configuration.md).

```
{
    repo: nuxeo
    hallo: du,
    wer:da
}

```

### Example Configuration for S3 Storage
This configuration enables SAP to store documents through the HTTP-Content Server protocol in an Amazon S3 bucket. Make sure to change at least the storage credentials.
You'll find the full reference of details amongst all possible settings and values in the [Configuration section](/aqilink/configuration.md).

```
{
    repo: s3
    hallo: du,
    wer:da
}

```


## Third-Party Software

The **`aqilink`** release includes the following third-party software which is either used at runtime or while packaging the Docker image:


| Abbreviation | Description |
|---|---|
|jcabi-manifests| Manager of MANIFEST.MF files |
|example | this is an example |
|example 1| this is an example 1 |