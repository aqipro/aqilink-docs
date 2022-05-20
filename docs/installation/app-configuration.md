# Basic `aqilink` App Configuration
Some basic `aqilink` app related configuration options are maintained in the `app.yaml` which is stored in the `/configs` directory. The most important setting to be maintained in the file is the license key. 

## Enter License Key
The license key must be stored in property `license` of the file. If there is no license key or no valid license key available, `aqilink` runs in demo mode.

```
license: <ENTER-LICENSE-KEY>
```


## Queue Monitor
To provide monitoring of the [Tasks](../configuration/aqishare/tasks.md), the [BullMQ library](https://docs.bullmq.io/) is used along with an UI

Default `queueMonitorEnabled` is `false`

> If `queueMonitorEnabled` is enabled, the Web UI is open to public. Everyone


## Example Configuration
An example configuration is shown below.

```
license: <ENTER-LICENSE-KEY>
queueMonitorEnabled: true
redis:
  host: redis
  port: 6379

```