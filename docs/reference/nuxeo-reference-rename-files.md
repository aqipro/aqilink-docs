# Nuxeo - Reference for Feature *Rename*

The feature *Rename* (for both the data file and the parent folder) allows you to rename all SAP ArchiveLink stored files to their original filenames.


### Requirements
1) The metadata replication is enabled and facet `SAP Replicated Details` is available on the document.
2) The feature has been enabled for the repository.

## Administration
The feature can be enabled or disabled on each available SAP Content Repository file in Nuxeo. This means it can be either enabled or disabled for all documents stored in this repository. The SAP Content Repository files are found within the corresponding [SAP HTTP-Content Server Connection](/configuration/aqilink/#sap-http-content-server-connection) folder in the `aqilink` administration path of Nuxeo. 
The file extension is **`.repo`** and can be found within the specified `adminPath` (in configuration file `storage.yaml`, refer to [Storage Connection for Hyland Nuxeo Repository](/configuration/aqilink/#hyland-nuxeo-repository)) within the repository.

![Rename 0](../_media/reference/feature_rename_0.png)

Edit the properties of the `.repo` file to enable the feature:


![Rename 1](../_media/reference/feature_rename_1.png)


> Note: Existing documents in Nuxeo are not touched by any change of this setting. The rename will work for new arriving documents only.

## User Expericence
Once the feature is enabled, both the *data* file and the parent folder of each incoming SAP ArchiveLink document stored in this repository will be automatically renamed with the original filename stored in SAP. There is no additional action required by the user.

### Before renaming

Before enabling the *Rename* feature, the documents stored from SAP looks like below. The filename of the file is just *data* (without any extension) and the parent folder containing the components (such as the *data* document or a SAP note) will have the SAP ArchiveLink Document Id as name.
![Structure before](../_media/reference/feature_rename_3_before_structure.png)


### After renaming
After the feature is enabled, the names of the folder and the *data* file will become the original filename once the `SAP Replicated Details` facet is available on the document.
![Structure after](../_media/reference/feature_rename_4_after_structure.png)

The value of "SAP Filename" from the "SAP Business Object Details" will be used to rename the *data* file and the parent folder. 

![Structure after](../_media/reference/feature_rename_5_facet.png)