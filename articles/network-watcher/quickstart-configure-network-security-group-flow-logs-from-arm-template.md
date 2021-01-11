---
title: クイック スタート:Azure Resource Manager テンプレートを使用して NSG フロー ログを構成する
description: Azure Resource Manager テンプレートと Azure PowerShell を使用して、プログラムで NSG フロー ログを有効にする方法について説明します。
services: network-watcher
author: damendo
Customer intent: I need to enable the NSG Flow Logs using Azure Resource Manager Template
ms.service: network-watcher
ms.topic: quickstart
ms.date: 07/22/2020
ms.author: damendo
ms.custom: subject-armqs
ms.openlocfilehash: 7d8cb89b1187bb15e7b361e1b6b9505400c612b5
ms.sourcegitcommit: 25bb515efe62bfb8a8377293b56c3163f46122bf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2020
ms.locfileid: "87986319"
---
# <a name="quickstart-configure-nsg-flow-logs-from-arm-template"></a>クイック スタート:ARM テンプレートから NSG フロー ログを構成する

このクイックスタートでは、[Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/management/overview) テンプレート (ARM テンプレート) と Azure PowerShell を使用してプログラムから [NSG フロー ログ](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview)を有効にする方法について説明します。 

まず、NSG フロー ログ オブジェクトのプロパティの概要と、いくつかのサンプル テンプレートを提供します。 次に、ローカルの PowerShell インスタンスを使用してテンプレートをデプロイします。

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="nsg-flow-logs-object"></a>NSG フロー ログ オブジェクト

すべてのパラメーターを持つ NSG フロー ログ オブジェクトを以下に示します。
プロパティの完全な概要については、[NSG フロー ログのテンプレート リファレンス](https://docs.microsoft.com/azure/templates/microsoft.network/networkwatchers/flowlogs)を参照してください。

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

* [Resource Manager テンプレートと Azure PowerShell を使用したリソースのデプロイ](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-powershell#deploy-local-template)
* [チュートリアル:初めての Azure Resource Manager テンプレートを作成およびデプロイする](https://docs.microsoft.com/azure/azure-resource-manager/templates/template-tutorial-create-first-template?tabs=azure-powershell)

このクイックスタートで使用されるテンプレートは [Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/101-networkwatcher-flowlogs-create)からのものです。

以下に示した完全なテンプレートの例は、NSG フロー ログを設定するための最小限のパラメーターを渡す最も単純なバージョンです。 その他の例については、こちらの[リンク](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-azure-resource-manager)に移動してください。

**例**:次のテンプレートを使用すると、ターゲット NSG の NSG フロー ログが有効になり、特定のストレージ アカウントに格納されます。

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


## <a name="deploying-your-azure-resource-manager-template"></a>Azure Resource Manager テンプレートをデプロイする

このチュートリアルでは、既存のリソース グループと、フローのログ記録を有効にできる NSG があることを前提としています。
上記の例のテンプレートは、すべて `azuredeploy.json` としてローカルに保存できます。 プロパティ値を更新して、サブスクリプション内の有効なリソースを参照するようにします。

テンプレートをデプロイするには、PowerShell で次のコマンドを実行します。
```azurepowershell-interactive
$context = Get-AzSubscription -SubscriptionId 56acfbd6-vc72-43e9-831f-bcdb6f2c5505
Set-AzContext $context
New-AzResourceGroupDeployment -Name EnableFlowLog -ResourceGroupName NetworkWatcherRG `
    -TemplateFile "C:\MyTemplates\azuredeploy.json"
```

> [!NOTE]
> 上記のコマンドでは、NetworkWatcherRG リソース グループにリソースがデプロイされ、NSG を含むリソース グループにはデプロイされません


## <a name="validate-the-deployment"></a>デプロイの検証

デプロイが成功したかどうかを確認するには、いくつかの方法があります。 PowerShell コンソールの "ProvisioningState" が "Succeeded" と表示されます。 また、変更内容を確認するには、[NSG フロー ログのポータル ページ](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs)を参照してください。 デプロイに問題がある場合は、「[Azure Resource Manager を使用した Azure へのデプロイで発生する一般的なエラーのトラブルシューティング](https://docs.microsoft.com/azure/azure-resource-manager/templates/common-deployment-errors)」を参照してください。

## <a name="deleting-your-resource"></a>リソースの削除
Azure では、"完全" デプロイ モードによってリソースを削除できます。 フロー ログ リソースを削除するには、削除するリソースを含めずに、完全モードでデプロイを指定します。 [完全デプロイ モード](https://docs.microsoft.com/azure/azure-resource-manager/templates/deployment-modes#complete-mode)の詳細をお読みください

または、以下の手順に従って、NSG フロー ログを Azure portal から無効にすることもできます。
1. Azure portal にログインします。
2. ポータルの左上隅の **[すべてのサービス]** を選択します。 *[フィルター]* ボックスに「**Network Watcher**」と入力します。 検索結果に **[Network Watcher]** が表示されたら、それを選択します。
3. **[ログ]** の **[NSG フロー ログ]** を選択します。
4. NSG の一覧から、フロー ログを無効にする NSG を選択します。
5. **[フローのログ設定]** で、フロー ログの状態を **[オフ]** に設定します。
6. 下へスクロールし、 **[保存]** を選択します。

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、NSG フロー ログを有効にしました。 この後は、以下の記事を使用して、NSG フロー データを視覚化する方法について学習しましょう。 

* [Microsoft Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)
* [オープン ソース ツール](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
* [Azure Traffic Analytics](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)
