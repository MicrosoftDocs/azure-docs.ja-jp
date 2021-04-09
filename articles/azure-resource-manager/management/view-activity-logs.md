---
title: リソースを監視する Azure アクティビティ ログの表示
description: アクティビティ ログを使用してユーザーの操作やエラーを確認します。 Azure portal、PowerShell、Azure CLI、REST を表示します。
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: 14015e9b2792515e6818af551b8bd9f54c686bee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91371594"
---
# <a name="view-activity-logs-to-monitor-actions-on-resources"></a>リソースのアクションを監視するアクティビティ ログの表示

アクティビティ ログを使用すると、次の内容を判断することができます。

* サブスクリプション内のリソースで行われた操作
* 操作を開始したユーザー
* 操作が発生した時間
* 操作の状態
* 操作を調査するために役立つ可能性のあるその他のプロパティの値

アクティビティ ログには、リソースのすべての書き込み操作 (PUT、POST、DELETE) が含まれます。 読み取り操作 (GET) は含まれません。 リソース アクションの一覧については、「[Azure リソース プロバイダーの操作](../../role-based-access-control/resource-provider-operations.md)」を参照してください。 アクティビティ ログを使用して、トラブルシューティングを行うときにエラーを探したり、組織のユーザーがどのようにリソースを変更したかを監視したりできます。

アクティビティ ログは 90 日間保持されます。 開始日が過去 90 日以内である限り、任意の日付の範囲に対するクエリを実行できます。

ポータル、PowerShell、Azure CLI、Insights REST API、または [Insights .NET Library](https://www.nuget.org/packages/Microsoft.Azure.Insights/)を利用し、アクティビティ ログから情報を取得できます。

## <a name="azure-portal"></a>Azure portal

ポータルからアクティビティ ログを表示するには、次の手順を実行します。

1. Azure portal メニューで **[モニター]** を選択するか、または任意のページから **[モニター]** を検索して選択します。

    ![監視を選択する](./media/view-activity-logs/select-monitor-from-menu.png)

1. **[アクティビティ ログ]** を選択します。

    ![[アクティビティ ログ] を選択する](./media/view-activity-logs/select-activity-log.png)

1. 最近の操作の概要が表示されます。 操作には、既定のフィルターセットが適用されます。 概要に関する情報には、アクションを誰がいつ開始したかが含まれます。

    ![最近の操作の概要を表示する](./media/view-activity-logs/audit-summary.png)

1. 定義済みのフィルター セットをすばやく実行するには、 **[クイック分析情報]** を選択します。

    ![クイック分析情報の選択](./media/view-activity-logs/select-quick-insights.png)

1. いずれかのオプションを選択します。 たとえば、 **[失敗したデプロイ]** を選択すると、デプロイのエラーが表示されます。

    ![失敗したデプロイの選択](./media/view-activity-logs/select-failed-deployments.png)

1. 過去 24 時間のデプロイ エラーに焦点を合わせるため、フィルターが変更されています。 フィルターに一致する操作のみが表示されます。

    ![フィルターの表示](./media/view-activity-logs/view-filters.png)

1. 特定の操作に焦点を合わせるには、フィルターを変更するか、新しいフィルターを適用します。 たとえば、次の図は、 **[Timespan]\(期間\)** の新しい値と、 **[リソースの種類]** がストレージ アカウントに設定されていることを示しています。

    ![フィルター オプションを設定する](./media/view-activity-logs/set-filter.png)

1. 後で再度クエリを実行する必要がある場合は、 **[現在のフィルターをピン留めする]** を選択します。

    ![フィルターをピン留めする](./media/view-activity-logs/pin-filters.png)

1. フィルターに名前を付けます。

    ![フィルターに名前を付ける](./media/view-activity-logs/name-filters.png)

1. フィルターはダッシュ ボードで提供されます。 Azure portal のメニューで **[ダッシュボード]** を選択します。

    ![ダッシュ ボードでフィルターを表示する](./media/view-activity-logs/activity-log-on-dashboard.png)

1. ポータルで、リソースへの変更を表示できます。 [監視] で既定のビューに戻り、リソースの変更が関係した操作を選択します。

    ![操作の選択](./media/view-activity-logs/select-operation.png)

1. **[変更履歴 (プレビュー)]** を選択し、利用可能ないずれかの操作を選択します。

    ![変更履歴の選択](./media/view-activity-logs/select-change-history.png)

1. リソースでの変更が表示されます。

    ![変更の表示](./media/view-activity-logs/show-changes.png)

変更履歴の詳細については、「[リソースの変更の取得](../../governance/resource-graph/how-to/get-resource-changes.md)」を参照してください。

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

ログ エントリを取得するには、**Get-AzLog** コマンドを実行します。 パラメーターを追加し、エントリの一覧を絞り込むことができます。 開始時間と終了時間を指定しない場合は、過去 7 日間のエントリが返されます。

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup
```

次の例は、アクティビティ ログを使用して、指定した時間に行われた操作を調査する方法を示しています。 開始日と終了日は、日付の形式で指定されます。

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -StartTime 2019-05-05T06:00 -EndTime 2019-05-09T06:00
```

または、日付関数を使用して、最後の 14 日など、日付の範囲を指定することができます。

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14)
```

特定のユーザーが実行したアクションを検索することができます。

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14) -Caller someone@contoso.com
```

