---
title: 'Azure Active Directory Terms of Use| Microsoft Docs'
description: Azure AD Terms of Use will allow you and your company the ability to provide terms of use to users of Azure AD servcies.
services: active-directory
documentationcenter: ''
author: billmath
manager: femila
editor: ''
ms.assetid: d55872ef-7e45-4de5-a9a0-3298e3de3565
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/17/2017
ms.author: billmath

---

# Azure Active Directory Terms of Use feature (Preview)
Azure AD Terms of Use provides a simple method organizations can use to present information to end users.  This ensures users see relevant disclaimers for legal or compliance requirements.

Azure AD Terms of Use uses the pdf format to present content.   This pdf can be any content, such as existing contract documents, allowing you to collect end user agreements during user sign-in.  You can use the terms of use for applications, groups of users, or if you have multiple terms of use for different purposes.

The remainder of this document describes how to get going with Azure AD Terms of Use.  

## Why use Azure AD Terms of Use
Finding it difficult to get employee’s or guests to agree to your terms of use before getting access? Need help figuring out who has or hasn’t agreed to your company terms of use?  Azure AD Terms of Use provides a simple method organizations can use to present information to end users.  This ensures that they see relevant disclaimers for legal or compliance requirements.

Azure AD Terms of Use can be used in the following scenarios:
-	General terms of use for all users in your organization.
-	Specific terms of use based on a user attributes (ex. doctors vs nurses or domestic vs international employees, done by [dynamic groups](https://azure.microsoft.com/updates/azure-active-directory-dynamic-membership-for-groups)).
-	Specific terms of use based on accessing high business impact apps, like Salesforce.


## Prerequisites
Use the following steps to configure Azure AD Terms of Use:

1. Sign in to Azure AD using a global administrator, security administrator, or a conditional access administrator for the directory you want to configure Azure AD Terms of Use.
2. Ensure that the directory has an Azure AD Premium P1, P2, EMS E3, or EMS E5 subscription.  If you do not [Get Azure AD Premium](active-directory-get-started-premium.md) or [start a trial](https://azure.microsoft.com/trial/get-started-active-directory/).
3. View the Azure AD Terms of User dashboard at [https://aka.ms/catou](https://aka.ms/catou).

>[!IMPORTANT]
>Conditional access policy controls (including terms of use) do not support enforcement on service accounts.  We recommend excluding all service accounts from the conditional access policy.

## Add Company Terms of Use
Once you have finalized your Terms of Use, use the following procedure to add it.

### To add Terms of Use
1. Navigate to the dashboard at [https://aka.ms/catou](https://aka.ms/catou)
2. Click Add.</br>
![Add TOU](media/active-directory-tou/tou2.png)
3. Enter the **Name** for the Terms of Use
4. Enter **Display Name**.  This header is what users see when they sign in.
5. **Browse** to your finalized terms of use pdf and select it.  The recommended font size is 24.
6. You can **Enforce** the uploaded terms of use by using a template or a custom conditional access policy.  Custom conditional access policies enables granular terms of use, down to a specific cloud application or group of users.  For more information, see [configuring conditional access policies](active-directory-conditional-access-best-practices.md)
7. Click **Create**.
8. If you selected a custom conditional access template, then a new screen appears which allows you to customize the CA policy.
7. You should now see your new Terms of Use.</br>

![Add TOU](media/active-directory-tou/tou3.png)

## Delete Terms of Use
You can remove or delete old terms of use using the following procedure:

### To delete Terms of Use
1. Navigate to the dashboard at [https://aka.ms/catou](https://aka.ms/catou)
2. Select the terms of use you want to remove.
3. Click **Delete**.
4. You should no longer see your new terms of use.


## Audit Terms of Use
Azure AD Terms of Use provides easy to use auditing so that you can see who has accepted and when they accepted your terms of use.  To get started with auditing use the following procedure:

### To audit Terms of Use
1. Navigate to the dashboard at [https://aka.ms/catou](https://aka.ms/catou)
2. Click Audit Event.</br>
![Audit Event](media/active-directory-tou/tou8.png)
3.  On the Azure AD audit logs screen, you can filter the information using the provided drops downs to target specific audit log information.
![Audit Event](media/active-directory-tou/tou9.png)
4.  You can also download the information in a .csv file for use locally.

## What users see
Users, who are in scope, will see the following once a terms of use is created and enforced.  They will see these screens during sign in.
-	Best practice is to have the font within the PDF at size 24.
![Audit Event](media/active-directory-tou/tou10.png)
-	This screen is how it appears on mobiles</br></br>
![Audit Event](media/active-directory-tou/tou11.png)

## Additional information
The following information is something to be aware of and can assist with using terms of use.

Users in scope will need to sign-out and sign-in in order to satisfy a new policy if:
 - a conditional access policy is enabled on a terms of use
 - or a second terms of use is created

This is because conditional access policies take effect immediately. When this happens the admin will start to see “sad clouds” or "Azure AD token issues". The admin must sign-out and sign-in again in order to satisfy the new policy.





## Frequently asked questions

**Q: How do I see when/if a user has accepted a terms of use?**</br>
A: A user accepting the terms of use is written to the audit log. You can search the Azure AD audit log to see the results.  

**Q: If you change the terms of use terms does it require users to accept again?**</br>
A: Yes, an admin can change the terms of use terms and it requires reaccepting the new terms.

**Q: Can a terms of use support multi languages?**</br>
A: No, currently it is not possible to have multiple languages in a single terms of use.  However, you can scope to a group (for example, terms of use for France is different from terms of use for UK). 

**Q: When is the terms of use triggered?**</br>
A: The terms of use is triggered during the sign-in experience.

**Q: What applications can I target a terms of use too?**</br>
A: You can create a conditional access policy on the enterprise applications using modern authentication.  For more information, see [enterprise applications](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-view-azure-portal).

**Q: Can I add multiple terms of use to a given user or app?**</br>
A: Yes, by creating multiple conditional access policies targeting those groups or apps. If a user falls in scope of multiple terms of use they agree to one terms of use at a time.
 
**Q: What happens if a user declines the terms of use?**</br>
A: The user is blocked from getting access to the application. The user would have to sign-in again and agree to the terms in order to get access.