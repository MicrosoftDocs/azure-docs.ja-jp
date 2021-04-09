---
title: Network Watcher - Azure Resource Manager テンプレートを使用して NSG フロー ログを作成する
description: Azure Resource Manager テンプレートと PowerShell を使用して、NSG フロー ログを簡単に設定できます。
services: network-watcher
documentationcenter: na
author: damendo
manager: twooley
editor: ''
tags: azure-resource-manager
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.custom: fasttrack-edit
ms.openlocfilehash: 2cae673dd319e55670357ae468d21ff63e4f9c72
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104669442"
---
# <a name="configure-nsg-flow-logs-from-an-azure-resource-manager-template"></a>Azure Resource Manager テンプレートから NSG フロー ログを構成する

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Azure CLI](network-watcher-nsg-flow-logging-cli.md)
> - [REST API](network-watcher-nsg-flow-logging-rest.md)
> - [Azure Resource Manager](network-watcher-nsg-flow-logging-azure-resource-manager.md)


[Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) は、[インフラストラクチャをコードとして](/azure/devops/learn/what-is-infrastructure-as-code)管理する、強力な Azure ネイティブな方法です。

この記事では、Azure Resource Manager テンプレートと Azure PowerShell を使用して、プログラムで [NSG フロー ログ](./network-watcher-nsg-flow-logging-overview.md)を有効にする方法について説明します。 まず、NSG フロー ログ オブジェクトのプロパティの概要と、いくつかのサンプル テンプレートを提供します。 次に、ローカルの PowerShell インスタンスを使用してテンプレートをデプロイします。


## <a name="nsg-flow-logs-object"></a>NSG フロー ログ オブジェクト