フィルターを使って、失敗した操作を抽出できます。

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup -Status Failed
```

1 つのエラーに焦点を当てるには、そのエントリのステータス メッセージを確認します。

```azurepowershell-interactive
(Get-AzLog -ResourceGroup ExampleGroup -Status Failed).Properties.Content.statusMessage | ConvertFrom-Json
```

特定の値を選択し、返されるデータを制限することができます。

```azurepowershell-interactive
Get-AzLog -ResourceGroupName ExampleGroup | Format-table EventTimeStamp, Caller, @{n='Operation'; e={$_.OperationName.value}}, @{n='Status'; e={$_.Status.value}}, @{n='SubStatus'; e={$_.SubStatus.LocalizedValue}}
```

指定した開始時刻によっては、前のコマンドを実行したときに、そのリソース グループの操作が長い一覧で返されることがあります。 検索基準を指定すると、探しものの結果を絞り込むことができます。 たとえば、操作の種類でフィルター処理することができます。

```azurepowershell-interactive
Get-AzLog -ResourceGroup ExampleGroup | Where-Object {$_.OperationName.value -eq "Microsoft.Resources/deployments/write"}
```

Resource Graph を使用すると、リソースの変更履歴を確認できます。 詳細については、「[リソースの変更の取得](../../governance/resource-graph/how-to/get-resource-changes.md)」を参照してください。

## <a name="azure-cli"></a>Azure CLI

ログ エントリを取得するには、期間を示すオフセットを指定して、[az monitor activity-log list](/cli/azure/monitor/activity-log#az-monitor-activity-log-list) コマンドを実行します。

```azurecli-interactive
az monitor activity-log list --resource-group ExampleGroup --offset 7d
```

次の例は、アクティビティ ログを使用して、指定した時間に行われた操作を調査する方法を示しています。 開始日と終了日は、日付の形式で指定されます。

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --start-time 2019-05-01 --end-time 2019-05-15
```

存在しなくなったリソース グループであっても、特定のユーザーが行ったアクションを検索できます。

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --caller someone@contoso.com --offset 5d
```

フィルターを使って、失敗した操作を抽出できます。

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --status Failed --offset 1d
```

1 つのエラーに焦点を当てるには、そのエントリのステータス メッセージを確認します。

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --status Failed --offset 1d --query [].properties.statusMessage
```

特定の値を選択し、返されるデータを制限することができます。

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --offset 1d --query '[].{Operation: operationName.value, Status: status.value, SubStatus: subStatus.localizedValue}'
```

指定した開始時刻によっては、前のコマンドを実行したときに、そのリソース グループの操作が長い一覧で返されることがあります。 検索基準を指定すると、探しものの結果を絞り込むことができます。 たとえば、操作の種類でフィルター処理することができます。

```azurecli-interactive
az monitor activity-log list -g ExampleGroup --offset 1d --query "[?operationName.value=='Microsoft.Storage/storageAccounts/write']"
```

Resource Graph を使用すると、リソースの変更履歴を確認できます。 詳細については、「[リソースの変更の取得](../../governance/resource-graph/how-to/get-resource-changes.md)」を参照してください。

## <a name="rest-api"></a>REST API

アクティビティ ログを利用するための REST 操作は [Insights REST API](/rest/api/monitor/)に含まれています。 アクティビティ ログのイベントを取得するには、 [サブスクリプション内の管理イベントの一覧表示](/rest/api/monitor/activitylogs)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

* Azure アクティビティ ログは、サブスクリプション内のアクションに関してさらに洞察を得るために、Power BI で使用できます。 [Power BI などでの Azure アクティビティ ログの表示と分析](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/)に関する記事をご覧ください。
* セキュリティ ポリシーを設定する方法については、[Azure ロールベースのアクセス制御 (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md) に関する記事を参照してください。
* インフラストラクチャ レイヤーからアプリケーションのデプロイまで、アプリケーションに対する変更の詳細については、[Azure Monitor でのアプリケーション変更分析の使用](../../azure-monitor/app/change-analysis.md)に関するページをご覧ください。
* デプロイ操作を表示するコマンドについては、[デプロイ操作の表示](../templates/deployment-history.md)に関するページをご覧ください。
* すべてのユーザーのリソースに対する削除を回避する方法については、「 [Azure Resource Manager によるリソースのロック](lock-resources.md)」をご覧ください。
* 各 Microsoft Azure Resource Manager プロバイダーで使用できる操作の一覧については、「[Azure リソース プロバイダーの操作](../../role-based-access-control/resource-provider-operations.md)」をご覧ください
