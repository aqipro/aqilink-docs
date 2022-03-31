# aqilink

This is the main entry for the full documentation of our SAP certified storage connector `aqilink`.
The connector serves the following external storage systems / ECM systems:

* Nuxeo
* Amazon S3

## Prerequisites
Below are the environment/software prerequisites for installing and using **`aqilink`**.


### General Requirements

* A valid license for **`aqilink`** covering the desired target system. Read more in the [License](aqilink/license.md) section.
* All involved systems, meaning the **`aqilink`**, SAP and the target system, must be available in the same network, or connected through a VPN.

### Target Systems Requirements
As the **`aqilink`** connector is able to connect to different target systems, it's recommended to have at least some skills and knowledge about the target system.  

#### Nuxeo (Enterprise Content Management)
* Nuxeo version 19
* Firewall does not block HTTP traffic on port 80 / 8080 / 8082.
* ?

#### Amazon S3 (Storage)
Technical requirements to store documents from SAP through **`aqilink`** to an Amazon S3 storage:
* 1
* 2
* 3

## SAP Requirements
* SAP ECC 6.0 (or up to latest) with at least SAP GUI 7.30
* SAP S/4HANA (build 1909 or up to latest) with at least SAP GUI 7.50
* SAP dialog user who is able to:
   * Create new SAP Content Repositories (transaction `OAC0`)
   * Create related ArchiveLink customization as described in the SAP Implementation Guide (available via transaction `SPRO`)
   * Test the ArchiveLink interface in any related module (for example transaction `FB03` for Finance)