すべてのパラメーターを持つ NSG フロー ログ オブジェクトを以下に示します。
プロパティの完全な概要については、[NSG フロー ログのテンプレート リファレンス](/azure/templates/microsoft.network/2019-11-01/networkwatchers/flowlogs#RetentionPolicyParameters)を参照してください。

```json
{
  "name": "string",
  "type": "Microsoft.Network/networkWatchers/flowLogs",
  "location": "string",
  "apiVersion": "2019-09-01",
  "properties": {
    "targetResourceId": "string",
    "storageId": "string",
    "enabled": "boolean",
    "flowAnalyticsConfiguration": {
      "networkWatcherFlowAnalyticsConfiguration": {
         "enabled": "boolean",
         "workspaceResourceId": "string",
          "trafficAnalyticsInterval": "integer"
        },
        "retentionPolicy": {
           "days": "integer",
           "enabled": "boolean"
         },
        "format": {
           "type": "string",
           "version": "integer"
         }
      }
    }
  }
```
Microsoft.Network/networkWatchers/flowLogs リソースを作成するには、上記の JSON をテンプレートのリソース セクションに追加します。


## <a name="creating-your-template"></a>テンプレートを作成する

Azure Resource Manager テンプレートを初めて使用する場合は、以下のリンクから詳細を確認できます。

* [Resource Manager テンプレートと Azure PowerShell を使用したリソースのデプロイ](../azure-resource-manager/templates/deploy-powershell.md#deploy-local-template-or-bicep-file)
* [チュートリアル:初めての Azure Resource Manager テンプレートを作成およびデプロイする](../azure-resource-manager/templates/template-tutorial-create-first-template.md?tabs=azure-powershell)


NSG フロー ログを設定するための完全なテンプレートの 2 つの例を次に示します。

**例 1**:最小パラメーターが渡された、上記の最も単純なバージョンです。 次のテンプレートを使用すると、ターゲット NSG の NSG フロー ログが有効になり、特定のストレージ アカウントに格納されます。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "apiProfile": "2019-09-01",
  "resources": [
 {
    "name": "NetworkWatcher_centraluseuap/Microsoft.NetworkDalanDemoPerimeterNSG",
    "type": "Microsoft.Network/networkWatchers/FlowLogs/",
    "location": "centraluseuap",
    "apiVersion": "2019-09-01",
    "properties": {
      "targetResourceId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/DalanDemo/providers/Microsoft.Network/networkSecurityGroups/PerimeterNSG",
      "storageId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/MyCanaryFlowLog/providers/Microsoft.Storage/storageAccounts/storagev2ira",
      "enabled": true,
      "flowAnalyticsConfiguration": {},
      "retentionPolicy": {},
      "format": {}
    }

  }
  ]
}
```

> [!NOTE]
> * リソースの名前の形式は、"親リソース_子リソース" となります。 ここで、親リソースはリージョンの Network Watcher インスタンス (形式:NetworkWatcher_RegionName。 例:NetworkWatcher_centraluseuap)
> * targetResourceId はターゲット NSG のリソース ID です
> * storageId は、コピー先のストレージ アカウントのリソース ID です

**例 2**:次のテンプレートでは、NSG フロー ログ (バージョン 2) を有効にし、5 日間保持することができます。 処理間隔が 10 分の Traffic Analytics を有効にします。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "apiProfile": "2019-09-01",
  "resources": [
    {
      "name": "NetworkWatcher_centraluseuap/Microsoft.NetworkDalanDemoPerimeterNSG",
      "type": "Microsoft.Network/networkWatchers/FlowLogs/",
      "location": "centraluseuap",
      "apiVersion": "2019-09-01",
      "properties": {
        "targetResourceId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/DalanDemo/providers/Microsoft.Network/networkSecurityGroups/PerimeterNSG",
        "storageId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/MyCanaryFlowLog/providers/Microsoft.Storage/storageAccounts/storagev2ira",
        "enabled": true,
        "flowAnalyticsConfiguration": {
          "networkWatcherFlowAnalyticsConfiguration": {
            "enabled": true,
            "workspaceResourceId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/defaultresourcegroup-wcus/providers/Microsoft.OperationalInsights/workspaces/1c4f42e5-3a02-4146-ac9b-3051d8501db0",
            "trafficAnalyticsInterval": 10
          }
        },
        "retentionPolicy": {
          "days": 5,
          "enabled": true
        },
        "format": {
          "type": "JSON",
          "version": 2
        }
      }
    }
  ]
}
```

## <a name="deploying-your-azure-resource-manager-template"></a>Azure Resource Manager テンプレートをデプロイする

このチュートリアルでは、既存のリソース グループと、フローのログ記録を有効にできる NSG があることを前提としています。
上記の例のテンプレートは、すべて `azuredeploy.json` としてローカルに保存できます。 プロパティ値を更新して、サブスクリプション内の有効なリソースを参照するようにします。

テンプレートをデプロイするには、PowerShell で次のコマンドを実行します。
```azurepowershell
$context = Get-AzSubscription -SubscriptionId <SubscriptionId>
Set-AzContext $context
New-AzResourceGroupDeployment -Name EnableFlowLog -ResourceGroupName NetworkWatcherRG `
    -TemplateFile "C:\MyTemplates\azuredeploy.json"
```

> [!NOTE]
> 上記のコマンドでは、NetworkWatcherRG リソース グループにリソースがデプロイされ、NSG を含むリソース グループにはデプロイされません


## <a name="verifying-your-deployment"></a>デプロイの確認

デプロイが成功したかどうかを確認するには、いくつかの方法があります。 PowerShell コンソールの "ProvisioningState" が "Succeeded" と表示されます。 また、変更内容を確認するには、[NSG フロー ログのポータル ページ](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs)を参照してください。 デプロイに問題がある場合は、「[Azure Resource Manager を使用した Azure へのデプロイで発生する一般的なエラーのトラブルシューティング](../azure-resource-manager/templates/common-deployment-errors.md)」を参照してください。

## <a name="deleting-your-resource"></a>リソースの削除
Azure では、"完全" デプロイ モードによってリソースを削除できます。 フロー ログ リソースを削除するには、削除するリソースを含めずに、完全モードでデプロイを指定します。 [完全デプロイ モード](../azure-resource-manager/templates/deployment-modes.md#complete-mode)の詳細をお読みください

## <a name="next-steps"></a>次のステップ

以下を使用して、NSG フロー データを視覚化する方法について学習します。
* [Microsoft Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)
* [オープン ソース ツール](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
* [Azure Traffic Analytics](./traffic-analytics.md)
