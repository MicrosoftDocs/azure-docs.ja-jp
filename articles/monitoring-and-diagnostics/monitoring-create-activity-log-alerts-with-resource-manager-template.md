---
title: "Resource Manager テンプレートでのアクティビティ ログ アラートの作成 | Microsoft Docs"
description: "Azure リソースの作成時に通知を受け取ります。"
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
ms.date: 07/06/2017
ms.author: ancav
ms.openlocfilehash: 92076c7fe1f867919b7e02abf79cf0fb74fb7eb4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-activity-log-alert-with-a-resource-manager-template"></a>Resource Manager テンプレートでのアクティビティ ログ アラートの作成
この記事では、[Azure Resource Manager テンプレート](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authoring-templates)を使用してアクティビティ ログ アラートを構成する方法について説明します。 テンプレートを使用すると、自動デプロイ プロセスの一環として、特定のアクティビティ ログ イベント条件に基づいてアクティブ化する多数のアラートを簡単に設定できます。

基本的な手順は次のとおりです。

1. アクティビティ ログ アラートの作成方法が記述された JSON ファイルとしてテンプレートを作成します。

2. [任意のデプロイ方法](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)を使用してテンプレートをデプロイします。

## <a name="resource-manager-template-for-an-activity-log-alert"></a>アクティビティ ログ アラート用の Resource Manager テンプレート
Resource Manager テンプレートを使用してアクティビティ ログ アラートを作成するには、`microsoft.insights/activityLogAlerts` 型のリソースを作成します。 次に、関連するすべてのプロパティを入力します。 アクティビティ ログ アラートを作成するテンプレートを以下に示します。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "activityLogAlertName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Activity log alert."
      }
    },
    "activityLogAlertEnabled": {
      "type": "bool",
      "defaultValue": true,
      "metadata": {
        "description": "Indicates whether or not the alert is enabled."
      }
    },
    "actionGroupResourceId": {
      "type": "string",
      "metadata": {
        "description": "Resource Id for the Action group."
      }
    }
  },
  "resources": [   
    {
      "type": "Microsoft.Insights/activityLogAlerts",
      "apiVersion": "2017-04-01",
      "name": "[parameters('activityLogAlertName')]",      
      "location": "Global",
      "properties": {
        "enabled": "[parameters('activityLogAlertEnabled')]",
        "scopes": [
            "[subscription().id]"
        ],        
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "Administrative"
            },
            {
              "field": "operationName",
              "equals": "Microsoft.Resources/deployments/write"
            },
            {
              "field": "resourceType",
              "equals": "Microsoft.Resources/deployments"
            }
          ] 
        },
        "actions": {
          "actionGroups": 
          [
            {
              "actionGroupId": "[parameters('actionGroupResourceId')]"
            }
          ]
        }
      }
    }
  ]
}
```

アクティビティ ログ アラート テンプレートの例については、[Azure クイックスタート ギャラリー](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Insights)を参照してください。

## <a name="next-steps"></a>次のステップ
- [アラート](monitoring-overview-alerts.md)の詳細について学習します。
- [Resource Manager テンプレートを使用してアクション グループ](monitoring-create-action-group-with-resource-manager-template.md)を追加する方法について確認します。
- [アクティビティ ログ アラートを作成して、サブスクリプションで自動スケールのエンジン操作をすべて監視する](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)方法について確認します。
- [アクティビティ ログ アラートを作成して、サブスクリプションで失敗した自動スケールのスケールイン/スケールアウト操作をすべて監視する](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)方法について確認します。
