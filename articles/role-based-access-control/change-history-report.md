---
title: Azure RBAC の変更のアクティビティ ログを表示する
description: Azure のロールベースのアクセス制御 (Azure RBAC) の変更のアクティビティ ログを過去 90 日間分表示します。
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 03/01/2021
ms.author: rolyon
ms.custom: H1Hack27Feb2017, devx-track-azurecli
ms.openlocfilehash: d9b39bc9a2f00fe83cae0ff78c6346042967e8bf
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102042133"
---
# <a name="view-activity-logs-for-azure-rbac-changes"></a>Azure RBAC の変更のアクティビティ ログを表示する

監査やトラブルシューティングなどの目的で、Azure のロールベースのアクセス制御 (Azure RBAC) の変更に関する情報が必要になる場合があります。 サブスクリプション内のロール割り当てまたはロール定義が変更されると、常に [Azure アクティビティ ログ](../azure-monitor/essentials/platform-logs-overview.md)に変更が記録されます。 アクティビティ ログを表示して、過去 90 日間の Azure RBAC のすべての変更を確認できます。

## <a name="operations-that-are-logged"></a>ログに記録される操作

アクティビティ ログに記録される Azure RBAC 関連の操作を次に示します。

- ロール割り当ての作成
- ロール割り当ての削除
- カスタムのロール定義の作成または更新
- カスタムのロール定義の削除

## <a name="azure-portal"></a>Azure portal

作業を開始する最も簡単な方法は、Azure Portal でアクティビティ ログを表示することです。 次のスクリーンショットは、アクティビティ ログのロール割り当て操作の例を示しています。 ログを CSV ファイルとしてダウンロードするオプションも含まれています。

![Portal を使用したアクティビティ ログ - スクリーンショット](./media/change-history-report/activity-log-portal.png)

詳細を表示するには、エントリをクリックし、[概要] ウィンドウを開きます。 **[JSON]** タブをクリックし、詳細なログを取得します。

![ポータルを利用したアクティビティ ログ。[概要] ウィンドウが開いています - スクリーンショット](./media/change-history-report/activity-log-summary-portal.png)

ポータルのアクティビティ ログには複数のフィルターがあります。 Azure RBAC 関連のフィルターを次に示します。

| Assert | 値 |
| --------- | --------- |
| イベント カテゴリ | <ul><li>管理</li></ul> |
| 操作 | <ul><li>ロール割り当ての作成</li><li>ロール割り当ての削除</li><li>カスタムのロール定義の作成または更新</li><li>カスタムのロール定義の削除</li></ul> |

アクティビティ ログの詳細については、「[リソースのアクションを監査するアクティビティ ログの表示](../azure-resource-manager/management/view-activity-logs.md?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json)」を参照してください。


## <a name="interpret-a-log-entry"></a>ログ エントリの解釈

[JSON] タブ、Azure PowerShell、または Azure CLI からのログ出力にはたくさんの情報が含まれていることがあります。 ログ エントリの解釈を試みるとき、次のような主要なプロパティをご確認ください。 Azure PowerShell または Azure CLI を利用し、ログ出力をフィルター処理する方法については、次のセクションをご覧ください。

> [!div class="mx-tableFixed"]
> | プロパティ | 値の例 | 説明 |
> | --- | --- | --- |
> | authorization:action | Microsoft.Authorization/roleAssignments/write | ロール割り当ての作成 |
> |  | Microsoft.Authorization/roleAssignments/delete | ロール割り当ての削除 |
> |  | Microsoft.Authorization/roleDefinitions/write | ロールの定義の作成または更新 |
> |  | Microsoft.Authorization/roleDefinitions/delete | ロールの定義の削除 |
> | authorization:scope | /subscriptions/{subscriptionId}<br/>/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId} | アクションのスコープ |
> | caller | admin@example.com<br/>{objectId} | アクションを開始したユーザー |
> | eventTimestamp | 2021-03-01T22:07:41.126243Z | アクションが発生した時刻 |
> | status:value | Started<br/>成功<br/>失敗 | アクションの状態 |

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell を使用してアクティビティ ログを表示するには、[Get-AzLog](/powershell/module/Az.Monitor/Get-AzLog) コマンドを使用します。

