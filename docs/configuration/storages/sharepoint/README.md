# Microsoft SharePoint
To integrate Microsoft SharePoint Online with **`aqilink`**, it is crucial to set up an application in the Microsoft Azure portal and configure it with a certificate. This certificate must then be utilized in **`aqilink`**'s configuration to connect successfully to SharePoint Online. This documentation provides a step-by-step guide to achieve this integration, ensuring secure and seamless connectivity between **`aqilink`** and SharePoint Online. Once you've accomplished this step, you can proceed with the next chapter [Connect SAP with SharePoint](./sharepoint.md).

## Prerequisites <!-- {docsify-ignore} -->
Before starting, ensure you have the following:
* **Active Microsoft Azure Subscription:** You need an active subscription to Microsoft Azure. This subscription allows you to access the Azure portal and use Azure Active Directory (AD), which is crucial for registering your application and configuring authentication and permissions.

* **Administrator Access to Azure Portal:** Administrator access is required to perform actions such as registering a new application in Azure AD, configuring permissions, and managing certificates. This level of access ensures that you can fully manage the integration process without restrictions.

* **Basic Knowledge of Azure Active Directory (AD) and SharePoint Online:** Familiarity with Azure AD and SharePoint Online is important for understanding the integration process. Azure AD is used for authentication and authorization, while SharePoint Online is the service you'll be connecting to.

* **SSL Certificate:** Certificates are crucial for securing the communication between the **`aqilink`** and SharePoint Online. You must have knowledge to generate a new certificate or you must make sure to have an existing certificate available.