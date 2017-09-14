---
title: "Azure マネージ アプリケーションの PublicIpAddressCombo UI 要素 | Microsoft Docs"
description: "Azure マネージ アプリケーションの Microsoft.Network.PublicIpAddressCombo UI 要素について説明します。"
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
ms.openlocfilehash: 2eb773f5f0cf389fc39bc3a0f5fbf9ac726d1949
ms.contentlocale: ja-jp
ms.lasthandoff: 05/13/2017

---
# <a name="microsoftnetworkpublicipaddresscombo-ui-element"></a>Microsoft.Network.PublicIpAddressCombo UI 要素
新規または既存のパブリック IP アドレスを選択するコントロールのグループです。 この要素は、[Azure マネージ アプリケーションを作成する](managed-application-publishing.md)ときに使用します。

## <a name="ui-sample"></a>UI サンプル
![Microsoft.Network.PublicIpAddressCombo](./media/managed-application-elements/microsoft.network.publicipaddresscombo.png)

- ユーザーがパブリック IP アドレスとして [なし] を選択した場合、[ドメイン名ラベル] ボックスは表示されません。
- ユーザーが既存のパブリック IP アドレスを選択した場合、[ドメイン名ラベル] ボックスは無効になります。 その値は、選択した IP アドレスのドメイン名ラベルになります。
- ドメイン名のサフィックス (たとえば、westus.cloudapp.azure.com) は、選択した場所に基づいて自動的に更新されます。

## <a name="schema"></a>スキーマ
```json
{
  "name": "element1",
  "type": "Microsoft.Network.PublicIpAddressCombo",
  "label": {
    "publicIpAddress": "Public IP address",
    "domainNameLabel": "Domain name label"
  },
  "toolTip": {
    "publicIpAddress": "",
    "domainNameLabel": ""
  },
  "defaultValue": {
    "publicIpAddressName": "ip01",
    "domainNameLabel": "foobar"
  },
  "constraints": {
    "required": {
      "domainNameLabel": true
    }
  },
  "options": {
    "hideNone": false,
    "hideDomainNameLabel": false,
    "hideExisting": false
  },
  "visible": true
}
```

## <a name="remarks"></a>解説
- `constraints.required.domainNameLabel` が **true** に設定されている場合、新しいパブリック IP アドレスを作成するときに、ドメイン名ラベルを指定する必要があります。 ラベルがない既存のパブリック IP アドレスは選択できません。
- `options.hideNone` が **true** に設定されている場合、パブリック IP アドレスとして **[なし]** を選択するオプションは表示されません。 既定値は **false** です。
- `options.hideDomainNameLabel` が **true** に設定されている場合、ドメイン名ラベルのテキスト ボックスは表示されません。 既定値は **false** です。
- `options.hideExisting` が true である場合、既存のパブリック IP アドレスを選択することはできません。 既定値は **false** です。

## <a name="sample-output"></a>サンプル出力
ユーザーがパブリック IP アドレスを選択しなかった場合、出力は次のようになります。
```json
{
  "newOrExistingOrNone": "none"
}
```

ユーザーが新規または既存の IP アドレスを選択した場合、出力は次のようになります。
```json
{
  "name": "ip01",
  "resourceGroup": "rg01",
  "domainNameLabel": "foobar",
  "newOrExistingOrNone": "new"
}
```
- `options.hideNone` が指定されると、`newOrExistingOrNone` は常に **none** を返します。
- `options.hideDomainNameLabel` が指定されると、`domainNameLabel` は宣言されません。

## <a name="next-steps"></a>次のステップ
* マネージ アプリケーションの概要については、「[Azure マネージ アプリケーションの概要](managed-application-overview.md)」を参照してください。
* UI 定義の作成の概要については、「[CreateUiDefinition の基本概念](managed-application-createuidefinition-overview.md)」を参照してください。
* UI 要素の共通プロパティの説明については、「[CreateUiDefinition の要素](managed-application-createuidefinition-elements.md)」を参照してください。