このコマンドでは、過去 7 日間のサブスクリプションのロール割り当てのすべての変更が一覧表示されます。

```azurepowershell
Get-AzLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleAssignments/*'}
```

このコマンドでは、過去 7 日間のリソース グループのロール定義のすべての変更が一覧表示されます。

```azurepowershell
Get-AzLog -ResourceGroupName pharma-sales -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleDefinitions/*'}
```

### <a name="filter-log-output"></a>ログ出力のフィルター処理

ログ出力には大量の情報が含まれることがあります。 このコマンドでは、あるサブスクリプションに含まれるロールの割り当てとロールの定義のすべての変更が過去 7 日間分、一覧表示され、出力がフィルター処理されます。

```azurepowershell
Get-AzLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/role*'} | Format-List Caller,EventTimestamp,{$_.Authorization.Action},Properties
```

次からは、ロールの割り当ての作成時、ログ出力がフィルター処理された例を確認できます。

```azurepowershell
Caller                  : admin@example.com
EventTimestamp          : 3/1/2021 10:07:42 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          statusCode     : Created
                          serviceRequestId: {serviceRequestId}
                          eventCategory  : Administrative
                          entity         : /subscriptions/{subscriptionId}/resourceGroups/example-group/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}
                          message        : Microsoft.Authorization/roleAssignments/write
                          hierarchy      : {tenantId}/{subscriptionId}

Caller                  : admin@example.com
EventTimestamp          : 3/1/2021 10:07:41 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          requestbody    : {"Id":"{roleAssignmentId}","Properties":{"PrincipalId":"{principalId}","PrincipalType":"User","RoleDefinitionId":"/providers/Microsoft.Authorization/roleDefinitions/fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64","Scope":"/subscriptions/
                          {subscriptionId}/resourceGroups/example-group"}}
                          eventCategory  : Administrative
                          entity         : /subscriptions/{subscriptionId}/resourceGroups/example-group/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}
                          message        : Microsoft.Authorization/roleAssignments/write
                          hierarchy      : {tenantId}/{subscriptionId}

```

サービス プリンシパルを使用してロールの割り当てを作成する場合、Caller プロパティはサービス プリンシパル オブジェクト ID になります。 サービス プリンシパルに関する情報は、[Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal) で取得できます。

```Example
Caller                  : {objectId}
EventTimestamp          : 3/1/2021 9:43:08 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              : 
                          statusCode     : Created
                          serviceRequestId: {serviceRequestId}
                          eventCategory  : Administrative
```

## <a name="azure-cli"></a>Azure CLI

