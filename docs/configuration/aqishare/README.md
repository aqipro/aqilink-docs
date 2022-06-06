# Configure *Data Exchange*

This chapter explain the configuration options for the *Data Exchange* module which allows to replicate any additional information from the SAP system towards the repository. The module also allows to trigger any action from the repository in SAP.

To continue with the additional metadata exchange, we recommend to have successfully completed the [*Document Exchange*](/configuration/aqilink/) configuration.

The configuration is split the following YAML files.

| File      | Description | 
| ----------- | ----------- |
| ``storage.yaml`` | Define the repository connection details. This is also the main configuration file for *Document Exchange*. Refer to [Storage Connections](/configuration/aqilink/#storage-connections). |
| ``sapConnection.yaml`` | Define the connection properties and the connection type (either Rest or RFC) to connect to the SAP systems. |
| ``tasks/myTask1.yaml``, ``tasks/myTask2.yaml``, ``tasks/myTaskx.yaml`` | Each file defines an action to be executed against the SAP system. This includes the Rest call or the RFC call, passing and receiving values from/to SAP and the storage of these values in the repository.  |
