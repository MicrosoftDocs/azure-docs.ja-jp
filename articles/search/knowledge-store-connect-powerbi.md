---
title: Power BI を使用してナレッジ ストアに接続する - Azure Search
description: Azure portal で [データのインポート] ウィザードを使用してナレッジ ストアを作成し、解析および探索のために Power BI を使用して接続します。
author: heidisteen
services: search
ms.service: search
ms.subservice: cognitive-search
ms.topic: tutorial
ms.date: 07/30/2019
ms.author: heidist
ms.openlocfilehash: 1c7f297092760f2a92f524347a3c1a5e353d0965
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2019
ms.locfileid: "69635548"
---
# <a name="create-an-azure-search-knowledge-store-and-connect-using-power-bi"></a>Azure Search のナレッジ ストアを作成し、Power BI を使用して接続する

> [!Note]
> ナレッジ ストアはプレビュー段階であるため、運用環境では使用しないでください。 この機能は、[Azure Search REST API バージョン 2019-05-06-Preview](search-api-preview.md) で提供されています。 現時点で .NET SDK のサポートはありません。
>

ナレッジ ストアは Azure Search の機能の 1 つで、後から下流で行われる解析などの処理のために AI エンリッチメント パイプラインからの出力を保持するものです。 AI エンリッチ パイプラインは、サポート対象のデータ ソースから画像ファイルまたは構造化されていないテキスト ファイルを受け取り、Azure Search によるインデックス作成のために送信し、Cognitive Services の AI エンリッチメント (画像分析、自然言語処理など) を適用したうえで、Azure ストレージ内のナレッジ ストアにその結果を保存します。 ナレッジ ストアからは、Power BI や Storage Explorer などのツールをアタッチして結果を調べることができます。

この記事では、ポータル内でナレッジ ストアを作成し、Power BI Desktop の Power Query を使用して接続および探索を行います。 

このチュートリアルでは、カスタマー レビューと評価から成るデータ セットを使用し、Cognitive Services からセンチメント スコアリングを実施した後、Power Query を使ってドキュメントにクエリを実行します。 データは、Kaggle.com のホテル レビュー データを出典とするものです。 

## <a name="prerequisites"></a>前提条件

+ [ホテルのレビューの CSV ファイル (HotelReviews_Free.csv)](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D) をダウンロードします。 このデータ セットには、ホテルに関するカスタマー フィードバックのレコードが含まれています。

