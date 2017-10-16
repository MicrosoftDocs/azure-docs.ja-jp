---
title: "Azure Search サービスでの使用状況と統計の監視 | Microsoft Docs"
description: "Microsoft Azure のホスト型クラウド検索サービスである Azure Search のリソース使用量とインデックス サイズを追跡記録します。"
services: search
documentationcenter: 
author: bernitorres
manager: jlembicz
editor: 
tags: azure-portal
ms.assetid: 122948de-d29a-426e-88b4-58cbcee4bc23
ms.service: search
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 05/01/2017
ms.author: betorres
ms.openlocfilehash: 16cb5a1e16a59200f0e731622398efcf24c3f777
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="monitoring-an-azure-search-service"></a>Azure Search サービスの監視

Azure Search には検索サービスの使用状況とパフォーマンスを追跡する各種リソースが用意されています。 メトリック、ログ、インデックスの統計、および Power BI の監視拡張機能にアクセスできます。 この記事では、異なる監視戦略を有効にする方法と、結果のデータを解釈する方法について説明します。

## <a name="azure-search-metrics"></a>Azure Search のメトリック
メトリックを使用すると検索サービスについてほぼリアルタイムに把握でき、すべてのサービスで追加設定なしで使用できます。 サービスのパフォーマンスを最大で 30 日間追跡できます。

Azure Search は次の 3 つの異なるメトリックについてデータを収集します。

* 検索の待ち時間: 検索サービスが検索クエリを処理するために必要とした時間。分単位で集計。
* 1 秒あたりの検索クエリ数 (QPS): 1 秒間に受信した検索クエリの数。分単位で集計。
* スロットルされた検索クエリの割合: スロットルされた検索クエリの割合。分単位で集計。

![QPS アクティビティのスクリーン ショット][1]

### <a name="set-up-alerts"></a>アラートを設定する
メトリックの詳細ページで、メトリックが指定したしきい値を超えたときに電子メール通知または自動化されたアクションをトリガーするアラートを構成できます。

メトリックについて詳しくは、Azure Monitor の全ドキュメントをご覧ください。  

## <a name="how-to-track-resource-usage"></a>リソースの使用状況を追跡する方法
インデックスとドキュメントのサイズの増加率を追跡することで、サービスに設定した上限に達する前に、容量を事前に調整することができます。 これはポータルで実行、または REST API を使用してプログラムで実行できます。

### <a name="using-the-portal"></a>ポータルの使用

