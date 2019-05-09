---
title: 検索サービスのリソースの使用状況とクエリのメトリックを監視する - Azure Search
description: ログ記録を有効にし、Azure Search サービスからクエリ アクティビティのメトリック、リソースの使用状況、およびその他のシステム データを取得します。
author: HeidiSteen
manager: cgronlun
tags: azure-portal
services: search
ms.service: search
ms.devlang: na
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: f4a0cba18f27c9cabfc03d1934469e6899c5cd18
ms.sourcegitcommit: e43ea344c52b3a99235660960c1e747b9d6c990e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/04/2019
ms.locfileid: "59010415"
---
# <a name="monitor-resource-consumption-and-query-activity-in-azure-search"></a>Azure Search でリソースの消費量とクエリ アクティビティを監視する

Azure Search サービスの [概要] ページでは、リソースの使用状況、クエリのメトリック、およびさらに多くのインデックス、インデクサー、データ ソースを作成するのに使用できるクォータについてのシステム データを見ることができます。 また、ポータルを使用して、ログ分析または永続的なデータ コレクションに使用される別のリソースを構成することもできます。 

ログを設定することは、自己診断と操作履歴の保持に役立ちます。 内部的には、ログがバックエンドに存在しているのは、ユーザーがサポート チケットを提出した場合に調査と分析が行われるのに十分な短い時間です。 ログ情報を制御してそれにアクセスする場合は、この記事で説明されているソリューションのいずれかを設定する必要があります。

この記事では、監視オプション、ログ記録とログ ストレージを有効にする方法、およびログの内容を表示する方法について説明します。

## <a name="metrics-at-a-glance"></a>メトリックの概要

[概要] ページの **[利用状況]** および **[監視]** セクションでは、リソースの消費量とクエリの実行に関するメトリックが提供されます。 この情報は、サービスを使い始めるとすぐに利用できるようになり、構成を行う必要はありません。 このページは、数分ごとに更新されます。 [運用環境のワークロードに使用するレベル](search-sku-tier.md)について、または[アクティブなレプリカとパーティションの数を調整する](search-capacity-planning.md)かどうかについて最終的に判断する場合、これらのメトリックを見ると、リソースが消費される速さや、現在の構成で既存の負荷がどの程度うまく処理されているかがわかるので、これらの決定を下すのに役立ちます。

**[利用状況]** タブには、現在の[制限](search-limits-quotas-capacity.md)を基準にしたリソースの可用性が表示されます。 次の図は、オブジェクトが種類ごとに 3 つ、ストレージが 50 MB に制限されている Free サービスを示したものです。 Basic または Standard サービスでは上限がこれより高く、パーティションの数を増やすと最大ストレージがそれに比例して増加します。

![有効な制限の基準とした利用状況](./media/search-monitor-usage/usage-tab.png
 "有効な制限の基準とした利用状況")

## <a name="queries-per-second-qps-and-other-metrics"></a>1 秒あたりのクエリ数 (QPS) およびその他のメトリック

**[監視]** タブでは、検索の *1 秒あたりのクエリ数* (QPS) などのメトリックが分単位で集計されて、その移動平均が表示されます。 
"*検索の待ち時間*" は、検索サービスが検索クエリを処理するのに要した時間を、分単位で集計したものです。 "*スロットルされた検索クエリの割合*" (示されていません) は、スロットルされた検索クエリの割合を、やはり分単位で集計したものです。

これらの値は概数であり、システムによる要求の処理の余裕についての概要を提供することが意図されています。 実際の QPS は、ポータルで報告された値より高い、または低い可能性があります。

![1 秒あたりのクエリ数アクティビティ](./media/search-monitor-usage/monitoring-tab.png "1 秒あたりのクエリ数アクティビティ")

## <a name="activity-logs"></a>アクティビティ ログ

**アクティビティ ログ**では、Azure Resource Manager から情報が収集されます。 アクティビティ ログでわかる情報の例としては、サービスの作成または削除、リソース グループの更新、名前を使用できるかどうかのチェック、要求を処理するためのサービス アクセス キーの取得などがあります。 

**アクティビティ ログ**には、左側のナビゲーション ウィンドウ、上部ウィンドウのコマンド バーの通知、または **[問題の診断と解決]** ページからアクセスすることができます。

インデックスの作成やデータ ソースの削除のような稼働中のタスクの場合は、各要求に対する "管理者キーの取得" のような一般的な通知は表示されますが、具体的なアクション自体は表示されません。 このレベルの情報については、アドオンの監視ソリューションを有効にする必要があります。

## <a name="add-on-monitoring-solutions"></a>アドオンの監視ソリューション

