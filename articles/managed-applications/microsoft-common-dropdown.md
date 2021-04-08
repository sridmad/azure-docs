---
title: Azure Managed Application DropDown UI element | Microsoft Docs
description: Describes the Microsoft.Common.DropDown UI element for Azure Managed Applications
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

# Microsoft.Common.DropDown UI element
A selection control with a dropdown list. You use this element when [creating an Azure Managed Application](publish-service-catalog-app.md).

## UI sample
![Microsoft.Common.DropDown](./media/managed-application-elements/microsoft.common.dropdown.png)

## Schema
```json
{
  "name": "element1",
  "type": "Microsoft.Common.DropDown",
  "label": "Some drop down",
  "defaultValue": "Foo",
  "toolTip": "",
  "constraints": {
    "allowedValues": [
      {
        "label": "Foo",
        "value": "Bar"
      },
      {
        "label": "Baz",
        "value": "Qux"
      }
    ]
  },
  "visible": true
}
```

## Remarks
- The label for `constraints.allowedValues` is the display text for an item, and its value is the output value of the element when selected.
- If specified, the default value must be a label present in `constraints.allowedValues`. If not specified, the first item in `constraints.allowedValues` is selected. The default value is **null**.
- `constraints.allowedValues` must contain at least one item.
- This element doesn't support the `constraints.required` property. To emulate this behavior, add an item with a label and value of `""` (empty string) to `constraints.allowedValues`.

## Sample output
```json
"Bar"
```

## Next steps
* For an introduction to managed applications, see [Azure Managed Application overview](overview.md).
* For an introduction to creating UI definitions, see [Getting started with CreateUiDefinition](create-uidefinition-overview.md).
* For a description of common properties in UI elements, see [CreateUiDefinition elements](create-uidefinition-elements.md).
