---

  title: What is group-based licensing in Azure Active Directory? | Microsoft Docs
  description: Description of Azure Active Directory group-based licensing, how it works and best practices
  services: active-directory
  keywords: Azure AD licensing
  documentationcenter: ''
  author: curtand
  manager: femila
  editor: ''

  ms.assetid:
  ms.service: active-directory
  ms.devlang: na
  ms.topic: article
  ms.tgt_pltfrm: na
  ms.workload: identity
  ms.date: 06/29/2017
  ms.author: curtand
  ms.reviewer: piotrci

  ms.custom: H1Hack27Feb2017;it-pro

---

# Group-based licensing basics in Azure Active Directory

Using Microsoft paid cloud services, such as Office 365, Enterprise Mobility + Security, Dynamics CRM, and other similar products, requires licenses. These licenses are assigned to each user who needs access to these services. To manage licenses, administrators use one of the management portals (Office or Azure) and PowerShell cmdlets. Azure Active Directory (Azure AD) is the underlying infrastructure that supports identity management for all Microsoft cloud services. Azure AD stores information about license assignment states for users.

Until now, licenses could only be assigned at the individual user level, which can make large-scale management difficult. For example, to add or remove user licenses based on organizational changes, such as users joining or leaving the organization or a department, an administrator often must write a complex PowerShell script. This script makes individual calls to the cloud service.

To address those challenges, Azure AD now includes group-based licensing. You can assign one or more product licenses to a group. Azure AD ensures that the licenses are assigned to all members of the group. Any new members who join the group are assigned the appropriate licenses. When they leave the group, those licenses are removed. This eliminates the need for automating license management via PowerShell to reflect changes in the organization and departmental structure on a per-user basis.

## Features

Here are the main features of group-based licensing:

- Licenses can be assigned to any security group in Azure AD. Security groups can be synced on-premises, by using Azure AD Connect. You can also create security groups directly in Azure AD (also called cloud-only groups), or automatically via the Azure AD dynamic group feature.

- When a product license is assigned to a group, the administrator can disable one or more service plans in the product. Typically, this is done when the organization is not yet ready to start using a service included in a product. For example, the administrator might assign Office 365 to a department, but temporarily disable the Yammer service.

- All Microsoft cloud services that require user-level licensing are supported. This includes all Office 365 products, Enterprise Mobility + Security, and Dynamics CRM.

- Group-based licensing is currently available only through [the Azure portal](https://portal.azure.com). If you primarily use other management portals for user and group management, such as the Office 365 portal, you can continue to do so. But you should use the Azure portal to manage licenses at group level.

- Azure AD automatically manages license modifications that result from group membership changes. Typically, license modifications are effective within minutes of a membership change.

- A user can be a member of multiple groups with license policies specified. A user can also have some licenses that were directly assigned, outside of any groups. The resulting user state is a combination of all assigned product and service licenses.

- In some cases, licenses cannot be assigned to a user. For example, there might not be enough available licenses in the tenant, or conflicting services might have been assigned at the same time. Administrators have access to information about users for whom Azure AD could not fully process group licenses. They can then take corrective action based on that information.

- During public preview, a paid or trial subscription for Azure AD Basic or Premium editions is required in the tenant to use group-based license management.

## Your feedback is welcome!

If you have feedback or feature requests, please share them with us using [this forum](https://feedback.azure.com/forums/169401-azure-active-directory/category/317677-group-based-licensing).

## Next steps

To learn more about other scenarios for license management through group-based licensing, see:

* [Get started with licenses in Azure Active Directory](active-directory-licensing-get-started-azure-portal.md)
* [Assigning licenses to a group in Azure Active Directory](active-directory-licensing-group-assignment-azure-portal.md)
* [Identifying and resolving license problems for a group in Azure Active Directory](active-directory-licensing-group-problem-resolution-azure-portal.md)
* [How to migrate individual licensed users to group-based licensing in Azure Active Directory](active-directory-licensing-group-migration-azure-portal.md)
* [Azure Active Directory group-based licensing additional scenarios](active-directory-licensing-group-advanced.md)
