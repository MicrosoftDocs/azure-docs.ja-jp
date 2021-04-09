---
title: 'クイックスタート: Azure Resource Manager テンプレート (ARM テンプレート) を使用してネットワーク セキュリティ グループのフロー ログを構成する'
description: Azure Resource Manager テンプレート (ARM テンプレート) と Azure PowerShell を使用してプログラムでネットワーク セキュリティ グループ (NSG) のフロー ログを有効にする方法について学習します。
services: network-watcher
author: damendo
Customer intent: I need to enable the network security group flow logs by using an Azure Resource Manager template.
ms.service: network-watcher
ms.topic: quickstart
ms.date: 01/07/2021
ms.author: damendo
ms.custom: subject-armqs
ms.openlocfilehash: bc075e5074fe39ad38e45235af932b40fef78fce
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102521868"
---
# <a name="quickstart-configure-network-security-group-flow-logs-by-using-an-arm-template"></a>クイックスタート: ARM テンプレートを使用してネットワーク セキュリティ グループのフロー ログを構成する

このクイックスタートでは、[Azure Resource Manager](../azure-resource-manager/management/overview.md) テンプレート (ARM テンプレート) と Azure PowerShell を使用して[ネットワーク セキュリティ グループ (NSG) のフロー ログ](network-watcher-nsg-flow-logging-overview.md)を有効にする方法について学習します。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

まず、NSG フロー ログ オブジェクトのプロパティの概要を説明します。 サンプル テンプレートが用意されています。 このテンプレートを、ローカルの Azure PowerShell インスタンスを使用してデプロイします。

環境が前提条件を満たしていて、ARM テンプレートの使用に慣れている場合は、 **[Azure へのデプロイ]** ボタンを選択します。 テンプレートが Azure portal で開きます。

[![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-networkwatcher-flowLogs-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="review-the-template"></a>テンプレートを確認する

このクイックスタートで使用するテンプレートは、[Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/101-networkwatcher-flowlogs-create)からのものです。

:::code language="json" source="~/quickstart-templates/101-networkwatcher-flowlogs-create/azuredeploy.json":::

テンプレートでは、次のリソースが定義されています。

- [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
- [Microsoft.Resources/deployments](/azure/templates/microsoft.resources/deployments)

## <a name="nsg-flow-logs-object"></a>NSG フロー ログ オブジェクト

次のコードは、NSG フロー ログ オブジェクトとそのパラメーターを示しています。 `Microsoft.Network/networkWatchers/flowLogs` リソースを作成するには、次のコードをテンプレートの resources セクションに追加します。

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

NSG フロー ログ オブジェクトのプロパティの完全な概要については、「[Microsoft.Network networkWatchers/flowLogs](/azure/templates/microsoft.network/networkwatchers/flowlogs)」を参照してください。

## <a name="create-your-template"></a>テンプレートを作成する

ARM テンプレートを初めて使用する場合は、ARM テンプレートの詳細について、次の記事を参照してください。

- [ARM テンプレートと Azure PowerShell を使用したリソースのデプロイ](../azure-resource-manager/templates/deploy-powershell.md#deploy-local-template-or-bicep-file)
- [チュートリアル:初めての ARM テンプレートを作成してデプロイする](../azure-resource-manager/templates/template-tutorial-create-first-template.md)

完全なテンプレートの例を次に示します。 これは、テンプレートの最も単純なバージョンでもあります。 この例には、NSG フロー ログを設定するために渡される最小限のパラメーターが含まれています。 その他の例については、概要記事の「[Azure Resource Manager テンプレートから NSG フロー ログを構成する](network-watcher-nsg-flow-logging-azure-resource-manager.md)」を参照してください。

### <a name="example"></a>例

次のテンプレートでは、NSG のフロー ログを有効にした後、ログを特定のストレージ アカウントに保存します。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "apiProfile": "2019-09-01",
  "resources": [
    {
      "name": "NetworkWatcher_centraluseuap/Microsoft.NetworkDalanDemoPerimeterNSG",
      "type": "Microsoft.Network/networkWatchers/FlowLogs/",
      "location": "centraluseuap",
      "apiVersion": "2019-09-01",
      "properties": {
        "targetResourceId": "/subscriptions/<subscription Id>/resourceGroups/DalanDemo/providers/Microsoft.Network/networkSecurityGroups/PerimeterNSG",
        "storageId": "/subscriptions/<subscription Id>/resourceGroups/MyCanaryFlowLog/providers/Microsoft.Storage/storageAccounts/storagev2ira",
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
> - リソース名には、_ParentResource_ChildResource_ の形式を使用します。 この例では、親リソースはリージョンの Azure Network Watcher インスタンスです。
>    - **Format**: NetworkWatcher_RegionName
>    - **例**:NetworkWatcher_centraluseuap
> - `targetResourceId` は、ターゲット NSG のリソース ID です。
> - `storageId` は、コピー先のストレージ アカウントのリソース ID です。

## <a name="deploy-the-template"></a>テンプレートのデプロイ

このチュートリアルでは、既存のリソース グループと、フローのログ記録を有効にできる NSG があることを前提としています。

この記事に記載されているサンプル テンプレートは、すべて *azuredeploy.json* としてローカルに保存できます。 プロパティ値を更新して、お使いのサブスクリプション内の有効なリソースを参照するようにします。

テンプレートをデプロイするには、Azure PowerShell で次のコマンドを実行します。

```azurepowershell-interactive
$context = Get-AzSubscription -SubscriptionId <subscription Id>
Set-AzContext $context
New-AzResourceGroupDeployment -Name EnableFlowLog -ResourceGroupName NetworkWatcherRG `
    -TemplateFile "C:\MyTemplates\azuredeploy.json"
```

> [!NOTE]
> これらのコマンドを使用すると、NSG を含むリソース グループではなく、サンプルの NetworkWatcherRG リソース グループにリソースがデプロイされます。

## <a name="validate-the-deployment"></a>デプロイの検証

デプロイが成功したかどうかを確認するには、次の 2 つのオプションがあります。

- PowerShell コンソールに `ProvisioningState` が `Succeeded` として表示されます。
- [NSG フロー ログのポータル ページ](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs)に移動して、変更内容を確認します。

デプロイに問題がある場合は、「[Azure Resource Manager を使用した Azure へのデプロイで発生する一般的なエラーのトラブルシューティング](../azure-resource-manager/templates/common-deployment-errors.md)」を参照してください。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

完全デプロイ モードを使用して Azure リソースを削除することができます。 フロー ログ リソースを削除するには、削除するリソースを含めずに、完全モードでデプロイを指定します。 [完全デプロイ モード](../azure-resource-manager/templates/deployment-modes.md#complete-mode)の詳細をお読みください。

Azure portal で NSG フロー ログを無効にすることもできます。

1. Azure portal にサインインします。
1. **[すべてのサービス]** を選択します。 **[フィルター]** ボックスに、「**network watcher**」と入力します。 検索結果で、 **[Network Watcher]** を選択します。
1. **[ログ]** の **[NSG フロー ログ]** を選択します。
1. NSG の一覧で、フロー ログを無効にする NSG を選択します。
1. **[フローのログ設定]** で、 **[オフ]** を選択します。
1. **[保存]** を選択します。

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、ARM テンプレートを使用して NSG フロー ログを有効にする方法について学習しました。 次は、以下のいずれかのオプションを使用して NSG フロー データを視覚化する方法について学習しましょう。

- [Microsoft Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)
- [オープンソース ツール](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
- [Azure Traffic Analytics](traffic-analytics.md)
