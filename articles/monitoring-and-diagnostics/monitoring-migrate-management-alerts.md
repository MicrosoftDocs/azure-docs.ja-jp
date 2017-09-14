---
title: "管理イベントに関する Azure アラートをアクティビティ ログ アラートに移行する | Microsoft Docs"
description: "管理イベントに関するアラートは 10 月 1 日に削除されます。 既存に関するアラートを移行して準備してください。"
author: johnkemnetz
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
ms.date: 08/14/2017
ms.author: johnkem
ms.translationtype: HT
ms.sourcegitcommit: b309108b4edaf5d1b198393aa44f55fc6aca231e
ms.openlocfilehash: 08a457029d3721f5c38dbcd2d2aab7d09a241d8f
ms.contentlocale: ja-jp
ms.lasthandoff: 08/15/2017

---
# <a name="migrate-azure-alerts-on-management-events-to-activity-log-alerts"></a>管理イベントに関する Azure アラートをアクティビティ ログ アラートに移行する


> [!WARNING]
> 管理イベントに関するアラートは 10 月 1 日以降に無効になります。 以下の手順に従って、該当するアラートがあるかどうかを確認し、存在する場合はそのアラートを移行してください。
>
> 

## <a name="what-is-changing"></a>変更点

Azure Monitor (旧称 Azure Insights) では、管理イベントからトリガーされ、webhook の URL または電子メール アドレスへの通知を生成するアラートを作成する機能が提供されていました。 次のいずれかの方法でアラートを作成している可能性があります。
* Azure Portal で、特定の種類のリソースについて、[監視]、[アラート]、[アラートの追加] の順に移動して、[アラート対象] を [イベント] に設定する
* Add-AzureRmLogAlertRule PowerShell コマンドレットを実行する
* [アラート REST API](http://docs.microsoft.com/rest/api/monitor/alertrules) を odata.type = “ManagementEventRuleCondition” および dataSource.odata.type = “RuleManagementEventDataSource” と共に直接使用する
 
次の PowerShell スクリプトは、各アラートに設定されている条件だけでなく、サブスクリプション内にある管理イベントに関するすべてのアラートの一覧を返します。

```powershell
Login-AzureRmAccount
$alerts = $null
foreach ($rg in Get-AzureRmResourceGroup ) {
  $alerts += Get-AzureRmAlertRule -ResourceGroup $rg.ResourceGroupName
}
foreach ($alert in $alerts) {
  if($alert.Properties.Condition.DataSource.GetType().Name.Equals("RuleManagementEventDataSource")) {
    "Alert Name: " + $alert.Name
    "Alert Resource ID: " + $alert.Id
    "Alert conditions:"
    $alert.Properties.Condition.DataSource
    "---------------------------------"
  }
} 
```

管理イベントに関するアラートが存在しない場合、上記の PowerShell コマンドレットによって、次のような一連の警告メッセージが出力されます。

`WARNING: The output of this cmdlet will be flattened, i.e. elimination of the properties field, in a future release to improve the user experience.`

これらの警告メッセージは無視できます。 管理イベントに関するアラートが存在する場合、この PowerShell コマンドレットの出力は次のようになります。

```
Alert Name: webhookEvent1
Alert Resource ID: /subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>/providers/microsoft.insights/alertrules/webhookEvent1
Alert conditions:

EventName            : 
EventSource          : 
Level                : 
OperationName        : microsoft.web/sites/start/action
ResourceGroupName    : 
ResourceProviderName : 
Status               : succeeded
SubStatus            : 
Claims               : Microsoft.Azure.Management.Monitor.Management.Models.RuleManagementEventClaimsDataSource
ResourceUri          : /subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>/providers/Microsoft.Web/sites/samplealertapp

---------------------------------
Alert Name: someclilogalert
Alert Resource ID: /subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>/providers/microsoft.insights/alertrules/someclilogalert
Alert conditions:

EventName            : 
EventSource          : 
Level                : 
OperationName        : Start
ResourceGroupName    : 
ResourceProviderName : 
Status               : 
SubStatus            : 
Claims               : Microsoft.Azure.Management.Monitor.Management.Models.RuleManagementEventClaimsDataSource
ResourceUri          : /subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>/providers/Microsoft.Compute/virtualMachines/Seaofclouds

---------------------------------
```

各アラートは破線で分離されており、詳細には、アラートのリソース ID と監視対象の特定のルールが含まれます。

この機能は、[Azure Monitor アクティビティ ログ アラート](monitoring-activity-log-alerts.md)に移行されました。 この新しいアラートを使用すると、アクティビティ ログのイベントに条件を設定し、新しいイベントがその条件に一致するときに通知を受信できます。 管理イベントに関するアラートから強化された機能も提供しています。
* [アクション グループ](monitoring-action-groups.md)を使用して、多くのアラートにわたって通知の受信者 (「アクション」) のグループを再利用すると、アラートを受信する必要のあるユーザーを変更するときの複雑さが軽減されます。
* アクション グループと SMS を使用して、携帯電話で直接通知を受信できます。
* [Resource Manager テンプレートでアクティビティ ログ アラートを作成](monitoring-create-activity-log-alerts-with-resource-manager-template.md)できます。
* 特定のニーズを満たす、より柔軟で複雑な条件を作成できます。
* 通知は、より迅速に配信されます。
 
## <a name="how-to-migrate"></a>移行方法
 
新しいアクティビティ ログ アラートを作成するには、次のいずれかの方法を使用できます。
* [Azure Portal でアラートを作成する方法についてのガイド](monitoring-activity-log-alerts.md)に従う
* [Resource Manager テンプレートを使用してアラートを作成する](monitoring-create-activity-log-alerts-with-resource-manager-template.md)方法を学習する
 
以前に作成した、管理イベントに関するアラートは、アクティビティ ログ アラートに自動的には移行されません。 上記の PowerShell スクリプトを使用して、現在構成済みで、かつアクティビティ ログ アラートとして手動で再作成する、管理イベントに関するアラートを一覧表示する必要があります。 この操作は、10 月 1 日より前に行う必要があります。10 月 1 日以降、管理イベントに関するアラートが Azure サブスクリプションに表示されなくなります。 Azure Monitor のメトリック アラート、Application Insights のアラート、および Log Analytics のアラートを含むその他の種類の Azure アラートは、この変更による影響を受けません。 ご質問があれば、後のコメントに投稿してください。


## <a name="next-steps"></a>次のステップ

* [アクティビティ ログ](monitoring-overview-activity-logs.md) の詳細
* [Azure Portal からのアクティビティ ログ アラート](monitoring-activity-log-alerts.md)の構成
* [Resource Manager からのアクティビティ ログ アラート](monitoring-create-activity-log-alerts-with-resource-manager-template.md)の構成
* [アクティビティ ログ アラート webhook スキーマ](monitoring-activity-log-alerts-webhook.md) の確認
* [サービス通知](monitoring-service-notifications.md) の詳細
* [アクション グループ](monitoring-action-groups.md)の詳細については、こちらをご覧ください。

