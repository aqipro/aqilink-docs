# Configuration

This chapter explains in detail how our product **`aqilink`** and the component **`aqishare`** can be configured. The main configuration is split into different [YAML files](https://yaml.org) to offer maximum configuration flexibility. Before deep dive into the possible configuration options, the sections below will explain on a birds-eye view how the configuration files are related to each other. This makes it easier to understand which configuration file has to be tweaked to achieve a desired behavior.


## Component Overview <!-- {docsify-ignore} -->
Before going further, let's get familar with the terms **`aqilink`** and **`aqishare`**, which are important to understand upfront. Based on this components, you might start on the right place with the configuration.

| Name      | Description | Related Configuration Files
| ----------- | ----------- |----------- |
| **``aqilink``** | This is the product name and also the SAP certified component. It is responsible for all the traffic **from** the SAP HTTP-Content-Server **to** the related repository.  | ``sapHttp.yaml``,<br/>``storage.yaml`` |
| **``aqishare``** | It's a separate component and responsible for all metadata related actions. It can be used to either trigger any action in the related SAP system by calling Rest services (OData) or by calling function modules through RFC.   | ``sapConnection.yaml``, <br/> ``storage.yaml``, <br/> ``/tasks/myTask1.yaml``, <br/> ``/tasks/myTask2.yaml``, <br/> ... |

> Note: ``aqishare`` needs to be licensed separately.

## Configuration Files Overview <!-- {docsify-ignore} -->
The below table lists all available configuration files and its purpose. As explained above, these files are in [YAML files](https://yaml.org) format.

| File      | Description |
| ----------- | ----------- |
| ``sapHttp.yaml`` | Defines all connections **from** the SAP HTTP-Content-Server and maps it to the related storage. |
| ``storage.yaml`` | Defines all connections **to** the related repositories. This is the only file which is required by both, `aqilink` and `aqishare`.  |
| ``sapConnection.yaml`` | Defines all connections **to** related SAP Systems either via Rest (OData) or RFC.  |
| ``tasks/myTask1.yaml``, <br/>``tasks/myTask2.yaml``, <br/> ``tasks/myTaskx.yaml`` | Defines the action based on the connection. For each action, a separate file has to be created with a unique name within the folder ``tasks/``. This file name itself can be set to any name but should not contain special characters. These are the most important files for the connection **from** the repository **to** the SAP System and requires the most attention during the configuration. <br> |

## High-level File Association & Flow <!-- {docsify-ignore} -->
To help understand how the files above are related to each other in a more visual way, refer to the following image . With this in mind, the next step would be to start with the configuration of either [**`aqilink`**](/configuration/aqilink/) or/and [**`aqishare`**](/configuration/aqishare/).

![File Association](/_media/aqilink_high-level-architecture-files.png)