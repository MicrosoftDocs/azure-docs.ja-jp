---
title: チュートリアル:JSON BLOB の半構造化データのインデックス作成
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search REST API と Postman を使用して、半構造化 Azure JSON BLOB のインデックスを作成し、検索する方法について説明します。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 01/25/2021
ms.openlocfilehash: a7a010e3c60d6b96947597878fcd870e9845b2b3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98746139"
---
# <a name="tutorial-index-json-blobs-from-azure-storage-using-rest"></a>チュートリアル:Azure Storage に格納されている JSON BLOB のインデックスを REST で作成する

Azure Cognitive Search は、半構造化データの読み取り方法を解している[インデクサー](search-indexer-overview.md)を使用して、Azure Blob Storage に格納されている JSON のドキュメントや配列のインデックスを作成することができます。 半構造化データには、データ内のコンテンツを区別するタグやマーキングが含まれます。 このデータは、非構造化データ (全体にインデックスを付ける必要がある) と正式に構造化されたデータ (フィールドごとにインデックス付け可能な、リレーショナル データベース スキーマなどのデータ モデルに準拠) を折衷するものです。

このチュートリアルでは、Postman と [Search REST API](/rest/api/searchservice/) を使用して次のタスクを実行します。

> [!div class="checklist"]
> * Azure BLOB コンテナー用に Azure Cognitive Search データ ソースを構成する
> * 検索可能なコンテンツを格納する Azure Cognitive Search インデックスを作成する
> * コンテナーを読み取って検索可能なコンテンツを Azure Blob Storage から抽出するようにインデクサーを構成して実行する
> * 作成したインデックスを検索する

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件

