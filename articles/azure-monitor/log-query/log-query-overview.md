---
title: Azure Monitor でログ データを分析する | Microsoft Docs
description: Azure Monitor からログ データを取得するにはログ クエリが必要です。  この記事では、Azure Monitor で新しいログ クエリを使用する方法と、ログ クエリを作成する前に理解しておく必要がある概念について説明します。
services: log-analytics
author: bwren
ms.service: log-analytics
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: bwren
ms.openlocfilehash: dcac701f3c1b6d64a7017c31679c019b91103ba2
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2019
ms.locfileid: "58904731"
---
# <a name="analyze-log-data-in-azure-monitor"></a>Azure Monitor でログ データを分析する

Azure Monitor で収集されたログ データは、[Azure データ エクスプローラー](/azure/data-explorer)に基づく Log Analytics ワークスペースに保存されます。 さまざまなソースからテレメトリが収集され、Data Explorer で使用される [Kusto クエリ言語](/azure/kusto/query)によってデータが取得および分析されます。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="log-queries"></a>ログ クエリ

Azure Monitor からログ データを取得するにはログ クエリが必要です。  [ポータルでデータを分析する](portals.md)、特定の条件の通知を受け取る[警告ルールを構成する](../platform/alerts-metric.md)、[Azure Monitor Logs API](https://dev.loganalytics.io/) を使用してデータを取得するといった場合はいずれも、クエリを使用して必要なデータを指定します。  この記事では、Azure Monitor 内でログ クエリを使用する方法と、ログ クエリを作成する前に理解しておく必要がある概念について説明します。

## <a name="where-log-queries-are-used"></a>ログ クエリを使用する場所

Azure Monitor のクエリは、次に示すさまざまな方法で使用します。

- **ポータル。** [Azure portal](portals.md) では、ログ データの対話型分析を行うことができます。  これにより、クエリを編集し、さまざまな形式および視覚化で結果を分析することができます。  
- **警告ルール。** [警告ルール](../platform/alerts-overview.md)は、ワークスペースのデータの問題を事前に特定します。  各警告ルールは、定期的に自動実行されるログ検索に基づいてます。  結果を検査することで、警告を作成するかどうかが決まります。
- **ダッシュボード。** クエリの結果は [Azure ダッシュボード](../learn/tutorial-logs-dashboards.md)にピン留めすることができます。これにより、ログ データとメトリック データをまとめて視覚化し、必要に応じて、他の Azure ユーザーと共有することができます。 
- **ビュー。**  ユーザー ダッシュボードに含めるデータの視覚化を作成するには、[ビュー デザイナー](../platform/view-designer.md)を使用します。  ログ クエリによって提供されるのは、各ビューの[タイル](../platform/view-designer-tiles.md)および[視覚化パーツ](../platform/view-designer-parts.md)で使用するデータです。  

- **エクスポート。**  Azure Monitor から Excel または [Power BI](../platform/powerbi.md) にログ データをインポートする場合は、ログ クエリを作成して、エクスポートするデータを定義します。
- **PowerShell。** [Get-AzOperationalInsightsSearchResults](/powershell/module/az.operationalinsights/get-azoperationalinsightssearchresults) を使用する Azure Automation Runbook またはコマンド ラインから PowerShell スクリプトを実行して、Azure Monitor からログ データを取得できます。  このコマンドレットでは、クエリによって、取得するデータを決定する必要があります。
- **Azure Monitor Logs API。**  [Azure Monitor Logs API](../platform/alerts-overview.md) を使用すると、任意の REST API クライアントによってワークスペースからログ データを取得することができます。  API 要求には Azure Monitor に対して実行するクエリが含まれており、これにより取得するデータを決定します。

![ログ検索](media/log-query-overview/queries-overview.png)

## <a name="write-a-query"></a>クエリを記述する
Azure Monitor では、[Kusto クエリ言語のバージョン](get-started-queries.md)を使用して、さまざまな方法でログ データを取得および分析します。  通常、基本的なクエリから開始し、要件の複雑化に合わせて、より高度な機能の使用へと進んでいきます。

クエリの基本構造では、ソース テーブルの後に、一連の演算子をパイプ文字 `|` で区切って記述します。  複数の演算子を連結してデータを絞り込み、高度な機能を実行できます。

たとえば、過去 1 日で、エラー イベントが多い上位 10 台のコンピューターを確認する必要があるとします。

```Kusto
Event
| where (EventLevelName == "Error")
| where (TimeGenerated > ago(1days))
| summarize ErrorCount = count() by Computer
| top 10 by ErrorCount desc
```

また、過去 1 日に、ハートビートがなかったコンピューターを確認したい場合もあります。

```Kusto
Heartbeat
| where TimeGenerated > ago(7d)
| summarize max(TimeGenerated) by Computer
| where max_TimeGenerated < ago(1d)  
```

先週の各コンピューターにおけるプロセッサ使用率を表す折れ線グラフはどうでしょうか。

```Kusto
Perf
| where ObjectName == "Processor" and CounterName == "% Processor Time"
| where TimeGenerated  between (startofweek(ago(7d)) .. endofweek(ago(7d)) )
| summarize avg(CounterValue) by Computer, bin(TimeGenerated, 5min)
| render timechart    
```

この簡単な例からわかるように、操作しているデータの種類に関係なく、クエリの構造は似ています。  これは個別のステップに分割できます。このステップで 1 つのコマンドの結果として生成されるデータが、パイプラインを介して次のコマンドに送信されます。

サブスクリプション内の Log Analytics ワークスペース全体でデータをクエリすることもできます。

```Kusto
union Update, workspace("contoso-workspace").Update
| where TimeGenerated >= ago(1h)
| summarize dcount(Computer) by Classification 
```

## <a name="how-azure-monitor-log-data-is-organized"></a>Azure Monitor のログ データの編成方法
クエリを作成する場合は、最初に、どのテーブルに目的のデータが含まれるかを確認します。 種類が異なるデータは、各 [Log Analytics ワークスペース](../learn/quick-create-workspace.md)内で専用のテーブルにそれぞれ分けられます。  各種データ ソースのドキュメントには、作成されたデータ型の名前と、各プロパティの説明が含まれています。  多くのクエリでは、1 つのテーブルのデータのみが必要ですが、複数のテーブルのデータを含めるためにさまざまなオプションを使用するクエリもあります。

要求、例外、トレース、および Azure Monitor での使用状況などのアプリケーション データは [Application Insights](../app/app-insights-overview.md) によって格納されますが、このデータはその他のログ データとは別のパーティションに格納されます。 このデータにアクセスするには、同じクエリ言語を使用します。ただし、アクセスするには [Application Insights コンソール](../app/analytics.md)または [Application Insights REST API](https://dev.applicationinsights.io/) を使用する必要があります。 [クロスリソース クエリ](../log-query/cross-workspace-query.md)を使用すれば、Application Insights のデータを Azure Monitor 内の他のログ データと結合することができます。


![テーブル](media/log-query-overview/queries-tables.png)




## <a name="next-steps"></a>次の手順
- [ログ検索を作成および編集するための Log Analytics](../log-query/portals.md) の使用について確認します。
- 新しいクエリ言語を使用した[クエリ記述のチュートリアル](../log-query/get-started-queries.md)を確認します。
