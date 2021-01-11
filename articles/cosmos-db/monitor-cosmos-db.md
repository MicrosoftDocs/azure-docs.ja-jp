---
title: Azure Cosmos DB の監視 | Microsoft Docs
description: Azure Cosmos DB のパフォーマンスと可用性を監視する方法について説明します。
author: bwren
services: cosmos-db
ms.service: cosmos-db
ms.topic: how-to
ms.date: 08/24/2020
ms.author: bwren
ms.custom: subject-monitoring
ms.openlocfilehash: 12bf87e16bf4506f2015dd75fb360f8de8399902
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2020
ms.locfileid: "88797821"
---
# <a name="monitoring-azure-cosmos-db"></a>Azure Cosmos DB の監視

Azure リソースに依存するクリティカルなアプリケーションとビジネス プロセスがある場合は、それらのリソースの可用性、パフォーマンス、操作を監視する必要があります。 この記事では、Azure Cosmos データベースによって生成される監視データと、Azure Monitor の機能を使用してこのデータについての分析とアラートを行う方法について説明します。

クライアント側のメトリックとサーバー側のメトリックを使用してデータを監視することができます。 サーバー側のメトリックを使用する場合、Azure Cosmos DB に格納されているデータを監視するには、次のオプションを使用します。

* **Azure Cosmos DB ポータルからの監視:** Azure Cosmos アカウントの **[メトリック]** タブ内で使用可能なメトリックを使用して監視できます。 このタブのメトリックには、スループット、ストレージ、可用性、待機時間、一貫性、システム レベルのメトリックが含まれます。 既定では、これらのメトリックの保有期間は 7 日間です。 詳細については、この記事の「[Azure Cosmos DB から収集したデータの監視](#monitoring-from-azure-cosmos-db)」を参照してください。

* **Azure Monitor のメトリックを使用した監視:** Azure Cosmos アカウントのメトリックを監視し、Azure Monitor からダッシュボードを作成することができます。 既定では、Azure Monitor は Azure Cosmos DB メトリックを収集するため、何も明示的に構成する必要はありません。 これらのメトリックは 1 分単位で収集されます。粒度は、選択したメトリックによって異なる場合があります。 既定では、これらのメトリックの保有期間は 30 日間です。 前のオプションで使用できるほとんどのメトリックも、これらのメトリックで利用できます。 メトリックのディメンション値 (コンテナー名など) は、大文字と小文字が区別されません。 そのため、これらのディメンション値に対して文字列比較を行う場合は、大文字と小文字を区別しない比較を使用する必要があります。 詳細については、この記事の「[メトリック データの分析」](#analyze-metric-data)を参照してください。

* **Azure Monitor の診断ログを使用した監視:** Azure Cosmos アカウントのログを監視し、Azure Monitor からダッシュボードを作成することができます。 1 秒単位で発生するイベントやトレースなどのテレメトリは、ログとして格納されます。 たとえば、コンテナーのスループットが変化した場合、Cosmos アカウントのプロパティが変更されます。これらのイベントはログ内でキャプチャされます。 収集したデータに対してクエリを実行することで、これらのログを分析できます。 詳細については、この記事の「[ログ データの分析](#analyze-log-data)」を参照してください。

* **SDK を使用したプログラムによる監視:** .NET、Java、Python、Node.js SDK、および REST API のヘッダーを使用して、Azure Cosmos アカウントをプログラムによって監視できます。 詳細については、この記事の「[Azure Cosmos DB をプログラムで監視する](#monitor-cosmosdb-programmatically)」セクションを参照してください。

次の図は、Azure portal を通じて Azure Cosmos DB アカウントを監視するために使用できるさまざまなオプションを示しています。

:::image type="content" source="media/monitor-cosmos-db/monitoring-options-portal.png" alt-text="Azure portal で使用可能な監視オプション" border="false":::

Azure Cosmos DB を使用すると、クライアント側で、要求の料金、アクティビティ ID、例外/スタック トレース情報、HTTP ステータス/サブステータス コード、診断文字列の詳細を収集して、発生する可能性のある問題をデバッグできます。 この情報は、Azure Cosmos DB サポート チームに問い合わせる必要がある場合にも必要です。  

## <a name="what-is-azure-monitor"></a>Azure Monitor とは

Azure Cosmos DB では、[Azure Monitor](../azure-monitor/overview.md) を使用して監視データを作成します。Azure Monitor は Azure のフルスタックの監視サービスであり、他のクラウドやオンプレミスのリソースに加えて、Azure リソースを監視するための完全な機能セットが提供されます。

Azure サービスの監視について理解が十分でない場合は、まず「[Azure Monitor を使用した Azure リソースの監視](../azure-monitor/insights/monitor-azure-resource.md)」の記事にある次の事項の説明をお読みください。

* Azure Monitor とは
* 監視に関連するコスト
* Azure で収集される監視データ
* データ収集の構成
* 監視データの分析とアラート生成のための Azure の標準ツール

以下のセクションでは、この記事を基に、Azure Cosmos DB から収集される特定のデータについて説明します。また、Azure のツールを使用してデータの収集を構成し、このデータを分析するための例を示します。

## <a name="azure-monitor-for-azure-cosmos-db"></a>Azure Monitor for Azure Cosmos DB

Azure Monitor for Azure Cosmos DB は、[Azure Monitor のブック機能](../azure-monitor/platform/workbooks-overview.md)に基づいており、以下のセクションで説明している、Cosmos DB のために収集されるのと同じ監視データを使用します。 Azure Monitor は、すべての Azure Cosmos DB リソースの全体的なパフォーマンス、障害、容量、および運用の正常性を、一元的な対話型エクスペリエンスで表示するために使用します。また、詳細な分析とアラート生成のために、Azure Monitor のその他の機能を利用します。 詳細については、[Azure Monitor for Azure Cosmos DB の詳細](../azure-monitor/insights/cosmosdb-insights-overview.md)に関するページを参照してください。

> [!NOTE]
> コンテナーを作成するときは、同じ名前で大文字と小文字が異なる 2 つのコンテナーを作成しないようにしてください。 これは、Azure プラットフォームの一部で大文字と小文字が区別されないため、このような名前のコンテナーでテレメトリとアクションの混同や衝突が発生する可能性があるためです。

## <a name="monitor-data-collected-from-azure-cosmos-db-portal"></a><a id="monitoring-from-azure-cosmos-db"></a> Azure Cosmos DB ポータルから収集したデータの監視

Azure Cosmos DB は、他の Azure リソースと同じ種類の監視データを収集します。これについては、[Azure リソースの監視データ](../azure-monitor/insights/monitor-azure-resource.md#monitoring-data)に関する記事を参照してください。 Azure Cosmos DB によって作成されるログおよびメトリックの詳細なリファレンスについては、[Azure Cosmos DB 監視データ リファレンス](monitor-cosmos-db-reference.md)を参照してください。

Azure portal で、各 Azure Cosmos データベースの **[概要]** ページでは、データベースの要求や時間ごとの課金使用量など、データベースの使用状況の要約を表示します。 これは有用な情報ですが、見ることのできる監視データはごくわずかです。 このデータの一部は自動的に収集され、データベースを作成するとすぐに分析に使用できるようになりますが、一部の構成では追加のデータ収集を有効にすることができます。

:::image type="content" source="media/monitor-cosmos-db/overview-page.png" alt-text="[概要] ページ":::

## <a name="analyzing-metric-data"></a><a id="analyze-metric-data"></a> メトリック データの分析メトリック データの分析

Azure Cosmos DB は、メトリックを操作するためのカスタム エクスペリエンスを提供します。 このエクスペリエンスの使用と、さまざまな Azure Cosmos DB シナリオの分析の詳細については、[Azure Monitor からの Azure Cosmos DB メトリックの監視とデバッグ](cosmos-db-azure-monitor-metrics.md)に関するページを参照してください。

**Azure Monitor** のメニューから **[Metrics]\(メトリック\)** を開き、メトリックス エクスプローラーを使用して、Azure Cosmos DB のメトリックを、他の Azure サービスからのメトリックと一緒に分析することができます。 このツールの使用方法の詳細については、「[Azure メトリックス エクスプローラーの概要](../azure-monitor/platform/metrics-getting-started.md)」を参照してください。 Azure Cosmos DB のすべてのメトリックは、**Cosmos DB standard metrics** (Cosmos DB 標準メトリック) 名前空間に含まれています。 フィルターをグラフに追加するときは、次のディメンションをこれらのメトリックと共に使用できます。

* CollectionName
* DatabaseName
* OperationType
* リージョン
* StatusCode

### <a name="view-operation-level-metrics-for-azure-cosmos-db"></a>Azure Cosmos DB の操作レベルのメトリックを表示する

1. [Azure portal](https://portal.azure.com/) にサインインします。

1. 左側のナビゲーション バーから **[監視]** を選択し、 **[メトリック]** を選択します。

   :::image type="content" source="./media/monitor-cosmos-db/monitor-metrics-blade.png" alt-text="Azure Monitor のメトリック ペイン":::

1. **[メトリック]** ウィンドウから、 **[リソースの選択]** を選択し、必要な**サブスクリプション**と**リソース グループ**を選択します。 **[リソースの種類]** で、 **[Azure Cosmos DB accounts]\(Azure Cosmos DB アカウント\)** を選択し、既存の Azure Cosmos アカウントの一つを選択し、 **[適用]** を選択します。

   :::image type="content" source="./media/monitor-cosmos-db/select-cosmosdb-account.png" alt-text="メトリックを表示する Cosmos DB アカウントの選択":::

1. 次に、使用可能なメトリックの一覧からメトリックを選択できます。 要求ユニット、ストレージ、待機時間、可用性、Cassandra などに固有のメトリックを選択できます。 この一覧で使用可能なすべてのメトリックの詳細については、「[カテゴリ別のメトリック](monitor-cosmos-db-reference.md)」の記事を参照してください。 この例では、 **[要求ユニット]** および集計値として **[Avg]** を選択します。

   これらの詳細に加えて、メトリックの **[時間の範囲]** と **[時間の粒度]** を選択することもできます。 最大で、過去 30 日間のメトリックを表示できます。  フィルターを適用すると、そのフィルターに基づいてグラフが表示されます。 選択した期間に消費された要求ユニットの 1 分あたりの平均数を確認できます。  

   :::image type="content" source="./media/monitor-cosmos-db/metric-types.png" alt-text="Azure portal からのメトリックの選択":::

### <a name="add-filters-to-metrics"></a>メトリックにフィルターを追加する

メトリックと、特定の **CollectionName**、**DatabaseName**、**OperationType**、**Region**、および **StatusCode** によって表示されるグラフをフィルターすることもできます。 メトリックにフィルターを適用するには、 **[フィルターの追加]** を選択し、**OperationType** などの必要なプロパティを選択し、**Query** などの値を選択します。 その後グラフには、選択した期間のクエリ操作で消費された要求ユニットが表示されます。 ストアド プロシージャを介して実行された操作は、ログに記録されないため、OperationType メトリックでは使用できません。

:::image type="content" source="./media/monitor-cosmos-db/add-metrics-filter.png" alt-text="メトリック細分性を選択するためのフィルターの追加":::

**[Apply splitting]\(分割の適用\)** オプションを使用すると、メトリックをグループ化できます。 たとえば、次の図に示すように、要求ユニットを操作の種類ごとにグループ化し、すべての操作のグラフを一度に表示できます。

:::image type="content" source="./media/monitor-cosmos-db/apply-metrics-splitting.png" alt-text="分割の適用フィルターの追加":::

## <a name="analyzing-log-data"></a><a id="analyze-log-data"></a> ログ データの分析

Azure Monitor のログのデータはテーブルに格納され、各テーブルには独自の一意のプロパティ セットがあります。 Azure Cosmos DB は、次のテーブルにデータを格納します。

| テーブル | 説明 |
|:---|:---|
| AzureDiagnostics | リソース ログを格納するために複数のサービスによって使用される共通テーブル。 Azure Cosmos DB のリソース ログは `MICROSOFT.DOCUMENTDB` で識別できます。   |
| AzureActivity    | アクティビティ ログのすべてのレコードを格納する共通テーブル。

> [!IMPORTANT]
> Azure Cosmos DB のメニューから **[ログ]** を選択すると、クエリのスコープが現在の Azure Cosmos データベースに設定された状態で Log Analytics が開きます。 つまり、ログ クエリには、そのリソースからのデータのみが含まれます。 他のデータベースのデータや他の Azure サービスのデータを含むクエリを実行する場合は、**Azure Monitor** のメニューから **[ログ]** を選択します。 詳細については、「[Azure Monitor Log Analytics のログ クエリのスコープと時間範囲](../azure-monitor/log-query/scope.md)」を参照してください。

### <a name="azure-cosmos-db-log-analytics-queries-in-azure-monitor"></a>Azure Monitor での Azure Cosmos DB Log Analytics のクエリ

**[ログ検索]** 検索バーに入力して Azure Cosmos コンテナーの監視に利用できるクエリを紹介します。 これらのクエリは[新しい言語](../log-analytics/log-analytics-log-search-upgrade.md)で使用できます。

次に、Azure Cosmos データベースの監視に使用できるクエリを示します。

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

## <a name="monitor-azure-cosmos-db-programmatically"></a><a id="monitor-cosmosdb-programmatically"></a> Azure Cosmos DB をプログラムで監視する

ポータルで使用できるアカウント レベルのメトリック (アカウント ストレージの使用状況、要求総数) は、SQL API で使用することはできません。 ただし、SQL API を使用してコレクション レベルで使用状況データを取得できます。 コレクション レベルのデータを取得するには、次の操作を行います。

* REST API を使用するには、 [コレクションに対して GET を実行](https://msdn.microsoft.com/library/mt489073.aspx)します。 コレクションのクォータおよび使用状況の情報が、応答の x-ms-resource-quota および x-ms-resource-usage ヘッダーに返されます。

* .NET SDK を使用するには、[DocumentClient.ReadDocumentCollectionAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.readdocumentcollectionasync.aspx) メソッドを使用します。これは、**CollectionSizeUsage**、**DatabaseUsage**、**DocumentUsage** などの多数の使用状況プロパティを含む [ResourceResponse](https://msdn.microsoft.com/library/dn799209.aspx) を返します。

その他のメトリックにアクセスするには、 [Azure Monitor SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights)を使用します。 使用できるメトリック定義は、次の URL を呼び出すことで取得できます。

```http
https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/providers/microsoft.insights/metricDefinitions?api-version=2018-01-01
```

メトリックを個別に取得する場合には、次の形式を使用します。

```http
https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/providers/microsoft.insights/metrics?timespan={StartTime}/{EndTime}&interval={AggregationInterval}&metricnames={MetricName}&aggregation={AggregationType}&`$filter={Filter}&api-version=2018-01-01
```

詳細については、[Azure 監視 REST API](../azure-monitor/platform/rest-api-walkthrough.md) に関する記事をご覧ください。

## <a name="next-steps"></a>次のステップ

* Azure Cosmos DB によって作成されるログおよびメトリックのリファレンスについては、[Azure Cosmos DB 監視データ リファレンス](monitor-cosmos-db-reference.md)を参照してください。
* Azure リソースの監視の詳細については、「[Azure Monitor を使用した Azure リソースの監視](../azure-monitor/insights/monitor-azure-resource.md)」を参照してください。