+ [Azure ストレージ](../storage/common/storage-account-create.md)
+ [Postman デスクトップ アプリ](https://www.getpostman.com/)
+ [作成](search-create-service-portal.md)または[既存の検索サービスの用意](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) 

> [!Note]
> このチュートリアルには無料のサービスを使用できます。 無料の検索サービスでは、3 つのインデックス、3 つのインデクサー、3 つのデータ ソースという制限があります。 このチュートリアルでは、それぞれ 1 つずつ作成します。 開始する前に、ご利用のサービスに新しいリソースを受け入れる余地があることを確認してください。

## <a name="download-files"></a>ファイルのダウンロード

[Clinical-trials-json.zip](https://github.com/Azure-Samples/storage-blob-integration-with-cdn-search-hdi/raw/master/clinical-trials-json.zip) には、このチュートリアルで使用されるデータが含まれています。 このファイルをローカルのフォルダーにダウンロードし、展開します。 データの出典は [clinicaltrials.gov](https://clinicaltrials.gov/ct2/results) で、このチュートリアルで使用するために JSON に変換しています。

## <a name="1---create-services"></a>1 - サービスを作成する

このチュートリアルでは、インデックス作成とクエリに Azure Cognitive Search を使用し、そのデータを得る手段として Azure Blob Storage を使用しています。 

可能である場合は、近接性と管理性を高めるために、両方を同じリージョンおよびリソース グループ内に作成してください。 実際には、Azure Storage アカウントは任意のリージョンに置くことができます。

### <a name="start-with-azure-storage"></a>Azure Storage の使用を開始する

1. [Azure portal にサインイン](https://portal.azure.com/)し、 **[+ リソースの作成]** をクリックします。

1. *[ストレージ アカウント]* を検索し、Microsoft のストレージ アカウント オファリングを選択します。

   :::image type="content" source="media/cognitive-search-tutorial-blob/storage-account.png" alt-text="ストレージ アカウントの作成" border="false":::

1. [基本] タブでは、次の項目が必要です。 それ以外のすべてのものには、既定値をそのまま使用します。

   + **リソース グループ**。 既存のものを選択するか、新しいものを作成します。ただし、すべてのサービスに同じグループを使用して、それらをまとめて管理できるようにします。

   + **ストレージ アカウント名**。 同じ種類のリソースが複数存在することになると考えられる場合は、名前を使用して、種類とリージョンを基に区別が付くようにします (たとえば、*blobstoragewestus*)。 

   + **場所**。 可能であれば、Azure Cognitive Search と Cognitive Services に使用するのと同じ場所を選択します。 1 つの場所であれば、帯域幅の料金がかかりません。

   + **アカウントの種類**。 既定値の *[StorageV2 (general purpose v2)]\(StorageV2 (汎用 v2)\)* を選択します。

1. **[確認および作成]** をクリックしてサービスを作成します。

1. 作成されたら、 **[Go to the resource]\(リソースに移動\)** をクリックして [概要] ページを開きます。

1. **[BLOB]** サービスをクリックします。

1. [BLOB コンテナーを作成](../storage/blobs/storage-quickstart-blobs-portal.md)してサンプル データを含めます。 パブリック アクセス レベルは、有効な任意の値に設定できます。

1. コンテナーが作成されたら、コンテナーを開いてコマンド バーの **[アップロード]** を選択します。

   :::image type="content" source="media/search-semi-structured-data/upload-command-bar.png" alt-text="コマンド バーの [アップロード]" border="false":::

1. サンプル ファイルを含むフォルダーに移動します。 すべてを選択し、 **[アップロード]** をクリックします。

   :::image type="content" source="media/search-semi-structured-data/clinicalupload.png" alt-text="ファイルをアップロードする" border="false":::

アップロードが完了したら、データ コンテナー内の独自のサブフォルダーにファイルが表示されます。

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

次のリソースは Azure Cognitive Search であり、[ポータルで作成](search-create-service-portal.md)できます。 このチュートリアルは Free レベルを使用して完了できます。 

Azure Blob Storage と同様に、アクセス キーを収集してください。 さらに、要求の構築を始めるときに、各要求の認証に使用されるエンドポイントと管理者 API キーを指定する必要があります。

### <a name="get-a-key-and-url"></a>キーと URL を入手する

REST 呼び出しには、要求ごとにサービス URL とアクセス キーが必要です。 両方を使用して検索サービスが作成されるので、Azure Cognitive Search をサブスクリプションに追加した場合は、次の手順に従って必要な情報を入手してください。

1. [Azure portal にサインイン](https://portal.azure.com/)し、ご使用の検索サービスの **[概要]** ページで、URL を入手します。 たとえば、エンドポイントは `https://mydemo.search.windows.net` のようになります。

1. **[設定]**  >  **[キー]** で、サービスに対する完全な権限の管理キーを取得します。 管理キーをロールオーバーする必要がある場合に備えて、2 つの交換可能な管理キーがビジネス継続性のために提供されています。 オブジェクトの追加、変更、および削除の要求には、主キーまたはセカンダリ キーのどちらかを使用できます。

   :::image type="content" source="media/search-get-started-rest/get-url-key.png" alt-text="HTTP エンドポイントとアクセス キーを取得する" border="false":::

すべての要求では、サービスに送信されるすべての要求に API キーが必要です。 有効なキーがあれば、要求を送信するアプリケーションとそれを処理するサービスの間で、要求ごとに信頼を確立できます。

## <a name="2---set-up-postman"></a>2 - Postman を設定する

Postman を開始し、HTTP 要求を設定します。 このツールに慣れていない場合は、[REST API を使用した検索インデックスの作成](search-get-started-rest.md)に関するページを参照してください。

このチュートリアルで使用する各呼び出しの要求メソッドは **POST** と **GET** です。 検索サービスに対して 3 つの API 呼び出しを行い、データ ソース、インデックス、およびインデクサーを作成します。 データ ソースには、ストレージ アカウントと JSON データへのポインターが含まれています。 データを読み込むときに、検索サービスは接続を行います。

ヘッダーで "Content-type" を `application/json` に設定し、`api-key` に自分の Azure Cognitive Search サービスの管理者 API キーを設定します。 ヘッダーを設定したら、この演習の各要求でそれらを使用できます。

  :::image type="content" source="media/search-get-started-rest/postman-url.png" alt-text="Postman の要求 URL とヘッダー" border="false":::

URI では API バージョンを指定する必要があり、各呼び出しで **201 Created** が返される必要があります。 JSON 配列の使用を目的として一般公開が開始されている API バージョンは、`2020-06-30` です。

## <a name="3---create-a-data-source"></a>3 - データ ソースを作成する

[データ ソースの作成 API](/rest/api/searchservice/create-data-source) では、インデックス作成の対象データを指定する Azure Cognitive Search オブジェクトが作成されます。

1. この呼び出しのエンドポイントを `https://[service name].search.windows.net/datasources?api-version=2020-06-30` に設定します。 `[service name]` を検索サービスの名前に置き換えます。 

1. 次の JSON を要求本文にコピーします。

    ```json
    {
        "name" : "clinical-trials-json-ds",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=[storage account name];AccountKey=[storage account key];" },
        "container" : { "name" : "[blob container name]"}
    }
    ```

1. 接続文字列を自分のアカウントの有効な文字列に置き換えます。

1. "[blob container name]" を、サンプル データ用に作成したコンテナーに置き換えます。 

1. 要求を送信します。 応答は次のようになります。

    ```json
    {
        "@odata.context": "https://exampleurl.search.windows.net/$metadata#datasources/$entity",
        "@odata.etag": "\"0x8D505FBC3856C9E\"",
        "name": "clinical-trials-json-ds",
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

## <a name="4---create-an-index"></a>4 - インデックスを作成する

2 つ目の呼び出しは[インデックスの作成 API](/rest/api/searchservice/create-index) であり、検索可能なすべてのデータを格納する Azure Cognitive Search インデックスを作成します。 インデックスでは、すべてのパラメーターとその属性を指定します。

1. この呼び出しのエンドポイントを `https://[service name].search.windows.net/indexes?api-version=2020-06-30` に設定します。 `[service name]` を検索サービスの名前に置き換えます。

1. 次の JSON を要求本文にコピーします。

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
      ]
    }
   ```

1. 要求を送信します。 応答は次のようになります。

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
          }
    ```

## <a name="5---create-and-run-an-indexer"></a>5 - インデクサーを作成して実行する

インデクサーはデータ ソースに接続してターゲットの検索インデックスにデータをインポートし、データ更新を自動化するスケジュールを必要に応じて提供します。 REST API は[インデクサーの作成](/rest/api/searchservice/create-indexer)です。

1. この呼び出しの URL を `https://[service name].search.windows.net/indexers?api-version=2020-06-30` に設定します。 `[service name]` を検索サービスの名前に置き換えます。

1. 次の JSON を要求本文にコピーします。

    ```json
    {
      "name" : "clinical-trials-json-indexer",
      "dataSourceName" : "clinical-trials-json-ds",
      "targetIndexName" : "clinical-trials-json-index",
      "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
    }
    ```

1. 要求を送信します。 要求はすぐに処理されます。 応答が返された時点で、フルテキスト検索可能なインデックスが得られます。 応答は次のようになります。

    ```json
    {
        "@odata.context": "https://exampleurl.search.windows.net/$metadata#indexers/$entity",
        "@odata.etag": "\"0x8D505FDE143D164\"",
        "name": "clinical-trials-json-indexer",
        "description": null,
        "dataSourceName": "clinical-trials-json-ds",
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

## <a name="6---search-your-json-files"></a>6 - JSON ファイルを検索する

最初のドキュメントが読み込まれたらすぐに、検索を始めることができます。

1. 動詞を **GET** に変更します。

1. この呼び出しの URL を `https://[service name].search.windows.net/indexes/clinical-trials-json-index/docs?search=*&api-version=2020-06-30&$count=true` に設定します。 `[service name]` を検索サービスの名前に置き換えます。

1. 要求を送信します。 これは、指定のないフルテキスト検索クエリです。インデックスで取得可能としてマークされたすべてのフィールドがドキュメント数と共に返されます。 応答は次のようになります。

    ```json
    {
        "@odata.context": "https://exampleurl.search.windows.net/indexes('clinical-trials-json-index')/$metadata#docs(*)",
        "@odata.count": 100,
        "value": [
            {
                "@search.score": 1.0,
                "FileName": "NCT00000102.txt",
                "MinimumAge": 14,
                "Title": "Congenital Adrenal Hyperplasia: Calcium Channels as Therapeutic Targets",
                "MyURL": "https://azure.storagedemos.com/clinical-trials/NCT00000102.txt",
                "Gender": "Both",
                "MaximumAge": 35,
                "Summary": "This study will test the ability of extended release nifedipine (Procardia XL), a blood pressure medication, to permit a decrease in the dose of glucocorticoid medication children take to treat congenital adrenal hyperplasia (CAH).",
                "NCTID": "NCT00000102",
                "Phase": "Phase 1/Phase 2",
                "Date": "ClinicalTrials.gov processed this data on October 25, 2016",
                "OverallStatus": "Completed",
                "OrgStudyId": "NCRR-M01RR01070-0506",
                "HealthyVolunteers": "No",
                "Keywords": [],
                "metadata_storage_last_modified": "2019-04-09T18:16:24Z",
                "metadata_storage_size": "33060",
                "metadata_content_type": null
            },
            . . . 
    ```

1. 結果を制限してフィールドを絞り込むために、`$select` クエリ パラメーターを追加します (`https://[service name].search.windows.net/indexes/clinical-trials-json-index/docs?search=*&$select=Gender,metadata_storage_size&api-version=2020-06-30&$count=true`)。  このクエリでは 100 件のドキュメントが一致しますが、Azure Cognitive Search から結果として返されるのは、既定では 50 件のみです。

   :::image type="content" source="media/search-semi-structured-data/lastquery.png" alt-text="パラメーター化クエリ" border="false":::

1. より複雑なクエリの例として `$filter=MinimumAge ge 30 and MaximumAge lt 75` があります。このクエリは、MinimumAge パラメーターが 30 以上および MaximumAge パラメーターが 75 未満の結果のみ返します。 `$select` 式は `$filter` 式に置き換えてください。

   :::image type="content" source="media/search-semi-structured-data/metadatashort.png" alt-text="半構造化検索" border="false":::

論理演算子 (and、or、not) と比較演算子 (eq、ne、gt、lt、ge、le) を使用することもできます。 文字列比較では大文字と小文字が区別されます。 詳細と例については、[簡単なクエリを作成する](search-query-simple-examples.md)方法に関するページを参照してください。

> [!NOTE]
> `$filter` パラメーターは、インデックスの作成時にフィルター可能としてマークされたメタデータでのみ使用できます。

## <a name="reset-and-rerun"></a>リセットして再実行する

開発の初期の実験的な段階では、設計反復のための最も実用的なアプローチは、Azure Cognitive Search からオブジェクトを削除してリビルドできるようにすることです。 リソース名は一意です。 オブジェクトを削除すると、同じ名前を使用して再作成することができます。

インデックス、インデクサー、データ ソースは、ポータルを使用して削除できます。 または、**DELETE** を使用して、各オブジェクトの URL を指定します。 次のコマンドは、インデクサーを削除します。

```http
DELETE https://[YOUR-SERVICE-NAME].search.windows.net/indexers/clinical-trials-json-indexer?api-version=2020-06-30
```

正常に削除されると、状態コード 204 が返されます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

所有するサブスクリプションを使用している場合は、プロジェクトの終了時に、不要になったリソースを削除することをお勧めします。 リソースを実行したままにすると、お金がかかる場合があります。 リソースは個別に削除することも、リソース グループを削除してリソースのセット全体を削除することもできます。

ポータルの左側のナビゲーション ペインにある [すべてのリソース] または [リソース グループ] リンクを使って、リソースを検索および管理できます。

## <a name="next-steps"></a>次のステップ

Azure Blob のインデックス作成の基礎を理解したら、Azure Storage 内の JSON BLOB に使用されるインデクサーの構成について詳しく見てみましょう。

> [!div class="nextstepaction"]
> [JSON BLOB のインデックス作成の構成](search-howto-index-json-blobs.md)