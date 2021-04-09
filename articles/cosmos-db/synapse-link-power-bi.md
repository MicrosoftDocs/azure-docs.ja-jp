---
title: Synapse Link で Azure Cosmos DB データを分析するための Power BI とサーバーレス SQL プール
description: Azure Cosmos DB の Synapse Link 上にサーバーレス SQL プール データベースおよびビューを構築し、Azure Cosmos DB コンテナーに対してクエリを実行し、Power BI を使用してそれらのビュー上にモデルを構築する方法について説明します。
author: ArnoMicrosoft
ms.service: cosmos-db
ms.topic: how-to
ms.date: 11/30/2020
ms.author: acomet
ms.custom: synapse-cosmos-db
ms.openlocfilehash: 7a9324923164d6d5cbbdc48bfa60fc444bdc4fb1
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104868972"
---
# <a name="use-power-bi-and-serverless-synapse-sql-pool-to-analyze-azure-cosmos-db-data-with-synapse-link"></a>Power BI とサーバーレス Synapse SQL プールを使用して Synapse Link で Azure Cosmos DB データを分析する 
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

この記事では、Azure Cosmos DB の Synapse Link 上にサーバーレス SQL プール データベースおよびビューを構築する方法について説明します。 Azure Cosmos DB コンテナーに対してクエリを実行し、そのクエリを反映するために、それらのビュー上に Power BI でモデルを構築します。

