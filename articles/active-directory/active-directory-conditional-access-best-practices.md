---
title: Best practices for conditional access in Azure Active Directory  | Microsoft Docs
description: Learn about things you should know and what it is you should avoid doing when configuring conditional access policies.
services: active-directory
keywords: conditional access to apps, conditional access with Azure AD, secure access to company resources, conditional access policies
documentationcenter: ''
author: MarkusVi
manager: femila
editor: ''

ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/16/2017
ms.author: markvi
ms.reviewer: calebb

---
# Best practices for conditional access in Azure Active Directory

This topic provides you with information about things you should know and what it is you should avoid doing when configuring conditional access policies. Before reading this topic, you should familiarize yourself with the concepts and the terminology outlined in [Conditional access in Azure Active Directory](active-directory-conditional-access-azure-portal.md)

## What you should know

### What’s required to make a policy work?

When you create a new policy, there are no users, groups, apps or access controls selected.

![Cloud apps](./media/active-directory-conditional-access-best-practices/02.png)


To make your policy work, you must configure the following:


|What           | How                                  | Why|
|:--            | :--                                  | :-- |
|**Cloud apps** |You need to select one or more apps.  | The goal of a conditional access policy is to enable you to fine-tune how authorized users can access your apps.|
| **Users and groups** | You need to select at least one user or group that is authorized to access the cloud apps you have selected. | A conditional access policy that has no users and groups assigned, is never triggered. |
| **Access controls** | You need to select at least one access control. | Your policy processor needs to know what to do if your conditions are satisfied.|


In addition to these basic requirements, in many cases, you should also configure a condition. While a policy would also work without a configured condition, conditions are the driving factor for fine-tuning access to your apps.


![Cloud apps](./media/active-directory-conditional-access-best-practices/04.png)



### How are assignments evaluated?

All assignments are logically **ANDed**. If you have more than one assignment configured, to trigger a policy, all assignments must be satisfied.  

If you need to configure a location condition that applies to all connections made from outside your organization's network, you can accomplish this by:

- Including **All locations**
- Excluding **All trusted IPs**

### What happens if you have policies in the Azure classic portal and Azure portal configured?  
Both policies are enforced by Azure Active Directory and the user gets access only when all requirements are met.

### What happens if you have policies in the Intune Silverlight portal and the Azure Portal?
Both policies are enforced by Azure Active Directory and the user gets access only when all requirements are met.

### What happens if I have multiple policies for the same user configured?  
For every sign-in, Azure Active Directory evaluates all policies and ensures that all requirements are met before granted access to the user.


### Does conditional access work with Exchange ActiveSync?

Yes, you can use Exchange ActiveSync in a conditional access policy.


## What you should avoid doing

The conditional access framework provides you with a great configuration flexibility. However, great flexibility  also means that you should carefully review each configuration policy prior to releasing it to avoid undesirable results. In this context, you should pay special attention to assignments affecting complete sets such as **all users / groups / cloud apps**.

In your environment, you should avoid the following configurations:


**For all users, all cloud apps:**

- **Block access** - This configuration blocks your entire organization, which is definitely not a good idea.

- **Require compliant device** - For users that don't have enrolled their devices yet, this policy blocks all access including access to the Intune portal. If you are an administrator without an enrolled device, this policy blocks you from getting back into the Azure portal to change the policy.

- **Require domain join** - This policy block access has also the potential to block access for all users in your organization if you don't have a domain-joined device yet.


**For all users, all cloud apps, all device platforms:**

- **Block access** - This configuration blocks your entire organization, which is definitely not a good idea.



## Policy migration

If you have policies in the Azure classic portal configured, you should migrate them to the Azure portal because:


- A user who is in an Azure classic portal policy and an Azure portal policy needs to meet the requirements in both policies 

- If you don't migrate your existing policies, you will not be able to implement policies that are granting access


### Migration from the Azure classic portal

In this scenario: 

- In your [Azure classic portal](https://manage.windowsazure.com), you have configured:

    - SharePoint Online

    ![Conditional access](./media/active-directory-conditional-access-best-practices/14.png)

    - A device-based conditional access policy

    ![Conditional access](./media/active-directory-conditional-access-best-practices/15.png)

- You want to configure a mobile application management conditional access policy in the Azure portal 
 

#### Configuration 

- Review your device-based conditional access policies

- Migrate them to the Azure portal 

- Add mobile application management conditional access policies


### Migrating from Intune 

In this scenario:

- In [Intune](https://portal.azure.com/#blade/Microsoft_Intune/SummaryBlade ), you have a mobile application management conditional access policy for either Exchange Online or SharePoint Online configured

    ![Conditional access](./media/active-directory-conditional-access-best-practices/15.png)

- You want to migrate to using mobile application management conditional access in the Azure portal


#### Configuration 
 
- Review your device-based conditional access policies

- Migrate them to the Azure portal 

- Review you mobile application management conditional access policies configured for Exchange Online or SharePoint Online in Intune

- Add the control for **Require approved applications** in addition to the device-based control 
 

### Migrating from the Azure classic portal and Intune

In this scenario:

- You have the following configured:

    - **Azure classic portal:** Device-based conditional 

    - **Intune:** Mobile application management conditional access policies 
    
- You want to migrate both policies to using mobile application management conditional access policies in the Azure portal


#### Configuration

- Review your device-based conditional access policies

- Migrate them to the Azure portal 

- Review you mobile application management conditional access policy configured for Exchange Online or SharePoint Online in Intune

- Add the control for **Require approved applications** in addition to the device-based 




## Next steps

If you want to know how to configure a conditional access policy, see [Get started with conditional access in Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md).
