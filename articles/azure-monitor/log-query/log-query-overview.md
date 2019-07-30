---
title: Azure Monitor のログ クエリの概要 | Microsoft Docs
description: ログ クエリに関してよく寄せられる質問に回答し、その使用の概要を示します。
services: log-analytics
author: bwren
ms.service: log-analytics
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: bwren
ms.openlocfilehash: 7605bf36c41c5b1276d29076173efd52409afaa9
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/21/2019
ms.locfileid: "67310338"
---
# <a name="overview-of-log-queries-in-azure-monitor"></a>Azure Monitor のログ クエリの概要
ログ クエリは、[Azure Monitor ログ](../platform/data-platform-logs.md)内に収集されたデータの価値を最大限に活用するのに役立ちます。 強力なクエリ言語により、複数のテーブルのデータを結合したり、大量のデータ セットを集約したり、最小限のコードによって複雑な操作を実行したりできます。 有用なデータが収集されていて、適切なクエリを作成する方法を理解していれば、ほぼすべての疑問に答えたり、分析を実行したりすることができます。

[分析情報](../insights/insights-overview.md)や[ソリューション](../insights/solutions-inventory.md)など、Azure Monitor の一部の機能では、基になるクエリをユーザーに公開せずに、ログ データを処理します。 Azure Monitor の他の機能を十分に活用するには、クエリを作成する方法と、それを使用して Azure Monitor ログ内のデータを対話形式で分析する方法を理解する必要があります。

この記事は、Azure Monitor のログ クエリについて学習する際の開始点として使用してください。 よく寄せられる質問に回答し、詳細情報やレッスンを提供するその他のドキュメントへのリンクを示します。

## <a name="how-can-i-learn-how-to-write-queries"></a>クエリを作成する方法はどこで学習できますか?
すぐに始めたい場合、次のチュートリアルを利用して開始できます。

- [Azure Monitor で Log Analytics の使用を開始する](get-started-portal.md)。
- [Azure Monitor でログ クエリの使用を開始する](get-started-queries.md)。

基本をマスターしたら、お持ちのデータまたは Microsoft のデモ環境のデータを使用して、以下をはじめとする複数のレッスンを完了します。 

- [Azure Monitor ログ クエリ内の文字列を操作する](string-operations.md)
 
## <a name="what-language-do-log-queries-use"></a>ログ クエリではどの言語を使用しますか?
Azure Monitor ログは [Azure Data Explorer](/azure/data-explorer) を基盤としており、ログ クエリは同じ Kusto クエリ言語 (KQL) を使用して作成します。 これは、読みやすく、簡単に作成できるよう設計されたリッチな言語で、最小限のガイダンスで使用を開始できます。

KQL に関する完全なドキュメントと使用可能なさまざまな関数のリファレンスについては、[Azure Data Explorer の KQL のドキュメント](/azure/kusto/query)を参照してください。<br>
Azure Monitor ログ内のデータを使用した言語の簡単なチュートリアルについては、「[Azure Monitor でログ クエリの使用を開始する](get-started-queries.md)」を参照してください。
Azure Monitor によって使用される KQL のバージョンの小さな違いについては、「[Azure Monitor ログ クエリ言語の違い](data-explorer-difference.md)」を参照してください。

## <a name="what-data-is-available-to-log-queries"></a>ログ クエリでは、どのようなデータを使用できますか?
ログ クエリでの取得と分析には、Azure Monitor ログ内に収集されたすべてのデータを使用できます。 さまざまなデータ ソースからさまざまなテーブルにそれぞれのデータが書き込まれますが、1 つのクエリに複数のテーブルを含めて、複数のソースをまたいでデータを分析することができます。 クエリを作成する場合は、最初に、どのテーブルに目的のデータが含まれているかを確認します。そうすると、Azure Monitor ログ内のデータがどのような構造になっているかについて、少なくとも基本的な理解を得ることができます。