このシナリオでは、パートナー小売店での Surface 製品の売上に関するダミー データを使用します。 大所帯への近さと、特定の週の広告の効果に基づいた店舗ごとの収益を分析します。 この記事では **RetailSales** と **StoreDemographics** という名前の 2 つのビューと、それらの間のクエリを作成します。 この [GitHub](https://github.com/Azure-Samples/Synapse/tree/main/Notebooks/PySpark/Synapse%20Link%20for%20Cosmos%20DB%20samples/Retail/RetailData) リポジトリからサンプル製品データを取得できます。

## <a name="prerequisites"></a>前提条件

開始する前に、次のリソースを作成してください。

* [SQL (コア) または MongoDB の種類の Azure Cosmos DB アカウントを作成します。](create-cosmosdb-resources-portal.md)

* [Azure Cosmos アカウント](configure-synapse-link.md#enable-synapse-link)の Azure Synapse Link を有効にします

* Azure Cosmos アカウント内にデータベースと、[分析ストアが有効にされている](configure-synapse-link.md#create-analytical-ttl) 2 つのコンテナーを作成します。

* この[バッチ データ インジェスト](https://github.com/Azure-Samples/Synapse/blob/main/Notebooks/PySpark/Synapse%20Link%20for%20Cosmos%20DB%20samples/Retail/spark-notebooks/pyspark/1CosmoDBSynapseSparkBatchIngestion.ipynb) ノートブックで説明されているように、Azure Cosmos コンテナーに製品データを読み込みます。

* **SynapseLinkBI** という名前の [Synapse ワークスペースを作成します](../synapse-analytics/quickstart-create-workspace.md)。

* [Azure Cosmos データベースを Synapse ワークスペースに接続します](../synapse-analytics/synapse-link/how-to-connect-synapse-link-cosmos-db.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json)。

## <a name="create-a-database-and-views"></a>データベースとビューを作成する

Synapse ワークスペースで、 **[開発]** タブをクリックし、 **+** アイコンを選択して、 **[SQL スクリプト]** を選択します。

:::image type="content" source="./media/synapse-link-power-bi/add-sql-script.png" alt-text="Synapse Analytics ワークスペースに SQL スクリプトを追加する":::

すべてのワークスペースに、サーバーレス SQL エンドポイントが付属しています。 SQL スクリプトを作成したら、上部のツール バーから **[組み込み]** に接続します。

:::image type="content" source="./media/synapse-link-power-bi/enable-sql-on-demand-endpoint.png" alt-text="SQL スクリプトで、ワークスペースのサーバーレス SQL エンドポイントを使用できるようにする":::

**マスター** または **既定** のデータベースにビューを作成することは推奨されず、サポートもされていません。 **RetailCosmosDB** という名前の新しいデータベースと、Synapse Link 対応コンテナーに SQL ビューを作成します。 次のコマンドは、データベースを作成する方法を示しています。

```sql
-- Create database
Create database RetailCosmosDB
```

次に、さまざまな Synapse Link 対応 Azure Cosmos コンテナー全体の多数のビューを作成します。 ビューにより、T-SQL を使用して、さまざまなコンテナーに格納されている Azure Cosmos DB データを結合し、クエリを実行することができます。  ビューの作成時に、**RetailCosmosDB** データベースを選択してください。

次のスクリプトは、各コンテナーでビューを作成する方法を示しています。 わかりやすくするために、Synapse Link 対応コンテナーで、サーバーレス SQL プールの[自動スキーマ推論](analytical-store-introduction.md#analytical-schema)機能を使用してみましょう。


### <a name="retailsales-view"></a>RetailSales ビュー:

```sql
-- Create view for RetailSales container
CREATE VIEW  RetailSales
AS  
SELECT  *
FROM OPENROWSET (
    'CosmosDB', N'account=<Your Azure Cosmos account name>;database=<Your Azure Cosmos database name>;region=<Your Azure Cosmos DB Region>;key=<Your Azure Cosmos DB key here>',RetailSales)
AS q1
```

前の SQL スクリプトに Azure Cosmos DB リージョンと主キーを挿入してください。 リージョン名のすべての文字は、スペースなしの小文字にする必要があります。 `OPENROWSET` コマンドの他のパラメーターと異なり、コンテナー名パラメーターは引用符で囲まずに指定する必要があります。

### <a name="storedemographics-view"></a>StoreDemographics ビュー:

```sql
-- Create view for StoreDemographics container
CREATE VIEW StoreDemographics
AS  
SELECT  *
FROM OPENROWSET (
    'CosmosDB', N'account=<Your Azure Cosmos account name>;database=<Your Azure Cosmos database name>;region=<Your Azure Cosmos DB Region>;key=<Your Azure Cosmos DB key here>', StoreDemographics)
AS q1
```

次に、 **[実行]** コマンドを選択して、SQL スクリプトを実行します。

## <a name="query-the-views"></a>ビューのクエリ

2 つのビューが作成されたので、次のようにそれらの 2 つのビューを結合するクエリを定義してみましょう。

```sql
SELECT 
sum(p.[revenue]) as revenue
,p.[advertising]
,p.[storeId]
,p.[weekStarting]
,q.[largeHH]
 FROM [dbo].[RetailSales] as p
INNER JOIN [dbo].[StoreDemographics] as q ON q.[storeId] = p.[storeId]
GROUP BY p.[advertising], p.[storeId], p.[weekStarting], q.[largeHH]
```

**[実行]** を選択すると、結果として次のテーブルが得られます。

:::image type="content" source="./media/synapse-link-power-bi/join-views-query-results.png" alt-text="StoreDemographics ビューと RetailSales ビューの結合後のクエリ結果":::

## <a name="model-views-over-containers-with-power-bi"></a>Power BI によるコンテナー上のモデル ビュー

次に、以下の手順に従って、Power BI デスクトップを開き、サーバーレス SQL エンドポイントに接続します。

1. Power BI Desktop アプリケーションを開きます。 **[データの取得]** を選択し、 **[その他]** を選択します。

1. 接続オプションの一覧から **[Azure Synapse Analytics (SQL DW)]** を選択します。

1. データベースが配置されている SQL エンドポイントの名前を入力します。 **[サーバー]** フィールドに `SynapseLinkBI-ondemand.sql.azuresynapse.net` と入力します。 この例では、**SynapseLinkBI** がワークスペースの名前です。 ワークスペースに別の名前を指定している場合は、それを置き換えます。 データ接続モードで、 **[直接クエリ]** を選択し、 **[OK]** をクリックします。

1. Azure AD など、優先する認証方法を選択します。

1. **RetailCosmosDB** データベースと **RetailSales** ビュー、**StoreDemographics** ビューを選択します。

1. 2 つのビューを直接クエリ モードに読み込むには **[読み込み]** を選択します。

1. **[モデル]** を選択し、 **[storeId]** 列から、2 つのビュー間のリレーションシップを作成します。

1. **RetailSales** ビューの **StoreId** 列を **StoreDemographics** ビューの **StoreId** 列までドラッグします。

1. **RetailSales** ビューには同じ店舗 ID を持つ複数の行があるため、多対一 (*: 1) リレーションシップを選択します **StoreDemographics** の店舗 ID 行は 1 行のみです (これはディメンション テーブルです)。

ここで、**レポート** ウィンドウに移動し、収益と LargeHH インデックスの分散表現に基づいて、店舗あたりの平均収益に対する世帯規模の相対的な重要度を比較するレポートを作成します。

1. **[散布図]** を選択します。

1. **StoreDemographics** ビューの **LargeHH** を X 軸に ドラッグ アンド ドロップします。

1. **[RetailSales]** ビューの **[Revenue]** を Y 軸にドラッグ アンド ドロップします。 **[平均]** を選択して、店舗あたりおよび 1 週間あたりの製品ごとの平均売上を取得します。

1. **RetailSales** ビューの **productCode** を凡例にドラッグ アンド ドロップして、特定の製品ラインを選択します。
これらのオプションを選択すると、次のスクリーンショットのようなグラフが表示されます。

:::image type="content" source="./media/synapse-link-power-bi/household-size-average-revenue-report.png" alt-text="店舗あたりの平均収益と世帯規模の相対的重要度を比較するレポート":::

## <a name="next-steps"></a>次のステップ

[T-SQL で Azure Synapse Link を使用して Azure Cosmos DB のデータのクエリを実行する](../synapse-analytics/sql/query-cosmos-db-analytical-store.md)

サーバーレス SQL プールを使用して、[Azure Open Datasets を分析し、Azure Synapse Studio で結果を視覚化する](../synapse-analytics/sql/tutorial-data-analyst.md)
