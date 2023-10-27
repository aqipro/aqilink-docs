# Configure *Data Exchange*

This chapter explains the configuration options for the *Data Exchange* module, which allows for the replication of any additional information from the SAP system to the repository. The module also enables triggering actions from the repository in SAP.

Before proceeding with the additional metadata exchange, we recommend having successfully completed the [*Document Exchange*](/configuration/aqilink/) configuration.

The configuration is split across the following YAML files:

| File      | Description | 
| ----------- | ----------- |
| ``storage.yaml`` | Define the repository connection details. This is also the main configuration file for *Document Exchange*. Refer to [Storage Connections](/configuration/aqilink/#storage-connections). |
| ``sapConnection.yaml`` | Define the connection properties and the connection type (either REST or RFC) to connect to the SAP systems. |
| ``tasks/myTask1.yaml``, ``tasks/myTask2.yaml``, ``tasks/myTaskx.yaml`` | Each file defines an action for execution against the SAP system. This encompasses the REST or RFC call, the exchange of values with SAP, and their storage in the repository.  |
