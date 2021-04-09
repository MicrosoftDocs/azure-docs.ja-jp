---
title: 'クイックスタート: Python で検索インデックスを作成する'
titleSuffix: Azure Cognitive Search
description: Python、Jupyter Notebook、Python 用 Azure.Documents.Search クライアント ライブラリを使用して検索インデックスを作成し、データを読み込んでクエリを実行する方法について説明します。
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 03/12/2021
ms.custom: devx-track-python
ms.openlocfilehash: 8b9c4792fa6dbdc70f657ce3c5f1757473a22fda
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103225219"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-python-using-jupyter-notebook"></a>クイックスタート: Jupyter Notebook を使用して Python で Azure Cognitive Search インデックスを作成する

> [!div class="op_single_selector"]
> * [Python](search-get-started-python.md)
> * [PowerShell (REST)](search-get-started-powershell.md)
> * [C#](search-get-started-dotnet.md)
> * [REST](search-get-started-rest.md)
> * [ポータル](search-get-started-portal.md)
>

Python と Azure SDK for Python の [azure-search-documents ライブラリ](/python/api/overview/azure/search-documents-readme)を使用して、Azure Cognitive Search インデックスの作成、読み込み、およびクエリの実行を行うノートブックを作成します。 この記事では、ノートブックを作成する方法を順番に説明します。 または、[完成した Jupyter Python ノートブックをダウンロードして実行する](https://github.com/Azure-Samples/azure-search-python-samples)こともできます。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件

このクイックスタートでは、次のサービスとツールが必要です。

* [Anaconda 3.x](https://www.anaconda.com/distribution/#download-section)。Python 3.x と Jupyter Notebook を提供します。

* [azure-search-documents パッケージ](https://pypi.org/project/azure-search-documents/)

* [検索サービスを作成](search-create-service-portal.md)するか、現在のサブスクリプションから[既存のサービスを検索](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)します。 このクイック スタート用には、Free レベルを使用できます。 

## <a name="copy-a-key-and-url"></a>キーと URL をコピーする

REST 呼び出しには、要求ごとにサービス URL とアクセス キーが必要です。 両方を使用して検索サービスが作成されるので、Azure Cognitive Search をサブスクリプションに追加した場合は、次の手順に従って必要な情報を入手してください。

1. [Azure portal にサインイン](https://portal.azure.com/)し、ご使用の検索サービスの **[概要]** ページで、URL を入手します。 たとえば、エンドポイントは `https://mydemo.search.windows.net` のようになります。

1. **[設定]**  >  **[キー]** で、サービスに対する完全な権限の管理キーを取得します。 管理キーをロールオーバーする必要がある場合に備えて、2 つの交換可能な管理キーがビジネス継続性のために提供されています。 オブジェクトの追加、変更、および削除の要求には、主キーまたはセカンダリ キーのどちらかを使用できます。

   ![HTTP エンドポイントとアクセス キーを取得する](media/search-get-started-rest/get-url-key.png "HTTP エンドポイントとアクセス キーを取得する")

すべての要求では、サービスに送信されるすべての要求に API キーが必要です。 有効なキーがあれば、要求を送信するアプリケーションとそれを処理するサービスの間で、要求ごとに信頼を確立できます。

## <a name="connect-to-azure-cognitive-search"></a>Azure Cognitive Search に接続する

このタスクでは、Jupyter Notebook を起動し、Azure Cognitive Search に接続できることを確認します。 これを行うには、サービスからのインデックスの一覧を要求します。 Windows と Anaconda 3 の組み合わせでは、Anaconda Navigator を使用してノートブックを起動できます。

1. 新しい Python3 ノートブックを作成します。

1. 最初のセルで、[azure-search-documents](/python/api/azure-search-documents) を含むライブラリを Azure SDK for Python から読み込みます。

   ```python
    !pip install azure-search-documents --pre
    !pip show azure-search-documents
    
    import os
    from azure.core.credentials import AzureKeyCredential
    from azure.search.documents.indexes import SearchIndexClient 
    from azure.search.documents import SearchClient
    from azure.search.documents.indexes.models import (
        ComplexField,
        CorsOptions,
        SearchIndex,
        ScoringProfile,
        SearchFieldDataType,
        SimpleField,
        SearchableField
    )
   ```

1. 2 番目のセルでは、どの要求でも定数となる要求要素を入力します。 前の手順でコピーした検索サービス名、管理 API キー、およびクエリ API キーを指定します。 このセルでは、特定の操作に使用するクライアントも設定します。[SearchIndexClient](/python/api/azure-search-documents/azure.search.documents.indexes.searchindexclient) を使用してインデックスを作成し、[SearchClient](/python/api/azure-search-documents/azure.search.documents.searchclient) を使用してインデックスを照会します。

   ```python
    service_name = "YOUR-SEARCH-SERIVCE-NAME"
    admin_key = "YOUR-SEARCH-SERVICE-ADMIN-API-KEY"
    
    index_name = "hotels-quickstart"
    
    # Create an SDK client
    endpoint = "https://{}.search.windows.net/".format(service_name)
    admin_client = SearchIndexClient(endpoint=endpoint,
                          index_name=index_name,
                          credential=AzureKeyCredential(admin_key))
    
    search_client = SearchClient(endpoint=endpoint,
                          index_name=index_name,
                          credential=AzureKeyCredential(admin_key))
   ```

1. 3 番目のセルで、delete_index 操作を実行して、既存の *hotels-quickstart* インデックスのサービスを消去します。 インデックスを削除することで、同じ名前の別の *hotels-quickstart* インデックスを作成できるようになります。

   ```python
    try:
        result = admin_client.delete_index(index_name)
        print ('Index', index_name, 'Deleted')
    except Exception as ex:
        print (ex)
   ```

1. 各手順を行います。

## <a name="1---create-an-index"></a>1 - インデックスの作成

インデックスの必要な要素には、名前、フィールド コレクション、およびキーが含まれます。 fields コレクションでは、データの読み込みと結果の取得の両方に使用される、論理 "*検索ドキュメント*" の構造を定義します。 

各フィールドには、名前、型、およびフィールドの使用方法を決定する属性 (たとえば、フルテキスト検索可能、フィルター可能、または検索結果で取得可能) があります。 インデックス内には、`Edm.String` 型のフィールドのいずれかをドキュメント ID の *キー* として指定する必要があります。

このインデックスは "hotels-quickstart" という名前で、次に示すフィールド定義が含まれています。 これは、他のチュートリアルで使用されている、より大きい [Hotels インデックス](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/Hotels_IndexDefinition.JSON)のサブセットです。 簡潔にするために、このクイックスタートではそれをトリミングしています。

1. 次のセルでは、スキーマを指定するセルに次の例を貼り付けます。

    ```python
    # Specify the index schema
    name = index_name
    fields = [
            SimpleField(name="HotelId", type=SearchFieldDataType.String, key=True),
            SearchableField(name="HotelName", type=SearchFieldDataType.String, sortable=True),
            SearchableField(name="Description", type=SearchFieldDataType.String, analyzer_name="en.lucene"),
            SearchableField(name="Description_fr", type=SearchFieldDataType.String, analyzer_name="fr.lucene"),
            SearchableField(name="Category", type=SearchFieldDataType.String, facetable=True, filterable=True, sortable=True),
        
            SearchableField(name="Tags", collection=True, type=SearchFieldDataType.String, facetable=True, filterable=True),
    
            SimpleField(name="ParkingIncluded", type=SearchFieldDataType.Boolean, facetable=True, filterable=True, sortable=True),
            SimpleField(name="LastRenovationDate", type=SearchFieldDataType.DateTimeOffset, facetable=True, filterable=True, sortable=True),
            SimpleField(name="Rating", type=SearchFieldDataType.Double, facetable=True, filterable=True, sortable=True),
    
            ComplexField(name="Address", fields=[
                SearchableField(name="StreetAddress", type=SearchFieldDataType.String),
                SearchableField(name="City", type=SearchFieldDataType.String, facetable=True, filterable=True, sortable=True),
                SearchableField(name="StateProvince", type=SearchFieldDataType.String, facetable=True, filterable=True, sortable=True),
                SearchableField(name="PostalCode", type=SearchFieldDataType.String, facetable=True, filterable=True, sortable=True),
                SearchableField(name="Country", type=SearchFieldDataType.String, facetable=True, filterable=True, sortable=True),
            ])
        ]
    cors_options = CorsOptions(allowed_origins=["*"], max_age_in_seconds=60)
    scoring_profiles = []
    suggester = [{'name': 'sg', 'source_fields': ['Tags', 'Address/City', 'Address/Country']}]
    ```

1. 別のセルで、要求を作成します。 この create_index 要求では、ご利用の検索サービスのインデックス コレクションがターゲットとされ、前のセルで指定したインデックス スキーマに基づいて [SearchIndex](/python/api/azure-search-documents/azure.search.documents.indexes.models.searchindex) が作成されます。

    ```python
    index = SearchIndex(
        name=name,
        fields=fields,
        scoring_profiles=scoring_profiles,
        suggesters = suggester,
        cors_options=cors_options)
    
    try:
        result = admin_client.create_index(index)
        print ('Index', result.name, 'created')
    except Exception as ex:
        print (ex)
    ```

1. 各手順を行います。

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 - ドキュメントを読み込む

ドキュメントを読み込むには、操作の種類 (upload、merge-and-upload など) に対する[インデックス アクション](/python/api/azure-search-documents/azure.search.documents.models.indexaction)を使用して、documents コレクションを作成します。 ドキュメントは GitHub の [HotelsData ](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/HotelsData_toAzureSearch.JSON) から作成されています。

1. 新しいセルでは、インデックス スキーマに準拠する 4 つのドキュメントを指定します。 ドキュメントごとにアップロード アクションを指定します。

    ```python
    documents = [
        {
        "@search.action": "upload",
        "HotelId": "1",
        "HotelName": "Secret Point Motel",
        "Description": "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
        "Description_fr": "L'hôtel est idéalement situé sur la principale artère commerciale de la ville en plein cœur de New York. A quelques minutes se trouve la place du temps et le centre historique de la ville, ainsi que d'autres lieux d'intérêt qui font de New York l'une des villes les plus attractives et cosmopolites de l'Amérique.",
        "Category": "Boutique",
        "Tags": [ "pool", "air conditioning", "concierge" ],
        "ParkingIncluded": "false",
        "LastRenovationDate": "1970-01-18T00:00:00Z",
        "Rating": 3.60,
        "Address": {
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
        "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
        "Category": "Boutique",
        "Tags": [ "pool", "free wifi", "concierge" ],
        "ParkingIncluded": "false",
        "LastRenovationDate": "1979-02-18T00:00:00Z",
        "Rating": 3.60,
        "Address": {
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
        "Description": "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel's restaurant services.",
        "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
        "Category": "Resort and Spa",
        "Tags": [ "air conditioning", "bar", "continental breakfast" ],
        "ParkingIncluded": "true",
        "LastRenovationDate": "2015-09-20T00:00:00Z",
        "Rating": 4.80,
        "Address": {
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
        "Description_fr": "Le sublime Cliff Hotel est situé au coeur du centre historique de sublime dans un quartier extrêmement animé et vivant, à courte distance de marche des sites et monuments de la ville et est entouré par l'extraordinaire beauté des églises, des bâtiments, des commerces et Monuments. Sublime Cliff fait partie d'un Palace 1800 restauré avec amour.",
        "Category": "Boutique",
        "Tags": [ "concierge", "view", "24-hour front desk service" ],
        "ParkingIncluded": "true",
        "LastRenovationDate": "1960-02-06T00:00:00Z",
        "Rating": 4.60,
        "Address": {
            "StreetAddress": "7400 San Pedro Ave",
            "City": "San Antonio",
            "StateProvince": "TX",
            "PostalCode": "78216",
            "Country": "USA"
            }
        }
    ]
    ```  

1. 別のセルで、要求を作成します。 この upload_documents 要求では、hotels-quickstart インデックスのドキュメント コレクションがターゲットとされ、前の手順で指定したドキュメントが Cognitive Search インデックスにプッシュされます。

    ```python
    try:
        result = search_client.upload_documents(documents=documents)
        print("Upload of new document succeeded: {}".format(result[0].succeeded))
    except Exception as ex:
        print (ex.message)
    ```

1. 各手順を行って、ご利用の検索サービス内のインデックスにドキュメントをプッシュします。

## <a name="3---search-an-index"></a>3 - インデックスの検索

この手順では、[search. client クラス](/python/api/azure-search-documents/azure.search.documents.searchclient)の **search** メソッドを使用してインデックスのクエリを実行する方法について説明します。

1. 次の手順では、空の検索が実行され (`search=*`)、任意のドキュメントのランクなしの一覧 (search score = 1.0) が返されます。 条件がないため、すべてのドキュメントが結果に含まれます。 このクエリでは、各ドキュメントのフィールドのうち 2 つだけを出力します。 さらに、`include_total_count=True` を追加して、結果に含まれるすべてのドキュメントの数 (4) を取得します。

    ```python
    results =  search_client.search(search_text="*", include_total_count=True)
    
    print ('Total Documents Matching Query:', results.get_count())
    for result in results:
        print("{}: {}".format(result["HotelId"], result["HotelName"]))
    ```

1. 次のクエリでは、検索式に完全な用語を追加しています ("wifi")。 このクエリでは、`select` ステートメント内のフィールドのみが結果に含まれることを指定しています。 返されるフィールドを制限すると、ネットワーク経由で返されるデータの量が最小限に抑えられ、検索の待ち時間が短縮されます。

    ```python
    results =  search_client.search(search_text="wifi", include_total_count=True, select='HotelId,HotelName,Tags')
    
    print ('Total Documents Matching Query:', results.get_count())
    for result in results:
        print("{}: {}: {}".format(result["HotelId"], result["HotelName"], result["Tags"]))
    ```

1. 次に、評価が 4 を超えるホテルのみを降順に並べ替えて返す filter 式を適用します。

    ```python
    results =  search_client.search(search_text="hotels", select='HotelId,HotelName,Rating', filter='Rating gt 4', order_by='Rating desc')
    
    for result in results:
        print("{}: {} - {} rating".format(result["HotelId"], result["HotelName"], result["Rating"]))
    ```

1. `search_fields` を追加して、単一のフィールドに一致するようクエリにスコープを設定します。

    ```python
    results =  search_client.search(search_text="sublime", search_fields='HotelName', select='HotelId,HotelName')
    
    for result in results:
        print("{}: {}".format(result["HotelId"], result["HotelName"]))
    ```

1. facets は、ファセット ナビゲーション構造を構成するために使用できるラベルです。 このクエリでは、Category のファセットとカウントが返されます。

    ```python
    results =  search_client.search(search_text="*", facets=["Category"])
    
    facets = results.get_facets()
    
    for facet in facets["Category"]:
        print("    {}".format(facet))
    ```

1. この例では、特定のドキュメントをそのキーに基づいて検索しています。 通常は、ユーザーが検索結果内のドキュメントをクリックしたときにドキュメントを返します。

    ```python
    result = search_client.get_document(key="3")
    
    print("Details for hotel '3' are:")
    print("Name: {}".format(result["HotelName"]))
    print("Rating: {}".format(result["Rating"]))
    print("Category: {}".format(result["Category"]))
    ```

1. この例では、autocomplete 関数を使用します。 これは、通常、ユーザーが検索ボックスに入力したときに一致する可能性のあるものをオートコンプリートするために、検索ボックスで使用されます。

   インデックスが作成されると、"sg" という名前の suggester も要求の一部として作成されます。 suggester の定義では、suggester の要求に一致する可能性のあるフィールドを見つけるために使用できるフィールドを指定します。 この例では、"Tags"、"Address/City"、"Address/Country" のフィールドが該当します。 オートコンプリートをシミュレートするには、文字 "sa" を部分文字列として渡します。 [SearchClient](/python/api/azure-search-documents/azure.search.documents.searchclient) の autocomplete メソッドにより、一致する可能性のある用語が返されます。

    ```python
    search_suggestion = 'sa'
    results = search_client.autocomplete(search_text=search_suggestion, suggester_name="sg", mode='twoTerms')
    
    print("Autocomplete for:", search_suggestion)
    for result in results:
        print (result['text'])
    ```

## <a name="clean-up"></a>クリーンアップ

独自のサブスクリプションを使用している場合は、プロジェクトの最後に、作成したリソースがまだ必要かどうかを確認してください。 リソースを実行したままにすると、お金がかかる場合があります。 リソースは個別に削除することも、リソース グループを削除してリソースのセット全体を削除することもできます。

ポータルの左側のナビゲーション ウィンドウにある **[すべてのリソース]** または **[リソース グループ]** リンクを使って、リソースを検索および管理できます。

無料サービスを使っている場合は、3 つのインデックス、インデクサー、およびデータソースに制限されることに注意してください。 ポータルで個別の項目を削除して、制限を超えないようにすることができます。 

## <a name="next-steps"></a>次のステップ

簡単にするために、このクイック スタートでは Hotels インデックスの短縮バージョンが使用されています。 完全なバージョンを作成して、より興味深いクエリを試してみることもできます。 完全バージョンと全部で 50 のドキュメントを取得するには、 **[データのインポート]** ウィザードを実行して、組み込みのサンプル データ ソースから *hotels-sample* を選択します。

> [!div class="nextstepaction"]
> [クイック スタート: Azure portal でインデックスを作成する](search-get-started-portal.md)