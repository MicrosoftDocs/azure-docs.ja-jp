---
title: "REST API を使用した Azure Search でのデータ アップロード | Microsoft Docs"
description: "REST API を使用して Azure Search のインデックスにデータをアップロードする方法について説明します。"
services: search
documentationcenter: 
author: ashmaka
manager: jhubbard
editor: 
tags: 
ms.assetid: 8d0749fb-6e08-4a17-8cd3-1a215138abc6
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 12/08/2016
ms.author: ashmaka
translationtype: Human Translation
ms.sourcegitcommit: 1f06a7197cc1a6dcf7a39c91183a4317bef126bb
ms.openlocfilehash: 7c1c14055507d77dfcefe87694167ca5a2fcfb97
ms.lasthandoff: 02/16/2017

---

# <a name="upload-data-to-azure-search-using-the-rest-api"></a>REST API を使用した Azure Search へのデータのアップロード
> [!div class="op_single_selector"]
>
> * [概要](search-what-is-data-import.md)
> * [.NET](search-import-data-dotnet.md)
> * [REST](search-import-data-rest-api.md)
>
>

この記事では、 [Azure Search REST API](https://docs.microsoft.com/rest/api/searchservice/) を使用して Azure Search インデックスにデータをインポートする方法について説明します。

このチュートリアルを開始する前に、既に [Azure Search インデックスを作成](search-what-is-an-index.md)してあります。

REST API を使用してインデックスにドキュメントをプッシュするには、HTTP POST 要求をインデックスの URL エンドポイントに発行します。 HTTP 要求の本文は、追加、変更、または削除するドキュメントを含む JSON オブジェクトです。

## <a name="identify-your-azure-search-services-admin-api-key"></a>Azure Search サービスの管理者 API キーの識別
REST API を使用してサービスに対する HTTP 要求を発行する場合、 *各* API 要求には、プロビジョニングした Search サービスに対して生成された API キーを含める必要があります。 有効なキーがあれば、要求を送信するアプリケーションとそれを処理するサービスの間で、要求ごとに信頼を確立できます。

1. サービスの API キーを探すには、[Azure Portal](https://portal.azure.com/) にサインインします。
2. Azure Search サービスのブレードに移動します。
3. "キー" アイコンをクリックします。

サービスで*管理者キー*と*クエリ キー*を使用できるようになります。

* プライマリおよびセカンダリ *管理者キー* は、サービスの管理のほか、インデックス、インデクサー、データ ソースの作成と削除など、すべての操作に対する完全な権限を付与するものです。 キーは 2 つあるため、プライマリ キーを再生成することにした場合もセカンダリ キーを使い続けることができます (その逆も可能です)。
* *クエリ キー* はインデックスとドキュメントに対する読み取り専用アクセスを付与するものであり、通常は、検索要求を発行するクライアント アプリケーションに配布されます。

データをインデックスにインポートする目的では、プライマリ管理者キーとセカンダリ管理者キーのどちらかを使用できます。

## <a name="decide-which-indexing-action-to-use"></a>利用するインデックス作成アクションの決定
REST API を使用する場合は、JSON 要求本文を利用して HTTP POST 要求を Azure Search インデックスのエンドポイント URL に発行します。 HTTP 要求本文内の JSON オブジェクトには、インデックスへの追加、更新、削除を行うドキュメントを表す JSON オブジェクトを含む "value" という 1 つの JSON 配列が含まれます。

"value" 配列内の各 JSON オブジェクトは、インデックスを作成するドキュメントを表します。 これらの各オブジェクトにはドキュメントのキーが含まれます。また、これらの各オブジェクトで、目的のインデックス作成アクション (アップロード、マージ、削除など) を指定します。 以下のアクションのうちどれを選ぶかに応じて、各ドキュメントに含める必要のあるフィールドは異なります。

| @search.action | Description | 各ドキュメントに必要なフィールド | メモ |
| --- | --- | --- | --- |
| `upload` |`upload` アクションは、ドキュメントが新しい場合は挿入され、存在する場合は更新/置換される "upsert" に似ています。 |キーのほか、定義するその他すべてのフィールド |既存のドキュメントを更新または置換する際に、要求で指定されていないフィールドは `null`に設定されます。 この処理は、フィールドが null 以外の値に設定されていた場合にも行われます。 |
| `merge` |指定されたフィールドで既存のドキュメントを更新します。 ドキュメントがインデックスに存在しない場合、マージは失敗します。 |キーのほか、定義するその他すべてのフィールド |マージで指定したすべてのフィールドは、ドキュメント内の既存のフィールドを置き換えます。 これには、 `Collection(Edm.String)`型のフィールドも含まれます。 たとえば、ドキュメントにフィールド `tags` があり、その値が `["budget"]` である場合、`tags` に値 `["economy", "pool"]` を指定してマージを実行すると、`tags` フィールドの最終的な値は `["economy", "pool"]` になります。 `["budget", "economy", "pool"]`にはなりません。 |
| `mergeOrUpload` |このアクションは、指定したキーを持つドキュメントがインデックスに既に存在する場合は、`merge` と同様の処理になります。 ドキュメントが存在しない場合は、 `upload` と同様の処理になり、新しいドキュメントが挿入されます。 |キーのほか、定義するその他すべてのフィールド |- |
| `delete` |インデックスから指定したドキュメントを削除します。 |キーのみ |指定したフィールドは、キー フィールド以外すべて無視されます。 ドキュメントから個々のフィールドを削除する場合は、代わりに `merge` を使用して、フィールドを明示的に null に設定します。 |

## <a name="construct-your-http-request-and-request-body"></a>HTTP 要求と要求本文の構築
インデックス アクションに必要なフィールド値を収集したので、実際の HTTP 要求と JSON 要求本文を構築してデータをインポートする準備が整いました。

#### <a name="request-and-request-headers"></a>要求と要求ヘッダー
URL では、サービス名とインデックス名 (この場合は "hotels") のほか、適切な API バージョン (このドキュメントが書かれた時点で最新の API バージョンは `2016-09-01` ) を指定する必要があります。 `Content-Type` および `api-key` 要求ヘッダーを定義する必要があります。 後者については、サービスの管理者キーのいずれかを使用してください。

    POST https://[search service].search.windows.net/indexes/hotels/docs/index?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

#### <a name="request-body"></a>要求本文
```JSON
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
            "@search.action": "mergeOrUpload",
            "hotelId": "3",
            "baseRate": 129.99,
            "description": "Close to town hall and the river"
        },
        {
            "@search.action": "delete",
            "hotelId": "6"
        }
    ]
}
```

この例では、検索アクションとして `upload`、`mergeOrUpload`、`delete` を使用しています。

この例の "hotels" インデックスには、既にさまざまなドキュメントが設定されているものとします。 `mergeOrUpload` を使用する場合はすべてのドキュメント フィールドを指定する必要はなく、`delete` を使用する場合はドキュメント キー (`hotelId`) のみを指定しています。

また、単一のインデックス作成要求に含めることのできるドキュメントは最大 1,000 個 (または 16 MB) であることにも注意が必要です。

## <a name="understand-your-http-response-code"></a>HTTP 応答コードの理解
#### <a name="200"></a>200
インデックス作成要求を正常に送信すると、状態コード `200 OK`の HTTP 応答が届きます。 HTTP 応答の JSON 本文は次のようになります。

```JSON
{
    "value": [
        {
            "key": "unique_key_of_document",
            "status": true,
            "errorMessage": null
        },
        ...
    ]
}
```

#### <a name="207"></a>207
1 つ以上の項目のインデックスが正常に作成されなかった場合は、状態コード `207` が返されます。 HTTP 応答の JSON 本文には、失敗したドキュメントに関する情報が格納されます。

```JSON
{
    "value": [
        {
            "key": "unique_key_of_document",
            "status": false,
            "errorMessage": "The search service is too busy to process this document. Please try again later."
        },
        ...
    ]
}
```

> [!NOTE]
> これは多くの場合、Search サービスでの負荷が、インデックス作成要求に対して `503` 応答が返され始めるポイントに到達していることを表します。 この場合は、クライアント コードをバックオフし、再試行するまで待機することを強くお勧めします。 こうすることで、システムが回復する時間が生まれ、その後の要求に成功する可能性が高くなります。 要求をすぐに再試行しても、状況が長引くだけです。
>
>

#### <a name="429"></a>429
インデックスあたりのドキュメント数に対するクォータを超過した場合は、状態コード `429` が返されます。

#### <a name="503"></a>503
要求内のどの項目についても正常にインデックスが作成されなかった場合は、状態コード `503` が返されます。 このエラーは、システムが過負荷の状態にあり、この時点では要求を処理できないことを示します。

> [!NOTE]
> この場合は、クライアント コードをバックオフし、再試行するまで待機することを強くお勧めします。 こうすることで、システムが回復する時間が生まれ、その後の要求に成功する可能性が高くなります。 要求をすぐに再試行しても、状況が長引くだけです。
>
>

ドキュメント アクションおよび成功/エラー応答の詳細については、「 [ドキュメントの追加、更新、削除](https://docs.microsoft.com/rest/api/searchservice/AddUpdate-or-Delete-Documents)」を参照してください。 エラーが発生した場合に返される可能性のあるその他の HTTP 状態コードの詳細については、「 [HTTP status codes (Azure Search) (HTTP 状態コード (Azure Search))](https://docs.microsoft.com/rest/api/searchservice/HTTP-status-codes)」を参照してください。

## <a name="next-steps"></a>次のステップ
Azure Search インデックスにデータを読み込んだら、ドキュメントを検索するクエリを発行できるようになります。 詳細については、「 [Azure Search インデックスの照会](search-query-overview.md) 」を参照してください。

