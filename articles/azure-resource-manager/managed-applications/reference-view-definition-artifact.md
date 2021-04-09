---
title: 定義の成果物のリファレンスを表示する
description: Azure Managed Applications のビュー定義の成果物の例を提供します。 ファイル名は viewDefinition.json です。
ms.topic: conceptual
ms.author: lazinnat
author: lazinnat
ms.date: 07/11/2019
ms.openlocfilehash: 5173db54abef132a4a4d5d117881352ca37d6b23
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "75649427"
---
# <a name="reference-view-definition-artifact"></a>リファレンス: ビュー定義アーティファクト

この記事は、Azure Managed Applications の *viewDefinition.json* 成果物のリファレンスです。 ビュー構成の作成の詳細については、[ビュー定義の成果物](concepts-view-definition.md)に関する記事を参照してください。

## <a name="view-definition"></a>定義の表示

次の JSON は、Azure Managed Applications 用の *viewDefinition.json* ファイルの例を示しています。

```json
{
  "views": [{
    "kind": "Overview",
    "properties": {
      "header": "Welcome to your Demo Azure Managed Application",
      "description": "This Managed application with Custom Provider is for demo purposes only.",
      "commands": [{
          "displayName": "Ping Action",
          "path": "/customping",
          "icon": "LaunchCurrent"
      }]
    }
  },
  {
    "kind": "CustomResources",
    "properties": {
      "displayName": "Users",
      "version": "1.0.0.0",
      "resourceType": "users",
      "createUIDefinition": {
        "parameters": {
          "steps": [{
            "name": "add",
            "label": "Add user",
            "elements": [{
              "name": "name",
              "label": "User's Full Name",
              "type": "Microsoft.Common.TextBox",
              "defaultValue": "",
              "toolTip": "Provide a full user name.",
              "constraints": { "required": true }
            },
            {
              "name": "location",
              "label": "User's Location",
              "type": "Microsoft.Common.TextBox",
              "defaultValue": "",
              "toolTip": "Provide a Location.",
              "constraints": { "required": true }
            }]
          }],
          "outputs": {
            "name": "[steps('add').name]",
            "properties": {
              "FullName": "[steps('add').name]",
              "Location": "[steps('add').location]"
            }
          }
        }
      },
      "commands": [{
        "displayName": "Custom Context Action",
        "path": "users/contextAction",
        "icon": "Start"
      }],
      "columns": [
        { "key": "properties.FullName", "displayName": "Full Name" },
        { "key": "properties.Location", "displayName": "Location", "optional": true }
      ]
    }
  }]
}
```

## <a name="next-steps"></a>次のステップ

- [チュートリアル:カスタム アクションとリソースを備えたマネージド アプリケーションを作成する](tutorial-create-managed-app-with-custom-provider.md)
- [リファレンス: ユーザー インターフェイス要素の成果物](reference-createuidefinition-artifact.md)
- [リファレンス: デプロイ テンプレートの成果物](reference-main-template-artifact.md)