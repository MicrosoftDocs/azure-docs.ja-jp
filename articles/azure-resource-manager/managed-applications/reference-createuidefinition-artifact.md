---
title: CreateUiDefinition 成果物
description: Azure Managed Application の createUiDefinition 成果物を作成する方法について説明します。 このファイルの名前は createUiDefinition.json です。
ms.topic: conceptual
ms.author: lazinnat
author: lazinnat
ms.date: 07/11/2019
ms.openlocfilehash: 9be1cb02c419314a33b9487bf183ef3659b2b549
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "75649515"
---
# <a name="reference-user-interface-elements-artifact"></a>リファレンス: ユーザー インターフェイス要素の成果物

この記事は、Azure Managed Applications の *createUiDefinition.json* 成果物のリファレンスです。 ユーザーインターフェイス要素の作成の詳細については、[ユーザー インターフェイス要素の作成](create-uidefinition-elements.md)に関する記事を参照してください。

## <a name="user-interface-elements"></a>ユーザー インターフェイスの要素

次の JSON は、Azure Managed Applications 用の *createUiDefinition.json* ファイルの例を示しています。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {}
    ],
    "steps": [
      {
        "name": "applicationSettings",
        "label": "Application Settings",
        "subLabel": {
          "preValidation": "Configure your application settings",
          "postValidation": "Done"
        },
        "bladeTitle": "Application Settings",
        "elements": [
          {
            "name": "funcname",
            "type": "Microsoft.Common.TextBox",
            "label": "Name of the function to be created",
            "toolTip": "Name of the function to be created",
            "visible": true,
            "constraints": {
              "required": true
            }
          },
          {
            "name": "storagename",
            "type": "Microsoft.Common.TextBox",
            "label": "Name of the storage to be created",
            "toolTip": "Name of the storage to be created",
            "visible": true,
            "constraints": {
              "required": true
            }
          },
          {
            "name": "zipFileBlobUri",
            "type": "Microsoft.Common.TextBox",
            "defaultValue": "https://github.com/Azure/azure-quickstart-templates/tree/master/101-custom-rp-with-function/artifacts/functionzip/functionpackage.zip",
            "label": "The Uri to the uploaded function zip file",
            "toolTip": "The Uri to the uploaded function zip file",
            "visible": true
          }
        ]
      }
    ],
    "outputs": {
      "funcname": "[steps('applicationSettings').funcname]",
      "storageName": "[steps('applicationSettings').storagename]",
      "zipFileBlobUri": "[steps('applicationSettings').zipFileBlobUri]"
    }
  }
}
```

## <a name="next-steps"></a>次のステップ

- [チュートリアル:カスタム アクションとリソースを備えたマネージド アプリケーションを作成する](tutorial-create-managed-app-with-custom-provider.md)
- [リファレンス: デプロイ テンプレートの成果物](reference-main-template-artifact.md)
- [リファレンス: ビュー定義の成果物](reference-view-definition-artifact.md)