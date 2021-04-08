---
title: Create identity for Azure app in portal | Microsoft Docs
description: Describes how to create a new Azure Active Directory application and service principal that can be used with the role-based access control in Azure Resource Manager to manage access to resources.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn

ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/16/2017
ms.author: tomfitz

---
# Use portal to create an Azure Active Directory application and service principal that can access resources

When you have an application that needs to access or modify resources, you must set up an Azure Active Directory (AD) application and assign the required permissions to it. This approach is preferable to running the app under your own credentials because:

* You can assign permissions to the app identity that are different than your own permissions. Typically, these permissions are restricted to exactly what the app needs to do.
* You do not have to change the app's credentials if your responsibilities change. 
* You can use a certificate to automate authentication when executing an unattended script.

This article shows you how to perform those steps through the portal. It focuses on a single-tenant application where the application is intended to run within only one organization. You typically use single-tenant applications for line-of-business applications that run within your organization.

## Required permissions

To complete this article, you must have sufficient permissions to register an application with your Azure AD tenant, and assign the application to a role in your Azure subscription. Let's make sure you have the right permissions to perform those steps.

### Check Azure Active Directory permissions

1. Log in to your Azure Account through the [Azure portal](https://portal.azure.com).

1. Select **Azure Active Directory**.

   ![select azure active directory](./media/resource-group-create-service-principal-portal/select-active-directory.png)

1. In Azure Active Directory, select **User settings**.

   ![select user settings](./media/resource-group-create-service-principal-portal/select-user-settings.png)

1. Check the **App registrations** setting. If set to **Yes**, non-admin users can register AD apps. This setting means any user in the Azure AD tenant can register an app. You can proceed to [Check Azure subscription permissions](#check-azure-subscription-permissions).

   ![view app registrations](./media/resource-group-create-service-principal-portal/view-app-registrations.png)

1. If the app registrations setting is set to **No**, only admin users can register apps. Check whether your account is an admin for the Azure AD tenant. Select **Overview** and **Find a user** from Quick tasks.

   ![find user](./media/resource-group-create-service-principal-portal/find-user.png)

1. Search for your account, and select it when you find it.

   ![search user](./media/resource-group-create-service-principal-portal/show-user.png)

1. For your account, select **Directory role**.

   ![directory role](./media/resource-group-create-service-principal-portal/select-directory-role.png)

1. View your assigned directory role in Azure AD. If your account is assigned to the User role, but the app registration setting (from the preceding steps) is limited to admin users, ask your administrator to either assign you to an administrator role, or to enable users to register apps.

   ![view role](./media/resource-group-create-service-principal-portal/view-role.png)

### Check Azure subscription permissions

In your Azure subscription, your account must have `Microsoft.Authorization/*/Write` access to assign an AD app to a role. This action is granted through the [Owner](../active-directory/role-based-access-built-in-roles.md#owner) role or [User Access Administrator](../active-directory/role-based-access-built-in-roles.md#user-access-administrator) role. If your account is assigned to the **Contributor** role, you do not have adequate permission. You receive an error when attempting to assign the service principal to a role.

To check your subscription permissions:

1. If you are not already looking at your Azure AD account from the preceding steps, select **Azure Active Directory** from the left pane.

1. Find your Azure AD account. Select **Overview** and **Find a user** from Quick tasks.

   ![find user](./media/resource-group-create-service-principal-portal/find-user.png)

1. Search for your account, and select it when you find it.

   ![search user](./media/resource-group-create-service-principal-portal/show-user.png)

1. Select **Azure resources**.

   ![select resources](./media/resource-group-create-service-principal-portal/select-azure-resources.png)

1. View your assigned roles, and determine if you have adequate permissions to assign an AD app to a role. If not, ask your subscription administrator to add you to User Access Administrator role. In the following image, the user is assigned to the Owner role for two subscriptions, which means that user has adequate permissions.

   ![show permissions](./media/resource-group-create-service-principal-portal/view-assigned-roles.png)

## Create an Azure Active Directory application

1. Log in to your Azure Account through the [Azure portal](https://portal.azure.com).
1. Select **Azure Active Directory**.

   ![select azure active directory](./media/resource-group-create-service-principal-portal/select-active-directory.png)

1. Select **App registrations**.

   ![select app registrations](./media/resource-group-create-service-principal-portal/select-app-registrations.png)

1. Select **New application registration**.

   ![add app](./media/resource-group-create-service-principal-portal/select-add-app.png)

1. Provide a name and URL for the application. Select **Web app / API** for the type of application you want to create. You cannot create credentials for a **Native** application; therefore, that type does not work for an automated application. After setting the values, select **Create**.

   ![name application](./media/resource-group-create-service-principal-portal/create-app.png)

You have created your application.

## Get application ID and authentication key

When programmatically logging in, you need the ID for your application and an authentication key. To get those values, use the following steps:

1. From **App registrations** in Azure Active Directory, select your application.

   ![select application](./media/resource-group-create-service-principal-portal/select-app.png)

1. Copy the **Application ID** and store it in your application code. The applications in the [sample applications](#sample-applications) section refer to this value as the client ID.

   ![client ID](./media/resource-group-create-service-principal-portal/copy-app-id.png)

1. To generate an authentication key, select **Keys**.

   ![select keys](./media/resource-group-create-service-principal-portal/select-keys.png)

1. Provide a description of the key, and a duration for the key. When done, select **Save**.

   ![save key](./media/resource-group-create-service-principal-portal/save-key.png)

   After saving the key, the value of the key is displayed. Copy this value because you are not able to retrieve the key later. You provide the key value with the application ID to log in as the application. Store the key value where your application can retrieve it.

   ![saved key](./media/resource-group-create-service-principal-portal/copy-key.png)

## Get tenant ID

When programmatically logging in, you need to pass the tenant ID with your authentication request.

1. Select **Azure Active Directory**.

   ![select azure active directory](./media/resource-group-create-service-principal-portal/select-active-directory.png)

1. To get the tenant ID, select **Properties** for your Azure AD tenant.

   ![select Azure AD properties](./media/resource-group-create-service-principal-portal/select-ad-properties.png)

1. Copy the **Directory ID**. This value is your tenant ID.

   ![tenant ID](./media/resource-group-create-service-principal-portal/copy-directory-id.png)

## Assign application to role

To access resources in your subscription, you must assign the application to a role. Decide which role represents the right permissions for the application. To learn about the available roles, see [RBAC: Built in Roles](../active-directory/role-based-access-built-in-roles.md).

You can set the scope at the level of the subscription, resource group, or resource. Permissions are inherited to lower levels of scope. For example, adding an application to the Reader role for a resource group means it can read the resource group and any resources it contains.

1. Navigate to the level of scope you wish to assign the application to. For example, to assign a role at the subscription scope, select **Subscriptions**. You could instead select a resource group or resource.

   ![select subscription](./media/resource-group-create-service-principal-portal/select-subscription.png)

1. Select the particular subscription (resource group or resource) to assign the application to.

   ![select subscription for assignment](./media/resource-group-create-service-principal-portal/select-one-subscription.png)

1. Select **Access Control (IAM)**.

   ![select access](./media/resource-group-create-service-principal-portal/select-access-control.png)

1. Select **Add**.

   ![select add](./media/resource-group-create-service-principal-portal/select-add.png)

1. Select the role you wish to assign to the application. The following image shows the **Reader** role.

   ![select role](./media/resource-group-create-service-principal-portal/select-role.png)

1. Search for your application, and select it.

   ![search for app](./media/resource-group-create-service-principal-portal/search-app.png)

1. Select **Save** to finish assigning the role. You see your application in the list of users assigned to a role for that scope.

## Log in as the application

Your application is now set up in Azure Active Directory. You have an ID and key to use for signing in as the application. The application is assigned to a role that gives it certain actions it can perform. For information about logging in as the application through different platforms, see:

* [PowerShell](resource-group-authenticate-service-principal.md#provide-credentials-through-powershell)
* [Azure CLI](resource-group-authenticate-service-principal-cli.md)
* [REST](/rest/api/#create-the-request)
* [.NET](/dotnet/azure/dotnet-sdk-azure-authenticate?view=azure-dotnet)
* [Java](/java/azure/java-sdk-azure-authenticate)
* [Node.js](/nodejs/azure/node-sdk-azure-get-started?view=azure-node-2.0.0)
* [Python](/python/azure/python-sdk-azure-authenticate?view=azure-python)
* [Ruby](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/)

## Next steps
* To set up a multi-tenant application, see [Developer's guide to authorization with the Azure Resource Manager API](resource-manager-api-authentication.md).
* To learn about specifying security policies, see [Azure Role-based Access Control](../active-directory/role-based-access-control-configure.md).  
* For a list of available actions that can be granted or denied to users, see [Azure Resource Manager Resource Provider operations](../active-directory/role-based-access-control-resource-provider-operations.md).
