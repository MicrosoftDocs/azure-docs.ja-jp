---
title: "REST API を使用した Azure Search インデックスの作成 | Microsoft Docs"
description: "Azure Search HTTP REST API を使用して、コードでインデックスを作成します。"
services: search
documentationcenter: 
author: ashmaka
manager: jhubbard
editor: 
tags: azure-portal
ms.assetid: ac6c5fba-ad59-492d-b715-d25a7a7ae051
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 12/08/2016
ms.author: ashmaka
translationtype: Human Translation
ms.sourcegitcommit: 702ea254c19d1f9782f83d8445b7f440f11963b9
ms.openlocfilehash: 7f4bccda8a7cebff0d80627320d34062d4d55add

---
# <a name="create-an-azure-search-index-using-the-rest-api"></a>REST API を使用した Azure Search インデックスの作成
> [!div class="op_single_selector"]
>
> * [概要](search-what-is-an-index.md)
> * [ポータル](search-create-index-portal.md)
> * [.NET](search-create-index-dotnet.md)
> * [REST ()](search-create-index-rest-api.md)
>
>

この記事では、Azure Search REST API を使用して Azure Search の [インデックス](https://docs.microsoft.com/rest/api/searchservice/Create-Index) を作成するプロセスについて説明します。

このガイドに従ってインデックスを作成する前に、既に [Azure Search サービスを作成済み](search-create-service-portal.md)です。

REST API を使用して Azure Search インデックスを作成するには、Azure Search サービスの URL エンドポイントに単一の HTTP POST 要求を発行します。 インデックス定義は、適切な形式の JSON コンテンツとして要求本文に含まれます。

## <a name="identify-your-azure-search-services-admin-api-key"></a>Azure Search サービスの管理者 API キーの識別
Azure Search サービスのプロビジョニングが完了すると、REST API を使用して、サービスの URL エンドポイントに対して HTTP 要求を発行できます。 "*すべての*" API 要求に、プロビジョニングした Search サービス用に生成された API キーを含める必要があります。 有効なキーがあれば、要求を送信するアプリケーションとそれを処理するサービスの間で、要求ごとに信頼を確立できます。

1. サービスの API キーを探すには、[Azure Portal](https://portal.azure.com/) にログインする必要があります。
2. Azure Search サービスのブレードに移動します。
3. "キー" アイコンをクリックします。

サービスで*管理者キー*と*クエリ キー*を使用できるようになります。

* プライマリおよびセカンダリ *管理者キー* は、サービスの管理のほか、インデックス、インデクサー、データ ソースの作成と削除など、すべての操作に対する完全な権限を付与するものです。 キーは 2 つあるため、プライマリ キーを再生成することにした場合もセカンダリ キーを使い続けることができます (その逆も可能です)。
* *クエリ キー* はインデックスとドキュメントに対する読み取り専用アクセスを付与するものであり、通常は、検索要求を発行するクライアント アプリケーションに配布されます。

インデックスを作成する目的では、プライマリ管理者キーとセカンダリ管理者キーのどちらかを使用できます。

## <a name="define-your-azure-search-index-using-well-formed-json"></a>適切な形式の JSON を使用した Azure Search インデックスの定義
サービスに対する 1 つの HTTP POST 要求で、インデックスを作成します。 HTTP POST 要求の本文には、Azure Search インデックスを定義する 1 つの JSON オブジェクトが含まれます。

1. この JSON オブジェクトの最初のプロパティは、インデックスの名前です。
2. この JSON オブジェクトの 2 番目のプロパティは、 `fields` という名前の JSON 配列で、インデックスのフィールドごとに個別の JSON オブジェクトを格納します。 これらの各 JSON オブジェクトには、"name"、"type" などのフィールド属性ごとに複数の名前/値ペアが含まれます。

各フィールドには [適切な属性](https://docs.microsoft.com/rest/api/searchservice/Create-Index)を割り当てる必要があるため、インデックスを設計する際は、検索のユーザー エクスペリエンスとビジネス ニーズに留意することが重要です。 これらの属性では、どのフィールドにどの検索機能 (フィルター、ファセット、フルテキスト検索の並べ替えなど) が適用されるかを制御します。 指定しない属性については、明確に無効にしない限り、既定では、対応する検索機能が有効になります。

この例では、インデックスに "hotels" という名前を付け、次のようにフィールドを定義しました。

```JSON
{
    "name": "hotels",  
    "fields": [
        {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false},
        {"name": "baseRate", "type": "Edm.Double"},
        {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
        {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
        {"name": "hotelName", "type": "Edm.String", "facetable": false},
        {"name": "category", "type": "Edm.String"},
        {"name": "tags", "type": "Collection(Edm.String)"},
        {"name": "parkingIncluded", "type": "Edm.Boolean", "sortable": false},
        {"name": "smokingAllowed", "type": "Edm.Boolean", "sortable": false},
        {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
        {"name": "rating", "type": "Edm.Int32"},
        {"name": "location", "type": "Edm.GeographyPoint"}
    ]
}
```

各フィールドのインデックスの属性は、アプリケーションでどのように使用されるかに応じて、慎重に選択しています。 たとえば、`hotelId` は、ユーザーが知らないホテルについて検索する一意のキーです。そのため、`searchable` を `false` に設定してそのフィールドのフルテキスト検索を無効にします。これにより、インデックスの領域が節約されます。

インデックスの `Edm.String` 型のフィールドを 1 つだけ、"key" フィールドとして指定する必要があることに注意してください。

上記のインデックス定義では、フランス語のテキストを格納することを目的としているため、`description_fr` フィールドに言語アナライザーを使用しています。 言語アナライザーの詳細については、[言語サポートのトピック](https://docs.microsoft.com/rest/api/searchservice/Language-support)と、対応する[ブログ記事](https://azure.microsoft.com/blog/language-support-in-azure-search/)を参照してください。

## <a name="issue-the-http-request"></a>HTTP 要求の発行
1. インデックス定義を要求本文として使用して、Azure Search サービス エンドポイントの URL に HTTP POST 要求を発行します。 URL では、ホスト名としてサービス名を使用し、クエリ文字列パラメーターとして適切な `api-version` を配置するようにしてください (このドキュメントが書かれた時点で最新の API バージョンは `2016-09-01` です)。
2. 要求ヘッダーで、`Content-Type` を `application/json` として指定します。 また、 `api-key` ヘッダーでは、ステップ I で特定したサービスの管理者キーを指定する必要があります。

独自のサービス名と API キーを指定して以下の要求を発行する必要があります。

    POST https://[service name].search.windows.net/indexes?api-version=2016-09-01
    Content-Type: application/json
    api-key: [api-key]


要求が成功した場合は、状態コード 201 (Created) が表示されます。 REST API を使用したインデックスの作成の詳細については、[API リファレンス](https://docs.microsoft.com/rest/api/searchservice/Create-Index)を参照してください。 エラーが発生した場合に返される可能性のあるその他の HTTP 状態コードの詳細については、「 [HTTP status codes (Azure Search) (HTTP 状態コード (Azure Search))](https://docs.microsoft.com/rest/api/searchservice/HTTP-status-codes)」を参照してください。

インデックスが不要になり、それを削除する場合は、HTTP DELETE 要求を発行するだけです。 たとえば、"hotels" を削除する方法を次に示します。

    DELETE https://[service name].search.windows.net/indexes/hotels?api-version=2016-09-01
    api-key: [api-key]


## <a name="next-steps"></a>次のステップ
Azure Search インデックスを作成すると、データの検索を開始できるように [インデックスにコンテンツをアップロードする](search-what-is-data-import.md) 準備が完了します。



<!--HONumber=Jan17_HO2-->


