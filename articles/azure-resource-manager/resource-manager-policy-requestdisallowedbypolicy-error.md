---
title: RequestDisallowedByPolicy error with Azure resource policy | Microsoft Docs
description: Describes the cause of the RequestDisallowedByPolicy error.
services: azure-resource-manager,azure-portal
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''

ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: support-article
ms.date: 11/03/2017
ms.author: genli

---
# RequestDisallowedByPolicy error with Azure resource policy

This article describes the cause of the RequestDisallowedByPolicy error, it also provides solution for this error.

## Symptom

When you try to do an action during deployment, you might receive a **RequestDisallowedByPolicy** error that prevents the action from completing. The following example shows the error:

```json
{
  "statusCode": "Forbidden",
  "serviceRequestId": null,
  "statusMessage": "{\"error\":{\"code\":\"RequestDisallowedByPolicy\",\"message\":\"The resource action 'Microsoft.Network/publicIpAddresses/write' is disallowed by one or more policies. Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'.\"}}",
  "responseBody": "{\"error\":{\"code\":\"RequestDisallowedByPolicy\",\"message\":\"The resource action 'Microsoft.Network/publicIpAddresses/write' is disallowed by one or more policies. Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'.\"}}"
}
```

## Troubleshooting

To retrieve details about the policy that blocked your deployment, use the following one of the methods:

### Method 1

In PowerShell, provide that policy identifier as the `Id` parameter to retrieve details about the policy that blocked your deployment.

```PowerShell
(Get-AzureRmPolicyDefinition -Id "/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition").Properties.policyRule | ConvertTo-Json
```

### Method 2 

In Azure CLI 2.0, provide the name of the policy definition: 

```azurecli
az policy definition show --name regionPolicyAssignment
```

## Solution

For security or compliance, your IT department might enforce a resource policy that prohibits creating Public IP addresses, Network Security Groups, User-Defined Routes, or route tables. The error message in the **Symptoms** section shows a policy named **regionPolicyDefinition**. Your policy might have a different name.
To resolve this problem, work with your IT department to review the resource policies, and determine how to perform the requested action in compliance with those policies.

For more information, see the following articles:

- [Resource policy overview](resource-manager-policy.md)
- [View policy assignments through portal](resource-manager-policy-portal.md)
