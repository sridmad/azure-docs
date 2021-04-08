---
title: Do you need help with the My Apps portal in Azure Active Directory | Microsoft Docs
description: Get instructions to perform common tasks when working with the access panel.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: femila
editor: ''

ms.assetid: c67cd675-b567-41e1-8bc2-e06fe0b38d3b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/21/2017
ms.author: markvi
ms.reviewer: japere

---
# Do you need help with the My Apps portal?

You have probably reached this page because you were unfortunately running into an issue when using the My Apps portal. While there are cases that require you to contact helpdesk or your administrator to get a problem solved, here are some troubleshooting topics that might be able to help, first.

## I am having trouble signing into the My Apps portal

General issues to check:

- Check to see if you are signing into the correct URL: [https://myapps.microsoft.com](https://myapps.microsoft.com)

- Try adding the URL to your browser’s trusted sites.

- Make sure your password is not expired or forgotten. Check [here](active-directory-passwords-update-your-own-password.md) for more details on how to update your password.

- Check to see if your authentication contact info is up to date and not blocking your access. Check [here](https://docs.microsoft.com/en-us/azure/multi-factor-authentication/end-user/multi-factor-authentication-end-user) for more details on setting up your authentication info.

- Try clearing your browser’s cookies, and then try to sign in again.

If you are still encountering issues while trying to sign in, please contact your administrator for further help.


## How do I update my password?

If you forgot your password, never received one from your IT staff, been locked out of your account, or want to change it, see [Help, I forgot my Azure AD password](active-directory-passwords-update-your-own-password.md) for more details.

## How do I register for password reset?

As an end user, you can reset your password or unlock your account without having to speak to a person using self-service password reset (SSPR). Before you can use this functionality, you have to register authentication methods or confirm the predefined authentication methods your administrator has populated. For more details, see [Register for self-service password reset](active-directory-passwords-reset-register.md).


## I am having trouble installing the My Apps portal browser extension

Check to see if you are meeting browser requirements:

- The portal requires a browser that supports JavaScript and has CSS enabled. If you are using password-based single sign-on apps, the accompanying extension must be installed as well. This extension is downloaded automatically when you launch an application that is configured for password-based single sign-on apps.

- The browser requirements for the extension are:
    - Internet Explorer 8, 9, 10, 11 on Windows 7 or later
    - Edge on Windows 10 Anniversary Edition or later
    - Chrome on Windows 7 or later, and on MacOS X or later
    - Firefox 26.0 or later on Windows XP SP2 or later, and on Mac OS X 10.6 or later

You can also download the extension for Chrome and Edge from the direct links below:

- [Chrome Extension](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

- [Edge Extension](https://www.microsoft.com/store/apps/9pc9sckkzk84)

After installation try the following steps if you are encountering issues:

- Check to in your browser extension settings that the extension is enabled.

- Restart your browser and sign in to the My Apps portal.

- Clear your browser’s cookies and sign in to the My Apps portal.

## How do I add a new app?

1.	On the **Apps** page, click **Add App**.

2.	Search the app you want to add, and then click **Add**.

**Remarks:**

- You only have access to this option if your admin has enabled this for your account.

- If the app requires permission, you may need to wait for admin approval.


## How do I manage my group memberships?

1. Click the Groups app tile. 
2. To create a group, under Groups I own, click Create group, and then follow the instructions.
3. To join a group, under Groups I'm in, click Join group, and then follow the instructions.

**Remarks:**

- You only have access to this option if your admin has enabled this for your account.

- Groups you are a member of allow you to view details and leave the group.

- Groups you are an owner of allows you to view details, add or remove members, and leave the group.


## Next steps

For troubleshooting related information, see [Problems using the application access panel website or mobile application](active-directory-application-access-panel-content-map.md)

