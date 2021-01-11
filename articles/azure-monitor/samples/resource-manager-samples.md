---
title: Azure Monitor 用の Resource Manager テンプレートのサンプル
description: Resource Manager テンプレートを使用して Azure Monitor の機能をデプロイし構成します
author: bwren
ms.author: bwren
services: azure-monitor
ms.topic: sample
ms.date: 05/18/2020
ms.subservice: ''
ms.openlocfilehash: ab869fc8577d4d1934be96404ded5a2051237bbf
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2020
ms.locfileid: "87922730"
---
# <a name="resource-manager-template-samples-for-azure-monitor"></a>Azure Monitor 用の Resource Manager テンプレートのサンプル

[Azure Resource Manager テンプレート](../../azure-resource-manager/templates/template-syntax.md)を使用すると、Azure Monitor を大規模にデプロイし、構成することができます。 以下の記事では、さまざまな Azure Monitor 機能向けのサンプル テンプレートを紹介しています。 これらのサンプルは特定の要件に合わせて変更可能であり、Resource Manager テンプレートの標準的なデプロイ方法によってデプロイできます。 

## <a name="deploying-the-sample-templates"></a>サンプル テンプレートのデプロイ
サンプルの基本的な使用手順は次のとおりです。

1. テンプレートをコピーし JSON ファイルとして保存します。
2. パラメーターを環境に合わせて変更し、JSON ファイルとして保存します。
4. [任意の Resource Manager テンプレートのデプロイ方法](../../azure-resource-manager/templates/deploy-powershell.md)に従って、テンプレートをデプロイします。 

たとえば、PowerShell または Azure CLI を使用してリソース グループにテンプレートとパラメーター ファイルをデプロイする場合は、次のコマンドを実行します。


```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName my-subscription
New-AzResourceGroupDeployment -Name AzureMonitorDeployment -ResourceGroupName my-resource-group -TemplateFile azure-monitor-deploy.json -TemplateParameterFile azure-monitor-deploy.parameters.json
```

```azurecli
az login
az deployment group create \
    --name AzureMonitorDeployment \
    --resource-group ResourceGroupofTargetResource \
    --template-file azure-monitor-deploy.json \
    --parameters azure-monitor-deploy.parameters.json
```

## <a name="list-of-sample-templates"></a>サンプル テンプレート一覧

- [エージェント](resource-manager-agent.md) - Log Analytics エージェントと診断拡張機能をデプロイして構成します。
- 警告
  - [ログ アラート ルール](resource-manager-alerts-log.md) - ログ クエリと Azure アクティビティ ログからのアラート。
  - [メトリック アラート ルール](resource-manager-alerts-metric.md) - さまざまなロジックを使用したメトリックからのアラート。
- [Application Insights](resource-manager-app-resource.md)
- [診断設定](resource-manager-diagnostic-settings.md) - 各種リソースからのログおよびメトリックを転送する診断設定を作成します。
- [ログ クエリ](resource-manager-log-queries.md) - ログ クエリを作成して Log Analytics ワークスペースに保存します。
- [Log Analytics ワークスペース](resource-manager-workspace.md) - Log Analytics ワークスペースを作成し、Log Analytics エージェントの各種データ ソースから成るコレクションを構成します。
- [ブック](resource-manager-workbooks.md) - ブックを作成します。
- [Azure Monitor for containers](resource-manager-container-insights.md) - Azure Monitor for containers にクラスターをオンボードします。
- [Azure Monitor for VMs](resource-manager-vminsights.md) - Azure Monitor for VMs に仮想マシンをオンボードします。



## <a name="next-steps"></a>次のステップ

- [Resource Manager テンプレート](../../azure-resource-manager/templates/overview.md)の詳細を確認します