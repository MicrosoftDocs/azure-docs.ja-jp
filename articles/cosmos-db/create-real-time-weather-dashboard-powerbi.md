---
title: Azure Cosmos DB、Azure Analysis Services、および Power BI を使用してリアルタイム ダッシュボードを作成する
description: Azure Cosmos DB と Azure Analysis Services を使用して Power BI でライブ天気ダッシュボードを作成する方法について説明します。
author: bharathsreenivas
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: bharathb
ms.reviewer: sngun
ms.openlocfilehash: d225a14edddcad58c08094dbc758d67df8f834e6
ms.sourcegitcommit: aebe5a10fa828733bbfb95296d400f4bc579533c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/05/2019
ms.locfileid: "70376484"
---
# <a name="create-a-real-time-dashboard-using-azure-cosmos-db-and-power-bi"></a>Azure Cosmos DB と Power BI を使用してリアルタイム ダッシュボードを作成する

この記事では、Azure Cosmos DB と Azure Analysis Services を使用して Power BI でライブ天気ダッシュボードを作成するために必要な手順について説明します。 Power BI ダッシュボードには、地域の気温と降水量に関するリアルタイムの情報を示すグラフが表示されます。

## <a name="reporting-scenarios"></a>レポートのシナリオ

Azure Cosmos DB に格納されているデータに対してレポート ダッシュボードを設定するには、複数の方法があります。 整合性制約の要件とデータのサイズに応じて、次の表に各シナリオのレポート設定を示します。


