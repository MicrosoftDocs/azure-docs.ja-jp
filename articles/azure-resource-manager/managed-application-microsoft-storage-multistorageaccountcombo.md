---
title: "Azure マネージ アプリケーションの MultiStorageAccountCombo UI 要素 | Microsoft Docs"
description: "Azure マネージ アプリケーションの Microsoft.Storage.MultiStorageAccountCombo UI 要素について説明します。"
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
ms.date: 05/12/2017
ms.author: tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: afa23b1395b8275e72048bd47fffcf38f9dcd334
ms.openlocfilehash: 27843b116d949899e4eae65f342324f77ebca70b
ms.contentlocale: ja-jp
ms.lasthandoff: 05/13/2017

---
# <a name="microsoftstoragemultistorageaccountcombo-ui-element"></a>Microsoft.Storage.MultiStorageAccountCombo UI 要素
名前が共通のプレフィックスで始まる複数のストレージ アカウントを作成するコントロールのグループです。 この要素は、[Azure マネージ アプリケーションを作成する](managed-application-publishing.md)ときに使用します。

## <a name="ui-sample"></a>UI サンプル
![Microsoft.Storage.MultiStorageAccountCombo](./media/managed-application-elements/microsoft.storage.multistorageaccountcombo.png)

## <a name="schema"></a>スキーマ
```json
{
  "name": "element1",
  "type": "Microsoft.Storage.MultiStorageAccountCombo",
  "label": {
    "prefix": "Storage account prefix",
    "type": "Storage account type"
  },
  "toolTip": {
    "prefix": "",
    "type": ""
  },
  "defaultValue": {
    "prefix": "sa",
    "type": "Premium_LRS"
  },
  "constraints": {
    "allowedTypes": [],
    "excludedTypes": []
  },
  "count": 2,
  "visible": true
}
```

## <a name="remarks"></a>解説
- `defaultValue.prefix` の値は、ストレージ アカウント名のシーケンスを生成するために、1 つまたは複数の整数と連結されます。 たとえば、`defaultValue.prefix` が **foobar** で `count` が **2** である場合、ストレージ アカウント名として **foobar1** と **foobar2** が生成されます。 生成されたストレージ アカウント名は、一意であることが自動的に検証されます。
- ストレージ アカウント名は、`count` に基づいて辞書式に生成されます。 たとえば、`count` が 10 である場合、ストレージ アカウント名は 2 桁の整数 (01、02、03 など) で終了します。
- `defaultValue.prefix` の既定値は **null** です。`defaultValue.type` の既定値は **Premium_LRS** です。
- `constraints.allowedTypes` で指定されていない型はすべて非表示となり、`constraints.excludedTypes` で指定されていない型はすべて表示されます。
`constraints.allowedTypes` と `constraints.excludedTypes` は両方とも使用できますが、同時に使用することはできません。
- `count` は、ストレージ アカウント名を生成するだけでなく、適切な乗数を要素に設定するためにも使用されます。 **2** などの静的な値のほか、他の要素からの `[steps('step1').storageAccountCount]` などの動的な値もサポートします。 既定値は **1** です。

## <a name="sample-output"></a>サンプル出力
```json
{
  "prefix": "sa",
  "count": 2,
  "resourceGroup": "rg01",
  "type": "Premium_LRS"
}
```

## <a name="next-steps"></a>次のステップ
* マネージ アプリケーションの概要については、「[Azure マネージ アプリケーションの概要](managed-application-overview.md)」を参照してください。
* UI 定義の作成の概要については、「[CreateUiDefinition の基本概念](managed-application-createuidefinition-overview.md)」を参照してください。
* UI 要素の共通プロパティの説明については、「[CreateUiDefinition の要素](managed-application-createuidefinition-elements.md)」を参照してください。