Azure Search では管理対象のオブジェクトの外部にデータは格納されません。つまり、ログ データは外部に保存する必要があります。 ログ データを保存したい場合は、次のリソースのいずれかを構成できます。 

次の表は、ログの格納、および Application Insights によるサービス操作とクエリ ワークロードの詳細監視の追加に関して、オプションを比較したものです。

| Resource | 使用対象 |
|----------|----------|
| [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) | アプリのユーザー イベントと関連付けられた、後述のスキーマに基づいてログに記録されるイベントとクエリ メトリック。 これは、アプリケーション コードによって送信されたフィルター要求ではなく、ユーザーが開始した検索からのイベントをマッピングする、ユーザーのアクションまたはシグナルが考慮された唯一のソリューションです。 この方法を使用するには、インストルメンテーション コードをコピーしてソース ファイルに貼り付け、要求の情報を Application Insights にルーティングします。 詳しくは、「[検索トラフィックの分析](search-traffic-analytics.md)」をご覧ください。 |
| [Azure Monitor ログ](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview) | 後述のスキーマに基づいてログに記録されるイベントとクエリ メトリック。 イベントは Log Analytics ワークスペースに記録されます。 ワークスペースに対してクエリを実行し、ログから詳細な情報を取得することができます。 詳細については、[Azure Monitor ログの使用](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-viewdata)に関するページを参照してください |
| [BLOB ストレージ](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) | 後述のスキーマに基づいてログに記録されるイベントとクエリ メトリック。 イベントのログは BLOB コンテナーに記録されて、JSON ファイルに格納されます。 ファイルの内容を表示するには JSON エディターを使用します。|
| [イベント ハブ](https://docs.microsoft.com/azure/event-hubs/) | この記事に記載されているスキーマに基づいてログに記録されるイベントとクエリ メトリック。 非常に大きなログに対する代替データ コレクション サービスとしては、これを選択します。 |

Azure Monitor ログと Blob Storage は、どちらも無料の共有サービスとして利用でき、Azure サブスクリプションの有効期間にわたって無料で試すことができます。 Application Insights は、アプリケーション データのサイズが一定の制限以下である限りは無料でサインアップして使用できます (詳しくは[価格のページ](https://azure.microsoft.com/pricing/details/monitor/)をご覧ください)。

次のセクションでは、Azure Blob Storage を有効にして使用し、Azure Search の操作によって作成されるログ データを収集してそれにアクセスする手順を説明します。

## <a name="enable-logging"></a>ログの有効化

インデックス作成とクエリのワークロードのログ記録は、既定では無効であり、ログ記録インフラストラクチャと長期外部ストレージ両方のアドオン ソリューションに依存します。 それだけでは、Azure Search で保持されるデータは作成および管理すされるオブジェクトだけなので、ログを別の場所に保存する必要があります。

このセクションでは、Blob Storage を使用して、ログに記録されたイベントとメトリック データを格納する方法を説明します。

1. まだない場合は、[ストレージ アカウントを作成](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)します。 この演習で使用したすべてのリソースを後で削除する場合にクリーンアップが簡単なように、Azure Search と同じリソース グループにそれを配置することができます。

2. 検索サービスの [概要] ページを開きます。 左側のナビゲーション ウィンドウで **[監視]** まで下にスクロールし、**[監視を有効にする]** をクリックします。

   ![監視を有効にする](./media/search-monitor-usage/enable-monitoring.png "監視を有効にする")

3. エクスポートするデータを選択します (ログ、メトリック、または両方)。 データはストレージ アカウントにコピーする、イベント ハブに送信する、または Azure Monitor ログにエクスポートすることができます。

   Blob Storage にアーカイブするには、ストレージ アカウントのみが存在する必要があります。 ログ データをエクスポートすると、コンテナーと BLOB が作成されます。

   ![Blob Storage アーカイブを構成する](./media/search-monitor-usage/configure-blob-storage-archive.png "Blob Storage アーカイブを構成する")

4. プロファイルを保存します。

5. オブジェクトを作成または削除し (ログ イベントの作成)、クエリを送信することで (メトリックの生成)、ログ記録をテストします。 

プロファイルを保存すると、ログ記録が有効になります。 コンテナーは、ログ記録または測定するアクティビティが発生した場合にのみ作成されます。 データをストレージ アカウントにコピーすると、データは JSON 形式でフォーマットされ、次の 2 つのコンテナーに置かれます。

* insights-logs-operationlogs: 検索トラフィックのログ用
* insights-metrics-pt1m: メトリック用

**Blob Storage にコンテナーが表示されるまで 1 時間かかります。 1 時間ごと、コンテナーごとに、1 つの BLOB があります。**

[Visual Studio Code](#download-and-open-in-visual-studio-code) または別の JSON エディターを使用して、ファイルを表示できます。 

### <a name="example-path"></a>パスの例

```
resourceId=/subscriptions/<subscriptionID>/resourcegroups/<resourceGroupName>/providers/microsoft.search/searchservices/<searchServiceName>/y=2018/m=12/d=25/h=01/m=00/name=PT1H.json
```

## <a name="log-schema"></a>ログのスキーマ
検索サービスのトラフィック ログが格納される BLOB は、このセクションで説明するような構成になっています。 各 BLOB には、ログ オブジェクトの配列を含む、**レコード**と呼ばれるルート オブジェクトが 1 つあります。 各 BLOB には、同じ時間帯に行われたすべての操作に関するレコードが含まれます。

| Name | Type | 例 | メモ |
| --- | --- | --- | --- |
| time |Datetime |"2018-12-07T00:00:43.6872559Z" |操作のタイムスタンプ |
| resourceId |string |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DEFAULT/PROVIDERS/<br/>  MICROSOFT.SEARCH/SEARCHSERVICES/SEARCHSERVICE" |使用している ResourceId |
| operationName |string |"Query.Search" |操作の名前 |
| operationVersion |string |"2017-11-11" |使用されている API バージョン |
| category |string |"OperationLogs" |定数 |
| resultType |string |"Success" |指定できる値成功または失敗 |
| resultSignature |int |200 |HTTP の結果コード |
| durationMS |int |50 |操作時間 (ミリ秒) |
| properties |object |次の表を参照 |操作固有データを含むオブジェクト |

**プロパティのスキーマ**

| Name | Type | 例 | メモ |
| --- | --- | --- | --- |
| 説明 |string |"GET /indexes('content')/docs" |操作のエンドポイント |
| クエリ |string |"?search=AzureSearch&$count=true&api-version=2017-11-11" |クエリ パラメーター |
| Documents |int |42 |処理されたドキュメント数 |
| IndexName |string |"testindex" |操作に関連付けられているインデックスの名前 |

## <a name="metrics-schema"></a>メトリックのスキーマ

メトリックはクエリ要求に対してキャプチャされます。

| Name | Type | 例 | メモ |
| --- | --- | --- | --- |
| resourceId |string |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DEFAULT/PROVIDERS/<br/> MICROSOFT.SEARCH/SEARCHSERVICES/SEARCHSERVICE" |使用しているリソース ID |
| metricName |string |"Latency" |メトリックの名前 |
| time |Datetime |"2018-12-07T00:00:43.6872559Z" |操作のタイムスタンプ |
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

## <a name="download-and-open-in-visual-studio-code"></a>ダウンロードして Visual Studio Code で開く

任意の JSON エディターを使用して、ログ ファイルを表示できます。 お持ちでない場合は、[Visual Studio Code](https://code.visualstudio.com/download) をお勧めします。

1. Azure portal でストレージ アカウントを開きます。 

2. 左側のナビゲーション ウィンドウで、**[BLOB]** をクリックします。 **insights-logs-operationlogs** と **insights-metrics-pt1m** が表示されるはずです。 これらのコンテナーは、ログ データが Blob Storage にエクスポートされるときに、Azure Search によって作成されます。

3. .json ファイルが表示されるまで、フォルダー階層を下に移動します。  コンテキスト メニューを使用して、ファイルをダウンロードします。

ファイルのダウンロードが済んだら、JSON エディターで開いて内容を表示します。

## <a name="use-system-apis"></a>システム API を使用する
Azure Search REST API または .NET SDK を使用することにより、プログラムでサービス メトリック、インデックスとインデクサーの情報、ドキュメント数にアクセスできます。

* [サービスの統計情報の取得](/rest/api/searchservice/get-service-statistics)
* [インデックス統計の取得](/rest/api/searchservice/get-index-statistics)
* [ドキュメントのカウント](/rest/api/searchservice/count-documents)
* [インデクサー状態の取得](/rest/api/searchservice/get-indexer-status)

PowerShell または Azure CLI の使用を有効にする方法については、[こちら](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs#how-to-enable-collection-of-diagnostic-logs)のマニュアルをご覧ください。

## <a name="next-steps"></a>次の手順

サービス管理の詳細については、[Microsoft Azure での Search サービスの管理](search-manage.md)に関する記事、チューニング ガイダンスについては、[パフォーマンスと最適化](search-performance-optimization.md)に関する記事。