|シナリオ |セットアップ |
|---------|---------|
|1.アドホック レポート (更新なし) を生成する    |  [Power BI Azure Cosmos DB コネクタとインポート モード](powerbi-visualize.md)       |
|2.定期更新があるアドホック レポートを生成する   |  [Power BI Azure Cosmos DB コネクタとインポート モード (スケジュールされた定期更新)](powerbi-visualize.md)       |
|3.大規模なデータセット (< 10 GB) についてレポートを作成する     |  Power BI Azure Cosmos DB コネクタと増分更新       |
|4.大規模なデータセットについてリアルタイムでレポートを作成する    |  Power BI Azure Analysis Services コネクタと直接クエリ + Azure Analysis Services (Azure Cosmos DB コネクタ)       |
|5.集計を使用してライブ データについてレポートを作成する     |  [Power BI Spark コネクタと直接クエリ + Azure Databricks + Cosmos DB Spark コネクタ](https://github.com/Azure/azure-cosmosdb-spark/wiki/Connecting-Cosmos-DB-with-PowerBI-using-spark-and-databricks-premium)       |
|6.大規模なデータセットの集計を使用してライブ データについてレポートを作成する   |  Power BI Azure Analysis Services コネクタと直接クエリ + Azure Analysis Services + Azure Databricks + Cosmos DB Spark コネクタ。       |

シナリオ 1 と 2 は、Azure Cosmos DB Power BI コネクタを使用して簡単に設定できます。 この記事では、シナリオ 3 と 4 の設定について説明します。

### <a name="power-bi-with-incremental-refresh"></a>増分更新がある Power BI

Power BI には、増分更新を構成できるモードがあります。 このモードにより、Azure Analysis Services のパーティションを作成および管理する必要がなくなります。 増分更新は、大規模なデータセットの最新の更新のみをフィルター処理するように設定できます。 ただし、このモードは、10 GB のデータセット制限がある Power BI Premium サービスでのみ機能します。

### <a name="power-bi-azure-analysis-connector--azure-analysis-services"></a>Power BI Azure Analysis コネクタ + Azure Analysis Services

Azure Analysis Services は、エンタープライズ レベルのデータ モデルをクラウドでホストする、完全に管理されたサービスとしてのプラットフォームを提供します。 大量のデータセットを Azure Cosmos DB から Azure Analysis Services に読み込むことができます。 常にデータセット全体に対してクエリを実行することを避けるために、データセットを Azure Analysis Services の複数のパーティションに分割して、異なる頻度で個別に更新することができます。

## <a name="power-bi-incremental-refresh"></a>Power BI 増分更新

### <a name="ingest-weather-data-into-azure-cosmos-db"></a>Azure Cosmos DB に気象データを取り込む

[気象データ](https://catalog.data.gov/dataset/local-weather-archive)を Azure Cosmos DB に読み込むためのインジェスト パイプラインを設定します。 HTTP ソースと Cosmos DB シンクを使用して最新の気象データを Azure Cosmos DB に定期的に読み込むように [Azure Data Factory (ADF)](../data-factory/connector-azure-cosmos-db.md) ジョブを設定できます。


### <a name="connect-power-bi-to-azure-cosmos-db"></a>Power BI を Azure Cosmos DB に接続する

1. **Azure Cosmos アカウントを Power BI に接続する** - Power BI Desktop を開き、Azure Cosmos DB コネクタを使用して適切なデータベースとコンテナーを選択します。

   ![Azure Cosmos DB Power BI コネクタ](./media/create-real-time-weather-dashboard-powerbi/cosmosdb-powerbi-connector.png)

1. **増分更新を構成する** - [Power BI での増分更新](/power-bi/service-premium-incremental-refresh)に関する記事の手順に従って、データセットの増分更新を構成します。 次のスクリーンショットに示すように、**RangeStart** パラメーターと **RangeEnd** パラメーターを追加します。

   ![範囲のパラメーターを構成する](./media/create-real-time-weather-dashboard-powerbi/configure-range-parameters.png)

   データセットにはテキスト形式の日付列があるため、次のフィルターを使用するには **RangeStart** パラメーターと **RangeEnd** パラメーターを変換する必要があります。 **[詳細エディター]** ウィンドウで、クエリを変更し、次のテキストを追加して、RangeStart パラメーターと RangeEnd パラメーターに基づいて行をフィルター処理するようにします。

   ```
   #"Filtered Rows" = Table.SelectRows(#"Expanded Document", each [Document.date] > DateTime.ToText(RangeStart,"yyyy-MM-dd") and [Document.date] < DateTime.ToText(RangeEnd,"yyyy-MM-dd"))
   ```
   
   ソース データセットに存在する列とデータ型に応じて、RangeStart フィールドと Rangestart フィールドを適宜変更できます。

   
   |プロパティ  |データ型  |filter  |
   |---------|---------|---------|
   |_ts     |   Numeric      |  [_ts] > Duration.TotalSeconds(RangeStart - #datetime(1970, 1, 1, 0, 0, 0)) と [_ts] < Duration.TotalSeconds(RangeEnd - #datetime(1970, 1, 1, 0, 0, 0)))       |
   |Date (例:- 2019-08-19)     |   string      | [Document.date]> DateTime.ToText(RangeStart,"yyyy-MM-dd") と [Document.date] < DateTime.ToText(RangeEnd,"yyyy-MM-dd")        |
   |Date (例:- 2019-08-11 12:00:00)   |  string       |  [Document.date]> DateTime.ToText(RangeStart," yyyy-mm-dd HH:mm:ss") と [Document.date] < DateTime.ToText(RangeEnd,"yyyy-mm-dd HH:mm:ss")       |


1. **更新ポリシーを定義する** - テーブルの**コンテキスト** メニューの **[増分更新]** タブに移動して、更新ポリシーを定義します。 **毎日**更新され、前月のデータが格納されるように更新ポリシーを設定します。

   ![更新ポリシーを定義する](./media/create-real-time-weather-dashboard-powerbi/define-refresh-policy.png)

   *M クエリが折りたたまれていることを確認できない*という警告は無視してください。 Azure Cosmos DB コネクタは、フィルター クエリを折りたたみます。

1. **データを読み込んでレポートを生成する** - 前に読み込んだデータを使用して、気温と降水量に関するレポートを作成するためのグラフを作成します。

   ![データを読み込んでレポートを生成する](./media/create-real-time-weather-dashboard-powerbi/load-data-generate-report.png)

1. **Power BI Premium にレポートを発行する** - 増分更新は Premium のみの機能であるため、[発行] ダイアログでは、Premium 容量におけるワークスペースの選択のみが可能です。 最初の更新は、履歴データをインポートするために時間がかかる場合があります。 その後のデータ更新は増分更新を使用するため、はるかに高速になります。


## <a name="power-bi-azure-analysis-connector--azure-analysis-services"></a>Power BI Azure Analysis コネクタ + Azure Analysis Services 

### <a name="ingest-weather-data-into-azure-cosmos-db"></a>Azure Cosmos DB に気象データを取り込む 

[気象データ](https://catalog.data.gov/dataset/local-weather-archive)を Azure Cosmos DB に読み込むためのインジェスト パイプラインを設定します。 HTTP ソースと Cosmos DB シンクを使用して最新の気象データを Azure Cosmos DB に定期的に読み込むように Azure Data Factory (ADF) ジョブを設定できます。

### <a name="connect-azure-analysis-services-to-azure-cosmos-account"></a>Azure Analysis Services を Azure Cosmos アカウントに接続する

1. **新しい Azure Analysis Services クラスターを作成する** - Azure Cosmos アカウントおよび Databricks クラスターと同じリージョンに [Azure Analysis Services のインスタンスを作成します](../analysis-services/analysis-services-create-server.md)。

1. **Visual Studio で新しい Analysis Services 表形式プロジェクトを作成する** -  [SQL Server Data Tools (SSDT) をインストール](/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017)し、Visual Studio で Analysis Services 表形式プロジェクトを作成します。

   ![Azure Analysis Services プロジェクトを作成する](./media/create-real-time-weather-dashboard-powerbi/create-analysis-services-project.png)

   **統合ワークスペース** インスタンスを選択し、互換性レベルを **SQL Server 2017 / Azure Analysis Services (1400)** に設定します。

   ![Azure Analysis Services 表形式モデル デザイナー](./media/create-real-time-weather-dashboard-powerbi/tabular-model-designer.png)

1. **Azure Cosmos DB データ ソースを追加する** - **[モデル]** >  **[データ ソース]**  >  **[新しいデータ ソース]** に移動し、次のスクリーンショットに示すように Azure Cosmos DB データ ソースを追加します。

   ![Cosmos DB データ ソースを追加する](./media/create-real-time-weather-dashboard-powerbi/add-data-source.png)

   **アカウント URI**、**データベース名**、および**コンテナー名**を指定して、Azure Cosmos DB に接続します。 Azure Cosmos コンテナーからのデータが Power BI にインポートされていることを確認できます。

   ![Azure Cosmos DB データをプレビューする](./media/create-real-time-weather-dashboard-powerbi/preview-cosmosdb-data.png)

1. **Analysis Services モデルを構築する** - クエリ エディターを開いて、読み込まれたデータセットを最適化するために必要な操作を実行します。

   * 気象に関連した列 (気温と降水量) だけを抽出する

   * テーブルから月の情報を抽出する。 このデータは、次のセクションで説明するパーティションの作成に役立ちます。

   * 気温列を数値に変換する

   結果の M 式は次のようになります。

   ```
    let
        Source=#"DocumentDB/https://[ACCOUNTNAME].documents.azure.com:443/",
        #"Expanded Document" = Table.ExpandRecordColumn(Source, "Document", {"id", "_rid", "_self", "_etag", "fogground", "snowfall", "dust", "snowdepth", "mist", "drizzle", "hail", "fastest2minwindspeed", "thunder", "glaze", "snow", "ice", "fog", "temperaturemin", "fastest5secwindspeed", "freezingfog", "temperaturemax", "blowingsnow", "freezingrain", "rain", "highwind", "date", "precipitation", "fogheavy", "smokehaze", "avgwindspeed", "fastest2minwinddir", "fastest5secwinddir", "_attachments", "_ts"}, {"Document.id", "Document._rid", "Document._self", "Document._etag", "Document.fogground", "Document.snowfall", "Document.dust", "Document.snowdepth", "Document.mist", "Document.drizzle", "Document.hail", "Document.fastest2minwindspeed", "Document.thunder", "Document.glaze", "Document.snow", "Document.ice", "Document.fog", "Document.temperaturemin", "Document.fastest5secwindspeed", "Document.freezingfog", "Document.temperaturemax", "Document.blowingsnow", "Document.freezingrain", "Document.rain", "Document.highwind", "Document.date", "Document.precipitation", "Document.fogheavy", "Document.smokehaze", "Document.avgwindspeed", "Document.fastest2minwinddir", "Document.fastest5secwinddir", "Document._attachments", "Document._ts"}),
        #"Select Columns" = Table.SelectColumns(#"Expanded Document",{"Document.temperaturemin", "Document.temperaturemax", "Document.rain", "Document.date"}),
        #"Duplicated Column" = Table.DuplicateColumn(#"Select Columns", "Document.date", "Document.month"),
        #"Extracted First Characters" = Table.TransformColumns(#"Duplicated Column", {{"Document.month", each Text.Start(_, 7), type text}}),
        #"Sorted Rows" = Table.Sort(#"Extracted First Characters",{{"Document.date", Order.Ascending}}),
        #"Changed Type" = Table.TransformColumnTypes(#"Sorted Rows",{{"Document.temperaturemin", type number}, {"Document.temperaturemax", type number}}),
        #"Filtered Rows" = Table.SelectRows(#"Changed Type", each [Document.month] = "2019-07")
    in
        #"Filtered Rows"
   ```

   また、気温列のデータ型を10 進数に変更して、これらの値を Power BI にプロットできるようにします。

1. **Azure Analysis のパーティションを作成する** - Azure Analysis Services でパーティションを作成して、データセットを、個別に、異なる頻度で更新できる論理パーティションに分割します。 この例では、データセットを最新月のデータとその他すべてに分割する 2 つのパーティションを作成します。

   ![Analysis Services のパーティションを作成する](./media/create-real-time-weather-dashboard-powerbi/create-analysis-services-partitions.png)

   Azure Analysis Services で次の 2 つのパーティションを作成します。

   * **最新月** - `#"Filtered Rows" = Table.SelectRows(#"Sorted Rows", each [Document.month] = "2019-07")`
   * **履歴** -  `#"Filtered Rows" = Table.SelectRows(#"Sorted Rows", each [Document.month] <> "2019-07")`

1. **Azure Analysis Server にモデルをデプロイする** - Azure Analysis Services プロジェクトを右クリックし、 **[デプロイ]** を選択します。 **[Deployment Server properties]\(デプロイ サーバーのプロパティ\)** ウィンドウで、サーバー名を追加します。

   ![Azure Analysis Services モデルをデプロイする](./media/create-real-time-weather-dashboard-powerbi/analysis-services-deploy-model.png)

1. **パーティションの更新とマージを構成する** - Azure Analysis Services では、パーティションを個別に処理することができます。 **最新月**のパーティションが最新データで常に更新されるようにするため、更新間隔を 5 分に設定します。 履歴パーティションのデータを更新する必要はありません。 さらに、最新月パーティションを履歴パーティションに統合し、新しい最新月パーティションを作成するためのコードを記述する必要があります。


## <a name="connect-power-bi-to-analysis-services"></a>Power BI を Analysis Services に接続する

1. **Azure Analysis Services データベース コネクタを使用して Azure Analysis Server に接続する** - **ライブ モード**を選択し、次のスクリーンショットに示すように Azure Analysis Services インスタンスに接続します。

   ![Azure Analysis Services からデータを取得](./media/create-real-time-weather-dashboard-powerbi/analysis-services-get-data.png)

1. **データを読み込んでレポートを生成する** - 前に読み込んだデータを使用して、気温と降水量に関するレポートを作成するためのグラフを作成します。 ライブ接続を作成しているため、前の手順でデプロイした Azure Analysis Services モデルのデータに対してクエリを実行する必要があります。 気温グラフは、新しいデータが Azure Cosmos DB に読み込まれてから 5 分以内に更新されます。

   ![データを読み込んでレポートを生成する](./media/create-real-time-weather-dashboard-powerbi/load-data-generate-report.png)

## <a name="next-steps"></a>次の手順

* Power BI の詳細については、「 [Power BI の概要](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/)」を参照してください。

* [Qlik Sense を Azure Cosmos DB に接続してデータを可視化する](visualize-qlik-sense.md)