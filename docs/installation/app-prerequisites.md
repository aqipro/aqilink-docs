# Prerequisites

Below are the environment/software prerequisites to successfully run **`aqilink`**. It also lists the necessary requirement of the related systems.

!> Make sure **`aqilink`**, SAP and the storage system (such as Nuxeo or Amazon S3) are available in the same network, or connected through a VPN.


## `aqilink`
To successfully run the SAP certified integration **`aqilink`**, the following requirements must be fulfilled:

* Valid License key
* Container Runtime
* Internet connection
* [Basic app settings](installation/app-configuration.md) within a configuration folder
* Download [SAP NetWeaver RFC SDK](installation/app-download?id=sap-netweaver-rfc-sdk) to be able to use the [Data Exchange](/configuration/aqishare) capability.

## SAP

* SAP ECC 6.0 (or up to latest) with at least SAP GUI 7.30
* SAP S/4HANA (build 2009 or up to latest) with at least SAP GUI 7.40
* SAP dialog user with at least the following permissions:
  * T-code `OAC0` - to create new SAP Content Repositories
  * T-codes `OAC2`, `OAC3`  - to create related ArchiveLink customization
  * T-code `FB03` - to test the ArchiveLink interface for Finance documents (or access to any other related SAP module)
  * T-code `OAM1`- to access the ArchiveLink Monitor with further customization options, such as Barcode
* SAP system user who is able to:
  * Invoke BAPIs via RFC connection
  * Invoke OData services

## Nuxeo

* Nuxeo LTS 2019
* Nuxeo LTS 2023
* Separate Nuxeo user only used for the SAP integration (recommended) 

## AWS S3

* AWS account
* Access / Secret key

## Microsoft SharePoint Online

* Permission to manage a site
* Access to deploy the content model using PnP-PowerShell
* Active Microsoft Azure Subscription and administrator access to the Azure Portal. [Learn more](/configuration/storages/sharepoint/azure)