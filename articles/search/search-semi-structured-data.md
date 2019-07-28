---
title: チュートリアル:JSON BLOB で半構造化されたデータのインデックスを作成する - Azure Search
description: Azure Search REST API と Postman を使用して、半構造化された Azure JSON BLOB のインデックスを作成し、検索する方法について説明します。
author: heidisteen
manager: cgronlun
services: search
ms.service: search
ms.topic: tutorial
ms.date: 05/02/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 3184b839087944d8d4335927810ec31d8876866e
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2019
ms.locfileid: "67485332"
---
# <a name="rest-tutorial-index-and-search-semi-structured-data-json-blobs-in-azure-search"></a>REST チュートリアル:Azure Search での半構造化されたデータ (JSON BLOB) のインデックス作成と検索

Azure Search は、半構造化データの読み取り方法を解している[インデクサー](search-indexer-overview.md)を使用して、Azure Blob Storage に格納されている JSON のドキュメントや配列のインデックスを作成することができます。 半構造化データには、データ内のコンテンツを区別するタグやマーキングが含まれます。 このデータは、非構造化データ (全体にインデックスを付ける必要がある) と正式に構造化されたデータ (フィールドごとにインデックス付け可能な、リレーショナル データベース スキーマなどのデータ モデルに準拠) を折衷するものです。