Azure CLI を使用してアクティビティ ログを表示するには、[az monitor activity-log list](/cli/azure/monitor/activity-log#az_monitor_activity_log_list) コマンドを使用します。

このコマンドでは、あるリソース グループの 3 月 1 日から 7 日間のアクティビティ ログが一覧表示されます。

```azurecli
az monitor activity-log list --resource-group example-group --start-time 2021-03-01 --offset 7d
```

このコマンドでは、Authorization リソース プロバイダーの 3 月 1 日から 7 日間のアクティビティ ログが一覧表示されます。

```azurecli
az monitor activity-log list --namespace "Microsoft.Authorization" --start-time 2021-03-01 --offset 7d
```

### <a name="filter-log-output"></a>ログ出力のフィルター処理

ログ出力には大量の情報が含まれることがあります。 このコマンドでは、あるサブスクリプションに含まれるロールの割り当てとロールの定義のすべての変更が 7 日間分、一覧表示され、出力がフィルター処理されます。

```azurecli
az monitor activity-log list --namespace "Microsoft.Authorization" --start-time 2021-03-01 --offset 7d --query '[].{authorization:authorization, caller:caller, eventTimestamp:eventTimestamp, properties:properties}'
```

次からは、ロールの割り当ての作成時、ログ出力がフィルター処理された例を確認できます。

```azurecli
[
 {
    "authorization": {
      "action": "Microsoft.Authorization/roleAssignments/write",
      "role": null,
      "scope": "/subscriptions/{subscriptionId}/resourceGroups/example-group/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}"
    },
    "caller": "admin@example.com",
    "eventTimestamp": "2021-03-01T22:07:42.456241+00:00",
    "properties": {
      "entity": "/subscriptions/{subscriptionId}/resourceGroups/example-group/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}",
      "eventCategory": "Administrative",
      "hierarchy": "{tenantId}/{subscriptionId}",
      "message": "Microsoft.Authorization/roleAssignments/write",
      "serviceRequestId": "{serviceRequestId}",
      "statusCode": "Created"
    }
  },
  {
    "authorization": {
      "action": "Microsoft.Authorization/roleAssignments/write",
      "role": null,
      "scope": "/subscriptions/{subscriptionId}/resourceGroups/example-group/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}"
    },
    "caller": "admin@example.com",
    "eventTimestamp": "2021-03-01T22:07:41.126243+00:00",
    "properties": {
      "entity": "/subscriptions/{subscriptionId}/resourceGroups/example-group/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}",
      "eventCategory": "Administrative",
      "hierarchy": "{tenantId}/{subscriptionId}",
      "message": "Microsoft.Authorization/roleAssignments/write",
      "requestbody": "{\"Id\":\"{roleAssignmentId}\",\"Properties\":{\"PrincipalId\":\"{principalId}\",\"PrincipalType\":\"User\",\"RoleDefinitionId\":\"/providers/Microsoft.Authorization/roleDefinitions/fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64\",\"Scope\":\"/subscriptions/{subscriptionId}/resourceGroups/example-group\"}}"
    }
  }
]
```

## <a name="azure-monitor-logs"></a>Azure Monitor ログ

[Azure Monitor ログ](../azure-monitor/logs/log-query-overview.md)は、Azure のすべてのリソースに対する Azure RBAC の変更の収集および分析に使用できる別のツールです。 Azure Monitor ログには、次のような利点があります。

- 複雑なクエリとロジックを記述する
- アラート、Power BI、およびその他のツールと統合する
- より長い保有期間でデータを保存する
- セキュリティ、仮想マシン、およびカスタムなど他のログを相互参照する

作業を開始する基本的な手順を次に示します。

1. [Log Analytics ワークスペースを作成します](../azure-monitor/logs/quick-create-workspace.md)。

1. ワークスペースの [Activity Log Analytics ソリューションを構成します](../azure-monitor/essentials/activity-log.md#activity-log-analytics-monitoring-solution)。

1. [アクティビティ ログを表示します](../azure-monitor/essentials/activity-log.md#activity-log-analytics-monitoring-solution)。 Activity Log Analytics ソリューションの [概要] ページに簡単に移動する方法は、 **[ログ]** オプションをクリックすることです。

   ![ポータルの Azure Monitor ログのオプション](./media/change-history-report/azure-log-analytics-option.png)

1. 必要に応じて、[Azure Monitor Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md) を使用して、ログのクエリと表示を行うことができます。 詳細については、「[Azure Monitor でログ クエリの使用を開始する](../azure-monitor/logs/get-started-queries.md)」を参照してください。

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
* [リソースのアクションを監視するアクティビティ ログの表示](../azure-resource-manager/management/view-activity-logs.md?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json)
* [Azure アクティビティ ログでサブスクリプション アクティビティを監視する](../azure-monitor/essentials/platform-logs-overview.md)