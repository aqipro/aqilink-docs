# Basic `aqilink` App Configuration
Some basic `aqilink` app related configuration options are maintained in the `app.yaml` which is stored in the `/configs` directory. The most important setting to be maintained in this file is the license key. 

## Enter License Key
The license key must be stored in property `license` of the file. If there is no license key or no valid license key available, `aqilink` runs in demo mode.

```
license: <ENTER-LICENSE-KEY>
```


## Queue Monitor
To provide monitoring of the [Tasks](../configuration/aqishare/tasks.md), the [Bull Queue](https://optimalbits.github.io/bull/) from OptimalBits is used. This queue should only be enabled for debugging, therefore, by default the Bull Queue monitor is disabled. The related property in the `app.yaml` is `queueMonitorEnabled`.

```
queueMonitorEnabled: <TRUE-or-FALSE>
```

> If `queueMonitorEnabled` is enabled, the Web UI is open to public. Everyone who knows the IP-address and the port can access it. There is no further authentication. We do not recommend this setting for production environments.

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

## Example Configuration
An example configuration with all available properties for the `app.yaml`.

```
license: 12345-67890-09876-54321
queueMonitorEnabled: true
redis:
  host: redis
  port: 6379

```