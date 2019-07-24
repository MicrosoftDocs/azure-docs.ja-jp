---
title: リソースを監視する Azure アクティビティ ログの表示 | Microsoft Docs
description: アクティビティ ログを使用してユーザーの操作やエラーを確認します。 Azure Portal、PowerShell、Azure CLI、および REST を表示します。
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
ms.assetid: fcdb3125-13ce-4c3b-9087-f514c5e41e73
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: tomfitz
ms.openlocfilehash: 8348099d778a9ec65e907bb3d21ae995041b9fb6
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59786101"
---
# <a name="view-activity-logs-to-audit-actions-on-resources"></a>リソースのアクションを監査するアクティビティ ログの表示

アクティビティ ログを使用すると、次の内容を判断することができます。

* サブスクリプション内のリソースで行われた操作
* 操作を開始したユーザー
* 操作が発生した時間
* 操作の状態
* 操作を調査するために役立つ可能性のあるその他のプロパティの値

アクティビティ ログには、リソースで実行されたすべての書き込み操作 (PUT、POST、DELETE) が含まれます。 読み取り操作 (GET) は含まれません。 リソース アクションの一覧については、「[Azure Resource Manager のリソース プロバイダー操作](../role-based-access-control/resource-provider-operations.md)」を参照してください。 監査ログを使用して、トラブルシューティングを行うときにエラーを探したり、組織のユーザーがどのようにリソースを変更したかを監視したりできます。

アクティビティ ログは 90 日間保持されます。 開始日が過去 90 日以内である限り、任意の日付の範囲に対するクエリを実行できます。

