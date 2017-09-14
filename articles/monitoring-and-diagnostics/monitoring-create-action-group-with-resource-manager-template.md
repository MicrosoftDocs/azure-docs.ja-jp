---
title: "Resource Manager を使用してアクション グループを作成する | Microsoft Docs"
description: "Azure Resource Manager テンプレートを使用してアクション グループを作成する方法を説明します。"
author: anirudhcavale
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: ancav
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: 76bf353cac13f1c2169380f8dd3c1e163d4f3f41
ms.contentlocale: ja-jp
ms.lasthandoff: 08/24/2017

---

# <a name="create-an-action-group-with-a-resource-manager-template"></a>Resource Manager テンプレートを使用したアクション グループの作成
この記事では、[Azure Resource Manager テンプレート](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)を使用して、アクション グループを構成する方法について説明します。 テンプレートを使用することで、特定の種類のアラートで再利用できるアクション グループを自動的に設定できます。 このようなアクション グループを使用することで、アラートがトリガーされたときに、すべての適切な関係者が通知を確実に受け取ることができます。

基本的な手順は次のとおりです。

1. アクション グループの作成方法が記述された JSON ファイルとしてテンプレートを作成します。

2. [任意のデプロイ方法](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)を使用してテンプレートをデプロイします。

まず、アクション グループ用の Resource Manager テンプレートを作成する方法を説明します。テンプレートにはアクション定義がハードコーディングされます。 次に、テンプレートのデプロイ時に入力パラメータとして webhook 構成情報を受け取るテンプレートを作成する方法を説明します。

## <a name="resource-manager-templates-for-an-action-group"></a>アクション グループの Resource Manager テンプレート

Resource Manager テンプレートを使用してアクション グループを作成するには、`Microsoft.Insights/actionGroups` 型のリソースを作成します。 次に、関連するすべてのプロパティを入力します。 次に、アクション グループを作成するサンプル テンプレートを 2 つ示します。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "actionGroupName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Action group."
      }
    },
    "actionGroupShortName": {
      "type": "string",
      "metadata": {
        "description": "Short name (maximum 12 characters) for the Action group."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Insights/actionGroups",
      "apiVersion": "2017-04-01",
      "name": "[parameters('actionGroupName')]",
      "location": "Global",
      "properties": {
        "groupShortName": "[parameters('actionGroupShortName')]",
        "enabled": true,
        "smsReceivers": [
          {
            "name": "contosoSMS",
            "countryCode": "1",
            "phoneNumber": "5555551212"
          },
          {
            "name": "contosoSMS2",
            "countryCode": "1",
            "phoneNumber": "5555552121"
          }
        ],
        "emailReceivers": [
          {
            "name": "contosoEmail",
            "emailAddress": "devops@contoso.com"
          },
          {
            "name": "contosoEmail2",
            "emailAddress": "devops2@contoso.com"
          }
        ],
        "webhookReceivers": [
          {
            "name": "contosoHook",
            "serviceUri": "http://requestb.in/1bq62iu1"
          },
          {
            "name": "contosoHook2",
            "serviceUri": "http://requestb.in/1bq62iu2"
          }
        ]
      }
    }
  ],
  "outputs":{
      "actionGroupId":{
          "type":"string",
          "value":"[resourceId('Microsoft.Insights/actionGroups',parameters('actionGroupName'))]"
      }
  }
}
```

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "actionGroupName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Action group."
      }
    },
    "actionGroupShortName": {
      "type": "string",
      "metadata": {
        "description": "Short name (maximum 12 characters) for the Action group."
      }
    },
    "webhookReceiverName": {
      "type": "string",
      "metadata": {
        "description": "Webhook receiver service URI."
      }
    },    
    "webhookServiceUri": {
      "type": "string",
      "metadata": {
        "description": "Webhook receiver service URI."
      }
    }    
  },
  "resources": [
    {
      "type": "Microsoft.Insights/actionGroups",
      "apiVersion": "2017-04-01",
      "name": "[parameters('actionGroupName')]",
      "location": "Global",
      "properties": {
        "groupShortName": "[parameters('actionGroupShortName')]",
        "enabled": true,
        "smsReceivers": [
        ],
        "emailReceivers": [
        ],
        "webhookReceivers": [
          {
            "name": "[parameters('webhookReceiverName')]",
            "serviceUri": "[parameters('webhookServiceUri')]"
          }
        ]
      }
    }
  ],
  "outputs":{
      "actionGroupResourceId":{
          "type":"string",
          "value":"[resourceId('Microsoft.Insights/actionGroups',parameters('actionGroupName'))]"
      }
  }
}
```


## <a name="next-steps"></a>次のステップ
* [アクション グループ](monitoring-action-groups.md)の詳細について学習します。
* [アラート](monitoring-overview-alerts.md)の詳細について学習します。
* [Resource Manager テンプレートを使用してアクション グループ](monitoring-create-activity-log-alerts-with-resource-manager-template.md)を追加する方法を学習します。

