---
title: Azure Monitor で複数の Application Insights リソースを統合する | Microsoft Docs
description: この記事では、Azure Monitor のログで関数を使用して、複数の Application Insights リソースのクエリを実行し、そのデータを視覚化する方法について詳しく説明します。
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: magoedte
ms.openlocfilehash: 3f3de81197b05d4f025a3fd8638cffe4b07cecad
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/20/2019
ms.locfileid: "56429518"
---
# <a name="unify-multiple-azure-monitor-application-insights-resources"></a>Azure Monitor で複数の Application Insights リソースを統合する 
この記事では、非推奨になった Application Insights Connector を引き継ぐものとして、お使いのすべての Application Insights アプリケーションのログ データのクエリと表示を 1 か所で行う方法について説明します。Azure サブスクリプションは違っていてもかまいません。 1 回のクエリに含めることができる Application Insights リソースの数は 100 個に制限されています。  

## <a name="recommended-approach-to-query-multiple-application-insights-resources"></a>複数の Application Insights リソースに対してクエリを実行するための推奨される方法 
複数の Application Insights リソースを 1 つのクエリの中に指定するのは面倒であり、管理が難しくなる可能性があります。 代わりに、関数を活用して、クエリのロジックとアプリケーションのスコープを分離できます。  

次の例で、複数の Application Insights リソースを監視し、失敗した要求の数をアプリケーション名別に視覚化する方法を示します。 開始する前に、Application Insights リソースに接続されているワークスペースで次のクエリを実行して、接続されているアプリケーションの一覧を取得してください。 

```
ApplicationInsights
| summarize by ApplicationName
```

union 演算子とアプリケーションの一覧を使用して関数を作成した後、クエリを *applicationsScoping* というエイリアスの関数としてワークスペースに保存します。  

```
union withsource=SourceApp 
app('Contoso-app1').requests,  
app('Contoso-app2').requests, 
app('Contoso-app3').requests, 
app('Contoso-app4').requests, 
app('Contoso-app5').requests 
| parse SourceApp with * "('" applicationName "')" *  
```

>[!NOTE]
>アプリケーションの一覧は、ポータルでワークスペースのクエリ エクスプローラーに移動して、編集する関数を選択して保存するか、または `SavedSearch` PowerShell コマンドレットを使用することで、いつでも変更できます。 `withsource= SourceApp` コマンドを使用すると、ログを送信したアプリケーションを示す列が結果に追加されます。 
>
>このクエリでは、Application Insights のスキーマが使用されます。ただし、applicationsScoping 関数で Application Insights データ構造が返されるため、クエリはワークスペース内で実行されます。 
>
>この例では、SourceApp プロパティからアプリケーション名を抽出する parse 演算子は省略できます。 

これで、リソース間クエリで applicationsScoping 関数を使用する準備が整いました。  

```
applicationsScoping 
| where timestamp > ago(12h)
| where success == 'False'
| parse SourceApp with * '(' applicationName ')' * 
| summarize count() by applicationName, bin(timestamp, 1h) 
| render timechart
```

関数のエイリアスによって、定義したすべてのアプリケーションからの要求の和集合が返されます。 このクエリによって、失敗した要求がフィルター処理され、アプリケーション別に傾向が視覚化されます。

![クロスクエリの結果の例](media/unify-app-resource-data/app-insights-query-results.png)

## <a name="query-across-application-insights-resources-and-workspace-data"></a>Application Insights リソースとワークスペース データ間のクエリ 
Connector を停止しているときに、Application Insights のデータ保有期間 (90 日間) によって切り取られた期間のクエリを実行する必要がある場合は、ワークスペースと Application Insights リソースに対して中間期間の[クロスリソース クエリ](../../azure-monitor/log-query/cross-workspace-query.md)を実行する必要があります。 これは、前述の新しい Application Insights のデータ保有期間でアプリケーション データが蓄積されるまで必要です。 Application Insights とワークスペースではスキーマが異なるため、クエリではいくつかの操作が必要です。 このセクションで後述する、スキーマの相違点を強調している表を参照してください。 

