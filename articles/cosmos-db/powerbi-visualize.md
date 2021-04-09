---
title: Azure Cosmos DB コネクタ用 Power BI チュートリアル
description: この Power BI のチュートリアルでは、JSON をインポートしたり、洞察に富むレポートを作成したり、Cosmos DB および Power BI コネクタを使用してデータを視覚化する方法を説明します。
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 05/21/2019
ms.author: sngun
ms.openlocfilehash: b27bab9ea3029264143caaacf094f0a799894356
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97359865"
---
# <a name="visualize-azure-cosmos-db-data-by-using-the-power-bi-connector"></a>Power BI コネクタを使用して Azure Cosmos DB データを視覚化する
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

[Power BI](https://powerbi.microsoft.com/) は、ダッシュボードやレポートを作成して共有できるオンライン サービスです。 Power BI Desktop は、さまざまなデータ ソースからのデータを取得できるレポート作成ツールです。 Azure Cosmos DB は、Power BI Desktop で使用できるデータ ソースの 1 つです。 Power BI 用の Azure Cosmos DB コネクタを使用して、Power BI Desktop を Azure Cosmos DB アカウントに接続できます。  Azure Cosmos DB データを Power BI にインポートした後、それを変換したり、レポートを作成したり、そのレポートを Power BI に発行したりできます。   

この記事では、Azure Cosmos DB アカウントを Power BI Desktop に接続するために必要な手順について説明します。 接続した後、コレクションに移動してデータを抽出し、JSON データを表形式に変換して、Power BI にレポートを発行します。

> [!NOTE]
> Azure Cosmos DB 用の Power BI コネクタは Power BI Desktop に接続します。 Power BI Desktop で作成されたレポートは、PowerBI.com に発行できます。 PowerBI.com から Azure Cosmos DB データの直接抽出を実行することはできません。 

> [!NOTE]
> Power BI コネクタによる Azure Cosmos DB への接続は、現在は Azure Cosmos DB SQL アカウントと Gremlin API アカウントでのみサポートされています。

## <a name="prerequisites"></a>前提条件
この Power BI チュートリアルの手順に従う前に、次のリソースにアクセスできることを確認してください。

* [最新バージョンの Power BI Desktop をダウンロードします](https://powerbi.microsoft.com/desktop)。

* GitHub から[サンプル火山データ](https://github.com/Azure-Samples/azure-cosmos-db-sample-data/blob/main/SampleData/VolcanoData.json)をダウンロードします。

* [Azure Cosmos データベース アカウントを作成し](create-cosmosdb-resources-portal.md#create-an-azure-cosmos-db-account)、[Azure Cosmos DB データ移行ツール](import-data.md)を使用して火山データをインポートします。 データをインポートするときは、データ移行ツールのソースとインポート先に対して次の設定を考慮してください。

   * **ソース パラメーター** 

       * **インポート元:** JSON ファイル

   * **ターゲット パラメーター** 

      * **接続文字列:** `AccountEndpoint=<Your_account_endpoint>;AccountKey=<Your_primary_or_secondary_key>;Database= <Your_database_name>` 

      * **パーティション キー:** /Country 

      * **コレクションのスループット:** 1000 

PowerBI.com でレポートを共有するには、PowerBI.com のアカウントが必要です。  Power BI と Power BI Pro の詳細については、[https://powerbi.microsoft.com/pricing](https://powerbi.microsoft.com/pricing) を参照してください。

## <a name="lets-get-started"></a>作業を開始する
このチュートリアルでは、ユーザーが世界中の火山を研究している地質学者であると仮定します。 火山データは Azure Cosmos DB アカウント内に格納されており、JSON ドキュメント形式は次のとおりです。

```json
{
    "Volcano Name": "Rainier",
        "Country": "United States",
        "Region": "US-Washington",
        "Location": {
          "type": "Point",
          "coordinates": [
            -121.758,
            46.87
          ]
        },
        "Elevation": 4392,
        "Type": "Stratovolcano",
        "Status": "Dendrochronology",
        "Last Known Eruption": "Last known eruption from 1800-1899, inclusive"
}
```

Azure Cosmos DB アカウントから火山データを取得し、対話型の Power BI レポートでデータを視覚化します。

1. Power BI Desktop を実行します。

2. ようこそ画面から直接 **[データの取得]** を実行したり、 **[最近のソース]** を参照したり、 **[Open Other Reports] \(他のレポートを開く)** を実行したりできます。 右上隅にある [X] を選択して画面を閉じます。 Power BI Desktop の **[レポート]** ビューが表示されます。
   
   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbireportview.png" alt-text="Power BI Desktop のレポート ビュー - Power BI コネクタ":::

3. **[ホーム]** リボンをクリックし、 **[データの取得]** をクリックします。  **[データの取得]** ウィンドウが表示されます。

4. **[Azure]** をクリックし、 **[Azure Cosmos DB (ベータ版)]** を選択して、 **[接続]** をクリックします。 

   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbigetdata.png" alt-text="Power BI Desktop のデータの取得 - Power BI コネクタ":::

5. **[Preview Connector] \(プレビュー コネクタ)** ページで、 **[続行]** をクリックします。 **[Azure Cosmos DB]** ウィンドウが表示されます。

6. 次のように、データを取得する Azure Cosmos DB アカウント エンドポイント URL を指定し、 **[OK]** をクリックします。 独自のアカウントを使用するには、Azure Portal の **[キー]** ブレードにある [URI] ボックスから URL を取得できます。 オプションで、データベース名とコレクション名を指定するか、またはナビゲーターを使用してデータベースとコレクションを選択することによってデータの取得元を識別できます。
   
7. このエンドポイントに初めて接続している場合は、アカウント キーの入力を求められます。 独自のアカウントの場合は、Azure Portal の **[Read-only Keys]/(読み取り専用キー/)** ブレードにある **[主キー]** ボックスからキーを取得します。 適切なキーを入力し、 **[接続]** をクリックします。
   
   レポートを作成する際は読み取り専用キーを使用することをお勧めします。 これにより、プライマリ キーが不用意に公開される潜在的なセキュリティ リスクを抑えることができます。 読み取り専用キーは、Azure Portal の **[キー]** ブレードから入手できます。 
    
8. アカウントが正常に接続されると、 **ナビゲーター** ウィンドウが表示されます。 **ナビゲーター** には、アカウントで利用できるデータベースの一覧が表示されます。

9. レポート用のデータを取得するデータベースをクリックして展開し、 **[volcanodb]** を選択します (実際のデータベース名は異なる場合があります)。   

10. 次に、取得するデータを含むコレクションを選択し、 **[volcano1]** を選択します (実際のコレクション名は異なる場合があります)。
    
    プレビュー ウィンドウに、 **Record** アイテムの一覧が表示されます。  Power BI では、ドキュメントは **Record** タイプとして表されます。 同様に、ドキュメント内の入れ子になった JSON ブロックも、 **Record** として表されます。
    
    :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbinavigator.png" alt-text="Azure Cosmos DB Power BI コネクタの Power BI チュートリアル - ナビゲーション ウィンドウ":::

12. データを変換するには、 **[編集]** をクリックしてクエリ エディターを新しいウィンドウで起動します。

## <a name="flattening-and-transforming-json-documents"></a>JSON ドキュメントをフラット化して変換する
1. [Power BI Query Editor] \(Power BI クエリ エディター) ウィンドウに切り替えます。中央のウィンドウに **[ドキュメント]** 列が表示されます。

   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbiqueryeditor.png" alt-text="Power BI Desktop クエリ エディター":::

1. **[ドキュメント]** 列ヘッダーの右側にある展開コントロールをクリックします。  フィールドの一覧を示すコンテキスト メニューが表示されます。  Volcano Name、Country、Region、Location、Elevation、Type、Status、Last Know Eruption など、レポートに必要なフィールドを選択します。 **[Use original column name as prefix]\(元の列名をプレフィックスとして使用する\)** チェックボックスをオフにし、 **[OK]** をクリックします。
   
   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbiqueryeditorexpander.png" alt-text="Azure Cosmos DB Power BI コネクタの Power BI チュートリアル - ドキュメントの展開":::

1. 中央のウィンドウに、選択したフィールドを含む結果のプレビューが表示されます。
   
   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbiresultflatten.png" alt-text="Azure Cosmos DB Power BI コネクタの Power BI チュートリアル - フラット化された結果":::

1. この例では、Location プロパティは、ドキュメント内の GeoJSON ブロックです。  ご覧のように、Power BI Desktop では Location は **Record** タイプとして表されます。  

1. [Document.Location] 列ヘッダーの右側にある展開コントロールをクリックします。  type フィールドと coordinates フィールドを含むコンテキスト メニューが表示されます。  coordinates フィールドを選択してみましょう。 **[Use original column name as prefix]\(元の列名をプレフィックスとして使用する\)** が選択されていないことを確認し、 **[OK]** をクリックします。
   
   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbilocationrecord.png" alt-text="Azure Cosmos DB Power BI コネクタの Power BI チュートリアル - 場所レコード":::

1. 中央のウィンドウに、 **List** タイプの [coordinates] 列が表示されます。  このチュートリアルの冒頭に示されているように、ここで使用する GeoJSON データは、Latitude 値と Longitude 値が coordinates 配列に記録された Point タイプです。
   
   coordinates[0] 要素は経度を表し、coordinates[1] は緯度を表します。

   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbiresultflattenlist.png" alt-text="Azure Cosmos DB Power BI コネクタの Power BI チュートリアル - 座標一覧":::

1. coordinates 配列をフラット化するために、LatLong という **カスタム列** を作成します。  **[列の追加]** リボンを選択し、 **[カスタム列]** をクリックします。  **[カスタム列]** ウィンドウが表示されます。

1. "LatLong" など、新しい列の名前を入力します。

1. 次に、新しい列のカスタム式を指定します。  たとえば、次のような式を使用して、緯度と経度の値をコンマ区切りで連結します: `Text.From([coordinates]{1})&","&Text.From([coordinates]{0})`。 **[OK]** をクリックします。
   
   DAX 関数など Data Analysis Expressions (DAX) の詳細については、「[Power BI Desktop における DAX の基本事項](/power-bi/desktop-quickstart-learn-dax-basics)」を参照してください。
   
   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbicustomlatlong.png" alt-text="Azure Cosmos DB Power BI コネクタの Power BI チュートリアル - カスタム列の追加":::

1. これで、中央のウィンドウに、値が入力された新しい LatLong 列が表示されます。
    
    :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbicolumnlatlong.png" alt-text="Azure Cosmos DB コネクタの Power BI チュートリアル - カスタム LatLong 列":::
    
    新しい列でエラーが発生する場合は、クエリ設定で適用された手順が次の図と一致することを確認してください。
    
    :::image type="content" source="./media/powerbi-visualize/power-bi-applied-steps.png" alt-text="適用された手順は、Source、Navigation、Expanded Document、Expanded Document.Location、Added Custom である必要がある":::
    
    手順が異なる場合は、余分な手順を削除し、カスタム列をもう一度追加してみてください。 

1. **[終了して適用]** をクリックしてデータ モデルを保存します。

   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbicloseapply.png" alt-text="Azure Cosmos DB Power BI コネクタの Power BI チュートリアル - 閉じて適用":::

<a id="build-the-reports"></a>
## <a name="build-the-reports"></a>レポートを作成する

Power BI Desktop レポート ビューは、データを視覚化するためにレポート作成を開始できる場所です。  **[レポート]** キャンバスにフィールドをドラッグ アンド ドロップすることにより、レポートを作成できます。

:::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbireportview2.png" alt-text="Power BI Desktop のレポート ビュー - 必要なフィールドをドラッグ アンド ドロップします":::

レポート ビューには以下が表示されます。

1. **[フィールド]** ウィンドウ。ここで、レポートに使用できるデータ モデルとフィールドの一覧を確認できます。
1. **[視覚エフェクト]** ウィンドウ。 レポートに 1 つまたは複数の視覚エフェクトを含めることができます。  **[視覚エフェクト]** ウィンドウで、ニーズに合った視覚エフェクトの種類を指定します。
1. **[レポート]** キャンバス。ここで、レポート用のビジュアルを作成します。
1. **[レポート]** ページ。 Power BI Desktop では、複数のレポート ページを追加できます。

単純な対話型マップ ビュー レポートを作成する基本的な手順を次に示します。

1. 例として、個々の火山の場所を示すマップ ビューを作成します。  **[視覚エフェクト]** ウィンドウで、ビジュアルの種類として、上記のスクリーンショットで強調表示されているマップをクリックします。  **[レポート]** キャンバスに、ビジュアルの種類のマップが描かれます。  **[視覚化エフェクト]** ウィンドウには、ビジュアルの種類のマップに関連するプロパティのセットも表示されます。
1. ここで、 **[フィールド]** ウィンドウの LatLong フィールドを **[視覚エフェクト]** ウィンドウの **Location** プロパティにドラッグ アンド ドロップします。
1. 次に、Volcano Name フィールドを **Legend** プロパティにドラッグ アンド ドロップします。  
1. さらに、Elevation フィールドを **Size** プロパティにドラッグ アンド ドロップします。  
1. ここで、マップ ビジュアルに複数のバブルが表示されます。バブルは個々の火山の場所を示し、バブルのサイズは火山の高度と相関性があります。
1. これで、基本的なレポートを作成できました。  別の視覚エフェクトを追加することで、レポートをさらにカスタマイズできます。  この例では、レポートを対話的にするために Volcano Type スライサーを追加しました。  
   
1. [ファイル] メニューの **[保存]** をクリックして、ファイルを PowerBITutorial.pbix として保存します。

## <a name="publish-and-share-your-report"></a>レポートを発行して共有する
レポートを共有するには、PowerBI.com のアカウントが必要です。

1. Power BI Desktop で、 **[ホーム]** リボンをクリックします。
1. **[発行]** をクリックします。  PowerBI.com アカウントのユーザー名とパスワードの入力が求められます。
1. 資格情報が認証されると、選択済みの宛先にレポートが発行されます。
1. レポートを PowerBI.com に表示して共有するには、 **['PowerBITutorial.pbix' を Power BI で開く]** をクリックします。
   
   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_open_in_powerbi.png" alt-text="Power BI への発行が成功しました!Power BI でチュートリアルを開く":::

## <a name="create-a-dashboard-in-powerbicom"></a>PowerBI.com でのダッシュボードの作成
レポートが用意できたので、PowerBI.com で共有しましょう。

レポートを Power BI Desktop から PowerBI.com に発行すると、PowerBI.com テナントに **[レポート]** と **[データセット]** が生成されます。 たとえば、**PowerBITutorial** という名前のレポートを PowerBI.com に発行すると、PowerBI.com の **[レポート]** セクションと **[データセット]** セクションの両方に PowerBITutorial が表示されます。

   :::image type="content" source="./media/powerbi-visualize/powerbi-reports-datasets.png" alt-text="PowerBI.com の新しいレポートとデータセットのスクリーンショット":::

共有可能なダッシュ ボードを作成するには、PowerBI.com レポートで **[ライブ ページをピン留めする]** ボタンをクリックします。

   :::image type="content" source="./media/powerbi-visualize/power-bi-pin-live-tile.png" alt-text="レポートを PowerBI.com にピン留めする方法のスクリーンショット":::

その後、「 [レポートからタイルをピン留め](https://powerbi.microsoft.com/documentation/powerbi-service-pin-a-tile-to-a-dashboard-from-a-report/#pin-a-tile-from-a-report) 」の指示に従って、新しいダッシュボードを作成します。 

ダッシュボードを作成する前に、レポートをその場で変更することもできます。 ただし、変更は Power BI Desktop で実行し、変更後のレポートを PowerBI.com にもう一度発行することをお勧めします。

<!-- ## Refresh data in PowerBI.com
There are two ways to refresh data, ad hoc and scheduled.

For an ad hoc refresh, simply click on the eclipses (…) by the **Dataset**, e.g. PowerBITutorial. You should see a list of actions including **Refresh Now**. Click **Refresh Now** to refresh the data.

:::image type="content" source="./media/powerbi-visualize/power-bi-refresh-now.png" alt-text="Screenshot of Refresh Now in PowerBI.com":::

For a scheduled refresh, do the following.

1. Click **Schedule Refresh** in the action list. 

    :::image type="content" source="./media/powerbi-visualize/power-bi-schedule-refresh.png" alt-text="Screenshot of the Schedule Refresh in PowerBI.com":::
2. In the **Settings** page, expand **Data source credentials**. 
3. Click on **Edit credentials**. 
   
    The Configure popup appears. 
4. Enter the key to connect to the Azure Cosmos DB account for that data set, then click **Sign in**. 
5. Expand **Schedule Refresh** and set up the schedule you want to refresh the dataset. 
6. Click **Apply** and you are done setting up the scheduled refresh.
-->
## <a name="next-steps"></a>次のステップ
* Power BI の詳細については、「 [Power BI の概要](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/)」を参照してください。
* Azure Cosmos DB について詳しくは、[Azure Cosmos DB ドキュメントのランディング ページ](https://azure.microsoft.com/documentation/services/cosmos-db/)をご覧ください。