Azure Monitor ログに書き込むさまざまなデータ ソースの一覧については、「[Azure Monitor ログのソース](../platform/data-platform-logs.md#sources-of-azure-monitor-logs)」を参照してください。<br>
データがどのような構造になっているかの説明については、「[Azure Monitor ログの構造](logs-structure.md)」を参照してください。

## <a name="what-does-a-log-query-look-like"></a>ログ クエリはどのようなものですか?
クエリは、1 つのテーブル名という簡単なものでは、そのテーブルからすべてのレコードを取得できます。

```Kusto
Syslog
```

また、フィルターして特定のレコードを抽出し、集計して、結果をグラフで視覚化することもできます。

```
SecurityEvent
| where TimeGenerated > ago(7d)
| where EventID == 4625
| summarize count() by Computer, bin(TimeGenerated, 1h)
| render timechart 
```

さらに複雑な分析を行う場合は、結合を使用して複数のテーブルからデータを取得し、まとめて結果を分析できます。

```Kusto
app("ContosoRetailWeb").requests
| summarize count() by bin(timestamp,1hr)
| join kind= inner (Perf
    | summarize avg(CounterValue) 
      by bin(TimeGenerated,1hr))
on $left.timestamp == $right.TimeGenerated
```
KQL をよく知らなくても、これらのクエリによって使用されている基本的なロジックは少なくとも理解できると思います。 先頭にテーブルの名前があり、その後にそのデータをフィルターして処理するコマンドが複数追加されています。 クエリでは任意の数のコマンドを使用できます。使用可能なさまざまな KQL コマンドについて理解すると、より複雑なクエリを作成することができます。

言語および一般的な関数について説明しているログ クエリのチュートリアルについては、「[Azure Monitor でログ クエリの使用を開始する](get-started-queries.md)」を参照してください。<br>


## <a name="what-is-log-analytics"></a>Log Analytics とは
Log Analytics は、Azure portal 内で、ログ クエリを作成したり、その結果を対話形式で分析したりするための主要なツールです。 Azure Monitor の他の場所でログ クエリを使用する場合でも、通常、まず Log Analytics を使用してクエリを作成およびテストします。

Log Analytics は、Azure portal 内の複数の場所から起動できます。 Log Analytics で使用できるデータのスコープは、起動方法によって決まります。 詳しくは、「[クエリ スコープ](scope.md)」を参照してください。

- **[Azure Monitor]** メニューまたは **[Log Analytics ワークスペース]** メニューの **[ログ]** を選択します。
- Application Insights アプリケーションの **[概要]** ページから **[分析]** を選択します。
- Azure リソースのメニューから **[ログ]** を選択します。

![Log Analytics](media/log-query-overview/log-analytics.png)

Log Analytics のいくつかの機能を紹介するチュートリアルについては、「[Azure Monitor で Log Analytics の使用を開始する](get-started-portal.md)」を参照してください。

## <a name="where-else-are-log-queries-used"></a>ログ クエリは他にどのような場合に使用しますか?
Log Analytics でログ クエリとその結果を対話形式で操作するほかに、クエリを使用する Azure Monitor 内の領域には以下があります。

- **警告ルール。** [警告ルール](../platform/alerts-overview.md)は、ワークスペースのデータの問題を事前に特定します。  各警告ルールは、定期的に自動実行されるログ検索に基づいてます。  結果を検査することで、警告を作成するかどうかが決まります。
- **ダッシュボード。** クエリの結果は [Azure ダッシュボード](../learn/tutorial-logs-dashboards.md)にピン留めすることができます。これにより、ログ データとメトリック データをまとめて視覚化し、必要に応じて、他の Azure ユーザーと共有することができます。
- **ビュー。**  ユーザー ダッシュボードに含めるデータの視覚化を作成するには、[ビュー デザイナー](../platform/view-designer.md)を使用します。  ログ クエリによって提供されるのは、各ビューの[タイル](../platform/view-designer-tiles.md)および[視覚化パーツ](../platform/view-designer-parts.md)で使用するデータです。  
- **エクスポート。**  Azure Monitor から Excel または [Power BI](../platform/powerbi.md) にログ データをインポートする場合は、ログ クエリを作成して、エクスポートするデータを定義します。
- **PowerShell。** [Get-AzOperationalInsightsSearchResults](/powershell/module/az.operationalinsights/get-azoperationalinsightssearchresult) を使用する Azure Automation Runbook またはコマンド ラインから PowerShell スクリプトを実行して、Azure Monitor からログ データを取得できます。  このコマンドレットでは、クエリによって、取得するデータを決定する必要があります。
- **Azure Monitor Logs API。**  [Azure Monitor Logs API](../platform/alerts-overview.md) を使用すると、任意の REST API クライアントによってワークスペースからログ データを取得することができます。  API 要求には Azure Monitor に対して実行するクエリが含まれており、これにより取得するデータを決定します。


## <a name="next-steps"></a>次の手順
- [Azure portal 内での Log Analytics の使用に関するチュートリアル](get-started-portal.md)を進めます。
- [クエリの作成に関するチュートリアル](get-started-queries.md)を進めます。
