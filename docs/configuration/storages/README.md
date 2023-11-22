# Storages
In this chapter, you will learn in detail how to connect SAP with a related storage system (SAP Content Repository). Before proceeding, ensure that **`aqilink`** has already been [installed](/installation/) and [configured](/configuration/) for the desired repository.

## Prerequisites
Unlike the specific requirements for the repository you wish to connect, the following requirements are mandatory for all. 

### SAP dialog user
To customize the SAP side, an SAP dialog user must be available who has the appropriate permissions to manage SAP Content Repositories. This entails having read and write access to the following transactions: `OAC0`, `OAC2` and `OAC3`. Additionally, for functional testing of the aqilink interface, access to transaction `SE38` is necessary to invoke the test report `RSCMST`. Ensure you have an SAP user with these permissions ready.

### Administration user in the repository
In the related repository, it is also necessary to log in with an administration user who has all permissions in the system, at least during the installation and configuration process.
