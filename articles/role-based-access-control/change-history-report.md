---
title: Azure RBAC の変更のアクティビティ ログを表示する
description: 過去 90 日間の Azure リソースに対する Azure のロールベースのアクセス制御 (Azure RBAC) の変更のアクティビティ ログを表示します。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 2bc68595-145e-4de3-8b71-3a21890d13d9
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/27/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 332d9a9ec28c4309fb1cf1d3e24d3cfd2d7d13d9
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87321974"
---
# <a name="view-activity-logs-for-azure-rbac-changes"></a>Azure RBAC の変更のアクティビティ ログを表示する

監査やトラブルシューティングなどの目的で、Azure のロールベースのアクセス制御 (Azure RBAC) の変更に関する情報が必要になる場合があります。 サブスクリプション内のロール割り当てまたはロール定義が変更されると、常に [Azure アクティビティ ログ](../azure-monitor/platform/platform-logs-overview.md)に変更が記録されます。 アクティビティ ログを表示して、過去 90 日間の Azure RBAC のすべての変更を確認できます。

## <a name="operations-that-are-logged"></a>ログに記録される操作

アクティビティ ログに記録される Azure RBAC 関連の操作を次に示します。

- ロール割り当ての作成
- ロール割り当ての削除
- カスタムのロール定義の作成または更新
- カスタムのロール定義の削除

## <a name="azure-portal"></a>Azure portal

作業を開始する最も簡単な方法は、Azure Portal でアクティビティ ログを表示することです。 次のスクリーンショットは、アクティビティ ログのロール割り当て操作の例を示しています。 ログを CSV ファイルとしてダウンロードするオプションも含まれています。

![Portal を使用したアクティビティ ログ - スクリーンショット](./media/change-history-report/activity-log-portal.png)

ポータルのアクティビティ ログには複数のフィルターがあります。 Azure RBAC 関連のフィルターを次に示します。

| Assert | 値 |
| --------- | --------- |
| イベント カテゴリ | <ul><li>管理</li></ul> |
| 操作 | <ul><li>ロール割り当ての作成</li><li>ロール割り当ての削除</li><li>カスタムのロール定義の作成または更新</li><li>カスタムのロール定義の削除</li></ul> |

アクティビティ ログの詳細については、「[リソースのアクションを監査するアクティビティ ログの表示](/azure/azure-resource-manager/resource-group-audit?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json)」を参照してください。

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
EventTimestamp          : 2/27/2020 9:18:07 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          statusCode     : Created
                          serviceRequestId: 11111111-1111-1111-1111-111111111111
                          eventCategory  : Administrative

Caller                  : alain@example.com
EventTimestamp          : 2/27/2020 9:18:05 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          requestbody    : {"Id":"22222222-2222-2222-2222-222222222222","Properties":{"PrincipalId":"33333333-3333-3333-3333-333333333333","RoleDefinitionId":"/subscriptions/00000000-0000-0000-0000-000000000000/providers
                          /Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c","Scope":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"}}

```

サービス プリンシパルを使用してロールの割り当てを作成する場合、Caller プロパティはオブジェクト ID になります。 サービス プリンシパルに関する情報は、[Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal) で取得できます。

```Example
Caller                  : 44444444-4444-4444-4444-444444444444
EventTimestamp          : 6/4/2020 9:43:08 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              : 
                          statusCode     : Created
                          serviceRequestId: 55555555-5555-5555-5555-555555555555
                          category       : Administrative
```

## <a name="azure-cli"></a>Azure CLI

Azure CLI を使用してアクティビティ ログを表示するには、[az monitor activity-log list](/cli/azure/monitor/activity-log#az-monitor-activity-log-list) コマンドを使用します。

次のコマンドでは、リソース グループの 2 月 27 日から 7 日間のアクティビティ ログが一覧表示されます。

```azurecli
az monitor activity-log list --resource-group pharma-sales --start-time 2020-02-27 --offset 7d
```

次のコマンドでは、Authorization リソース プロバイダーの 2 月 27 日から 7 日間のアクティビティ ログが一覧表示されます。

```azurecli
az monitor activity-log list --namespace "Microsoft.Authorization" --start-time 2020-02-27 --offset 7d
```

## <a name="azure-monitor-logs"></a>Azure Monitor ログ

[Azure Monitor ログ](../log-analytics/log-analytics-overview.md)は、Azure のすべてのリソースに対する Azure RBAC の変更の収集および分析に使用できる別のツールです。 Azure Monitor ログには、次のような利点があります。

- 複雑なクエリとロジックを記述する
- アラート、Power BI、およびその他のツールと統合する
- より長い保有期間でデータを保存する
- セキュリティ、仮想マシン、およびカスタムなど他のログを相互参照する

作業を開始する基本的な手順を次に示します。

1. [Log Analytics ワークスペースを作成します](../azure-monitor/learn/quick-create-workspace.md)。

1. ワークスペースの [Activity Log Analytics ソリューションを構成します](../azure-monitor/platform/activity-log-collect.md#activity-logs-analytics-monitoring-solution)。

1. [アクティビティ ログを表示します](../azure-monitor/platform/activity-log-collect.md#activity-logs-analytics-monitoring-solution)。 Activity Log Analytics ソリューションの [概要] ページに簡単に移動する方法は、 **[ログ]** オプションをクリックすることです。

   ![ポータルの Azure Monitor ログのオプション](./media/change-history-report/azure-log-analytics-option.png)

1. 必要に応じて、[Azure Monitor Log Analytics](../azure-monitor/log-query/get-started-portal.md) を使用して、ログのクエリと表示を行うことができます。 詳細については、「[Azure Monitor でログ クエリの使用を開始する](../azure-monitor/log-query/get-started-queries.md)」を参照してください。

ターゲットのリソース プロバイダー別に編成された新しいロール割り当てを返すクエリを、次に示します。

```Kusto
AzureActivity
| where TimeGenerated > ago(60d) and Authorization contains "Microsoft.Authorization/roleAssignments/write" and ActivityStatus == "Succeeded"
| parse ResourceId with * "/providers/" TargetResourceAuthProvider "/" *
| summarize count(), makeset(Caller) by TargetResourceAuthProvider
```

グラフに表示されるロール割り当ての変更を返すクエリを、次に示します。

```Kusto
AzureActivity
| where TimeGenerated > ago(60d) and Authorization contains "Microsoft.Authorization/roleAssignments"
| summarize count() by bin(TimeGenerated, 1d), OperationName
| render timechart
```

![高度な分析ポータルを使用したアクティビティ ログ - スクリーンショット](./media/change-history-report/azure-log-analytics.png)

## <a name="next-steps"></a>次のステップ
* [アクティビティ ログでのイベントの表示](/azure/azure-resource-manager/resource-group-audit?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json)
* [Azure アクティビティ ログでサブスクリプション アクティビティを監視する](/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)
