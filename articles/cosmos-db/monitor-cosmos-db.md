---
title: Azure Cosmos DB の監視 | Microsoft Docs
description: Azure Cosmos DB のパフォーマンスと可用性を監視する方法について説明します。
author: SnehaGunda
services: cosmos-db
ms.service: cosmos-db
ms.topic: how-to
ms.date: 12/01/2020
ms.author: sngun
ms.custom: subject-monitoring
ms.openlocfilehash: f18d1850cb6ccf28ff70f826e3d4bfe74ae05c40
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102178730"
---
# <a name="monitor-azure-cosmos-db"></a>Azure Cosmos DB を監視する
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure リソースに依存するクリティカルなアプリケーションとビジネス プロセスがある場合は、それらのリソースの可用性、パフォーマンス、操作を監視する必要があります。 この記事では、Azure Cosmos データベースによって生成される監視データと、Azure Monitor の機能を使用してこのデータについての分析とアラートを行う方法について説明します。

クライアント側のメトリックとサーバー側のメトリックを使用してデータを監視することができます。 サーバー側のメトリックを使用する場合、Azure Cosmos DB に格納されているデータを監視するには、次のオプションを使用します。

* **Azure Cosmos DB ポータルからの監視:** Azure Cosmos アカウントの **[メトリック]** タブ内で使用可能なメトリックを使用して監視できます。 このタブのメトリックには、スループット、ストレージ、可用性、待機時間、一貫性、システム レベルのメトリックが含まれます。 既定では、これらのメトリックの保有期間は 7 日間です。 詳細については、この記事の「[Azure Cosmos DB から収集したデータの監視](#monitoring-from-azure-cosmos-db)」を参照してください。

* **Azure Monitor のメトリックを使用した監視:** Azure Cosmos アカウントのメトリックを監視し、Azure Monitor からダッシュボードを作成することができます。 既定では、Azure Monitor は Azure Cosmos DB メトリックを収集するため、何も明示的に構成する必要はありません。 これらのメトリックは 1 分単位で収集されます。粒度は、選択したメトリックによって異なる場合があります。 既定では、これらのメトリックの保有期間は 30 日間です。 前のオプションで使用できるほとんどのメトリックも、これらのメトリックで利用できます。 メトリックのディメンション値 (コンテナー名など) は、大文字と小文字が区別されません。 そのため、これらのディメンション値に対して文字列比較を行う場合は、大文字と小文字を区別しない比較を使用する必要があります。 詳細については、この記事の「[メトリック データの分析」](#analyze-metric-data)を参照してください。

* **Azure Monitor の診断ログを使用した監視:** Azure Cosmos アカウントのログを監視し、Azure Monitor からダッシュボードを作成することができます。 1 秒単位で発生するイベントやトレースなどのテレメトリは、ログとして格納されます。 たとえば、コンテナーのスループットが変化した場合、Cosmos アカウントのプロパティが変更されます。これらのイベントはログ内でキャプチャされます。 収集したデータに対してクエリを実行することで、これらのログを分析できます。 詳細については、この記事の「[ログ データの分析](#analyze-log-data)」を参照してください。

* **SDK を使用したプログラムによる監視:** .NET、Java、Python、Node.js SDK、および REST API のヘッダーを使用して、Azure Cosmos アカウントをプログラムによって監視できます。 詳細については、この記事の「[Azure Cosmos DB をプログラムで監視する](#monitor-cosmosdb-programmatically)」セクションを参照してください。

次の図は、Azure portal を通じて Azure Cosmos DB アカウントを監視するために使用できるさまざまなオプションを示しています。

:::image type="content" source="media/monitor-cosmos-db/monitoring-options-portal.png" alt-text="Azure portal で使用可能な監視オプション" border="false":::

Azure Cosmos DB を使用すると、クライアント側で、要求の料金、アクティビティ ID、例外/スタック トレース情報、HTTP ステータス/サブステータス コード、診断文字列の詳細を収集して、発生する可能性のある問題をデバッグできます。 この情報は、Azure Cosmos DB サポート チームに問い合わせる必要がある場合にも必要です。 

## <a name="monitor-overview"></a>監視の概要

各 Azure Cosmos DB アカウントの Azure portal の **[概要]** ページには、要求の合計、特定の HTTP ステータス コードになった要求、時間ごとの課金など、リソースの使用状況の要約が表示されます。 この情報は役に立ちますが、このペインで入手できる監視データの量はごくわずかです。 このデータの一部は自動的に収集され、リソースを作成するとすぐに分析に使用できるようになります。 一定の構成によって追加の種類のデータ収集を有効にすることができます。

## <a name="what-is-azure-monitor"></a>Azure Monitor とは

Azure Cosmos DB では、[Azure Monitor](../azure-monitor/overview.md) を使用して監視データを作成します。Azure Monitor は Azure のフルスタックの監視サービスであり、他のクラウドやオンプレミスのリソースに加えて、Azure リソースを監視するための完全な機能セットが提供されます。

Azure サービスの監視について理解が十分でない場合は、まず「[Azure Monitor を使用した Azure リソースの監視](../azure-monitor/essentials/monitor-azure-resource.md)」の記事にある次の概念の説明をお読みください。

* Azure Monitor とは
* 監視に関連するコスト
* Azure で収集される監視データ
* データ収集の構成
* 監視データの分析とアラート生成のための Azure の標準ツール

以下のセクションでは、この記事を基に、Azure Cosmos DB から収集される特定のデータについて説明します。また、Azure のツールを使用してデータの収集を構成し、このデータを分析するための例を示します。

## <a name="azure-monitor-for-azure-cosmos-db"></a>Azure Monitor for Azure Cosmos DB

Azure Monitor for Azure Cosmos DB は、[Azure Monitor のブック機能](../azure-monitor/visualize/workbooks-overview.md)に基づいており、以下のセクションで説明している、Azure Cosmos DB のために収集されるのと同じ監視データを使用します。 Azure Monitor は、すべての Azure Cosmos DB リソースの全体的なパフォーマンス、障害、容量、および運用の正常性を、一元的な対話型エクスペリエンスで表示するために使用します。また、詳細な分析とアラート生成のために、Azure Monitor のその他の機能を利用します。 詳細については、[Azure Monitor for Azure Cosmos DB の詳細](../azure-monitor/insights/cosmosdb-insights-overview.md)に関するページを参照してください。

> [!NOTE]
> コンテナーを作成するときは、同じ名前で大文字と小文字が異なる 2 つのコンテナーを作成しないようにしてください。 これは、Azure プラットフォームの一部で大文字と小文字が区別されないため、このような名前のコンテナーでテレメトリとアクションの混同や衝突が発生する可能性があるためです。

## <a name="monitoring-data"></a><a id="monitoring-from-azure-cosmos-db"></a> データの監視 

Azure Cosmos DB は、他の Azure リソースと同じ種類の監視データを収集します。これについては、[Azure リソースの監視データ](../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data)に関する記事を参照してください。 Azure Cosmos DB によって作成されるログおよびメトリックの詳細なリファレンスについては、[Azure Cosmos DB 監視データ リファレンス](monitor-cosmos-db-reference.md)を参照してください。

Azure portal で、各 Azure Cosmos データベースの **[概要]** ページでは、データベースの要求や時間ごとの課金使用量など、データベースの使用状況の要約を表示します。 これは有用な情報ですが、見ることのできる監視データはごくわずかです。 このデータの一部は自動的に収集され、データベースを作成するとすぐに分析に使用できるようになりますが、一部の構成では追加のデータ収集を有効にすることができます。

:::image type="content" source="media/monitor-cosmos-db/overview-page.png" alt-text="[概要] ページ":::

## <a name="collection-and-routing"></a>収集とルーティング

プラットフォーム メトリックとアクティビティ ログは自動的に収集および格納されますが、診断設定を使用して他の場所にルーティングすることもできます。  

リソース ログは、診断設定を作成して 1 つ以上の場所にルーティングするまでは収集および格納されません。

Azure portal といくつかの診断クエリ例を使用して診断設定を作成するプロセスの詳細については、「[Azure でプラットフォーム ログとメトリックを収集するための診断設定を作成する](cosmosdb-monitor-resource-logs.md)」を参照してください。 診断設定を作成するときは、収集するログのカテゴリを指定します。

収集できるメトリックとログについては、次のセクションで説明します。

## <a name="analyzing-metrics"></a><a id="analyze-metric-data"></a> メトリックの分析

Azure Cosmos DB は、メトリックを操作するためのカスタム エクスペリエンスを提供します。 **Azure Monitor** のメニューから **[Metrics]\(メトリック\)** を開き、メトリックス エクスプローラーを使用して、Azure Cosmos DB のメトリックを、他の Azure サービスからのメトリックと一緒に分析することができます。 このツールの使用方法の詳細については、「[Azure メトリックス エクスプローラーの概要](../azure-monitor/essentials/metrics-getting-started.md)」を参照してください。 また、Azure Cosmos DB リソースの[サーバー側の待機時間](monitor-server-side-latency.md)、[要求ユニットの使用状況](monitor-request-unit-usage.md)、[正規化された要求ユニットの使用状況](monitor-normalized-request-units.md)を監視する方法についても確認できます。

Azure Cosmos DB 用に収集されるプラットフォーム メトリックの一覧については、[「Azure Cosmos DB 監視データのリファレンス」記事の「メトリック」](monitor-cosmos-db-reference.md#metrics)を参照してください。

Azure Cosmos DB のすべてのメトリックは、**Cosmos DB standard metrics** (Cosmos DB 標準メトリック) 名前空間に含まれています。 フィルターをグラフに追加するときは、次のディメンションをこれらのメトリックと共に使用できます。

* CollectionName
* DatabaseName
* OperationType
* リージョン
* StatusCode

参考のために、[Azure Monitor でサポートされているすべてのリソース メトリック](../azure-monitor/essentials/metrics-supported.md)の一覧を確認できます。

### <a name="view-operation-level-metrics-for-azure-cosmos-db"></a>Azure Cosmos DB の操作レベルのメトリックを表示する

1. [Azure portal](https://portal.azure.com/) にサインインします。

1. 左側のナビゲーション バーから **[監視]** を選択し、 **[メトリック]** を選択します。

   :::image type="content" source="./media/monitor-cosmos-db/monitor-metrics-blade.png" alt-text="Azure Monitor のメトリック ペイン":::

1. **[メトリック]** ウィンドウから、 **[リソースの選択]** を選択し、必要な **サブスクリプション** と **リソース グループ** を選択します。 **[リソースの種類]** で、 **[Azure Cosmos DB accounts]\(Azure Cosmos DB アカウント\)** を選択し、既存の Azure Cosmos アカウントの一つを選択し、 **[適用]** を選択します。

   :::image type="content" source="./media/monitor-cosmos-db/select-cosmosdb-account.png" alt-text="メトリックを表示する Cosmos DB アカウントの選択":::

1. 次に、使用可能なメトリックの一覧からメトリックを選択できます。 要求ユニット、ストレージ、待機時間、可用性、Cassandra などに固有のメトリックを選択できます。 この一覧で使用可能なすべてのメトリックの詳細については、「[カテゴリ別のメトリック](monitor-cosmos-db-reference.md)」の記事を参照してください。 この例では、 **[要求ユニット]** および集計値として **[Avg]** を選択します。

   これらの詳細に加えて、メトリックの **[時間の範囲]** と **[時間の粒度]** を選択することもできます。 最大で、過去 30 日間のメトリックを表示できます。  フィルターを適用すると、そのフィルターに基づいてグラフが表示されます。 選択した期間に消費された要求ユニットの 1 分あたりの平均数を確認できます。  

   :::image type="content" source="./media/monitor-cosmos-db/metric-types.png" alt-text="Azure portal からのメトリックの選択":::

### <a name="add-filters-to-metrics"></a>メトリックにフィルターを追加する

メトリックと、特定の **CollectionName**、**DatabaseName**、**OperationType**、**Region**、および **StatusCode** によって表示されるグラフをフィルターすることもできます。 メトリックにフィルターを適用するには、 **[フィルターの追加]** を選択し、**OperationType** などの必要なプロパティを選択し、**Query** などの値を選択します。 その後グラフには、選択した期間のクエリ操作で消費された要求ユニットが表示されます。 ストアド プロシージャを介して実行された操作は、ログに記録されないため、OperationType メトリックでは使用できません。

:::image type="content" source="./media/monitor-cosmos-db/add-metrics-filter.png" alt-text="メトリック細分性を選択するためのフィルターの追加":::

**[Apply splitting]\(分割の適用\)** オプションを使用すると、メトリックをグループ化できます。 たとえば、次の図に示すように、要求ユニットを操作の種類ごとにグループ化し、すべての操作のグラフを一度に表示できます。

:::image type="content" source="./media/monitor-cosmos-db/apply-metrics-splitting.png" alt-text="分割の適用フィルターの追加":::

## <a name="analyzing-logs"></a><a id="analyze-log-data"></a> ログの分析

Azure Monitor ログのデータはテーブルに格納され、各テーブルには独自の一意のプロパティ セットがあります。

Azure Monitor 内のすべてのリソース ログには、同じフィールドの後にサービス固有のフィールドがあります。 共通のスキーマの概要については、[Azure Monitor リソース ログのスキーマ](../azure-monitor/essentials/resource-logs-schema.md#top-level-common-schema)に関する記事をご覧ください。 Azure Cosmos DB 用に収集されるリソース ログの種類の一覧については、「[Azure Cosmos DB 監視データのリファレンス](monitor-cosmos-db-reference.md#resource-logs)」を参照してください。

[アクティビティ ログ](../azure-monitor/essentials/activity-log.md)は、サブスクリプション レベルのイベントの分析情報を提供するプラットフォーム ログイン Azure です。 個別に表示できるほか、Azure Monitor ログにルーティングして、Log Analytics を使用してより複雑なクエリを実行することもできます。  

Azure Cosmos DB は、次のテーブルにデータを格納します。

| テーブル | 説明 |
|:---|:---|
| AzureDiagnostics | リソース ログを格納するために複数のサービスによって使用される共通テーブル。 Azure Cosmos DB のリソース ログは `MICROSOFT.DOCUMENTDB` で識別できます。   |
| AzureActivity    | アクティビティ ログのすべてのレコードを格納する共通テーブル。

### <a name="sample-kusto-queries"></a>サンプル Kusto クエリ

> [!IMPORTANT]
> Azure Cosmos DB のメニューから **[ログ]** を選択すると、クエリのスコープが現在の Azure Cosmos DB アカウントに設定された状態で Log Analytics が開きます。 つまり、ログ クエリには、そのリソースからのデータのみが含まれます。 他のアカウントのデータや他の Azure サービスのデータを含むクエリを実行する場合は、 **[Azure Monitor]** メニューから **[ログ]** を選択します。 詳細については、「[Azure Monitor Log Analytics のログ クエリのスコープと時間範囲](../azure-monitor/logs/scope.md)」を参照してください。

**[ログ検索]** 検索バーに入力して Azure Cosmos リソースの監視に利用できるクエリを紹介します。 これらのクエリは[新しい言語](../azure-monitor/logs/log-query-overview.md)で使用できます。

* 指定した期間の Azure Cosmos DB のすべての診断ログをクエリするには:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="Microsoft.DocumentDb" and Category=="DataPlaneRequests"

    ```

* リソースでグループ分けされたすべての操作をクエリするには:

    ```Kusto
    AzureActivity 
    | where ResourceProvider=="Microsoft.DocumentDb" and Category=="DataPlaneRequests" 
    | summarize count() by Resource

    ```

* リソースでグループ分けされたすべてのユーザー アクティビティをクエリするには:

    ```Kusto
    AzureActivity 
    | where Caller == "test@company.com" and ResourceProvider=="Microsoft.DocumentDb" and Category=="DataPlaneRequests" 
    | summarize count() by Resource
    ```

## <a name="alerts"></a>警告

Azure Monitor のアラートは、監視データで重要な状態が見つかると事前に通知します。 これにより、ユーザーが気付く前に、管理者が問題を識別して対処できます。 アラートは[メトリック](../azure-monitor/alerts/alerts-metric-overview.md)、[ログ](../azure-monitor/alerts/alerts-unified-log.md)、[アクティビティ ログ](../azure-monitor/alerts/activity-log-alerts.md)に対して設定できます。 アラートの種類に応じて、さまざまな利点と欠点があります。

例として、リソースに関するアラート ルールをいくつか次の表に示します。 アラート ルールの詳細な一覧は、Azure portal にあります。 詳細については、[アラートの構成方法](create-alerts.md)に関する記事を参照してください。  

| アラートの種類 | 条件 | 説明  |
|:---|:---|:---|
|要求ユニットのレート制限 (メトリック アラート) |ディメンション名:StatusCode、演算子: Equals、ディメンション値: 429  | コンテナーまたはデータベースが、プロビジョニングされたスループット制限を超えるとアラートが表示されます。 |
|リージョンのフェールオーバー |演算子:Greater than、集計の種類: カウント、しきい値: 1 | 1 つのリージョンがフェールオーバーされたとき。 このアラートは、自動フェールオーバーを有効にしなかった場合に役立ちます。 |
| キーのローテーション (アクティビティ ログ アラート)| イベント レベル: 情報、ステータス: 開始| アカウント キーがローテーションされるとアラートが表示されます。 新しいキーを使用してアプリケーションを更新できます。 |

## <a name="monitor-azure-cosmos-db-programmatically"></a><a id="monitor-cosmosdb-programmatically"></a> Azure Cosmos DB をプログラムで監視する

ポータルで使用できるアカウント レベルのメトリック (アカウント ストレージの使用状況、要求総数) は、SQL API で使用することはできません。 ただし、SQL API を使用してコレクション レベルで使用状況データを取得できます。 コレクション レベルのデータを取得するには、次の操作を行います。

* REST API を使用するには、 [コレクションに対して GET を実行](/rest/api/cosmos-db/get-a-collection)します。 コレクションのクォータおよび使用状況の情報が、応答の x-ms-resource-quota および x-ms-resource-usage ヘッダーに返されます。

* .NET SDK を使用するには、[DocumentClient.ReadDocumentCollectionAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readdocumentcollectionasync) メソッドを使用します。これは、**CollectionSizeUsage**、**DatabaseUsage**、**DocumentUsage** などの多数の使用状況プロパティを含む [ResourceResponse](/dotnet/api/microsoft.azure.documents.client.resourceresponse-1) を返します。

その他のメトリックにアクセスするには、 [Azure Monitor SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights)を使用します。 使用できるメトリック定義は、次の URL を呼び出すことで取得できます。

```http
https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/providers/microsoft.insights/metricDefinitions?api-version=2018-01-01
```

メトリックを個別に取得する場合には、次の形式を使用します。

```http
https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/providers/microsoft.insights/metrics?timespan={StartTime}/{EndTime}&interval={AggregationInterval}&metricnames={MetricName}&aggregation={AggregationType}&`$filter={Filter}&api-version=2018-01-01
```

詳細については、[Azure 監視 REST API](../azure-monitor/essentials/rest-api-walkthrough.md) に関する記事をご覧ください。

## <a name="next-steps"></a>次のステップ

* Azure Cosmos DB によって作成されるログおよびメトリックのリファレンスについては、[Azure Cosmos DB 監視データ リファレンス](monitor-cosmos-db-reference.md)を参照してください。
* Azure リソースの監視の詳細については、「[Azure Monitor を使用した Azure リソースの監視](../azure-monitor/essentials/monitor-azure-resource.md)」を参照してください。
