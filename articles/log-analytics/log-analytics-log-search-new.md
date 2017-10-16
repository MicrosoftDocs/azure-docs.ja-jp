---
title: "Azure Log Analytics でのログ検索 | Microsoft Docs"
description: "Log Analytics からデータを取得するにはログ検索が必要です。  この記事では、Log Analytics で新しいログ検索を使用する方法と、ログ検索を作成する前に理解しておく必要がある概念について説明します。"
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2017
ms.author: bwren
ms.openlocfilehash: db271f5157fee29a5cc0c4534768bdb3c769ba74
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="understanding-log-searches-in-log-analytics"></a>Log Analytics でのログ検索について

> [!NOTE]
> この記事では、新しいクエリ言語を使用した Azure Log Analytics のログ検索について説明します。  新しい言語の詳細、およびワークスペースのアップグレード手順については、「[Upgrade your Azure Log Analytics workspace to new log search (新しいログ検索への Azure Log Analytics ワークスペースのアップグレード)](log-analytics-log-search-upgrade.md)」を参照してください。  
>
> ワークスペースが新しいクエリ言語にアップグレードされていない場合は、「[ログ検索を使用してデータを探す](log-analytics-log-searches.md)」を参照してください。

Log Analytics からデータを取得するにはログ検索が必要です。  ポータルでデータを分析する、特定の条件の通知を受け取る警告ルールを構成する、Log Analytics API を使用してデータを取得する、などの操作を行うときに、ログ検索を使用して必要なデータを指定します。  この記事では、Log Analytics でログ検索を使用する方法と、ログ検索を作成する前に理解しておく必要がある概念について説明します。 ログ検索の作成と編集の詳細、およびクエリ言語については、「[次のステップ](#next-steps)」セクションを参照してください。

## <a name="where-log-searches-are-used"></a>ログ検索を使用する場所

Log Analytics のログ検索は、次に示すさまざまな方法で使用します。

- **ポータル。** Azure Portal または[高度な分析ポータル](https://go.microsoft.com/fwlink/?linkid=856587)を使用して、リポジトリで対話式のデータ分析を実行できます。  これにより、クエリを編集し、さまざまな形式および視覚化で結果を分析することができます。  作成するクエリのほとんどがポータルのいずれかで開始され、期待どおりに動作することが確認された後に、コピーされます。
- **警告ルール。** [警告ルール](log-analytics-alerts.md)は、ワークスペースのデータの問題を事前に特定します。  各警告ルールは、定期的に自動実行されるログ検索に基づいてます。  結果を検査することで、警告を作成するかどうかが決まります。
- **ビュー。**  ユーザー ダッシュボードに含めるデータの視覚化を作成するには、[ビュー デザイナー](log-analytics-view-designer.md)を使用します。  ログ検索が提供するのは、各ビューの[タイル](log-analytics-view-designer-tiles.md)および[視覚化パーツ](log-analytics-view-designer-parts.md)で使用するデータです。  視覚化パーツからログ検索ページにドリルダウンすると、データをさらに詳しく分析できます。
- **エクスポート。**  Log Analytics ワークスペースからExcel または [Power BI](log-analytics-powerbi.md) にデータをエクスポートする場合は、ログ検索を作成して、エクスポートするデータを定義します。
- **PowerShell。** [Get-AzureRmOperationalInsightsSearchResults](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/get-azurermoperationalinsightssearchresults?view=azurermps-4.0.0) を使用する Azure Automation Runbook またはコマンド ラインから PowerShell スクリプトを実行して、Log Analytics からデータを取得できます。  このコマンドレットでは、クエリによって、取得するデータを決定する必要があります。
- **Log Analytics API。**  [Log Analytics のログ検索 API](log-analytics-log-search-api.md) を使用して、任意の REST API クライアントでワークスペースからデータを取得することができます。  API 要求には Log Analytics に対して実行するクエリが含まれており、これにより取得するデータを決定します。

![ログ検索](media/log-analytics-log-search-new/log-search-overview.png)

## <a name="how-log-analytics-data-is-organized"></a>Log Analytics データの編成方法
クエリを作成する場合は、最初に、どのテーブルに目的のデータが含まれるかを確認します。 [データ ソース](log-analytics-data-sources.md)および[ソリューション](../operations-management-suite/operations-management-suite-solutions.md)のデータはそれぞれ、Log Analytics ワークスペースの専用テーブルに格納されています。  データ ソースとソリューションのドキュメントにはそれぞれ、作成されたデータ型の名前と、各プロパティの説明が含まれています。  多くの場合、クエリに必要なデータは 1 つのテーブルだけに含まれますが、クエリの中には、さまざまなオプションを使用して、複数のテーブルのデータを含めるものもあります。

![テーブル](media/log-analytics-log-search-new/queries-tables.png)


## <a name="writing-a-query"></a>クエリの記述
Log Analytics のログ検索の中核をなすのは[広範なクエリ言語](https://docs.loganalytics.io/)で、これにより、さまざまな方法でリポジトリからデータを取得し、分析することができます。  この同じクエリ言語は、[Application Insights](../application-insights/app-insights-analytics.md) でも使用されます。  Log Analytics でログ検索を作成するには、クエリの記述方法を学ぶことが不可欠です。  通常、基本的なクエリから開始し、要件の複雑化に合わせて、より高度な機能の使用へと進んでいきます。

クエリの基本構造では、ソース テーブルの後に、一連の演算子をパイプ文字 `|` で区切って記述します。  複数の演算子を連結してデータを絞り込み、高度な機能を実行できます。

たとえば、過去 1 日で、エラー イベントが多い上位 10 台のコンピューターを確認する必要があるとします。

    Event
    | where (EventLevelName == "Error")
    | where (TimeGenerated > ago(1days))
    | summarize ErrorCount = count() by Computer
    | top 10 by ErrorCount desc

また、過去 1 日に、ハートビートがなかったコンピューターを確認したい場合もあります。

    Heartbeat
    | where TimeGenerated > ago(7d)
    | summarize max(TimeGenerated) by Computer
    | where max_TimeGenerated < ago(1d)  

先週の各コンピューターにおけるプロセッサ使用率を表す折れ線グラフはどうでしょうか。

    Perf
    | where ObjectName == "Processor" and CounterName == "% Processor Time"
    | where TimeGenerated  between (startofweek(ago(7d)) .. endofweek(ago(7d)) )
    | summarize avg(CounterValue) by Computer, bin(TimeGenerated, 5min)
    | render timechart    

この簡単な例からわかるように、操作しているデータの種類に関係なく、クエリの構造は似ています。  これは個別のステップに分割できます。このステップで 1 つのコマンドの結果として生成されるデータが、パイプラインを介して次のコマンドに送信されます。

サブスクリプション内の Log Analytics ワークスペース全体でデータをクエリすることもできます。

    union Update, workspace("contoso-workspace").Update
    | where TimeGenerated >= ago(1h)
    | summarize dcount(Computer) by Classification 


チュートリアル、言語リファレンスなど、Azure Log Analytics クエリ言語の詳細については、[Azure Log Analytics クエリ言語のドキュメント](https://docs.loganalytics.io/)を参照してください。

## <a name="next-steps"></a>次のステップ

- [ログ検索の作成および編集に使用するポータル](log-analytics-log-search-portals.md)について学習します。
- 新しいクエリ言語を使用した[クエリ記述のチュートリアル](log-analytics-tutorial-viewdata.md)を確認します。