リソース使用量を監視するには、サービスのカウントと統計を[ポータル](https://portal.azure.com)に表示します。

1. [ポータル](https://portal.azure.com)にサインインします。
2. Azure Search サービスのサービス ダッシュボードを開きます。 [ホーム] ページに、サービスのタイルが表示されます。またはジャンプ バーの [参照] で、サービスを参照することもできます。

[使用] セクションには、使用可能なリソースのうち、現在使用中の割合を示すメーターがあります。 インデックス、ドキュメント、および記憶域のサービスごとの制限に関する情報については、「[サービスの制限](search-limits-quotas-capacity.md)」を参照してください。

  ![[使用量] タイル][2]

> [!NOTE]
> 上記のスクリーン ショットは、無料サービスのデータを示しています。無料サービスでは、最大 1 つのレプリカとパーティションを使用でき、3 つのインデックス、10,000 個のドキュメント、または 50 MB のデータのいずれかが所定の数値に達するまでホストされます。 Basic または Standard レベルで作成されたサービスでは、サービス制限ははるかに高くなります。 レベル選択の詳細については、「[SKU または価格レベルの選択](search-sku-tier.md)」を参照してください。
>
>

### <a name="using-the-rest-api"></a>REST API の使用
Azure Search REST API または .NET SDK を使用することにより、プログラムからサービス メトリックにアクセスできます。  [インデクサー](https://msdn.microsoft.com/library/azure/dn946891.aspx)を使用して Azure SQL Database または Azure Cosmos DB からのインデックスを読み込む際には、必要な数値を取得するために追加されている API を使用できます。

* [インデックス統計の取得](/rest/api/searchservice/get-index-statistics)
* [ドキュメントの数](/rest/api/searchservice/count-documents)
* [インデクサーの状態の取得](/rest/api/searchservice/get-indexer-status)

## <a name="how-to-export-logs-and-metrics"></a>ログとメトリックをエクスポートする方法

前のセクションで説明されているサービスの操作ログやメトリックの生データをエクスポートできます。 操作ログを使用すると、データがストレージ アカウントからコピーされるときに Power BI でサービスがどのように使用され消費できるかを把握できます。 Azure Search にはこの目的に使用できる監視用 Power BI コンテンツ パックが用意されています。


### <a name="enabling-monitoring"></a>監視を有効にする
[Azure Portal](http://portal.azure.com) の [監視を有効にする] オプションで Azure Search サービスを開きます。

エクスポートするデータを選択します (ログ、メトリック、または両方)。 データはストレージ アカウントにコピーする、イベント ハブに送信する、または Log Analytics にエクスポートできます。

![ポータルで監視を有効にする方法][3]

PowerShell または Azure CLI の使用を有効にする方法については、[こちら](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs#how-to-enable-collection-of-diagnostic-logs)のマニュアルをご覧ください。

### <a name="logs-and-metrics-schemas"></a>ログとメトリックのスキーマ
データをストレージ アカウントにコピーすると、データは JSON 形式でフォーマットされ、次の 2 つのコンテナーに置かれます。

* insights-logs-operationlogs: 検索トラフィックのログ用
* insights-metrics-pt1m: メトリック用

1 時間ごと、コンテナーごとに、1 つの BLOB があります。

例のパス: `resourceId=/subscriptions/<subscriptionID>/resourcegroups/<resourceGroupName>/providers/microsoft.search/searchservices/<searchServiceName>/y=2015/m=12/d=25/h=01/m=00/name=PT1H.json`

#### <a name="log-schema"></a>ログのスキーマ
ログ BLOB には、検索サービスのトラフィック ログが含まれています。
各 BLOB には、ログ オブジェクトの配列を含む、 **レコード** と呼ばれるルート オブジェクトが 1 つあります。
各 BLOB には、同じ時間帯に行われたすべての操作に関するレコードが含まれます。

| 名前 | 型 | 例 | メモ |
| --- | --- | --- | --- |
| time |datetime |"2015-12-07T00:00:43.6872559Z" |操作のタイムスタンプ |
| resourceId |string |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DEFAULT/PROVIDERS/<br/> MICROSOFT.SEARCH/SEARCHSERVICES/SEARCHSERVICE" |使用している ResourceId |
| operationName |string |"Query.Search" |操作の名前 |
| operationVersion |string |"2015-02-28" |使用されている API バージョン |
| カテゴリ |string |"OperationLogs" |定数 |
| resultType |string |"Success" |使用可能な値: Success または Failure |
| resultSignature |int |200 |HTTP の結果コード |
| durationMS |int |50 |操作時間 (ミリ秒) |
| プロパティ |オブジェクト |次の表を参照 |操作固有データを含むオブジェクト |

**プロパティのスキーマ**
| 名前 | 型 | 例 | メモ |
| --- | --- | --- | --- |
| Description |string |"GET /indexes('content')/docs" |操作のエンドポイント |
| クエリ |string |"?search=AzureSearch&$count=true&api-version=2015-02-28" |クエリ パラメーター |
| ドキュメント |int |42 |処理されたドキュメント数 |
| IndexName |string |"testindex" |操作に関連付けられているインデックスの名前 |

#### <a name="metrics-schema"></a>メトリックのスキーマ
| 名前 | 型 | 例 | メモ |
| --- | --- | --- | --- |
| resourceId |string |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DEFAULT/PROVIDERS/<br/>MICROSOFT.SEARCH/SEARCHSERVICES/SEARCHSERVICE" |使用しているリソース ID |
| metricName |string |"Latency" |メトリックの名前 |
| time |datetime |"2015-12-07T00:00:43.6872559Z" |操作のタイムスタンプ |
| average |int |64 |メトリックの時間間隔内の生のサンプルの平均値 |
| minimum |int |37 |メトリックの時間間隔内の生のサンプルの最小値 |
| maximum |int |78 |メトリックの時間間隔内の生のサンプルの最大値 |
| total |int |258 |メトリックの時間間隔内の生のサンプルの合計値 |
| count |int |4 |メトリックの生成に使用される生のサンプル数 |
| timegrain |string |"PT1M" |ISO 8601 でのメトリックの時間グレイン |

すべてのメトリックは、1 分間隔で報告されます。 各メトリックは、1 分あたりの最小値、最大値、および平均値を公開します。

SearchQueriesPerSecond メトリックの場合、最小値は、該当する 1 分間に登録された秒あたりの検索クエリ数のうち最小の値になります。 最大値も同様です。 平均値は、1 分間にわたって集計されます。
1 分間のうち、1 秒は非常に負荷が高く、続く 58 秒は平均的な負荷で、最後の 1 秒はクエリが 1 つだけというシナリオを考えてみましょう。この場合、最初の 1 秒の値が SearchQueriesPerSecond の最大値、最後の １ 秒の値が最小値となります。

ThrottledSearchQueriesPercentage の場合は、最小値、最大値、平均値、および合計値はすべて同じ値になります。これは、スロットルされた検索クエリの割合であり、1 分間の検索クエリの合計数に基づきます。

## <a name="analyzing-your-data-with-power-bi"></a>Power BI でデータを分析する

[Power BI](https://powerbi.microsoft.com) を使用して、データを検索および視覚化することをお勧めします。 Azure Storage アカウントに容易に接続し、データの分析を迅速に開始できます。

Azure Search には、検索トラフィックを事前定義されたグラフやテーブルで監視および理解できる [Power BI コンテンツ パック](https://app.powerbi.com/getdata/services/azure-search)が用意されています。 これには、データに自動的に接続し、検索サービスに関する情報を視覚化できる Power BI レポートのセットが含まれています。 詳しくは、[コンテンツ パックのヘルプ ページ](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-search/)をご覧ください。

![Azure Search の Power BI ダッシュ ボード][4]

## <a name="next-steps"></a>次のステップ
[レプリカとパーティションのスケーリング](search-limits-quotas-capacity.md)に関するページで、既存のサービスに対するパーティションとレプリカの割り当てのバランスをとる方法を検討します。

サービス管理の詳細については、[Microsoft Azure での Search サービスの管理](search-manage.md)に関する記事を、チューニング ガイダンスについては、[パフォーマンスと最適化](search-performance-optimization.md)に関する考慮事項を参照してください。

レポートの作成についての詳細。 「[Power BI Desktop の概要](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/)」をご覧ください。

<!--Image references-->
[1]: ./media/search-monitor-usage/AzSearch-Monitor-BarChart.PNG
[2]: ./media/search-monitor-usage/AzureSearch-Monitor1.PNG
[3]: ./media/search-monitor-usage/AzureSearch-Enable-Monitoring.PNG
[4]: ./media/search-monitor-usage/AzureSearch-PowerBI-Dashboard.png