このチュートリアルでは、[Azure Search の REST API](https://docs.microsoft.com/rest/api/searchservice/) と REST クライアントを使用して次のタスクを実行します。

> [!div class="checklist"]
> * Azure BLOB コンテナー用に Azure Search データ ソースを構成する
> * 検索可能なコンテンツを格納する Azure Search インデックスを作成する
> * コンテナーを読み取って検索可能なコンテンツを Azure Blob Storage から抽出するようにインデクサーを構成して実行する
> * 作成したインデックスを検索する

## <a name="prerequisites"></a>前提条件

このクイック スタートでは、次のサービス、ツール、およびデータを使用します。 

[Azure Search サービスを作成](search-create-service-portal.md)するか、現在のサブスクリプションから[既存のサービスを見つけます](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。 このチュートリアル用には、無料のサービスを使用できます。 

サンプル データの格納に使用される [Azure ストレージ アカウントを作成](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)します。

Azure Search に要求を送信するための [Postman デスクトップ アプリ](https://www.getpostman.com/)。

[Clinical-trials-json.zip](https://github.com/Azure-Samples/storage-blob-integration-with-cdn-search-hdi/raw/master/clinical-trials-json.zip) には、このチュートリアルで使用されるデータが含まれています。 このファイルをローカルのフォルダーにダウンロードし、展開します。 データの出典は [clinicaltrials.gov](https://clinicaltrials.gov/ct2/results) で、このチュートリアルで使用するために JSON に変換しています。

## <a name="get-a-key-and-url"></a>キーと URL を入手する

REST 呼び出しには、要求ごとにサービス URL とアクセス キーが必要です。 両方を使用して検索サービスが作成されるので、Azure Search をサブスクリプションに追加した場合は、次の手順に従って必要な情報を入手してください。

1. [Azure portal にサインイン](https://portal.azure.com/)し、ご使用の検索サービスの **[概要]** ページで、URL を入手します。 たとえば、エンドポイントは `https://mydemo.search.windows.net` のようになります。

1. **[設定]**  >  **[キー]** で、サービスに対する完全な権限の管理者キーを取得します。 管理キーをロールオーバーする必要がある場合に備えて、2 つの交換可能な管理キーがビジネス継続性のために提供されています。 オブジェクトの追加、変更、および削除の要求には、主キーまたはセカンダリ キーのどちらかを使用できます。

![HTTP エンドポイントとアクセス キーを取得する](media/search-get-started-postman/get-url-key.png "HTTP エンドポイントとアクセス キーを取得する")

すべての要求では、サービスに送信されるすべての要求に API キーが必要です。 有効なキーがあれば、要求を送信するアプリケーションとそれを処理するサービスの間で、要求ごとに信頼を確立できます。

## <a name="prepare-sample-data"></a>サンプル データの準備

1. [Azure portal にサインインし](https://portal.azure.com)、Azure ストレージ アカウントに移動して **[BLOB]** をクリックし、 **[+ コンテナー]** をクリックします。

1. [BLOB コンテナーを作成](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)してサンプル データを含めます。 パブリック アクセス レベルは、有効な任意の値に設定できます。

1. コンテナーが作成されたら、コンテナーを開いてコマンド バーの **[アップロード]** を選択します。

   ![コマンド バーの [アップロード]](media/search-semi-structured-data/upload-command-bar.png "コマンド バーの [アップロード]")

1. サンプル ファイルを含むフォルダーに移動します。 すべてを選択し、 **[アップロード]** をクリックします。

   ![ファイルをアップロードする](media/search-semi-structured-data/clinicalupload.png "ファイルをアップロードする")

アップロードが完了したら、データ コンテナー内の独自のサブフォルダーにファイルが表示されます。

## <a name="set-up-postman"></a>Postman の設定

Postman を開始し、HTTP 要求を設定します。 このツールに慣れていない場合は、[Postman を使用して Azure Search REST API を調べる方法](search-get-started-postman.md)に関するページを参照してください。

このチュートリアルでの各呼び出しの要求メソッドは **POST** です。 ヘッダー キーは "Content-type" と "api-key" です。 ヘッダー キーの値は、それぞれ "application/json" と "管理者キー" (管理者キーは、検索の主キーを表すプレースホルダー) です。 本文は、呼び出しの実際のコンテンツを配置する場所です。 使用するクライアントによっては、クエリの作成方法にいくつかのバリエーションがありますが、それらは基本的な機能です。

  ![半構造化検索](media/search-semi-structured-data/postmanoverview.png)

Postman を使用して、検索サービスに対して 3 つの API 呼び出しを行い、データ ソース、インデックス、およびインデクサーを作成します。 データ ソースには、ストレージ アカウントと JSON データへのポインターが含まれています。 データを読み込むときに、検索サービスは接続を行います。

クエリ文字列では API バージョンを指定する必要があり、各呼び出しで **201 Created** が返される必要があります。 JSON 配列の使用を目的として一般公開が開始されている API バージョンは、`2019-05-06` です。

REST クライアントから次の 3 つの API 呼び出しを実行します。

## <a name="create-a-data-source"></a>データ ソースを作成する

[データ ソースの作成 API](https://docs.microsoft.com/rest/api/searchservice/create-data-source) で、インデックス作成の対象データを指定する Azure Search オブジェクトが作成されます。

この呼び出しのエンドポイントは `https://[service name].search.windows.net/datasources?api-version=2019-05-06` です。 `[service name]` を検索サービスの名前に置き換えます。 

この呼び出しでは、要求の本文にストレージ アカウントの名前、ストレージ アカウント キー、および BLOB コンテナー名を含める必要があります。 ストレージ アカウント キーは、Azure Portal のストレージ アカウントの **[アクセス キー]** にあります。 その場所を次の図に示します。

  ![半構造化検索](media/search-semi-structured-data/storagekeys.png)

呼び出しの本文の `[storage account name]`、`[storage account key]`、および `[blob container name]` を置き換えてから、呼び出しを実行してください。

```json
{
    "name" : "clinical-trials-json",
    "type" : "azureblob",
    "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=[storage account name];AccountKey=[storage account key];" },
    "container" : { "name" : "[blob container name]"}
}
```

応答は次のようになります。

```json
{
    "@odata.context": "https://exampleurl.search.windows.net/$metadata#datasources/$entity",
    "@odata.etag": "\"0x8D505FBC3856C9E\"",
    "name": "clinical-trials-json",
    "description": null,
    "type": "azureblob",
    "subtype": null,
    "credentials": {
        "connectionString": "DefaultEndpointsProtocol=https;AccountName=[mystorageaccounthere];AccountKey=[[myaccountkeyhere]]];"
    },
    "container": {
        "name": "[mycontainernamehere]",
        "query": null
    },
    "dataChangeDetectionPolicy": null,
    "dataDeletionDetectionPolicy": null
}
```

## <a name="create-an-index"></a>インデックスを作成する
    
2 つ目の呼び出しは[インデックスの作成 API](https://docs.microsoft.com/rest/api/searchservice/create-indexer) であり、検索可能なすべてのデータを格納する Azure Search インデックスを作成します。 インデックスでは、すべてのパラメーターとその属性を指定します。

この呼び出しの URL は `https://[service name].search.windows.net/indexes?api-version=2019-05-06` です。 `[service name]` を検索サービスの名前に置き換えます。

最初に、URL を置き換えます。 次のコードをコピーして本文に貼り付け、クエリを実行します。

```json
{
  "name": "clinical-trials-json-index",  
  "fields": [
  {"name": "FileName", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": false, "filterable": false, "sortable": true},
  {"name": "Description", "type": "Edm.String", "searchable": true, "retrievable": false, "facetable": false, "filterable": false, "sortable": false},
  {"name": "MinimumAge", "type": "Edm.Int32", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": true},
  {"name": "Title", "type": "Edm.String", "searchable": true, "retrievable": true, "facetable": false, "filterable": true, "sortable": true},
  {"name": "URL", "type": "Edm.String", "searchable": false, "retrievable": false, "facetable": false, "filterable": false, "sortable": false},
  {"name": "MyURL", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": false, "filterable": false, "sortable": false},
  {"name": "Gender", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
  {"name": "MaximumAge", "type": "Edm.Int32", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": true},
  {"name": "Summary", "type": "Edm.String", "searchable": true, "retrievable": true, "facetable": false, "filterable": false, "sortable": false},
  {"name": "NCTID", "type": "Edm.String", "key": true, "searchable": true, "retrievable": true, "facetable": false, "filterable": true, "sortable": true},
  {"name": "Phase", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
  {"name": "Date", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": false, "filterable": false, "sortable": true},
  {"name": "OverallStatus", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
  {"name": "OrgStudyId", "type": "Edm.String", "searchable": true, "retrievable": true, "facetable": false, "filterable": true, "sortable": false},
  {"name": "HealthyVolunteers", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
  {"name": "Keywords", "type": "Collection(Edm.String)", "searchable": true, "retrievable": true, "facetable": true, "filterable": false, "sortable": false},
  {"name": "metadata_storage_last_modified", "type":"Edm.DateTimeOffset", "searchable": false, "retrievable": true, "filterable": true, "sortable": false},
  {"name": "metadata_storage_size", "type":"Edm.String", "searchable": false, "retrievable": true, "filterable": true, "sortable": false},
  {"name": "metadata_content_type", "type":"Edm.String", "searchable": true, "retrievable": true, "filterable": true, "sortable": false}
  ],
  "suggesters": [
  {
    "name": "sg",
    "searchMode": "analyzingInfixMatching",
    "sourceFields": ["Title"]
  }
  ]
}
```

応答は次のようになります。

```json
{
    "@odata.context": "https://exampleurl.search.windows.net/$metadata#indexes/$entity",
    "@odata.etag": "\"0x8D505FC00EDD5FA\"",
    "name": "clinical-trials-json-index",
    "fields": [
        {
            "name": "FileName",
            "type": "Edm.String",
            "searchable": false,
            "filterable": false,
            "retrievable": true,
            "sortable": true,
            "facetable": false,
            "key": false,
            "indexAnalyzer": null,
            "searchAnalyzer": null,
            "analyzer": null,
            "synonymMaps": []
        },
        {
            "name": "Description",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "retrievable": false,
            "sortable": false,
            "facetable": false,
            "key": false,
            "indexAnalyzer": null,
            "searchAnalyzer": null,
            "analyzer": null,
            "synonymMaps": []
        },
        ...
          "scoringProfiles": [],
    "defaultScoringProfile": null,
    "corsOptions": null,
    "suggesters": [],
    "analyzers": [],
    "tokenizers": [],
    "tokenFilters": [],
    "charFilters": []
}
```

## <a name="create-and-run-an-indexer"></a>インデクサーの作成と実行

インデクサーはデータ ソースに接続してターゲットの検索インデックスにデータをインポートし、データ更新を自動化するスケジュールを必要に応じて提供します。 REST API は[インデクサーの作成](https://docs.microsoft.com/rest/api/searchservice/create-indexer)です。

この呼び出しの URL は `https://[service name].search.windows.net/indexers?api-version=2019-05-06` です。 `[service name]` を検索サービスの名前に置き換えます。

最初に、URL を置き換えます。 次のコードをコピーして本文に貼り付け、要求を送信します。 要求はすぐに処理されます。 応答が返された時点で、フルテキスト検索可能なインデックスが得られます。

```json
{
  "name" : "clinical-trials-json-indexer",
  "dataSourceName" : "clinical-trials-json",
  "targetIndexName" : "clinical-trials-json-index",
  "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
}
```

応答は次のようになります。

```json
{
    "@odata.context": "https://exampleurl.search.windows.net/$metadata#indexers/$entity",
    "@odata.etag": "\"0x8D505FDE143D164\"",
    "name": "clinical-trials-json-indexer",
    "description": null,
    "dataSourceName": "clinical-trials-json",
    "targetIndexName": "clinical-trials-json-index",
    "schedule": null,
    "parameters": {
        "batchSize": null,
        "maxFailedItems": null,
        "maxFailedItemsPerBatch": null,
        "base64EncodeKeys": null,
        "configuration": {
            "parsingMode": "jsonArray"
        }
    },
    "fieldMappings": [],
    "enrichers": [],
    "disabled": null
}
```

## <a name="search-your-json-files"></a>JSON ファイルの検索

最初のドキュメントが読み込まれたらすぐに、検索を始めることができます。 この作業には、ポータルの[**検索エクスプローラー**](search-explorer.md)を使用します。

Azure portal で、検索サービスの **[概要]** ページを開き、 **[インデックス]** 一覧で作成したインデックスを見つけます。

作成したインデックスを必ず選択してください。 

  ![非構造化検索](media/search-semi-structured-data/indexespane.png)

### <a name="user-defined-metadata-search"></a>ユーザー定義のメタデータの検索

前述のとおり、さまざまな方法 (フルテキスト検索、システム プロパティ、ユーザー定義のメタデータ) でデータを照会できます。 システム プロパティとユーザー定義のメタデータは、ターゲット インデックスの作成時に**取得可能**としてマークされている場合、`$select` パラメーターでのみ検索できます。 作成後にインデックス内のパラメーターを変更することはできません。 ただし、パラメーターの追加は可能です。

基本的なクエリの例として `$select=Gender,metadata_storage_size` があります。このクエリは、これらの 2 つのパラメーターに戻り値を制限します。

  ![半構造化検索](media/search-semi-structured-data/lastquery.png)

より複雑なクエリの例として `$filter=MinimumAge ge 30 and MaximumAge lt 75` があります。このクエリは、MinimumAge パラメーターが 30 以上および MaximumAge パラメーターが 75 未満の結果のみ返します。

  ![半構造化検索](media/search-semi-structured-data/metadatashort.png)

その他のクエリを試してみてもかまいません。 論理演算子 (and、or、not) と比較演算子 (eq、ne、gt、lt、ge、le) を使用できます。 文字列比較では大文字と小文字が区別されます。

`$filter` パラメーターは、インデックスの作成時にフィルター可能としてマークされたメタデータでのみ使用できます。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

チュートリアルの後で最も速くクリーンアップする方法は、Azure Search サービスが含まれているリソース グループを削除することです。 リソース グループを削除することで、そのすべての内容を完全に削除することができます。 ポータルでは、リソース グループ名は Azure Search サービスの [概要] ページに表示されます。

## <a name="next-steps"></a>次の手順

JSON BLOB のインデックス作成には、いくつかの方法と複数のオプションがあります。 次の手順では、さまざまなオプションの検討とテストを行い、自分のシナリオに最適なオプションを確認します。

> [!div class="nextstepaction"]
> [Azure Search BLOB インデクサーを使用して JSON BLOB のインデックスを作成する方法](search-howto-index-json-blobs.md)
