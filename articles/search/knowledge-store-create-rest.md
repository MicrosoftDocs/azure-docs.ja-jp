---
title: REST を使用してナレッジ ストアを作成する - Azure Search
description: REST API と Postman を使用して Azure Search のナレッジ ストアを作成し、コグニティブ検索パイプラインからのエンリッチメントを永続化します。
author: lobrien
services: search
ms.service: search
ms.topic: tutorial
ms.date: 10/01/2019
ms.author: laobri
ms.openlocfilehash: b67f0cf60d279c7bc52b4114d29c37847f5c57f1
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/10/2019
ms.locfileid: "72244459"
---
# <a name="create-an-azure-search-knowledge-store-by-using-rest"></a>REST を使用して Azure Search のナレッジ ストアを作成する

ナレッジ ストアは、Azure Search の機能の 1 つです。AI エンリッチメント パイプラインからの出力を、後から下流で行われる分析などの処理に使用できるよう永続化するものです。 AI エンリッチ パイプラインは、画像ファイルや構造化されていないテキスト ファイルを受け取って、Azure Search を使用してそのインデックスを作成し、Azure Cognitive Services の AI エンリッチメント (画像分析、自然言語処理など) を適用したうえで、Azure Storage 内のナレッジ ストアにその結果を保存します。 Azure portal で Power BI や Storage Explorer などのツールを使用して、ナレッジ ストアを調べることができます。

この記事では、REST API インターフェイスを使用して AI エンリッチメントを取り込み、インデックスを作成して、一連のホテルのレビューに適用します。 ホテルのレビューは、Azure Blob Storage にインポートされます。 結果は、Azure Table Storage にナレッジ ストアとして保存されます。

ナレッジ ストアを作成したら、[Storage Explorer](knowledge-store-view-storage-explorer.md) または [Power BI](knowledge-store-connect-power-bi.md) を使用してそのナレッジ ストアにアクセスする方法を学習します。

## <a name="create-services"></a>サービスを作成する

次のサービスを作成します。

