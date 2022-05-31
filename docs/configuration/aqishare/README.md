# Configure **`aqishare`**

This chapter explain the configuration options for to replicate additional information from the SAP system towards the repository. It also describes to trigger any action from the repository in SAP.

Requirement for any additional metadata exchange in this section is to have the basic [`aqilink` connection](../../configuration/aqilink/) successfully up and running.

The configuration is split the following YAML files.

| File      | Description | 
| ----------- | ----------- |
| ``storage.yaml`` | Define the repository connection details. This is also the main configuration file for `aqilink`. Refer to [Storage Connections](../configuration/aqilink/#storage-connections). |
| ``sapConnection.yaml`` | Define the connection properties and the connection type (either Rest or RFC) to connect to the SAP systems. |
| ``tasks/myTask1.yaml``, ``tasks/myTask2.yaml``, ``tasks/myTaskx.yaml`` | Each file defines an action to be executed against the SAP system. This includes the Rest call or the RFC call, passing and receiving values from/to SAP and the storage of these values in the repository.  |