+ [Power BI Desktop](https://powerbi.microsoft.com/downloads/)

+ [Azure Search](search-create-service-portal.md)。 このチュートリアルでは、無料のサービスを使用できます。 

+ [Azure Storage](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)。 アカウントが "汎用" ( *[StorageV2 (汎用 V2)]* (既定) または *[Storage (汎用 V1)]* ) であることを確認してください。 リージョンは、Azure Search と同じものを選択します。
 
## <a name="prepare-data"></a>データを準備する 

.csv ファイルを Azure Blob Storage に読み込んで、Azure Search インデクサーからアクセスできるようにします。

1. [Azure portal にサインインし](https://portal.azure.com)、Azure ストレージ アカウントに移動して **[BLOB]** をクリックし、 **[+ コンテナー]** をクリックします。

1. サンプル データを含める [BLOB コンテナーを作成](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)します。 

   1. コンテナーに `hotel-reviews` という名前を付けます。 
   
   1. パブリック アクセス レベルを有効な任意の値に設定します。 今回使用したのは既定値です。

1. コンテナーが作成されたら、コンテナーを開いてコマンド バーの **[アップロード]** を選択します。

1. **HotelReviews_Free.csv** が格納されているフォルダーに移動し、ファイルを選択して **[アップロード]** をクリックします。

   ![.csv ファイルのアップロード](media/knowledge-store-howto-powerbi/hotel-reviews-blob-container.png ".csv ファイルのアップロード")

1. Azure ストレージで、接続文字列とコンテナー名を取得します。  これらの文字列はどちらも、データ ソース オブジェクトの作成時に必要になります。

   1. 概要ページで、 **[アクセス キー]** をクリックして、"*接続文字列*" をコピーします。 これは `DefaultEndpointsProtocol=https;` で始まり、`EndpointSuffix=core.windows.net` で終わります。 その間にアカウント名とキーが含まれています。 

   1. コンテナー名は、`hotel-reviews` または自分が割り当てた任意の名前です。 

## <a name="create-and-run-ai-enrichments"></a>AI エンリッチメントを作成および実行する

ナレッジ ストアの作成には、[データのインポート] ウィザードを使用します。 データ セットをインポートし、エンリッチメントを選択して、ナレッジ ストアとインデックスを構成したら、実行します。

1. Azure portal で、[自分の検索サービスを探します](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。

1. コマンド バーの **[データのインポート]** をクリックします。

1. ウィザードの最初のページで、データ ソース オブジェクトを作成します。

   - **[Azure Blob Storage]** を選択します。

   - データ ソースに *hotel-reviews-ds* などの名前を付けます。

   - 今回のデータは .csv なので、[解析モード] に **[区切りテキスト]** を選択すると共に、 **[最初の行にヘッダーが含まれています]** を選択して、区切り文字がコンマであることを確認します。

   - Azure Storage アカウントへの接続文字列は、ポータルの **[アクセス キー]** のところから取得できます。

   - コンテナー名も、ポータルの Azure Storage Blob の一覧から取得できます。

      ![データ ソース オブジェクトの作成](media/knowledge-store-howto-powerbi/hotel-reviews-ds.png "データ ソース オブジェクトの作成")

1. ウィザードの 2 ページ目で、エンリッチメントの追加とナレッジ ストアの構成を行います。

   - **[Cognitive Services をアタッチする]** では、1 日あたり 20 件までのトランザクションに対応できる Free リソースを利用できます。 HotelReviews_Free.csv のレコード数は 20 件未満です。

   - **[エンリッチメントの追加]** では、スキルセットに *hotel-reviews-ss* という名前を付け、 *[reviews_text]* フィールドを選択し、細分性のレベルとして *[Pages (5000 characters chunks)]\(ページ (5,000 文字のチャンク)\)* を選択してから、次の 3 つのコグニティブ スキルを選択します: *[キー フレーズを抽出]* 、 *[言語の検出]* 、 *[感情の検出]* 。

      ![スキルセットの作成](media/knowledge-store-howto-powerbi/hotel-reviews-ss.png "スキルセットの作成")

   - **[Save enrichments to knowledge store]\(エンリッチメントをナレッジ ストアに保存\)** では、自分の汎用 Azure Storage アカウントへの接続文字列を指定します。 このセクションで *[Azure table projects]\(Azure テーブル プロジェクト\)* オプションを選択すると、Azure Table Storage にナレッジ ストアが作成されます。

      ![ナレッジ ストアの構成](media/knowledge-store-howto-powerbi/hotel-reviews-ks.png "ナレッジ ストアの構成")
   
   **[次へ: 対象インデックスをカスタマイズします]** をクリックして、次の手順に進みます。

1. オプションで Azure Search でのフルテキスト検索クエリに使用するインデックスを構成します。 このチュートリアルは Power BI を Azure Table Storage に接続することを主眼としたものですが、 **[データのインポート]** ウィザードでは、Azure Search でのフルテキスト検索に使用するインデックスを作成することもできます。 

   ウィザードによってデータ ソースがサンプリングされ、フィールドとデータ型が推定されるので、必要とする動作の実現に必要な属性を選択する以外の作業は不要です。 たとえば、 *[取得可能]* はサービスからフィールドのコンテンツを取得できるという意味です。また、 *[検索可能]* は、選択したフィールドを対象にフルテキスト検索ができるようにするためのものです。

   - インデックスに *hotel-reviews-idx* などの名前を付けます。
   - 全フィールドを **[取得可能]** に設定します。
   - *[city]* 、 *[name]* 、 *[reviews_text]* 、 *[language]* 、 *[keyphrases]* を **[検索可能]** に設定します。
   - *[sentiment]* 、 *[language]* 、 *[keyphrases]* を **[フィルター可能]** と **[ファセット可能]** に設定します。 
   
    インデックスは、次の画像のようになります。

     ![インデックスの構成](media/knowledge-store-howto-powerbi/hotel-reviews-idx.png "インデックスの構成")

   **[次へ: インデクサーの作成]** をクリックして、次の手順に進みます。

1. インデクサーの構成として、名前と実行スケジュールを指定します。 このチュートリアルでは、インデクサー名に *hotel-reviews-idxr* を使用します。また、スケジュールは **[一回のみ]** とし、インデクサーが即座に実行されるようにします。

   インデクサーが実行されると、ここまでの構成がすべて稼働します。 抽出、処理、取り込みはいずれも、この段階で実行されます。

1. ポータル内の [通知] キューでインデックスの作成を監視します。 実行が完了するまでには、数分かかります。

## <a name="connect-with-power-bi"></a>Power BI を使用した接続

1. Power BI Desktop を起動して、 **[データの取得]** を選びます。

1. [データの取得] で、 **[Azure]** 、 **[Azure Table Storage]** の順に選択します。 **[接続]** をクリックします。

1. [アカウント名または URL] に、Azure Storage アカウントの名前を貼り付けます (完全な URL は自動で解決されます)。

1. アカウント キーを入力します。

1. [Document]、[KeyPhrases]、[Pages] を選択します。 これらは、ナレッジ ストアの構成で同じ名前の項目を選択したときに [データのインポート] ウィザードによって作成されるテーブルです。 **[読み込み]** をクリックします。

1. **[クエリを編集]** コマンドをクリックして Power Query を開きます。

   ![Power Query を開く](media/knowledge-store-howto-powerbi/powerbi-edit-queries.png "Power Query を開く")

1. [Documents] について、次のことを行います。

   - Azure Table Storage により作成された [PartitionKey]、[RowKey]、[Timestamp] の 3 列を削除します。 この解析で使用するリレーションシップは、ナレッジ ストアにより提供されます。

   - [Content] 列を展開します。

     ![テーブルの編集](media/knowledge-store-howto-powerbi/powerbi-edit-table.png "テーブルの編集")

1. すべてのフィールドを選択し、"metadata" から始まるものを選択解除します。

1. 次の各列について、それぞれの ABC-123 アイコンを使用してデータ型を修正します。

   - [Date] 列は **DateTime** にします。
   - *[Latitude]* は **[Decimal Number]** にします。
   - *[Longitude]* は **[Decimal Number]** にします。

1. [KeyPhrases] について、前の手順を繰り返します ([PartitionKey] などの列を削除し、[Content] 列を展開してから、 *[SentimentScore]* を **[Decimal Number]** に設定します)。

1. [Pages] にも、同じことをもう一度繰り返します ([PartitionKey] などの列を削除し、[Content] 列を展開します)。 このテーブルについては、データ型を変更しません。

1. Power Query のコマンド バーの左端にある **[Close and Apply]\(終了して適用\)** をクリックします。

1. ナレッジ ストアによりデータ内に作成されたリレーションシップを Power BI が認識できているかどうかを確認します。 左側のナビゲーション ウィンドウにあるリレーションシップ タイルをクリックします。 テーブルが 3 つとも関連付けられている必要があります。 リレーションシップを編集し、"クロスフィルターの方向" が両方に設定されていることを確認します。これにより、フィルターが適用されると、すべてのビジュアルが更新されます。

   ![リレーションシップの検証](media/knowledge-store-howto-powerbi/powerbi-relationships.png "リレーションシップの検証")

## <a name="try-with-larger-data-sets"></a>もっと大規模なデータ セットを使って試してみる

デモを目的としたこのチュートリアルでは、請求の発生を防ぐために意図的に小規模なデータ セットを使用していました。 もっと現実に近い体験をする希望する場合には、多数のトランザクションに対応できる請求対象の Cognitive Services リソースを作成し、センチメント アナライザー、キーフレーズ抽出、言語検出のスキルにアタッチすることもできます。

Azure Blob Storage に新しいコンテナーを作成し、CSV ファイルをそれぞれ専用のコンテナーにアップロードします。 [データのインポート] ウィザードでデータ ソースを作成する手順では、このなかのいずれかのコンテナーを指定します。

| 説明 | Link |
|-------------|------|
| Free レベル   | [HotelReviews_Free.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D) |
| S (500 レコード) | [HotelReviews_Small.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Small.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D) |
| M (6,000 レコード)| [HotelReviews_Medium.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Medium.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D)
| L (35,000 レコードの完全なデータセット) | [HotelReviews_Large.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Large.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D)。 非常に大きなデータセットは処理にかかるコストも大きくなるので注意してください。 これの場合、かかるコストは約 1,000 米ドルです。|

サイズの比較的大きなデータ セットを使用する場合には、ウィザードのエンリッチメントの手順で、Azure Search と同じリージョンに *S0* レベルで作成した有償の [Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) リソースをアタッチします。 

  ![Cognitive Services リソースの作成](media/knowledge-store-howto-powerbi/create-cognitive-service.png "Cognitive Services リソースの作成")

## <a name="next-steps"></a>次の手順

ここに示した手順をもう一度やってみたい場合や、別の AI エンリッチメントに関するチュートリアルを実践してみたい場合には、今回作成した *hotel-reviews-idx* インデクサーを削除してください。 インデクサーを削除すると、1 日あたりの無料トランザクションのカウンターがリセットされ、ゼロに戻ります。 

ナレッジ ストアに関するもっと詳細なチュートリアルについては、各種の REST API と Postman を使ってナレッジ ストアを作成する方法を紹介したこの次の記事に進んでください。

> [!div class="nextstepaction"]
> [ナレッジ ストアの使用を開始する](knowledge-store-howto.md)
