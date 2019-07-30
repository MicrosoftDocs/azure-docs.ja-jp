---
title: Azure Monitor でのリソース間のクエリ | Microsoft Docs
description: この記事では、サブスクリプション内の複数のワークスペースや App Insights アプリのリソースに対してクエリを実行する方法について説明します。
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/05/2019
ms.author: magoedte
ms.openlocfilehash: 5e411182a26e370ef82a20e67ee18cedd5d96d86
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/20/2019
ms.locfileid: "67296104"
---
# <a name="perform-cross-resource-log-queries-in-azure-monitor"></a>Azure Monitor でクロスリソース ログ クエリを実行する  

Azure Monitor では以前、現在のワークスペース内からしかデータを分析できず、サブスクリプション内に定義されている複数のワークスペース間でクエリを実行する機能が制限されていました。  また、Application Insights を使用した Web ベースのアプリケーションから収集されたテレメトリ項目を、Application Insights または Visual Studio から直接検索することしかできませんでした。 またこれにより、運用データとアプリケーション データを一緒にネイティブ分析することが難しくなっていました。

現在は、複数の Log Analytics ワークスペース間だけでなく、同じリソース グループ、別のリソース グループ、または別のサブスクリプション内の特定の Application Insights アプリからのデータのクエリを実行できるようになりました。 これにより、システム全体のデータを確認できます。 これらの種類のクエリは、[Log Analytics](portals.md) でのみ行うことができます。

## <a name="cross-resource-query-limits"></a>リソース間のクエリの制限 

