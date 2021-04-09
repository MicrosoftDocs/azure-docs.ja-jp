---
title: クイック スタート:REST API を使用して PowerShell で検索インデックスを作成する
titleSuffix: Azure Cognitive Search
description: この REST API クイックスタートでは、PowerShell の Invoke-RestMethod と Azure Cognitive Search REST API を使用して、インデックスを作成し、データを読み込み、クエリを実行する方法について説明します。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.devlang: rest-api
ms.date: 11/17/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9dd9de9dcb01e9be200e07e5925d8b856432b620
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94742380"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-powershell-using-rest-apis"></a>クイック スタート:REST API を使用して PowerShell に Azure Cognitive Search インデックスを作成する
> [!div class="op_single_selector"]
> * [PowerShell (REST)]()
> * [C#](./search-get-started-dotnet.md)
> * [REST](search-get-started-rest.md)
> * [Python](search-get-started-python.md)
> * [ポータル](search-get-started-portal.md)
> 

この記事では、PowerShell および [Azure Cognitive Search REST API](/rest/api/searchservice/) を使用して Azure Cognitive Search のインデックスを作成、読み込み、クエリを実行するプロセスについて説明します。 この記事では、PowerShell コマンドを対話的に実行する方法について説明します。 または、同じ操作を実行する [PowerShell スクリプトをダウンロードして実行する](https://github.com/Azure-Samples/azure-search-powershell-samples/tree/master/Quickstart)こともできます。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件

このクイックスタートでは、次のサービスとツールが必要です。 

+ [PowerShell 5.1 以降](https://github.com/PowerShell/PowerShell) (シーケンシャルおよび対話型の手順で [Invoke-restmethod](/powershell/module/Microsoft.PowerShell.Utility/Invoke-RestMethod) を使用します)。

+ [Azure Cognitive Search サービスを作成](search-create-service-portal.md)するか、現在のサブスクリプションから[既存のサービスを見つけます](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。 このクイック スタート用には、無料のサービスを使用できます。 

## <a name="copy-a-key-and-url"></a>キーと URL をコピーする

REST 呼び出しには、要求ごとにサービス URL とアクセス キーが必要です。 両方を使用して検索サービスが作成されるので、Azure Cognitive Search をサブスクリプションに追加した場合は、次の手順に従って必要な情報を入手してください。

1. [Azure portal にサインイン](https://portal.azure.com/)し、ご使用の検索サービスの **[概要]** ページで、URL を入手します。 たとえば、エンドポイントは `https://mydemo.search.windows.net` のようになります。

2. **[設定]**  >  **[キー]** で、サービスに対する完全な権限の管理キーを取得します。 管理キーをロールオーバーする必要がある場合に備えて、2 つの交換可能な管理キーがビジネス継続性のために提供されています。 オブジェクトの追加、変更、および削除の要求には、主キーまたはセカンダリ キーのどちらかを使用できます。

![HTTP エンドポイントとアクセス キーを取得する](media/search-get-started-rest/get-url-key.png "HTTP エンドポイントとアクセス キーを取得する")

すべての要求では、サービスに送信されるすべての要求に API キーが必要です。 有効なキーがあれば、要求を送信するアプリケーションとそれを処理するサービスの間で、要求ごとに信頼を確立できます。

## <a name="connect-to-azure-cognitive-search"></a>Azure Cognitive Search に接続する

1. PowerShell では、 **$headers** オブジェクトを作成して、コンテンツ タイプと API キーを格納します。 管理 API キー (YOUR-ADMIN-API-KEY) を検索サービスに有効なキーに置き換えます。 このヘッダーはセッション中に 1 度設定するだけで済みますが、すべての要求に追加されます。 

    ```powershell
    $headers = @{
    'api-key' = '<YOUR-ADMIN-API-KEY>'
    'Content-Type' = 'application/json' 
    'Accept' = 'application/json' }
    ```

2. サービスのインデックスのコレクションを指定する **$url** オブジェクトを作成します。 サービス名 (YOUR-SEARCH-SERVICE-NAME) を有効な検索サービスに置き換えます。

    ```powershell
    $url = "https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes?api-version=2020-06-30&$select=name"
    ```

3. **Invoke-restmethod** を実行して、サービスに GET 要求を送信し、接続を確認します。 **Convertto-json** を追加して、サービスから返信された応答を表示できるようにします。

    ```powershell
    Invoke-RestMethod -Uri $url -Headers $headers | ConvertTo-Json
    ```

   サービスが空で、インデックスが含まれていない場合、結果は次の例のようになります。 それ以外の場合は、インデックス定義の JSON 表現が表示されます。

    ```
    {
        "@odata.context":  "https://mydemo.search.windows.net/$metadata#indexes",
        "value":  [

                ]
    }
    ```

## <a name="1---create-an-index"></a>1 - インデックスの作成

ポータルを使用している場合を除き、データを読み込むには、サービスにインデックスが存在する必要があります。 この手順では、インデックスを定義して、それをサービスにプッシュします。 この手順では[インデックスの作成 REST API](/rest/api/searchservice/create-index) を使用します。

インデックスの必要な要素には、名前とフィールド コレクションが含まれます。 フィールド コレクションは *ドキュメント* の構造を定義します。 各フィールドには、名前、型、およびその使用方法を決定する属性 (たとえば、フルテキスト検索可能、フィルター可能、または検索結果で取得可能) があります。 インデックス内には、`Edm.String` 型のフィールドのいずれかをドキュメント ID の *キー* として指定する必要があります。

このインデックスは "hotels-quickstart" という名前で、次に示すフィールド定義が含まれています。 これは、他のチュートリアル記事で使用されている、より大きい [Hotels インデックス](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/Hotels_IndexDefinition.JSON)のサブセットです。 このクイックスタートでは、簡潔にするためにフィールド定義が切り取られています。

1. この例を PowerShell に貼り付けて、インデックス スキーマを含む **$body** オブジェクトを作成します。

    ```powershell
    $body = @"
    {
        "name": "hotels-quickstart",  
        "fields": [
            {"name": "HotelId", "type": "Edm.String", "key": true, "filterable": true},
            {"name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": true, "facetable": false},
            {"name": "Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.lucene"},
            {"name": "Category", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
            {"name": "Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "sortable": false, "facetable": true},
            {"name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "sortable": true, "facetable": true},
            {"name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": true, "sortable": true, "facetable": true},
            {"name": "Rating", "type": "Edm.Double", "filterable": true, "sortable": true, "facetable": true},
            {"name": "Address", "type": "Edm.ComplexType", 
            "fields": [
            {"name": "StreetAddress", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "searchable": true},
            {"name": "City", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
            {"name": "StateProvince", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
            {"name": "PostalCode", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
            {"name": "Country", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true}
            ]
         }
      ]
    }
    "@
    ```

2. URI にご利用のサービスのインデックスのコレクションと *hotels-quickstart* インデックスを設定します。

    ```powershell
    $url = "https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart?api-version=2020-06-30"
    ```

3. **$url**、 **$headers**、および **$body** を使用してコマンドを実行して、サービスにインデックスを作成します。 

    ```powershell
    Invoke-RestMethod -Uri $url -Headers $headers -Method Put -Body $body | ConvertTo-Json
    ```

    結果は、次のようになります (簡潔にするため最初の 2 つのフィールドに切り捨てられています)。

    ```
    {
        "@odata.context":  "https://mydemo.search.windows.net/$metadata#indexes/$entity",
        "@odata.etag":  "\"0x8D6EDE28CFEABDA\"",
        "name":  "hotels-quickstart",
        "defaultScoringProfile":  null,
        "fields":  [
                    {
                        "name":  "HotelId",
                        "type":  "Edm.String",
                        "searchable":  true,
                        "filterable":  true,
                        "retrievable":  true,
                        "sortable":  true,
                        "facetable":  true,
                        "key":  true,
                        "indexAnalyzer":  null,
                        "searchAnalyzer":  null,
                        "analyzer":  null,
                        "synonymMaps":  ""
                    },
                    {
                        "name":  "HotelName",
                        "type":  "Edm.String",
                        "searchable":  true,
                        "filterable":  false,
                        "retrievable":  true,
                        "sortable":  true,
                        "facetable":  false,
                        "key":  false,
                        "indexAnalyzer":  null,
                        "searchAnalyzer":  null,
                        "analyzer":  null,
                        "synonymMaps":  ""
                    },
    . . .
    ```

> [!Tip]
> 検証のため、ポータルでインデックスの一覧を確認することもできます。

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 - ドキュメントを読み込む

ドキュメントをプッシュするには、インデックスの URL エンドポイントに対して HTTP POST 要求を使用します。 このタスクの REST API は、[ドキュメントの追加、更新、または削除](/rest/api/searchservice/addupdate-or-delete-documents)です。

1. この例を PowerShell に貼り付けて、アップロードするドキュメントを含む **$body** オブジェクトを作成します。 

    この要求には、2 つの完全なレコードと 1 つの部分的なレコードが含まれています。 部分的なレコードは、不完全なドキュメントをアップロードすることを示します。 `@search.action` パラメーターは、インデックス作成を行う方法を指定します。 有効な値には、upload、merge、mergeOrUpload、および delete が含まれます。 mergeOrUpload の動作では、hotelId = 3 の新しい文書が作成されるか、内容が既に存在している場合はそれが更新されます。

    ```powershell
    $body = @"
    {
        "value": [
        {
        "@search.action": "upload",
        "HotelId": "1",
        "HotelName": "Secret Point Motel",
        "Description": "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
        "Category": "Boutique",
        "Tags": [ "pool", "air conditioning", "concierge" ],
        "ParkingIncluded": false,
        "LastRenovationDate": "1970-01-18T00:00:00Z",
        "Rating": 3.60,
        "Address": 
            {
            "StreetAddress": "677 5th Ave",
            "City": "New York",
            "StateProvince": "NY",
            "PostalCode": "10022",
            "Country": "USA"
            } 
        },
        {
        "@search.action": "upload",
        "HotelId": "2",
        "HotelName": "Twin Dome Motel",
        "Description": "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
        "Category": "Boutique",
        "Tags": [ "pool", "free wifi", "concierge" ],
        "ParkingIncluded": false,
        "LastRenovationDate": "1979-02-18T00:00:00Z",
        "Rating": 3.60,
        "Address": 
            {
            "StreetAddress": "140 University Town Center Dr",
            "City": "Sarasota",
            "StateProvince": "FL",
            "PostalCode": "34243",
            "Country": "USA"
            } 
        },
        {
        "@search.action": "upload",
        "HotelId": "3",
        "HotelName": "Triple Landscape Hotel",
        "Description": "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
        "Category": "Resort and Spa",
        "Tags": [ "air conditioning", "bar", "continental breakfast" ],
        "ParkingIncluded": true,
        "LastRenovationDate": "2015-09-20T00:00:00Z",
        "Rating": 4.80,
        "Address": 
            {
            "StreetAddress": "3393 Peachtree Rd",
            "City": "Atlanta",
            "StateProvince": "GA",
            "PostalCode": "30326",
            "Country": "USA"
            } 
        },
        {
        "@search.action": "upload",
        "HotelId": "4",
        "HotelName": "Sublime Cliff Hotel",
        "Description": "Sublime Cliff Hotel is located in the heart of the historic center of Sublime in an extremely vibrant and lively area within short walking distance to the sites and landmarks of the city and is surrounded by the extraordinary beauty of churches, buildings, shops and monuments. Sublime Cliff is part of a lovingly restored 1800 palace.",
        "Category": "Boutique",
        "Tags": [ "concierge", "view", "24-hour front desk service" ],
        "ParkingIncluded": true,
        "LastRenovationDate": "1960-02-06T00:00:00Z",
        "Rating": 4.60,
        "Address": 
            {
            "StreetAddress": "7400 San Pedro Ave",
            "City": "San Antonio",
            "StateProvince": "TX",
            "PostalCode": "78216",
            "Country": "USA"
            }
        }
    ]
    }
    "@
    ```

1. *hotels-quickstart* ドキュメント コレクションへのエンドポイントを設定し、インデックス操作 (indexes/hotels-quickstart/docs/index) を含めます。

    ```powershell
    $url = "https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs/index?api-version=2020-06-30"
    ```

1. **$url**、 **$headers**、 **$body** を使用してコマンドを実行し、hotels-quickstart インデックスにドキュメントを読み込みます。

    ```powershell
    Invoke-RestMethod -Uri $url -Headers $headers -Method Post -Body $body | ConvertTo-Json
    ```
    結果は次の例のようになります。 [状態コード 201](/rest/api/searchservice/HTTP-status-codes) が表示されます。

    ```
    {
        "@odata.context":  "https://mydemo.search.windows.net/indexes(\u0027hotels-quickstart\u0027)/$metadata#Collection(Microsoft.Azure.Search.V2019_05_06.IndexResult)",
        "value":  [
                    {
                        "key":  "1",
                        "status":  true,
                        "errorMessage":  null,
                        "statusCode":  201
                    },
                    {
                        "key":  "2",
                        "status":  true,
                        "errorMessage":  null,
                        "statusCode":  201
                    },
                    {
                        "key":  "3",
                        "status":  true,
                        "errorMessage":  null,
                        "statusCode":  201
                    },
                    {
                        "key":  "4",
                        "status":  true,
                        "errorMessage":  null,
                        "statusCode":  201
                    }
                ]
    }
    ```

## <a name="3---search-an-index"></a>3 - インデックスの検索

この手順では、[Search Documents API](/rest/api/searchservice/search-documents) を使用してインデックスのクエリを実行する方法を示します。

$url の検索では、必ず単一引用符を使用してください。 クエリ文字列に **$** 文字が含まれているときに、文字列全体を単一引用符で囲むと、それらをエスケープする必要がなくなります。

1. *hotels-quickstart* ドキュメント コレクションへのエンドポイントを設定し、**search** パラメーターを追加してクエリ文字列を渡します。 
  
   この文字列では、空の検索が実行され (search=*)、任意のドキュメントのランクなしの一覧 (search score = 1.0) が返されます。 既定では、Azure Cognitive Search から一度に 50 個の一致が返されます。 構造化されているので、このクエリではドキュメント全体の構造と値が返されます。 **$count=true** を追加して、結果に含まれるすべてのドキュメントの数を取得します。

    ```powershell
    $url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?api-version=2020-06-30&search=*&$count=true'
    ```

1. コマンドを実行して、 **$url** をサービスに送信します。

    ```powershell
    Invoke-RestMethod -Uri $url -Headers $headers | ConvertTo-Json
    ```

    結果は次の出力のようになります。

    ```
    {
    "@odata.context":  "https://mydemo.search.windows.net/indexes(\u0027hotels-quickstart\u0027)/$metadata#docs(*)",
    "@odata.count":  4,
    "value":  [
                  {
                      "@search.score":  0.1547872,
                      "HotelId":  "2",
                      "HotelName":  "Twin Dome Motel",
                      "Description":  "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
                      "Category":  "Boutique",
                      "Tags":  "pool free wifi concierge",
                      "ParkingIncluded":  false,
                      "LastRenovationDate":  "1979-02-18T00:00:00Z",
                      "Rating":  3.6,
                      "Address":  "@{StreetAddress=140 University Town Center Dr; City=Sarasota; StateProvince=FL; PostalCode=34243; Country=USA}"
                  },
                  {
                      "@search.score":  0.009068266,
                      "HotelId":  "3",
                      "HotelName":  "Triple Landscape Hotel",
                      "Description":  "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel\u0027s restaurant services.",
                      "Category":  "Resort and Spa",
                      "Tags":  "air conditioning bar continental breakfast",
                      "ParkingIncluded":  true,
                      "LastRenovationDate":  "2015-09-20T00:00:00Z",
                      "Rating":  4.8,
                      "Address":  "@{StreetAddress=3393 Peachtree Rd; City=Atlanta; StateProvince=GA; PostalCode=30326; Country=USA}"
                  },
                . . . 
    ```

構文について大まかに把握するため、その他のクエリ例をいくつか試してください。 文字列の検索、逐語的な $filter クエリの実行、結果セットの制限、特定のフィールドへの検索範囲の設定などを行うことができます。

```powershell
# Query example 1
# Search the entire index for the terms 'restaurant' and 'wifi'
# Return only the HotelName, Description, and Tags fields
$url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?api-version=2020-06-30&search=restaurant wifi&$count=true&$select=HotelName,Description,Tags'

# Query example 2 
# Apply a filter to the index to find hotels rated 4 or higher
# Returns the HotelName and Rating. Two documents match.
$url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?api-version=2020-06-30&search=*&$filter=Rating gt 4&$select=HotelName,Rating'

# Query example 3
# Take the top two results, and show only HotelName and Category in the results
$url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?api-version=2020-06-30&search=boutique&$top=2&$select=HotelName,Category'

# Query example 4
# Sort by a specific field (Address/City) in ascending order

$url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?api-version=2020-06-30&search=pool&$orderby=Address/City asc&$select=HotelName, Address/City, Tags, Rating'
```
## <a name="clean-up-resources"></a>リソースをクリーンアップする

独自のサブスクリプションを使用している場合は、プロジェクトの最後に、作成したリソースがまだ必要かどうかを確認してください。 リソースを実行したままにすると、お金がかかる場合があります。 リソースは個別に削除することも、リソース グループを削除してリソースのセット全体を削除することもできます。

ポータルの左側のナビゲーション ウィンドウにある **[すべてのリソース]** または **[リソース グループ]** リンクを使って、リソースを検索および管理できます。

無料サービスを使っている場合は、3 つのインデックス、インデクサー、およびデータソースに制限されることに注意してください。 ポータルで個別の項目を削除して、制限を超えないようにすることができます。 

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、PowerShell を使用して、Azure Cognitive Search でコンテンツを作成してアクセスするための基本的なワークフローをステップ実行しました。 この概念を考慮に入れて、Azure データ ソースからインデックスを付けるなど、より高度なシナリオに進むことをお勧めします。

> [!div class="nextstepaction"]
> [REST チュートリアル: Azure Cognitive Search での半構造化されたデータ (JSON BLOB) のインデックス作成と検索](search-semi-structured-data.md)
