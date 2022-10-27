# Basic `aqilink` App Configuration
Some basic **`aqilink`** app related configuration options are stored in a file called `app.yaml`. This file should be created in a `/configs/` directory. The `/configs/` directory in turn must be provided to the **`aqilink`** Docker image or has to be mapped into the running container. 

At a glance: Configuration files and structure. For the other required files besides the `app.yaml` check the [Configuration](../configuration/) section.
```
/configs/
    |
    + app.yaml
    + sapHttp.yaml
    + storage.yaml
    + sapConnection.yaml        
    + /tasks/
        |
        + task1.yaml
        + task2.yaml
        + taskX.yaml
```


## Enter License Key
The most important setting to be maintained in the `app.yaml` is the license key. The key must be stored in property `license` of the file. If there is no license key or no valid license key available, **`aqilink`** runs in demo mode.

```
license: <ENTER-LICENSE-KEY>
```

## Queue monitoring
To manage the [Tasks](/configuration/aqishare/tasks.md) of **`aqilink`** the [Bull Queue](https://optimalbits.github.io/bull/) is implemented. As default Web UI to manage queue items, the [bull-monitor](https://github.com/s-r-x/bull-monitor) is available - which is disabled by default. The reason for that is, once the Web UI is available the queue items can be accessed without any authentication. This means not only viewing the items but also the creation of new or the manipulation of existing queue entries.

To enable the Web UI anyway, the property `queueMonitorEnabled` must be present in the `app.yaml` and set to value `TRUE`.

```
queueMonitorEnabled: true
```

> With this setting, the Web UI is open to public and can be accessed through http://localhost:3000/queues from everyone who knows the IP-address and the port without any further authentication. 

> We highly recommend to disable the Web UI on any production environments! It's intended for debugging purposes in development systems only.

![Queue moitoring with bull-monitor](../_media/installation/bullmonitor.png)


### Redis Database
As the Bull Queue is based on the open source database [Redis](https://redis.io), there are many more settings regarding the database configuration available. `aqilink` supports all, because it just pass the properties 1:1 to Redis. Check [ioredis](https://github.com/luin/ioredis/blob/v4/API.md) for a full list of available parameter.

The key used to present Redis related settings in the `app.yaml` is `redis:`.
Here are just some most important properties to drive the Redis behaviour:

| Property      | Mandatory | Description |
| ----------- | ----------- |----------- |
| ``host`` | No | The host name of the redis database. Default: `localhost`. | 
| ``type`` | No | The port number to reach the database on the host. Default: `6379`.   |  
| ``db`` | No | The number of the database to be used. Default: `0`.   |  
| ``password`` | No | Password to login to the databse.   |  

## Password Encryption
The usage of encrypted passwords in the configuration files requires the specification of the parameter `publicKeyPath` which must specify the path inside the app to the public key. This key must be mapped to the container or must be copied into the image before (this is described later in chapter [App start](/installation/app-start.md)). If there is no need to encrypt passwords, this setting can be skipped and removed.

> Refer to [Password Encryption](/reference/password-encryption.md) in the reference section to generate key pairs and to setup password encryption.

```
publicKeyPath: /usr/src/app/configs/publicKey.pem
```

## Example Configuration
This is an example configuration with the most relevant properties explained above that must be present in the `app.yaml`.

```
license: 12345-67890-09876-54321
queueMonitorEnabled: false
redis:
  host: redis
  port: 6379
publicKeyPath: /usr/src/app/configs/publicKey.pem
```