* 1 回のクエリに含めることができる Application Insights リソースと Log Analytics ワークスペースの数は 100 個に制限されています。
* リソース間のクエリは、ビュー デザイナーでサポートされていません。 Log Analytics でクエリを作成し、それを Azure ダッシュボードにピン留めして、[ログ クエリを視覚化](../learn/tutorial-logs-dashboards.md)できます。 
* ログ アラートでのリソース間のクエリは、新しい [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) でサポートされています。 [従来の Log Alerts API](../platform/alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api) から切り替えていない場合、Azure Monitor では既定で、[従来の Log Analytics Alert API](../platform/api-alerts.md) を使用して Azure portal から新しいログ アラート ルールが作成されます。 切り替えた後は、Azure portal での新しいアラート ルールに対して新しい API が既定になり、リソース間のクエリのログ アラート ルールを作成できます。 [scheduledQueryRules API 用の Azure Resource Manager テンプレート](../platform/alerts-log.md#log-alert-with-cross-resource-query-using-azure-resource-template)を使用することにより、切り替えを行わなくてもリソース間のクエリのログ アラート ルールを作成できます。ただし、このアラート ルールは [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) で管理できますが、Azure portal からは管理できません。


## <a name="querying-across-log-analytics-workspaces-and-from-application-insights"></a>Log Analytics ワークスペース間と Application Insights からのクエリ
クエリ内の別のワークスペースを参照するには、[*ワークスペース*](https://docs.microsoft.com/azure/log-analytics/query-language/workspace-expression)識別子を使用します。Application Insights のアプリの場合は、[*アプリ*](https://docs.microsoft.com/azure/log-analytics/query-language/app-expression)識別子を使用します。  

### <a name="identifying-workspace-resources"></a>ワークスペースのリソースの識別
次の例は、Log Analytics ワークスペース間でのクエリを実行し、*contosoretail-it* という名前のワークスペースの Update テーブルから、ログの集計されたカウントを返します。 

ワークスペースの識別は、次の方法のいずれかで実行できます。

* リソース名 - 人間が判読できるワークスペースの名前。"*コンポーネント名*" と呼ばれることもあります。 

    `workspace("contosoretail-it").Update | count`
 
    >[!NOTE]
    >ワークスペースを名前で識別する場合、その名前は、アクセス可能なすべてのサブスクリプション間で一意であることが前提とされます。 指定した名前を持つ複数のアプリケーションが存在する場合は、あいまいさのためにクエリが失敗します。 この場合、その他の識別子のいずれかを使用する必要があります。

* 修飾名 - ワークスペースの "完全な名前"。*subscriptionName/resourceGroup/componentName* の形式で、サブスクリプション名、リソース グループ、およびコンポーネント名から構成されます。 

    `workspace('contoso/contosoretail/contosoretail-it').Update | count`

    >[!NOTE]
    >Azure サブスクリプション名が一意ではない場合、この識別子はあいまいである可能性があります。 
    >

* ワークスペース ID - ワークスペース ID は、グローバル一意識別子 (GUID) として表される、各ワークスペースに割り当てられた一意で不変の識別子です。

    `workspace("b459b4u5-912x-46d5-9cb1-p43069212nb4").Update | count`

* Azure リソース ID - Azure で定義されている、ワークスペースの一意な ID。 リソース名があいまいな場合は、リソース ID を使用します。  ワークスペースごとに、 */subscriptions/subscriptionId/resourcegroups/resourceGroup/providers/microsoft.OperationalInsights/workspaces/componentName* という形式になります。  

    例:
    ``` 
    workspace("/subscriptions/e427519-5645-8x4e-1v67-3b84b59a1985/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail-it").Update | count
    ```

### <a name="identifying-an-application"></a>アプリケーションの識別
次の例では、Application Insights の *fabrikamapp* という名前のアプリに対して行われた要求の集計数を返します。 

Application Insights でのアプリケーションの識別は、*app(Identifier)* 式で実行できます。  *Identifier* 引数は、次のいずれかを使用してアプリを指定します。

* リソース名 - 人間が判読できるアプリの名前。"*コンポーネント名*" と呼ばれることもあります。  

    `app("fabrikamapp")`

* 修飾名 - アプリの "完全な名前"。*subscriptionName/resourceGroup/componentName* の形式で、サブスクリプション名、リソース グループ、およびコンポーネント名から構成されます。 

    `app("AI-Prototype/Fabrikam/fabrikamapp").requests | count`

     >[!NOTE]
    >Azure サブスクリプション名が一意ではない場合、この識別子はあいまいである可能性があります。 
    >

* ID - アプリケーションのアプリ GUID。

    `app("b459b4f6-912x-46d5-9cb1-b43069212ab4").requests | count`

* Azure リソース ID - Azure で定義されている、アプリの一意な ID。 リソース名があいまいな場合は、リソース ID を使用します。 形式は */subscriptions/subscriptionId/resourcegroups/resourceGroup/providers/microsoft.OperationalInsights/components/componentName* です。  

    例:
    ```
    app("/subscriptions/b459b4f6-912x-46d5-9cb1-b43069212ab4/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp").requests | count
    ```

### <a name="performing-a-query-across-multiple-resources"></a>複数のリソース間でのクエリの実行
任意のリソース インスタンスの複数のリソースにクエリを実行できます。ワークスペースとアプリを組み合わせることもできます。
    
2 つのワークスペースにわたるクエリの例:    

```
union Update, workspace("contosoretail-it").Update, workspace("b459b4u5-912x-46d5-9cb1-p43069212nb4").Update
| where TimeGenerated >= ago(1h)
| where UpdateState == "Needed"
| summarize dcount(Computer) by Classification
```

## <a name="using-cross-resource-query-for-multiple-resources"></a>複数のリソースにクロスリソース クエリを使用する
クロスリソース クエリを使用して、複数の Log Analytics ワークスペースおよび Application Insights リソースのデータを関連付ける場合、クエリは複雑になり、維持管理が困難になる可能性があります。 [Azure Monitor ログ クエリの関数](functions.md)を利用して、クエリ リソースの範囲設定からクエリ ロジックを分離する必要があります。こうすることで、クエリ構造が簡素化されます。 次の例は、複数の Application Insights リソースを監視し、アプリケーション名別に失敗した要求の数を視覚化する方法を示しています。 

Application Insights リソースの範囲を参照する次のようなクエリを作成します。 `withsource= SourceApp` コマンドで、ログを送信したアプリケーション名を指定する列を追加します。 エイリアス _applicationsScoping_ を使用して[関数としてクエリを保存します](functions.md#create-a-function)。

```Kusto
// crossResource function that scopes my Application Insights resources
union withsource= SourceApp
app('Contoso-app1').requests, 
app('Contoso-app2').requests,
app('Contoso-app3').requests,
app('Contoso-app4').requests,
app('Contoso-app5').requests
```



次のようなクロスリソース クエリで[この関数を使用](../../azure-monitor/log-query/functions.md#use-a-function)できるようになります。 関数のエイリアス _applicationsScoping_ から、すべての定義済みアプリケーションから要求テーブルの和集合が返されます。 このクエリによって、失敗した要求がフィルター処理され、アプリケーション別に傾向が視覚化されます。 _parse_ 演算子はこの例では省略可能です。 _SourceApp_ プロパティからアプリケーション名が抽出されます。

```Kusto
applicationsScoping 
| where timestamp > ago(12h)
| where success == 'False'
| parse SourceApp with * '(' applicationName ')' * 
| summarize count() by applicationName, bin(timestamp, 1h) 
| render timechart
```
![時間グラフ](media/cross-workspace-query/chart.png)

## <a name="next-steps"></a>次の手順

- ログ クエリの概要と、Azure Monitor ログ データがどのように構成されているかについては、[Azure Monitor でログ データを分析する](log-query-overview.md)ことに関するページを参照してください。
- Azure Monitor ログ クエリのすべてのリソースを確認するには、[Azure Monitor ログ クエリ](query-language.md)に関するページを参照してください。
