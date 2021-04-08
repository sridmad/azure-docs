---
title: Azure Managed Application Section UI element | Microsoft Docs
description: Describes the Microsoft.Common.Section UI element for Azure Managed Applications
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn

ms.service: azure-resource-manager
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/12/2017
ms.author: tomfitz

---
# Microsoft.Common.Section UI element
A control that groups one or more elements under a heading. You use this element when [creating an Azure Managed Application](publish-service-catalog-app.md).

## UI sample
![Microsoft.Common.Section](./media/managed-application-elements/microsoft.common.section.png)

## Schema
```json
{
  "name": "section1",
  "type": "Microsoft.Common.Section",
  "label": "Some section",
  "elements": [
    {
      "name": "element1",
      "type": "Microsoft.Common.TextBox",
      "label": "Some text box 1"
    },
    {
      "name": "element2",
      "type": "Microsoft.Common.TextBox",
      "label": "Some text box 2"
    }
  ],
  "visible": true
}
```

## Remarks
- `elements` must contain at least one element, and can contain all element
types except `Microsoft.Common.Section`.
- This element doesn't support the `toolTip` property.

## Sample output
To access the output values of elements in `elements`, use the [basics()](create-uidefinition-functions.md#basics) or
[steps()](create-uidefinition-functions.md#steps) functions and dot notation:

```json
basics('section1').element1
```

Elements of type `Microsoft.Common.Section` have no output values themselves.

## Next steps
* For an introduction to managed applications, see [Azure Managed Application overview](overview.md).
* For an introduction to creating UI definitions, see [Getting started with CreateUiDefinition](create-uidefinition-overview.md).
* For a description of common properties in UI elements, see [CreateUiDefinition elements](create-uidefinition-elements.md).
