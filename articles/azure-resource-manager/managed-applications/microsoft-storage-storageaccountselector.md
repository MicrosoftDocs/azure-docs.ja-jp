---
title: StorageAccountSelector UI 要素
description: Azure Portal の Microsoft.Storage.StorageAccountSelector UI 要素について説明します。
author: tfitzmac
ms.topic: conceptual
ms.date: 06/28/2018
ms.author: tomfitz
ms.openlocfilehash: fe70c42387e9dedea8943b5dcce04a1f9ded5190
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87033295"
---
# <a name="microsoftstoragestorageaccountselector-ui-element"></a>Microsoft.Storage.StorageAccountSelector UI 要素

新規または既存のストレージ アカウントを選択するコントロールです。

## <a name="ui-sample"></a>UI サンプル

コントロールには既定値が表示されます。

![Microsoft.Storage.StorageAccountSelector](./media/managed-application-elements/microsoft-storage-storageaccountselector.png)

このコントロールを使用すると、新しいストレージ アカウントを作成するか、既存のストレージ アカウントを選択することができます。

![Microsoft.Storage.StorageAccountSelector new](./media/managed-application-elements/microsoft-storage-storageaccountselector-new.png)

## <a name="schema"></a>スキーマ

```json
{
  "name": "element1",
  "type": "Microsoft.Storage.StorageAccountSelector",
  "label": "Storage account",
  "toolTip": "",
  "defaultValue": {
    "name": "storageaccount01",
    "type": "Premium_LRS"
  },
  "constraints": {
    "allowedTypes": [],
    "excludedTypes": []
  },
  "options": {
    "hideExisting": false
  },
  "visible": true
}
```

## <a name="sample-output"></a>サンプル出力

```json
{
  "name": "storageaccount01",
  "resourceGroup": "rg01",
  "type": "Premium_LRS",
  "newOrExisting": "new"
}
```

## <a name="remarks"></a>解説

- 指定した場合、`defaultValue.name` は一意であることが自動的に検証されます。 ストレージ アカウント名が一意でない場合、ユーザーは別の名前を指定するか、既存のストレージ アカウントを選択する必要があります。
- `defaultValue.type` の既定値は **Premium_LRS** です。
- `constraints.allowedTypes` で指定されていない型はすべて非表示となり、`constraints.excludedTypes` で指定されていない型はすべて表示されます。 `constraints.allowedTypes` と `constraints.excludedTypes` は両方ともオプションとして使用できますが、同時に使用することはできません。
- `options.hideExisting` が **true** である場合、既存のストレージ アカウントは選択できません。 既定値は **false** です。

## <a name="next-steps"></a>次のステップ
* UI 定義の作成の概要については、「[CreateUiDefinition の基本概念](create-uidefinition-overview.md)」を参照してください。
* UI 要素の共通プロパティの説明については、「[CreateUiDefinition の要素](create-uidefinition-elements.md)」を参照してください。
