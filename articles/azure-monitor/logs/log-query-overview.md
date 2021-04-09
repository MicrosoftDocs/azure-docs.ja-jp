---
title: Azure Monitor でのログ クエリ
description: Azure Monitor で使用される Kusto クエリ言語のリファレンス情報。 Azure Monitor に固有の追加要素と、Azure Monitor ログ クエリでサポートされていない要素が含まれています。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/09/2020
ms.openlocfilehash: 529fc432bf8777ef7e2b527f08e9cb59e42bf156
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102041027"
---
# <a name="log-queries-in-azure-monitor"></a>Azure Monitor でのログ クエリ
Azure Monitor ログは Azure Data Explorer を基盤としており、ログ クエリは同じ Kusto クエリ言語 (KQL) を使用して作成します。 これは、読みやすく、簡単に作成できるよう設計されたリッチ言語であるため、いくつかの基本的なガイダンスを使用してクエリの作成を開始できます。

クエリを使用する Azure Monitor の領域には、次が含まれます。

- [Log Analytics](../logs/log-analytics-overview.md)。 Azure portal 内で、ログ クエリを編集したり、その結果を対話形式で分析したりするための主要なツールです。 Azure Monitor の他の場所でログ クエリを使用する場合でも、通常は Log Analytics で作成してテストしてから最終的な場所にコピーします。
- [ログの警告ルール](../alerts/alerts-overview.md)。 ワークスペースのデータの問題を事前に特定します。  各警告ルールは、定期的に自動実行されるログ クエリに基づいてます。  結果を検査することで、警告を作成するかどうかが決まります。
- [Workbooks](../visualize/workbooks-overview.md)。 さまざまな視覚化を使用したログ クエリの結果を、Azure portal の対話形式のビジュアル レポートに含めます。
- [Azure ダッシュボード](../visualize/tutorial-logs-dashboards.md)。 クエリの結果を Azure ダッシュボードにピン留めすることで、ログとメトリックのデータをまとめて視覚化し、必要に応じて、他の Azure ユーザーと共有することができます。
- [Logic Apps](../logs/logicapp-flow-connector.md)。  Logic Apps を使用して、自動ワークフローでログ クエリの結果を使用します。
- [PowerShell](/powershell/module/az.operationalinsights/get-azoperationalinsightssearchresult)。 コマンド ラインまたは Get-AzOperationalInsightsSearchResults を使用する Azure Automation Runbook からのログ クエリの結果を、PowerShell スクリプトで使用します。
- [Azure Monitor Logs API](https://dev.loganalytics.io)。 任意の REST API クライアントのワークスペースからログ データを取得します。  API 要求には Azure Monitor に対して実行するクエリが含まれており、これにより取得するデータを決定します。

## <a name="getting-started"></a>作業の開始
KQL を使用してログ クエリを作成するための学習を開始するのに最適な方法は、使用可能なチュートリアルとサンプルを活用することです。

- [Log Analytics チュートリアル](./log-analytics-tutorial.md) - Log Analytics (Azure portal でクエリを編集および実行するために使用するツール) の機能の使用に関するチュートリアルです。 これを使用すると、クエリ言語を直接操作することなく、単純なクエリを記述することもできます。 以前に Log Analytics を使用したことがない場合は、ここから開始して、他のチュートリアルやサンプルで使用するツールについて理解してください。
- [KQL チュートリアル](/azure/data-explorer/kusto/query/tutorial?pivots=azuremonitor) - 基本的な KQL の概念と一般的な演算子に関するガイド付きチュートリアルです。 これは、言語自体とログ クエリの構造をすばやく理解するのに最適な場所です。 
- [クエリの例](../logs/example-queries.md) - Log Analytics で使用できるクエリの例について説明します。 クエリは、変更せずに使用することも、KQL を学習するためのサンプルとして使用することもできます。
- [クエリ サンプル](/azure/data-explorer/kusto/query/samples?pivots=azuremonitor) - さまざまな概念を示すサンプル クエリです。



## <a name="reference-documentation"></a>リファレンス ドキュメント
すべてのコマンドと演算子のリファレンスを含む [KQL のドキュメント](/azure/data-explorer/kusto/query/)は、Azure Data Explorer のドキュメントで入手できます。 KQL の使用に習熟している場合でも、これまでに使用したことのない新しいコマンドやシナリオを調べるために、定期的にリファレンスを使用することがあります。


## <a name="language-differences"></a>言語の相違点
Azure Monitor では Azure Data Explorer と同じ KQL が使用されていますが、いくつかの違いがあります。 KQL のドキュメントには、Azure Monitor でサポートされていない、または機能が異なる演算子が明記されます。 Azure Monitor 固有の演算子は、Azure Monitor のコンテンツに記載されています。 以降のセクションでは、クイック リファレンスの言語のバージョン間の違いの一覧を示します。

### <a name="statements-not-supported-in-azure-monitor"></a>Azure Monitor でサポートされていないステートメント

* [エイリアス](/azure/kusto/query/aliasstatement)
* [クエリ パラメーター](/azure/kusto/query/queryparametersstatement)

### <a name="functions-not-supported-in-azure-monitor"></a>Azure Monitor でサポートされていない関数

* [cluster()](/azure/kusto/query/clusterfunction)
* [cursor_after()](/azure/kusto/query/cursorafterfunction)
* [cursor_before_or_at()](/azure/kusto/query/cursorbeforeoratfunction)
* [cursor_current(), current_cursor()](/azure/kusto/query/cursorcurrent)
* [database()](/azure/kusto/query/databasefunction)
* [current_principal()](/azure/kusto/query/current-principalfunction)
* [extent_id()](/azure/kusto/query/extentidfunction)
* [extent_tags()](/azure/kusto/query/extenttagsfunction)

### <a name="operators-not-supported-in-azure-monitor"></a>Azure Monitor でサポートされていない演算子

* [クラスター間の結合](/azure/kusto/query/joincrosscluster)

### <a name="plugins-not-supported-in-azure-monitor"></a>Azure Monitor でサポートされていないプラグイン

* [Python プラグイン](/azure/kusto/query/pythonplugin)
* [sql_request プラグイン](/azure/kusto/query/sqlrequestplugin)


### <a name="additional-operators-in-azure-monitor"></a>Azure Monitor で追加されている演算子
以下の演算子は、Azure Monitor の特定の機能をサポートしており、Azure Monitor の外部では使用できません。

* [app()](../logs/app-expression.md)
* [resource()](./resource-expression.md)
* [workspace()](../logs/workspace-expression.md)

## <a name="next-steps"></a>次のステップ
- [クエリの作成に関するチュートリアル](/azure/data-explorer/kusto/query/tutorial?pivots=azuremonitor)を進めます。
- 完全な [Kusto クエリ言語のリファレンス ドキュメント](/azure/kusto/query/)にアクセスします。