- [Azure Search サービス](search-create-service-portal.md)を作成するか、現在のサブスクリプションから[既存のサービスを見つけます](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。 このチュートリアル用には、無料のサービスを使用できます。

- サンプル データとナレッジ ストアを格納するための [Azure ストレージ アカウント](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)を作成します。 ストレージ アカウントには、自分の Azure Search サービスと同じ場所 (米国西部など) を使用する必要があります。 "**アカウントの種類**" の値は、"**StorageV2 (汎用 V2)** " (既定) または "**Storage (汎用 V1)** " にする必要があります。

- 推奨:Azure Search に要求を送信するための [Postman デスクトップ アプリ](https://www.getpostman.com/)入手します。 REST API は、HTTP 要求および応答を操作できる任意のツールで使用できます。 Postman は、REST API を調べる場合に適しています。 この記事では Postman を使用します。 また、この記事の[ソース コード](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/knowledge-store/KnowledgeStore.postman_collection.json)には、Postman の要求のコレクションが含まれています。 

## <a name="store-the-data"></a>データを格納する

Azure Search のインデクサーからホテルのレビューにアクセスして AI エンリッチメント パイプラインを通じて取り込むことができるよう、その CSV ファイルを Azure Blob Storage に読み込みます。

### <a name="create-a-blob-container-by-using-the-data"></a>データを使用して BLOB コンテナーを作成する

1. CSV ファイルに保存された[ホテルのレビュー データ](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D) (HotelReviews_Free.csv) をダウンロードします。 このデータはホテルに関する顧客のフィードバックを含んでおり、出典は Kaggle.com です。
1. [Azure portal](https://portal.azure.com) にサインインし、お使いの Azure ストレージ アカウントに移動します。
1. [BLOB コンテナー](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)を作成します。 コンテナーを作成するには、ストレージ アカウントの左側のメニューで **[BLOB]** を選択し、 **[コンテナー]** を選択します。
1. 新しいコンテナーの **[名前]** に「**hotel-reviews**」と入力します。
1. **[パブリック アクセス レベル]** では任意の値を選択します。 今回使用したのは既定値です。
1. **[OK]** を選択して BLOB コンテナーを作成します。
1. 新しい **hotels-review** コンテナーを開き、 **[アップロード]** を選択し、最初の手順でダウンロードした HotelReviews-Free.csv ファイルを選択します。

    ![データをアップロードする](media/knowledge-store-create-portal/upload-command-bar.png "ホテルのレビューをアップロードする")

1. **[アップロード]** を選択して CSV ファイルを Azure Blob Storage にインポートします。 新しいコンテナーが表示されます。

    ![BLOB コンテナーの作成](media/knowledge-store-create-portal/hotel-reviews-blob-container.png "BLOB コンテナーの作成")

## <a name="configure-postman"></a>Postman を構成する

Postman をインストールして設定します。

### <a name="download-and-install-postman"></a>Postman をダウンロードしてインストールする

1. [Postman コレクション ソース コード](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/knowledge-store/KnowledgeStore.postman_collection.json)をダウンロードします。
1. **[File]\(ファイル\)**  >  **[Import]\(インポート\)** を選択して、ソース コードを Postman にインポートします。
1. **[Collections]\(コレクション\)** タブを選択し、 **[...]** (省略記号) ボタンを選択します。
1. **[編集]** を選択します。 
   
   ![ナビゲーションが表示される Postman アプリ](media/knowledge-store-create-rest/postman-edit-menu.png "Postman で [Edit]\(編集\) メニューに移動する")
1. **[Edit]\(編集\)** ダイアログ ボックスで、 **[Variables]\(変数\)** タブを選択します。 

**[Variables]\(変数\)** タブには、二重中かっこ内で特定の変数が出現するたびに、Postman でスワップされる値を追加できます。 たとえば、Postman によって、記号 `{{admin-key}}` は `admin-key` に設定した現在の値に置き換えられます。 Postman では、URL、ヘッダー、要求本文などの置き換えが行われます。 

`admin-key` の値を取得するには、Azure Search サービスに移動し、 **[キー]** タブを選択します。`search-service-name` および `storage-account-name` を「[サービスを作成する](#create-services)」で選択した値に変更します。 ストレージ アカウントの **[アクセス キー]** タブの値を使用して `storage-connection-string` を設定します。その他の値は既定値のままにしておくことができます。

![Postman アプリの変数タブ](media/knowledge-store-create-rest/postman-variables-window.png "Postman の変数ウインドウ")


| 変数    | 情報の入手元 |
|-------------|-----------------|
| `admin-key` | Azure Search サービスの **[キー]** タブにあります。  |
| `api-version` | **2019-05-06-Preview** のままにします。 |
| `datasource-name` | **hotel-reviews-ds** のままにします。 | 
| `indexer-name` | **hotel-reviews-ixr** のままにします。 | 
| `index-name` | **hotel-reviews-ix** のままにします。 | 
| `search-service-name` | Azure Search サービスの実際の名前。 URL は `https://{{search-service-name}}.search.windows.net` です。 | 
| `skillset-name` | **hotel-reviews-ss** のままにします。 | 
| `storage-account-name` | ストレージ アカウントの実際の名前。 | 
| `storage-connection-string` | ストレージ アカウントの **[アクセス キー]** タブで、 **[key1]**  >  **[接続文字列]** を選択します。 | 
| `storage-container-name` | **hotel-reviews** のままにします。 | 

### <a name="review-the-request-collection-in-postman"></a>Postman の要求コレクションの確認

ナレッジ ストアを作成する場合は、次の 4 つの HTTP 要求を発行する必要があります。 

- **インデックスを作成するための PUT 要求**:このインデックスでは、Azure Search で使用され、返されるデータが保持されます。
- **データソースを作成するための POST 要求**:このデータソースによって、Azure Search の動作がデータとナレッジ ストアのストレージ アカウントに接続されます。 
- **スキルセットを作成するための PUT 要求**:スキルセットでは、自分のデータとナレッジ ストアの構造に適用されるエンリッチメントを指定します。
- **インデクサーを作成するための PUT 要求**:インデクサーを実行すると、データの読み取り、スキルセットの適用、結果の格納が行われます。 この要求は最後に実行する必要があります。

[ソース コード](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/knowledge-store/KnowledgeStore.postman_collection.json)には、4 つの要求を持つ Postman コレクションが含まれています。 要求を発行するには、Postman で要求のタブを選択します。 次に、`api-key` と `Content-Type` の要求ヘッダーを追加します。 `api-key` の値を `{{admin-key}}` に設定します。 `Content-type` の値を `application/json` に設定します。 

![Postman のヘッダーのインターフェイスを示すスクリーンショット](media/knowledge-store-create-rest/postman-headers-ui.png)

> [!Note]
> すべての要求で `api-key` および `Content-type` ヘッダーを設定する必要があります。 Postman で変数が認識されている場合、前のスクリーンショットの `{{admin-key}}` のように、変数はオレンジ色のテキストで表示されます。 変数のスペルが間違っている場合は、赤いテキストで表示されます。
>

## <a name="create-an-azure-search-index"></a>Azure Search インデックスの作成

強化の検索、フィルター処理、および適用時に関心のあるデータを表すために、Azure Search インデックスを作成します。 PUT 要求を `https://{{search-service-name}}.search.windows.net/indexes/{{index-name}}?api-version={{api-version}}` に発行して、インデックスを作成します。 Postman では、二重中かっこで囲まれた記号 (`{{search-service-name}}`、`{{index-name}}`、`{{api-version}}` など) は「[Postman を構成する](#configure-postman)」で設定した値に置き換えられます。 別のツールを使用して REST コマンドを発行する場合は、これらの変数を自分で置き換える必要があります。

要求の本文で自分の Azure Search インデックスの構造を設定します。 Postman で、`api-key` および `Content-type` ヘッダーを設定した後、要求の **[本文]** ウィンドウに移動します。 次の JSON が表示されます。 そうでない場合は、 **[未加工]**  >  **[JSON (application/json)]** を選択し、本文として次のコードを貼り付けます。

```JSON
{
    "name": "{{index-name}}",
    "fields": [
        { "name": "address", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "categories", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "city", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false },
        { "name": "country", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "latitude", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "longitude", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "name", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false },
        { "name": "postalCode", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "province", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_date", "type": "Edm.DateTimeOffset", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_dateAdded", "type": "Edm.DateTimeOffset", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_rating", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_text", "type": "Edm.String", "filterable": false,  "sortable": false, "facetable": false },
        { "name": "reviews_title", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_username", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "AzureSearch_DocumentKey", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false, "key": true },
        { "name": "metadata_storage_content_type", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "metadata_storage_size", "type": "Edm.Int64", "searchable": false, "filterable": false, "sortable": false, "facetable": false},
        { "name": "metadata_storage_last_modified", "type": "Edm.DateTimeOffset", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "metadata_storage_name", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "metadata_storage_path", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "Sentiment", "type": "Collection(Edm.Double)", "searchable": false, "filterable": true, "retrievable": true, "sortable": false, "facetable": true },
        { "name": "Language", "type": "Edm.String", "filterable": true, "sortable": false, "facetable": true },
        { "name": "Keyphrases", "type": "Collection(Edm.String)", "filterable": true, "sortable": false, "facetable": true }
    ]
}

```

このインデックス定義は、ユーザーに提示したいデータ (ホテルの名前、レビューの内容、日付)、検索メタデータ、AI 強化データ (センチメント、キーフレーズ、言語) の組み合わせです。

**[送信]** を選択して PUT 要求を発行します。 状態 `201 - Created` が表示されます。 別の状態が表示される場合は、 **[本文]** ウィンドウでエラー メッセージを含む JSON 応答を探します。 

## <a name="create-the-datasource"></a>データソースを作成する

次に、「[データを格納する](#store-the-data)」で格納したホテル データに Azure Search 接続します。 データソースを作成するには、POST 要求を `https://{{search-service-name}}.search.windows.net/datasources?api-version={{api-version}}` に送信します。 前述のように、`api-key` および `Content-Type` ヘッダーを設定する必要があります。 

Postman で、 **[Create Datasource]\(データソースの作成\)** 要求、 **[Body]\(本文\)** ウィンドウの順に移動します。 次のコードが表示されます。

```json
{
  "name" : "{{datasource-name}}",
  "description" : "Demo files to demonstrate knowledge store capabilities.",
  "type" : "azureblob",
  "credentials" : { "connectionString" : "{{storage-connection-string}}" },
  "container" : { "name" : "{{storage-container-name}}" }
}
```

**[Send]\(送信\)** を選択して POST 要求を発行します。 

## <a name="create-the-skillset"></a>スキルセットを作成する 

次の手順ではスキルセットを指定します。これにより、適用される強化と、結果が格納されるナレッジ ストアの両方が指定されます。 Postman で、 **[Create the Skillset]\(スキルセットの作成\)** タブを選択します。この要求では、PUT が `https://{{search-service-name}}.search.windows.net/skillsets/{{skillset-name}}?api-version={{api-version}}` に送信されます。 前と同じように、`api-key` および `Content-type` ヘッダーを設定します。 

最上位レベルに大きなオブジェクトが 2 つあります (`skills` と `knowledgeStore`)。 `skills` オブジェクト内の各オブジェクトはエンリッチメント サービスです。 各エンリッチメント サービスには、`inputs` と `outputs` があります。 `LanguageDetectionSkill` には `Language` の出力 `targetName` があります。 このノードの値は、他のほとんどのスキルで入力として使用されます。 ソースは `document/Language` です。 あるノードの出力を別のノードの入力として使用するこの機能は、`ShaperSkill` でより顕著です。ここでは、ナレッジ ストアのテーブルに向かうデータの流れを指定します。

`knowledge_store` オブジェクトは、`{{storage-connection-string}}` Postman 変数を介してストレージ アカウントに接続します。 `knowledge_store` には、強化されたドキュメントと、ナレッジ ストア内のテーブルおよび列との間のマッピングのセットが格納されています。 

スキルセットを生成するには、Postman の **[Send]\(送信\)** ボタンを選択して要求を PUT します。

```json
{
    "name": "{{skillset-name}}",
    "description": "Skillset to detect language, extract key phrases, and detect sentiment",
    "skills": [ 
        {
            "@odata.type": "#Microsoft.Skills.Text.SplitSkill", 
            "context": "/document/reviews_text", "textSplitMode": "pages", "maximumPageLength": 5000,
            "inputs": [ 
                { "name": "text", "source": "/document/reviews_text" },
                { "name": "languageCode", "source": "/document/Language" }
            ],
            "outputs": [
                { "name": "textItems", "targetName": "pages" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
            "context": "/document/reviews_text/pages/*",
            "inputs": [
                { "name": "text", "source": "/document/reviews_text/pages/*" },
                { "name": "languageCode", "source": "/document/Language" }
            ],
            "outputs": [
                { "name": "score", "targetName": "Sentiment" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
            "context": "/document",
            "inputs": [
                { "name": "text", "source": "/document/reviews_text" }
            ],
            "outputs": [
                { "name": "languageCode", "targetName": "Language" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
            "context": "/document/reviews_text/pages/*",
            "inputs": [
                { "name": "text",  "source": "/document/reviews_text/pages/*" },
                { "name": "languageCode",  "source": "/document/Language" }
            ],
            "outputs": [
                { "name": "keyPhrases" , "targetName": "Keyphrases" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
            "context": "/document",
            "inputs": [
                { "name": "name",  "source": "/document/name" },
                { "name": "reviews_date",  "source": "/document/reviews_date" },
                { "name": "reviews_rating",  "source": "/document/reviews_rating" },
                { "name": "reviews_text",  "source": "/document/reviews_text" },
                { "name": "reviews_title",  "source": "/document/reviews_title" },
                { "name": "AzureSearch_DocumentKey",  "source": "/document/AzureSearch_DocumentKey" },
                { 
                    "name": "pages",
                    "sourceContext": "/document/reviews_text/pages/*",
                    "inputs": [
                        { "name": "SentimentScore", "source": "/document/reviews_text/pages/*/Sentiment" },
                        { "name": "LanguageCode", "source": "/document/Language" },
                        { "name": "Page", "source": "/document/reviews_text/pages/*" },
                        { 
                            "name": "keyphrase", "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
                            "inputs": [
                                { "name": "Keyphrases", "source": "/document/reviews_text/pages/*/Keyphrases/*" }
                            ]
                        }
                    ]
                }
            ],
            "outputs": [
                { "name": "output" , "targetName": "tableprojection" }
            ]
        }
    ],
    "knowledgeStore": {
        "storageConnectionString": "{{storage-connection-string}}",
        "projections": [
            {
                "tables": [
                    { "tableName": "hotelReviewsDocument", "generatedKeyName": "Documentid", "source": "/document/tableprojection" },
                    { "tableName": "hotelReviewsPages", "generatedKeyName": "Pagesid", "source": "/document/tableprojection/pages/*" },
                    { "tableName": "hotelReviewsKeyPhrases", "generatedKeyName": "KeyPhrasesid", "source": "/document/tableprojection/pages/*/keyphrase/*" },
                    { "tableName": "hotelReviewsSentiment", "generatedKeyName": "Sentimentid", "source": "/document/tableprojection/pages/*/sentiment/*" }
                ],
                "objects": []
            },
            {
                "tables": [
                    { 
                        "tableName": "hotelReviewsInlineDocument", "generatedKeyName": "Documentid", "sourceContext": "/document",
                        "inputs": [
                            { "name": "name", "source": "/document/name"},
                            { "name": "reviews_date", "source": "/document/reviews_date"},
                            { "name": "reviews_rating", "source": "/document/reviews_rating"},
                            { "name": "reviews_text", "source": "/document/reviews_text"},
                            { "name": "reviews_title", "source": "/document/reviews_title"},
                            { "name": "AzureSearch_DocumentKey", "source": "/document/AzureSearch_DocumentKey" }
                        ]
                    },
                    { 
                        "tableName": "hotelReviewsInlinePages", "generatedKeyName": "Pagesid", "sourceContext": "/document/reviews_text/pages/*",
                        "inputs": [
                            { "name": "SentimentScore", "source": "/document/reviews_text/pages/*/Sentiment"},
                            { "name": "LanguageCode", "source": "/document/Language"},
                            { "name": "Page", "source": "/document/reviews_text/pages/*" }
                        ]
                    },
                    { 
                        "tableName": "hotelReviewsInlineKeyPhrases", "generatedKeyName": "kpidv2", "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
                        "inputs": [
                            { "name": "Keyphrases", "source": "/document/reviews_text/pages/*/Keyphrases/*" }
                        ]
                    }
                ],
                "objects": []
            }
        ]
    }
}
```

## <a name="create-the-indexer"></a>インデクサーを作成する

最後の手順では、インデクサーを作成します。 インデクサーによってデータが読み取られ、スキルセットがアクティブ化されます。 Postman で **[Create Indexer]\(インデクサーの作成\)** 要求を選択し、本文を確認します。 これまでに作成したいくつかの他のリソース (データソース、インデックス、スキルセット) がインデクサーの定義によって参照されます。 

`parameters/configuration` オブジェクトでは、インデクサーがデータを取り込む方法を制御します。 ここでは、入力データは、ヘッダー行とコンマ区切り値がある単一のドキュメントに含まれています。 ドキュメント キーは、ドキュメントの一意の識別子です。 エンコード前のドキュメント キーはソース ドキュメントの URL です。 最後に、スキルセットの出力値 (言語コード、センチメント、キー フレーズなど) は、ドキュメント内の場所にマップされます。 `Language` には 1 つの値がありますが、`Sentiment` は `pages` の配列の各要素に適用されます。 `Keyphrases` は、`pages` 配列の各要素にも適用される配列です。

`api-key` および `Content-type` ヘッダーを設定し、要求の本文が次のソース コードのようになっていることを確認したら、Postman で **[Send]\(送信\)** を選択します。 Postman から `https://{{search-service-name}}.search.windows.net/indexers/{{indexer-name}}?api-version={{api-version}}` に PUT 要求が送信されます。 Azure Search でインデクサーが作成され、実行されます。 

```json
{
    "name": "{{indexer-name}}",
    "dataSourceName": "{{datasource-name}}",
    "skillsetName": "{{skillset-name}}",
    "targetIndexName": "{{index-name}}",
    "parameters": {
        "configuration": {
            "dataToExtract": "contentAndMetadata",
            "parsingMode": "delimitedText",
            "firstLineContainsHeaders": true,
            "delimitedTextDelimiter": ","
        }
    },
    "fieldMappings": [
        {
            "sourceFieldName": "AzureSearch_DocumentKey",
            "targetFieldName": "AzureSearch_DocumentKey",
            "mappingFunction": { "name": "base64Encode" }
        }
    ],
    "outputFieldMappings": [
        { "sourceFieldName": "/document/reviews_text/pages/*/Keyphrases/*", "targetFieldName": "Keyphrases" },
        { "sourceFieldName": "/document/Language", "targetFieldName": "Language" },
        { "sourceFieldName": "/document/reviews_text/pages/*/Sentiment", "targetFieldName": "Sentiment" }
    ]
}
```

## <a name="run-the-indexer"></a>インデクサーを実行する 

Azure portal で、Azure Search サービスの **[概要]** ページに移動します。 **[インデクサー]** タブを選択し、 **[hotels-reviews-ixr]** を選択します。 インデクサーがまだ実行されていない場合は、 **[実行]** を選択します。 インデックス作成タスクによって、言語認識に関連する警告が発生する場合があります。 このデータには、コグニティブ スキルでまだサポートされていない言語で記述されたレビューがいくつか含まれています。 

## <a name="next-steps"></a>次の手順

Cognitive Services を使用してデータをエンリッチし、その結果をナレッジ ストアに投影したら、エンリッチ済みのデータ セットを Storage Explorer または Power BI を使用して探索することができます。

Storage Explorer を使用してこのナレッジ ストアを調べる方法については、次のチュートリアルを参照してください。

> [!div class="nextstepaction"]
> [Storage Explorer を使用した表示](knowledge-store-view-storage-explorer.md)

このナレッジ ストアを Power BI に接続する方法については、次のチュートリアルを参照してください。

> [!div class="nextstepaction"]
> [Power BI を使用した接続](knowledge-store-connect-power-bi.md)

ここに示した手順をもう一度やってみたい場合や、別の AI エンリッチメントに関するチュートリアルに挑戦してみたい場合には、**hotel-reviews-idxr** インデクサーを削除してください。 インデクサーを削除すると、1 日あたりの無料トランザクションのカウンターがリセットされ、ゼロに戻ります。
