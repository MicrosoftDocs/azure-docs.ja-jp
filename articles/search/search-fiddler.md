---
title: クイック スタート:Postman で REST API シリーズを調べる - Azure Search
description: Postman を使用して HTTP 要求と REST API 呼び出しを Azure Search に発行する方法。
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: quickstart
ms.date: 04/08/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 0e14131ce45d20b99c1b5d5885cb1eb24c975d03
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2019
ms.locfileid: "59269114"
---
# <a name="quickstart-explore-azure-search-rest-apis-using-postman"></a>クイック スタート:Postman を使用して Azure Search REST API シリーズを調べる
> [!div class="op_single_selector"]
> * [postman](search-fiddler.md)
> * [C#](search-create-index-dotnet.md)
> * [ポータル](search-get-started-portal.md)
> * [PowerShell](search-howto-dotnet-sdk.md)
>*

[Azure Search REST API](https://docs.microsoft.com/rest/api/searchservice) を調べる最も簡単な方法の 1 つは、Postman や他の Web テスト ツールを使用して HTTP 要求を作成し、応答を検査することです。 適切なツールと以下の手順を利用すると、コードを記述する前に要求を送信して応答を確認できます。

> [!div class="checklist"]
> * Web API テスト プール ツールをダウンロードする
> * ご使用の検索サービスのキーと URL を入手する
> * Azure Search への接続
> * インデックスを作成する
> * インデックスを読み込む
> * インデックスを検索する

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成し、[Azure Search にサインアップ](search-create-service-portal.md)してください。

## <a name="prerequisites"></a>前提条件

このクイック スタートでは、次のサービスとツールを使用します。 

[Azure Search サービスを作成](search-create-service-portal.md)するか、現在のサブスクリプションから[既存のサービスを見つけます](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。 このクイック スタート用には、無料のサービスを使用できます。 

Azure Search に要求を送信するために、[Postman デスクトップ アプリ](https://www.getpostman.com/)または [Telerik Fiddler](https://www.telerik.com/fiddler) を使用します。

## <a name="get-a-key-and-url"></a>キーと URL を入手する

REST 呼び出しには、要求ごとにサービス URL とアクセス キーが必要です。 両方を使用して検索サービスが作成されるので、Azure Search をサブスクリプションに追加した場合は、次の手順に従って必要な情報を入手してください。

1. [Azure portal にサインインし](https://portal.azure.com/)、ご使用の検索サービスの **[概要]** ページで、URL を入手します。 たとえば、エンドポイントは `https://mydemo.search.windows.net` のようになります。

1. **[設定]** > **[キー]** で、サービスに対する完全な権限の管理者キーを取得します。 管理キーをロールオーバーする必要がある場合に備えて、2 つの交換可能な管理キーがビジネス継続性のために提供されています。 オブジェクトの追加、変更、および削除の要求には、主キーまたはセカンダリ キーのどちらかを使用できます。

![HTTP エンドポイントとアクセス キーを取得する](media/search-fiddler/get-url-key.png "HTTP エンドポイントとアクセス キーを取得する")

すべての要求では、サービスに送信されるすべての要求に API キーが必要です。 有効なキーがあれば、要求を送信するアプリケーションとそれを処理するサービスの間で、要求ごとに信頼を確立できます。

## <a name="connect-to-azure-search"></a>Azure Search への接続

このセクションでは、任意の Web ツールを使用して、Azure Search への接続を設定します。 各ツールには、セッションの要求ヘッダー情報が保持されます。つまり、API キーとコンテンツタイプの入力は 1 度だけで済みます。

いずれかのツールでコマンド (GET、POST、PUT など) を選択し、URL エンドポイントを指定し、一部のタスクでは要求の本文に JSON を入力する必要があります。 完全な URL は、次のようになります。

    https://<placeholder-for-your-service-name>.search.windows.net/indexes?api-version=2017-11-11

HTTPS プレフィックス、サービスの名前、オブジェクト (この例では、インデックス コレクション) の名前、[API バージョン](search-api-versions.md)に注目してください。 API バージョンは必須の小文字の文字列で、最新バージョンの場合は "?api-version=2017-11-11" として指定します。 API バージョンは定期的に更新されます。 各要求に API バージョンを含めるので、使用するバージョンが完全に制御されます。  

要求ヘッダーの構成には、コンテンツ タイプと、Azure Search への認証に使用した API キーという 2 つの要素が含まれます。

    api-key: <placeholder-api-key-for-your-service>
    Content-Type: application/json

Postman で、次のスクリーンショットのように要求を作成します。 動詞として **GET** を選択し、URL を入力し、**[Send]\(送信\)** をクリックします。 このコマンドは Azure Search に接続し、インデックス コレクションを読み取り、接続が成功すると HTTP 状態コード 200 を返します。 ご使用のサービスにインデックスが既に作成されている場合、応答にはインデックスの定義も含まれます。

![Postman の要求ヘッダー][6]

## <a name="1---create-an-index"></a>1 - インデックスの作成

Azure Search では、通常、データを読み込む前にインデックスを作成します。 このタスクには、[インデックスの作成](https://docs.microsoft.com/rest/api/searchservice/create-index) REST API を使用します。 

URL を拡張して、`hotel` インデックス名を含めます。

Postman でこれを行うには:

1. 動詞を **PUT** に変更します。
2. この URL をコピーします。 `https://<placeholder-for-your-service-name>.search.windows.net/indexes/hotel?api-version=2017-11-11`
3. 要求の本文にインデックス定義 (下記参照) を入力します。
4. ページの下部にある **[Send]**

![Postman の要求本文][8]

### <a name="index-definition"></a>インデックスの定義

フィールド コレクションにより、ドキュメントの構造を定義します。 各ドキュメントにはこれらのフィールドが必要で、各フィールドにはデータ型が必要です。 文字列フィールドは全文検索に使用されるので、コンテンツを検索可能にする必要がある場合は、数値データを文字列としてキャストすることができます。

フィールドの属性によって、使用できるアクションが決まります。 REST API は、既定で多くのアクションを使用できます。 たとえば、すべての文字列は既定で検索可能、取得可能、フィルター可能、およびファセット可能です。 動作を無効にする必要がある場合は、属性を設定するだけで済むことがよくあります。

          {
         "name": "hotels",  
         "fields": [
           {"name": "hotelId", "type": "Edm.String", "key":true, "searchable": false},
           {"name": "baseRate", "type": "Edm.Double"},
           {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
           {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
           {"name": "hotelName", "type": "Edm.String"},
           {"name": "category", "type": "Edm.String"},
           {"name": "tags", "type": "Collection(Edm.String)"},
           {"name": "parkingIncluded", "type": "Edm.Boolean"},
           {"name": "smokingAllowed", "type": "Edm.Boolean"},
           {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
           {"name": "rating", "type": "Edm.Int32"},
           {"name": "location", "type": "Edm.GeographyPoint"}
          ]
         }

この要求を送信すると、インデックスが正常に作成されたことを示す HTTP 201 応答が返されます。 ポータルでこのアクションを確認することもできますが、ポータル ページには更新間隔があるので、最新情報が表示されるまでに 1 ～ 2 分かかる可能性があります。

> [!TIP]
> HTTP 504 が表示された場合は、URL で HTTPS の指定を確認してください。 HTTP 400 または 404 を確認するには、要求の本文をチェックして、コピーまたは貼り付けのエラーがないことを確認します。 HTTP 403 は、API キーに問題があることを示しています (キーが無効か、または API キーの指定方法に構文エラーがあります)。

## <a name="2---load-documents"></a>2 - ドキュメントを読み込む

インデックスの作成とインデックスの設定は別の手順です。 Azure Search では、インデックスにはすべての検索可能なデータが含まれており、それを JSON ドキュメントとして提供できます。 このタスクには、[ドキュメントの追加、更新、または削除](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) REST API を使用します。 

URL を拡張して、`docs` コレクションと `index` 操作を含めます。

Postman でこれを行うには:

1. 動詞を **POST** に変更します。
2. この URL をコピーします。 `https://<placeholder-for-your-service-name>.search.windows.net/indexes/hotels/docs/index?api-version=2017-11-11`
3. 要求の本文に JSON ドキュメント (下記参照) を入力します。
4. ページの下部にある **[Send]**

![Postman の要求ペイロード][10]

### <a name="json-documents-to-load-into-the-index"></a>インデックスに読み込む JSON ドキュメント

要求の本文には、hotels インデックスに追加する 4 つのドキュメントが含まれています。

         {
         "value": [
         {
             "@search.action": "upload",
             "hotelId": "1",
             "baseRate": 199.0,
             "description": "Best hotel in town",
             "description_fr": "Meilleur hôtel en ville",
             "hotelName": "Fancy Stay",
             "category": "Luxury",
             "tags": ["pool", "view", "wifi", "concierge"],
             "parkingIncluded": false,
             "smokingAllowed": false,
             "lastRenovationDate": "2010-06-27T00:00:00Z",
             "rating": 5,
             "location": { "type": "Point", "coordinates": [-122.131577, 47.678581] }
           },
           {
             "@search.action": "upload",
             "hotelId": "2",
             "baseRate": 79.99,
             "description": "Cheapest hotel in town",
             "description_fr": "Hôtel le moins cher en ville",
             "hotelName": "Roach Motel",
             "category": "Budget",
             "tags": ["motel", "budget"],
             "parkingIncluded": true,
             "smokingAllowed": true,
             "lastRenovationDate": "1982-04-28T00:00:00Z",
             "rating": 1,
             "location": { "type": "Point", "coordinates": [-122.131577, 49.678581] }
           },
           {
             "@search.action": "upload",
             "hotelId": "3",
             "baseRate": 279.99,
             "description": "Surprisingly expensive",
             "hotelName": "Dew Drop Inn",
             "category": "Bed and Breakfast",
             "tags": ["charming", "quaint"],
             "parkingIncluded": true,
             "smokingAllowed": false,
             "lastRenovationDate": null,
             "rating": 4,
             "location": { "type": "Point", "coordinates": [-122.33207, 47.60621] }
           },
           {
             "@search.action": "upload",
             "hotelId": "4",
             "baseRate": 220.00,
             "description": "This could be the one",
             "hotelName": "A Hotel for Everyone",
             "category": "Basic hotel",
             "tags": ["pool", "wifi"],
             "parkingIncluded": true,
             "smokingAllowed": false,
             "lastRenovationDate": null,
             "rating": 4,
             "location": { "type": "Point", "coordinates": [-122.12151, 47.67399] }
           }
          ]
         }

数秒で、セッション一覧に HTTP 200 応答が表示されます。 この応答は、ドキュメントが正常に作成されたことを示します。 

207 が表示された場合は、少なくとも 1 つのドキュメントのアップロードが失敗しています。 404 が表示された場合は、要求のヘッダーまたは本文に構文エラーがあります。`/docs/index` を含めるようにエンドポイントを変更したことを確認してください。

> [!Tip]
> 選択したデータ ソースについて、インデックス作成に必要なコードを簡易化してコード量を減らすことができる別の*インデクサー* アプローチを選択することができます。 詳細については、[インデクサーの操作](https://docs.microsoft.com/rest/api/searchservice/indexer-operations)に関するページを参照してください。


## <a name="3---search-an-index"></a>3 - インデックスの検索

インデックスとドキュメントが読み込まれたら、[Search Documents](https://docs.microsoft.com/rest/api/searchservice/search-documents) REST API を使用してクエリを発行することができます。

URL を拡張して、検索演算子を使用して指定されたクエリ文字列を含めます。

Postman でこれを行うには:

+ 動詞を **GET** に変更します。
+ この URL をコピーします。 `https://<placeholder-for-your-service-name>.search.windows.net/indexes/hotels/docs?search=motel&$count=true&api-version=2017-11-11`
+ ページの下部にある **[Send]**

このクエリでは、"motel" という用語を検索し、検索結果でドキュメント数を返します。 Postman で **[Send]\(送信\)** をクリックすると、要求と応答は次のスクリーンショットのようになります。 状態コードは 200 になります。

 ![Postman のクエリ応答][11]


## <a name="get-index-properties"></a>インデックス プロパティの取得
システム情報のクエリを実行して、ドキュメント数とストレージ消費量を取得することもできます。 `https://mydemo.search.windows.net/indexes/hotels/stats?api-version=2017-11-11`

Postman の場合、要求は次のようになります。また、応答にはドキュメント数とバイト単位の使用領域が含まれます。

 ![Postman のシステム クエリ][12]

API バージョンの構文が異なる点に注目してください。 この要求では、`?` を使用して API バージョンを付加しています。 `?` により、URL パスとクエリ文字列を区切り、& により、クエリ文字列内の各 "名前=値" ペアを区切ります。 このクエリのクエリ文字列では、API バージョンは最初で唯一の項目です。

この API の詳細については、[インデックスの統計情報の取得 (REST)](https://docs.microsoft.com/rest/api/searchservice/get-index-statistics) に関するページを参照してください。


## <a name="use-fiddler"></a>Fiddler の使用

このセクションは、Fiddler のスクリーンショットと手順に置き換わっているだけで、前のセクションと同等です。

### <a name="connect-to-azure-search"></a>Azure Search への接続

次のスクリーンショットのように要求を作成します。 動詞として **GET** を選択します。 `User-Agent=Fiddler` が自動的に追加されます。 その下の新しい行に 2 つの追加の要求ヘッダーを貼り付けることができます。 サービスの管理者アクセス キーを使用して、サービスのコンテンツ タイプと API キーを含めます。

ターゲットには、この URL の変更されたバージョンをコピーします。 `https://<placeholder-for-your-service-name>.search.windows.net/indexes?api-version=2017-11-11`

![Fiddler の要求ヘッダー][1]

> [!Tip]
> 無関係な余分の HTTP アクティビティを非表示にするには、Web トラフィックをオフにします。 Fiddler で **[File]\(ファイル\)** メニューで、**[Capture Traffic]\(トラフィックのキャプチャ\)** をオフにします。 

### <a name="1---create-an-index"></a>1 - インデックスの作成

動詞を **PUT** に変更します。 この URL の変更されたバージョンをコピーします。`https://<placeholder-for-your-service-name>.search.windows.net/indexes/hotel?api-version=2017-11-11` 前述のインデックス定義を要求本文にコピーします。 実際のページは次のスクリーンショットのようになります。 右上にある **[Execute]\(実行\)** をクリックして、完成した要求を送信します。

![Fiddler の要求本文][7]

### <a name="2---load-documents"></a>2 - ドキュメントを読み込む

動詞を **POST** に変更します。 `/docs/index` を含めるように URL を変更します。 次のスクリーンショットのようにドキュメントを要求本文にコピーし、要求を実行します。

![Fiddler の要求ペイロード][9]

### <a name="tips-for-running-our-sample-queries-in-fiddler"></a>Fiddler で Microsoft のサンプル クエリを実行するためのヒント

次のクエリは、[検索インデックス操作 (Azure Search API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) に関する記事に掲載されている例です。 この記事のクエリ例の多くには、Fiddler では許可されないスペースが含まれています。 Fiddler でクエリを実行する場合は、次のように、スペースを + 文字に置き換えてからクエリ文字列に貼り付けてください。

**(lastRenovationDate desc の) スペースを置き換える前:**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2017-11-11

**(lastRenovationDate+desc の) + でスペースを置き換えた後:**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate+desc&api-version=2017-11-11

### <a name="tips-for-viewing-index-statistic-in-fiddler"></a>Fiddler でインデックスの統計情報を表示するためのヒント

Fiddler で、**[Inspectors]\(インスペクター\)** タブをクリックし、**[Headers]\(ヘッダー\)** タブをクリックして、JSON 形式を選択します。 ドキュメント数とストレージ サイズ (KB) が表示されます。

## <a name="next-steps"></a>次の手順

REST クライアントは即時の探索にとても有効です。この記事で REST API のしくみを理解したら、次はコードに進みましょう。 次のステップについては、以下のリンクを参照してください。

+ [クイック スタート:.NET SDK を使用してインデックスを作成する](search-create-index-dotnet.md)
+ [クイック スタート:PowerShell を使用してインデックス (REST) を作成する](search-create-index-rest-api.md)

<!--Image References-->
[1]: ./media/search-fiddler/fiddler-url.png
[2]: ./media/search-fiddler/AzureSearch_Fiddler2_PostDocs.png
[3]: ./media/search-fiddler/AzureSearch_Fiddler3_Query.png
[4]: ./media/search-fiddler/AzureSearch_Fiddler4_QueryResults.png
[5]: ./media/search-fiddler/AzureSearch_Fiddler5_QueryStats.png
[6]: ./media/search-fiddler/postman-url.png
[7]: ./media/search-fiddler/fiddler-request.png
[8]: ./media/search-fiddler/postman-request.png
[9]: ./media/search-fiddler/fiddler-docs.png
[10]: ./media/search-fiddler/postman-docs.png
[11]: ./media/search-fiddler/postman-query.png
[12]: ./media/search-fiddler/postman-system-query.png