ポータル、PowerShell、Azure CLI、Insights REST API、または [Insights .NET Library](https://www.nuget.org/packages/Microsoft.Azure.Insights/)を利用し、アクティビティ ログから情報を取得できます。

## <a name="the-azure-portal"></a>Azure ポータル

1. ポータルからアクティビティ ログを表示するには、**[監視]** を選択します。

    ![監視を選択する](./media/resource-group-audit/select-monitor.png)

1. **[アクティビティ ログ]** を選択します。

    ![[アクティビティ ログ] を選択する](./media/resource-group-audit/select-activity-log.png)

1. 最近の操作の概要が表示されます。 操作には、既定のフィルターセットが適用されます。

    ![最近の操作の概要を表示する](./media/resource-group-audit/audit-summary.png)

1. 定義済みのフィルター セットをすばやく実行するには、**[クイック分析情報]** を選択し、いずれかのオプションを選択します。

    ![クエリの選択](./media/resource-group-audit/quick-insights.png)

1. 特定の操作に焦点を合わせるには、フィルターを変更するか、新しいフィルターを適用します。 たとえば、次の図は、**[Timespan]\(期間\)** の新しい値と、**[リソースの種類]** がストレージ アカウントに設定されていることを示しています。 

    ![フィルター オプションを設定する](./media/resource-group-audit/set-filter.png)

1. 後で再度クエリを実行する必要がある場合は、**[現在のフィルターをピン留めする]** を選択します。

    ![フィルターをピン留めする](./media/resource-group-audit/pin-filters.png)

1. フィルターに名前を付けます。

    ![フィルターに名前を付ける](./media/resource-group-audit/name-filters.png)

1. フィルターはダッシュ ボードで提供されます。

    ![ダッシュ ボードでフィルターを表示する](./media/resource-group-audit/show-dashboard.png)

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* ログ エントリを取得するには、**Get-AzLog** コマンドを実行します。 パラメーターを追加し、エントリの一覧を絞り込むことができます。 開始時間と終了時間を指定しない場合は、過去 7 日間のエントリが返されます。

  ```azurepowershell-interactive
  Get-AzLog -ResourceGroup ExampleGroup
  ```

    次の例は、アクティビティ ログを使用して、指定した時間に行われた操作を調査する方法を示しています。 開始日と終了日は、日付の形式で指定されます。

  ```azurepowershell-interactive
  Get-AzLog -ResourceGroup ExampleGroup -StartTime 2019-01-09T06:00 -EndTime 2019-01-15T06:00
  ```

    または、日付関数を使用して、最後の 14 日など、日付の範囲を指定することができます。

  ```azurepowershell-interactive
  Get-AzLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14)
  ```

* 存在しなくなったリソース グループであっても、特定のユーザーが行ったアクションを検索できます。

  ```azurepowershell-interactive
  Get-AzLog -ResourceGroup deletedgroup -StartTime (Get-Date).AddDays(-14) -Caller someone@contoso.com
  ```

* フィルターを使って、失敗した操作を抽出できます。

  ```azurepowershell-interactive
  Get-AzLog -ResourceGroup ExampleGroup -Status Failed
  ```

* 1 つのエラーに焦点を当てるには、そのエントリのステータス メッセージを確認します。

  ```azurepowershell-interactive
  ((Get-AzLog -ResourceGroup ExampleGroup -Status Failed).Properties[0].Content.statusMessage | ConvertFrom-Json).error
  ```

* 特定の値を選択し、返されるデータを制限することができます。

  ```azurepowershell-interactive
  Get-AzLog -ResourceGroupName ExampleGroup | Format-table EventTimeStamp, Caller, @{n='Operation'; e={$_.OperationName.value}}, @{n='Status'; e={$_.Status.value}}, @{n='SubStatus'; e={$_.SubStatus.LocalizedValue}}
  ```

* 指定した開始時刻によっては、前のコマンドを実行したときに、そのリソース グループの操作が長い一覧で返されることがあります。 検索基準を指定すると、探しものの結果を絞り込むことができます。 たとえば、操作の種類でフィルター処理することができます。

  ```azurepowershell-interactive
  Get-AzLog -ResourceGroup ExampleGroup | Where-Object {$_.OperationName.value -eq "Microsoft.Resources/deployments/write"}
  ```

## <a name="azure-cli"></a>Azure CLI

* ログ エントリを取得するには、期間を示すオフセットを指定して、[az monitor activity-log list](/cli/azure/monitor/activity-log#az-monitor-activity-log-list) コマンドを実行します。

  ```azurecli-interactive
  az monitor activity-log list --resource-group ExampleGroup --offset 7d
  ```

  次の例は、アクティビティ ログを使用して、指定した時間に行われた操作を調査する方法を示しています。 開始日と終了日は、日付の形式で指定されます。

  ```azurecli-interactive
  az monitor activity-log list -g ExampleGroup --start-time 2019-01-01 --end-time 2019-01-15
  ```

* 存在しなくなったリソース グループであっても、特定のユーザーが行ったアクションを検索できます。

  ```azurecli-interactive
  az monitor activity-log list -g ExampleGroup --caller someone@contoso.com --offset 5d
  ```

* フィルターを使って、失敗した操作を抽出できます。

  ```azurecli-interactive
  az monitor activity-log list -g demoRG --status Failed --offset 1d
  ```

* 1 つのエラーに焦点を当てるには、そのエントリのステータス メッセージを確認します。

  ```azurecli-interactive
  az monitor activity-log list -g ExampleGroup --status Failed --offset 1d --query [].properties.statusMessage
  ```

* 特定の値を選択し、返されるデータを制限することができます。

  ```azurecli-interactive
  az monitor activity-log list -g ExampleGroup --offset 1d --query '[].{Operation: operationName.value, Status: status.value, SubStatus: subStatus.localizedValue}'
  ```

* 指定した開始時刻によっては、前のコマンドを実行したときに、そのリソース グループの操作が長い一覧で返されることがあります。 検索基準を指定すると、探しものの結果を絞り込むことができます。 たとえば、操作の種類でフィルター処理することができます。

  ```azurecli-interactive
  az monitor activity-log list -g ExampleGroup --offset 1d --query "[?operationName.value=='Microsoft.Storage/storageAccounts/write']"
  ```

## <a name="rest-api"></a>REST API

アクティビティ ログを利用するための REST 操作は [Insights REST API](/rest/api/monitor/)に含まれています。 アクティビティ ログのイベントを取得するには、 [サブスクリプション内の管理イベントの一覧表示](/rest/api/monitor/activitylogs)に関するページを参照してください。

## <a name="next-steps"></a>次の手順

* Azure アクティビティ ログは、サブスクリプション内のアクションに関してさらに洞察を得るために、Power BI で使用できます。 [Power BI などでの Azure アクティビティ ログの表示と分析](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/)に関する記事をご覧ください。
* セキュリティ ポリシーを設定する方法については、「[Azure のロールベースのアクセス制御](../role-based-access-control/role-assignments-portal.md)」を参照してください。
* デプロイ操作を表示するコマンドについては、[デプロイ操作の表示](resource-manager-deployment-operations.md)に関するページをご覧ください。
* すべてのユーザーのリソースに対する削除を回避する方法については、「 [Azure Resource Manager によるリソースのロック](resource-group-lock-resources.md)」をご覧ください。
* 各 Microsoft Azure Resource Manager プロバイダーで使用できる操作の一覧については、「[Azure Resource Manager のリソース プロバイダー操作](../role-based-access-control/resource-provider-operations.md)」をご覧ください。
