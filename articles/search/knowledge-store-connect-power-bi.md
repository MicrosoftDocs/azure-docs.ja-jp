---
title: Power BI を使用してナレッジ ストアに接続する
titleSuffix: Azure Cognitive Search
description: 分析と探索を目的に Power BI を使用して Azure Cognitive Search のナレッジ ストアに接続します。
author: HeidiSteen
ms.author: heidist
manager: nitinme
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 91e75b60f5324288c9f1adac59e31b9c1a1b0e9e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89289173"
---
# <a name="connect-a-knowledge-store-with-power-bi"></a>Power BI を使用してナレッジ ストアに接続する

この記事では、Power BI Desktop アプリの Power Query を使用してナレッジ ストアに接続し、探索する方法を説明します。 テンプレートを使用してすぐに作業を開始することも、カスタム ダッシュボードを最初から作成することもできます。 次の短いビデオでは、Azure Cognitive Search を Power BI と組み合わせて使用して、データのエクスペリエンスを向上させる方法を示します。


> [!VIDEO https://www.youtube.com/embed/XWzLBP8iWqg?version=3&start=593&end=663]



+ [Azure portal でのナレッジ ストアの作成](knowledge-store-create-portal.md)に関するページか、「[REST を使用して Azure Cognitive Search のナレッジ ストアを作成する](knowledge-store-create-rest.md)」の手順に従って、このチュートリアルに使用されているサンプル ナレッジ ストアを作成します。 ナレッジ ストアの作成に使用した Azure ストレージ アカウントの名前とそのアクセス キー (Azure portal から入手) も必要になります。

+ [Power BI Desktop をインストール](https://powerbi.microsoft.com/downloads/)します。

## <a name="sample-power-bi-template---azure-portal-only"></a>サンプル Power BI テンプレート - Azure portal のみ

[Azure portal を使用してナレッジ ストア](knowledge-store-create-portal.md)を作成するときは、**データのインポート** ウィザードの 2 ページ目で、[Power BI テンプレート](https://github.com/Azure-Samples/cognitive-search-templates)をダウンロードすることもできます。 このテンプレートでは、WordCloud や Network Navigator など、テキスト ベースのコンテンツ用の視覚エフェクトがいくつか提供されます。 

**[認知技術を追加します]** ページの **[Power BI テンプレートを取得する]** をクリックして、そのパブリックな GitHub の場所からテンプレートを取得してダウンロードします。 ウィザードでは、ウィザードで指定されたナレッジ ストアの予測でキャプチャされたデータの構造に合わせてテンプレートが変更されます。 このため、データ入力とスキルの選択が異なるとすると、ダウンロードしたテンプレートは、ウィザードを実行するたびに変化します。

![サンプル Azure Cognitive Search Power BI テンプレート](media/knowledge-store-connect-power-bi/powerbi-sample-template-portal-only.png "サンプル Power BI テンプレート")

> [!NOTE]
> テンプレートはウィザードの実行中にダウンロードされますが、ナレッジ ストアが Azure Table Storage に実際に作成されるまで待ってから、使用する必要があります。

## <a name="connect-with-power-bi"></a>Power BI を使用した接続

1. Power BI Desktop を起動して、 **[データの取得]** をクリックします。

1. **[データの取得]** ウィンドウで **[Azure]** を選択し、 **[Azure Table Storage]** を選択します。

1. **[Connect]** をクリックします。

1. **[アカウント名または URL]** に、Azure Storage アカウントの名前を入力します (完全な URL が自動的に作成されます)。

1. メッセージが表示されたら、ストレージ アカウント キーを入力します。

1. 前のチュートリアルで作成したホテル レビュー データが含まれるテーブルを選択します。 

   + ポータルのチュートリアルでは、テーブル名は *hotelReviewsSsDocument*、*hotelReviewsSsEntities*、*hotelReviewsSsKeyPhrases*、*hotelReviewsSsPages* です。 
   
   + REST のチュートリアルでは、テーブル名は *hotelReviewsDocument*、*hotelReviewsPages*、*hotelReviewsKeyPhrases*、*hotelReviewsSentiment* です。

1. **[読み込み]** をクリックします。

1. 上部のリボンの **[クエリを編集]** をクリックして **Power Query エディター** を開きます。

   ![Power Query を開く](media/knowledge-store-connect-power-bi/powerbi-edit-queries.png "Power Query を開く")

1. *[hotelReviewsSsDocument]* を選択し、 *[PartitionKey]* 、 *[RowKey]* 、 *[Timestamp]* の各列を削除します。 
   ![テーブルを編集する](media/knowledge-store-connect-power-bi/powerbi-edit-table.png "テーブルを編集する")

1. テーブルの右上にある対立する矢印を含んだアイコンをクリックして、 *[コンテンツ]* を展開します。 列が一覧表示されたら、すべての列を選択し、"metadata" で始まる列の選択を解除します。 **[OK]** をクリックすると、選択した列が表示されます。

   ![コンテンツを展開する](media/knowledge-store-connect-power-bi/powerbi-expand-content-table.png "コンテンツを展開する")

1. 列の左上にある [ABC-123] アイコンをクリックして、次の列のデータ型を変更します。

   + *[content.latitude]* と *[Content.longitude]* には、 **[小数]** を選択します。
   + *[Content.reviews_date]* と *[Content.reviews_dateAdded]* には、 **[日付/時刻]** を選択します。

   ![データ型を変更する](media/knowledge-store-connect-power-bi/powerbi-change-type.png "データ型を変更する")

1. *[hotelReviewsSsPages]* を選択し、手順 9. と手順 10. を繰り返して列を削除し、 *[コンテンツ]* を展開します。
1. *[Content.SentimentScore]* のデータ型を **[小数]** に変更します。
1. *[hotelReviewsSsKeyPhrases]* を選択し、手順 9. と手順 10. を繰り返して列を削除し、 *[コンテンツ]* を展開します。 このテーブルについては、データ型を変更しません。

1. コマンド バーで、 **[閉じて適用する]** をクリックします。

1. 左側のナビゲーション ウィンドウで [モデル] タイルをクリックし、3 つすべてのテーブル間のリレーションシップが Power BI によって表示されていることを確認します。

   ![リレーションシップを検証する](media/knowledge-store-connect-power-bi/powerbi-relationships.png "リレーションシップを検証する")

1. それぞれのリレーションシップをダブルクリックし、 **[クロスフィルターの方向]** が **[両方]** に設定されていることを確認します。  これにより、フィルターを適用したときにビジュアルが更新されます。

1. 左側のナビゲーション ペインで [レポート] タイルをクリックし、視覚エフェクトを使用してデータを探索します。 テキスト フィールドの場合、テーブルとカードは便利な視覚エフェクトです。 3 つの各テーブルからフィールドを選択して、テーブルまたはカードに入力できます。 

<!-- ## Try with larger data sets

We purposely kept the data set small to avoid charges for a demo walkthrough. For a more realistic experience, you can create and then attach a billable Cognitive Services resource to enable a larger number of transactions against the sentiment analyzer, keyphrase extraction, and language detector skills.

Create new containers in Azure Blob storage and upload each CSV file to its own container. Specify one of these containers in the data source creation step in Import data wizard.

| Description | Link |
|-------------|------|
| Free tier   | [HotelReviews_Free.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D) |
| Small (500 Records) | [HotelReviews_Small.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Small.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D) |
| Medium (6000 Records)| [HotelReviews_Medium.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Medium.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D)
| Large (Full dataset 35000 Records) | [HotelReviews_Large.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Large.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D). Be aware that very large data sets are expensive to process. This one costs roughly $1000 U.S dollars.|

In the enrichment step of the wizard, attach a billable [Cognitive Services](../cognitive-services/cognitive-services-apis-create-account.md) resource, created at the *S0* tier, in the same region as Azure Cognitive Search to use larger data sets. 

  ![Create a Cognitive Services resource](media/knowledge-store-connect-power-bi/create-cognitive-service.png "Create a Cognitive Services resource") -->

## <a name="clean-up"></a>クリーンアップ

独自のサブスクリプションを使用している場合は、プロジェクトの最後に、作成したリソースがまだ必要かどうかを確認してください。 リソースを実行したままにすると、お金がかかる場合があります。 リソースは個別に削除することも、リソース グループを削除してリソースのセット全体を削除することもできます。

ポータルの左側のナビゲーション ウィンドウにある **[すべてのリソース]** または **[リソース グループ]** リンクを使って、リソースを検索および管理できます。

無料サービスを使っている場合は、3 つのインデックス、インデクサー、およびデータソースに制限されることに注意してください。 ポータルで個別の項目を削除して、制限を超えないようにすることができます。

## <a name="next-steps"></a>次のステップ

Storage Explorer を使用してこのナレッジ ストアを探索する方法については、次の記事を参照してください。

> [!div class="nextstepaction"]
> [Storage Explorer を使用した表示](knowledge-store-view-storage-explorer.md)