>[!NOTE]
>ログ アラートでの[リソース間のクエリ](../log-query/cross-workspace-query.md)は、新しい [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) でサポートされています。 [従来の Log Alerts API](../platform/alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api) から切り替えていない場合、Azure Monitor では既定で、[従来の Log Analytics Alert API](../platform/api-alerts.md) を使用して Azure portal から新しいログ アラート ルールが作成されます。 切り替えた後は、Azure portal での新しいアラート ルールに対して新しい API が既定になり、リソース間のクエリのログ アラート ルールを作成できます。 切り替えを行わなくても、[scheduledQueryRules API 用の ARM テンプレート](../platform/alerts-log.md#log-alert-with-cross-resource-query-using-azure-resource-template)を使用することで、[リソース間のクエリ](../log-query/cross-workspace-query.md)のログ アラート ルールを作成できます。ただし、このアラート ルールは、[scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) では管理できますが、Azure portal では管理できません。

たとえば、Connector が 2018 年 11 月 1 日に動作を停止しているときに、Application Insights リソースとワークスペース内のアプリケーション データのログのクエリを実行する場合、そのクエリは次の例のようになります。

```
applicationsScoping //this brings data from Application Insights resources 
| where timestamp between (datetime("2018-11-01") .. now()) 
| where success == 'False' 
| where duration > 1000 
| union ( 
    ApplicationInsights //this is Application Insights data in Log Analytics workspace 
    | where TimeGenerated < (datetime("2018-12-01") 
    | where RequestSuccess == 'False' 
    | where RequestDuration > 1000 
    | extend duration = RequestDuration //align to Application Insights schema 
    | extend timestamp = TimeGenerated //align to Application Insights schema 
    | extend name = RequestName //align to Application Insights schema 
    | extend resultCode = ResponseCode //align to Application Insights schema 
    | project-away RequestDuration , RequestName , ResponseCode , TimeGenerated 
) 
| project timestamp , duration , name , resultCode 
```

## <a name="application-insights-and-log-analytics-workspace-schema-differences"></a>Application Insights と Log Analytics ワークスペースのスキーマの相違点
次の表に、Log Analytics と Application Insights のスキーマの違いを示します。  

| Log Analytics ワークスペースのプロパティ| Application Insights のリソースのプロパティ|
|------------|------------| 
| AnonUserId | user_id|
| ApplicationId | appId|
| ApplicationName | appName|
| ApplicationTypeVersion | application_Version |
| AvailabilityCount | itemCount |
| AvailabilityDuration | duration |
| AvailabilityMessage | message |
| AvailabilityRunLocation | location |
| AvailabilityTestId | id |
| AvailabilityTestName | name |
| AvailabilityTimestamp | timestamp |
| ブラウザー | client_browser |
| City | client_city |
| ClientIP | client_IP |
| Computer | cloud_RoleInstance | 
| Country | client_CountryOrRegion | 
| CustomEventCount | itemCount | 
| CustomEventDimensions | customDimensions |
| CustomEventName | name | 
| DeviceModel | client_Model | 
| DeviceType | client_Type | 
| ExceptionCount | itemCount | 
| ExceptionHandledAt | handledAt |
| ExceptionMessage | message | 
| ExceptionType | type |
| OperationID | operation_id |
| OperationName | operation_Name | 
| OS | client_OS | 
| PageViewCount | itemCount |
| PageViewDuration | duration | 
| PageViewName | name | 
| ParentOperationID | operation_Id | 
| RequestCount | itemCount | 
| RequestDuration | duration | 
| RequestID | id | 
| RequestName | name | 
| RequestSuccess | 成功 | 
| ResponseCode | resultCode | 
| Role | cloud_RoleName |
| RoleInstance | cloud_RoleInstance |
| SessionId | session_Id | 
| SourceSystem | operation_SyntheticSource |
| TelemetryTYpe | type |
| URL | _url |
| UserAccountId | user_AccountId |

## <a name="next-steps"></a>次の手順

[ログ検索](../../azure-monitor/log-query/log-query-overview.md)を使用して Application Insights アプリの詳細情報を表示します。
