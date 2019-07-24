---
title: Azure リソースに対する RBAC の変更のアクティビティ ログを表示する | Microsoft Docs
description: 過去 90 日間の Azure リソースに対するロールベースのアクセス制御 (RBAC) の変更のアクティビティ ログを表示します。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 2bc68595-145e-4de3-8b71-3a21890d13d9
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/02/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b808654baded5bbe721866441a8d1115eff7bcaa
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2019
ms.locfileid: "59997905"
---
# <a name="view-activity-logs-for-rbac-changes-to-azure-resources"></a>Azure リソースに対する RBAC の変更のアクティビティ ログを表示する

監査やトラブルシューティングなどの目的で、Azure リソースに対するロールベースのアクセス制御 (RBAC) の変更に関する情報が必要になる場合があります。 サブスクリプション内のロール割り当てまたはロール定義を変更したときは常に、[Azure アクティビティ ログ](../azure-monitor/platform/activity-logs-overview.md)に変更が記録されます。 アクティビティ ログを確認すると、過去 90 日間の RBAC のすべての変更を確認できます。

## <a name="operations-that-are-logged"></a>ログに記録される操作

アクティビティ ログに記録される RBAC 関連の操作を次に示します。

- ロール割り当ての作成
- ロール割り当ての削除
- カスタムのロール定義の作成または更新
- カスタムのロール定義の削除

## <a name="azure-portal"></a>Azure ポータル

作業を開始する最も簡単な方法は、Azure Portal でアクティビティ ログを表示することです。 次のスクリーンショットでは、ロール割り当て操作とロール定義操作だけを表示するようにフィルター処理されたアクティビティ ログの例を示します。 また、ログを CSV ファイルとしてダウンロードするためのリンクも含まれています。

![Portal を使用したアクティビティ ログ - スクリーンショット](./media/change-history-report/activity-log-portal.png)

ポータルのアクティビティ ログには複数のフィルターがあります。 RBAC 関連のフィルターを次に示します。

|filter  |値  |
|---------|---------|
|イベント カテゴリ     | <ul><li>管理</li></ul>         |
|Operation     | <ul><li>ロール割り当ての作成</li> <li>ロール割り当ての削除</li> <li>カスタムのロール定義の作成または更新</li> <li>カスタムのロール定義の削除</li></ul>      |


アクティビティ ログについて詳しくは、「[アクティビティ ログでのイベントの表示](/azure/azure-resource-manager/resource-group-audit?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json)」をご覧ください。

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

Azure PowerShell を使用してアクティビティ ログを表示するには、[Get-AzLog](/powershell/module/Az.Monitor/Get-AzLog) コマンドを使用します。

このコマンドでは、過去 7 日間のサブスクリプションのロール割り当てのすべての変更が一覧表示されます。

```azurepowershell
Get-AzLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleAssignments/*'}
```

このコマンドでは、過去 7 日間のリソース グループのロール定義のすべての変更が一覧表示されます。

```azurepowershell
Get-AzLog -ResourceGroupName pharma-sales -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleDefinitions/*'}
```

このコマンドでは、過去 7 日間のサブスクリプションのロール割り当てとロール定義のすべての変更が一覧表示され、結果が一覧で表示されます。

```azurepowershell
Get-AzLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/role*'} | Format-List Caller,EventTimestamp,{$_.Authorization.Action},Properties
```

```Example
Caller                  : alain@example.com
EventTimestamp          : 4/20/2018 9:18:07 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          statusCode     : Created
                          serviceRequestId: 11111111-1111-1111-1111-111111111111

Caller                  : alain@example.com
EventTimestamp          : 4/20/2018 9:18:05 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          requestbody    : {"Id":"22222222-2222-2222-2222-222222222222","Properties":{"PrincipalId":"33333333-3333-3333-3333-333333333333","RoleDefinitionId":"/subscriptions/00000000-0000-0000-0000-000000000000/providers
                          /Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c","Scope":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"}}

```

## <a name="azure-cli"></a>Azure CLI

Azure CLI を使用してアクティビティ ログを表示するには、[az monitor activity-log list](/cli/azure/monitor/activity-log#az-monitor-activity-log-list) コマンドを使用します。

このコマンドでは、開始時刻以降のリソース グループのアクティビティ ログが一覧表示されます。

```azurecli
az monitor activity-log list --resource-group pharma-sales --start-time 2018-04-20T00:00:00Z
```

このコマンドでは、開始時刻以降の承認リソース プロバイダーのアクティビティ ログが一覧表示されます。

```azurecli
az monitor activity-log list --resource-provider "Microsoft.Authorization" --start-time 2018-04-20T00:00:00Z
```

## <a name="azure-monitor-logs"></a>Azure Monitor ログ

[Azure Monitor ログ](../log-analytics/log-analytics-overview.md)は、Azure のすべてのリソースの RBAC の変更の収集および分析に使用できる、もう 1 つのツールです。 Azure Monitor ログには、次のような利点があります。

- 複雑なクエリとロジックを記述する
- アラート、Power BI、およびその他のツールと統合する
- より長い保有期間でデータを保存する
- セキュリティ、仮想マシン、およびカスタムなど他のログを相互参照する

作業を開始する基本的な手順を次に示します。

1. [Log Analytics ワークスペースを作成します](../azure-monitor/learn/quick-create-workspace.md)。

1. ワークスペースの [Activity Log Analytics ソリューションを構成します](../azure-monitor/platform/collect-activity-logs.md#configuration)。

1. [アクティビティ ログを表示します](../azure-monitor/platform/collect-activity-logs.md#using-the-solution)。 Activity Log Analytics ソリューションの [概要] ページに簡単に移動するには、**[Log Analytics]** オプションをクリックします。

   ![ポータルの Azure Monitor ログのオプション](./media/change-history-report/azure-log-analytics-option.png)

1. 必要に応じて、[ログ検索](../log-analytics/log-analytics-log-search.md)ページまたは[高度な分析ポータル](../azure-monitor/log-query/get-started-portal.md)を使用してログのクエリを実行し、ログを確認します。 これら 2 つのオプションの詳細については、[ログ検索ページまたは高度な分析ポータル](../azure-monitor/log-query/portals.md)に関するページをご覧ください。

ターゲットのリソース プロバイダー別に編成された新しいロール割り当てを返すクエリを、次に示します。

```
AzureActivity
| where TimeGenerated > ago(60d) and OperationName startswith "Microsoft.Authorization/roleAssignments/write" and ActivityStatus == "Succeeded"
| parse ResourceId with * "/providers/" TargetResourceAuthProvider "/" *
| summarize count(), makeset(Caller) by TargetResourceAuthProvider
```

グラフに表示されるロール割り当ての変更を返すクエリを、次に示します。

```
AzureActivity
| where TimeGenerated > ago(60d) and OperationName startswith "Microsoft.Authorization/roleAssignments"
| summarize count() by bin(TimeGenerated, 1d), OperationName
| render timechart
```

![高度な分析ポータルを使用したアクティビティ ログ - スクリーンショット](./media/change-history-report/azure-log-analytics.png)

## <a name="next-steps"></a>次の手順
* [アクティビティ ログでのイベントの表示](/azure/azure-resource-manager/resource-group-audit?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json)
* [Azure アクティビティ ログでサブスクリプション アクティビティを監視する](/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)
