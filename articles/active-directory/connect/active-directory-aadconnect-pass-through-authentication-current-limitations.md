---
title: 'Azure AD Connect: Pass-through Authentication - Current limitations | Microsoft Docs'
description: This article describes the current limitations of Azure Active Directory (Azure AD) Pass-through Authentication.
services: active-directory
keywords: Azure AD Connect Pass-through Authentication, install Active Directory, required components for Azure AD, SSO, Single Sign-on
documentationcenter: ''
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: billmath
---

# Azure Active Directory Pass-through Authentication: Current limitations

>[!IMPORTANT]
>Azure AD Pass-through Authentication is a free feature, and you don't need any paid editions of Azure AD to use it. Pass-through Authentication is only available in the world-wide instance of Azure AD, and not on [Microsoft Cloud Germany](http://www.microsoft.de/cloud-deutschland) and [Microsoft Azure Government Cloud](https://azure.microsoft.com/features/gov/).

## Supported scenarios

The following scenarios are fully supported:

- User sign-ins into all web browser-based applications.
- User sign-ins into Office 365 client applications that support [modern authentication](https://aka.ms/modernauthga) - Office 2016, and Office 2013 _with_ modern authentication.
- Azure AD Join for Windows 10 devices.
- Exchange ActiveSync support.

## Unsupported scenarios

The following scenarios are _not_ supported:

- User sign-ins into legacy Office client applications - Office 2010, and Office 2013 _without_ modern authentication). Organizations are encouraged to switch to modern authentication, if possible. Modern authentication allows for Pass-through Authentication support but also helps you secure your user accounts using [conditional access](../active-directory-conditional-access-azure-portal.md) features such as Multi-Factor Authentication (MFA).
- User sign-ins into Skype for Business client applications, including Skype for Business 2016.
- User sign-ins into PowerShell v1.0. It is recommended that you use PowerShell v2.0 instead.
- Azure AD Domain Services.
- App passwords for MFA.
- Detection of users with [leaked credentials](../active-directory-reporting-risk-events.md#leaked-credentials).

>[!IMPORTANT]
>As a workaround for unsupported scenarios _only_, enable Password Hash Synchronization on the [Optional features](active-directory-aadconnect-get-started-custom.md#optional-features) page in the Azure AD Connect wizard. Enabling Password Hash Synchronization also gives you the option to failover authentication if your on-premises infrastructure is completely disrupted. This failover from Pass-through Authentication to Password Hash Synchronization is not automatic, but has to done with the help of Microsoft Support.

## Next steps
- [**Quick Start**](active-directory-aadconnect-pass-through-authentication-quick-start.md) - Get up and running Azure AD Pass-through Authentication.
- [**Smart Lockout**](active-directory-aadconnect-pass-through-authentication-smart-lockout.md) - Configure Smart Lockout capability on your tenant to protect user accounts.
- [**Technical Deep Dive**](active-directory-aadconnect-pass-through-authentication-how-it-works.md) - Understand how this feature works.
- [**Frequently Asked Questions**](active-directory-aadconnect-pass-through-authentication-faq.md) - Answers to frequently asked questions.
- [**Troubleshoot**](active-directory-aadconnect-troubleshoot-pass-through-authentication.md) - Learn how to resolve common issues with the feature.
- [**Security Deep Dive**](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md) - Additional deep technical information on the feature.
- [**Azure AD Seamless SSO**](active-directory-aadconnect-sso.md) - Learn more about this complementary feature.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) - For filing new feature requests.
