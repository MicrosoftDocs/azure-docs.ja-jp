---
title: Azure Monitor ログ クエリ | Microsoft Docs
description: Azure Monitor でログ クエリを記述する方法について説明しているリソースへの参照。
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: bwren
ms.openlocfilehash: ecbea91cef69c80975dd105c35f526b357033a6b
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/24/2019
ms.locfileid: "56749820"
---
# <a name="azure-monitor-log-queries"></a>Azure Monitor ログ クエリ
Azure Monitor ログは、Azure Data Explorer 上に構築されており、Azure Monitor ログ クエリでは、同じ Kusto クエリ言語のバージョンが使用されます。 [Kusto クエリ言語のドキュメント](/azure/kusto/query)には言語に関するすべての詳細が記載されているため、Azure Monitor ログ クエリを記述するためのプライマリ リソースとなります。 このページでは、クエリの記述方法を学習するためのその他のリソースへのリンクと、言語による Azure Monitor の実装の違いに関する情報へのリンクを示します。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="getting-started"></a>使用の開始

- 「[Azure Monitor Log Analytics の使用を開始する](get-started-portal.md)」は、Azure portal でのクエリの作成と結果の操作に関するレッスンです。
- 「[Get started with Azure Monitor log queries (Azure Monitor ログ クエリの開始)](get-started-queries.md)」は、Azure Monitor ログ データを使用するクエリの記述に関するレッスンです。

## <a name="concepts"></a>概念
- 「[Analyze log data in Azure Monitor (Azure Monitor でログ データを分析する)](../../azure-monitor/log-query/log-query-overview.md)」では、ログ クエリの概要と、Azure Monitor ログ データがどのように構成されているかについて説明しています。
- 「[Viewing and analyzing log data in Azure Monitor (Azure Monitor でのログ データの表示と分析)](../../azure-monitor/log-query/portals.md)」では、ログ クエリを作成して実行するポータルについて説明しています。

## <a name="reference"></a>リファレンス

- [クエリ言語リファレンス](/azure/kusto/query)は、Kusto クエリ言語の完全な言語リファレンスです。
- 「[Azure Monitor ログ クエリ言語の違い](data-explorer-difference.md)」では、Kusto クエリ言語のバージョンによる違いについて説明しています。
- 「[Standard properties in Azure Monitor log records (Azure Monitor ログ レコードの標準プロパティ)](../../azure-monitor/platform/log-standard-properties.md)」では、すべての Azure Monitor ログ データの標準プロパティについて説明しています。
- 「[Perform cross-resource log queries in Azure Monitor Azure (Monitor でのリソース間のログ クエリの実行)](../../azure-monitor/log-query/cross-workspace-query.md)」では、複数の Log Analytics ワークスペースと Application Insights アプリケーションのデータを使用するログ クエリを記述する方法について説明しています。


## <a name="examples"></a>例

- 「[Azure Monitor log query examples (Azure Monitor ログ クエリの例)](examples.md)」では、Azure Monitor ログ データを使用するクエリの例が提供されています。



## <a name="lessons"></a>レッスン

- 「[Working with strings in Azure Monitor log queries (Azure Monitor ログ クエリでの文字列の操作)](string-operations.md)」では、文字列データを操作する方法について説明しています。
- 「[Working with date time values in Azure Monitor log queries (Azure Monitor ログ クエリでの日付時刻値の操作)](datetime-operations.md)」では、日時データを操作する方法について説明しています。 
- 「[Aggregations in Azure Monitor log queries (Azure Monitor ログ クエリでの集計)](aggregations.md)」と「[Advanced aggregations in Azure Monitor log queries (Azure Monitor ログ クエリでの高度な集計)](advanced-aggregations.md)」では、データの集計と要約を行う方法について説明しています。
- 「[Joins in Azure Monitor log queries (Azure Monitor ログ クエリでの結合)](joins.md)」では、複数のテーブルからデータを結合する方法について説明しています。
- 「[Working with JSON and data Structures in Azure Monitor log queries (Azure Monitor ログ クエリでの JSON とデータ構造の操作)](json-data-structures.md)」では、JSON データを解析する方法について説明しています。
- 「[Writing advanced queries in Azure Monitor (Azure Monitor での高度なログ クエリの記述)](advanced-query-writing.md)」では、複雑なクエリを作成し、コードを再利用するための戦略について説明しています。
- 「[Creating charts and diagrams from Azure Monitor log queries (Azure Monitor ログ クエリからのグラフと図の作成)](charts.md)」では、ログ クエリからデータを視覚化する方法について説明しています。

## <a name="cheatsheets"></a>チートシート

-  [SQL と Azure Monitor ログ クエリ](sql-cheatsheet.md)に関する記事は、SQL を使い慣れているユーザーにとって役立ちます。
-  [Splunk と Azure Monitor ログ クエリ](sql-cheatsheet.md)に関する記事は、Splunk を使い慣れているユーザーに役立ちます。
 
## <a name="next-steps"></a>次の手順

- 完全な [Kusto クエリ言語のリファレンス ドキュメント](/azure/kusto/query/)にアクセスします。