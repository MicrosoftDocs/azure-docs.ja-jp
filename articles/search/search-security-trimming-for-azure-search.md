---
title: 結果をトリミングするためのセキュリティ フィルター
titleSuffix: Azure Cognitive Search
description: セキュリティ フィルターとユーザー ID を使用した Azure Cognitive Search の検索結果に対するドキュメント レベルのセキュリティ特権を実装する方法について説明します。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/16/2020
ms.openlocfilehash: 8bd162fcf2011d2ccce716564763e7f54f19ff69
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97631805"
---
# <a name="security-filters-for-trimming-results-in-azure-cognitive-search"></a>Azure Cognitive Search の結果をトリミングするためのセキュリティ フィルター

ユーザー ID に基づいて Azure Cognitive Search の検索結果をトリミングするためのセキュリティ フィルターを適用できます。 一般的に、このような検索エクスペリエンスでは、検索を実行したユーザーの ID と、ドキュメントにアクセス許可を持つユーザーに関する原則を含むフィールドを比較する必要があります。 一致が見つかった場合、ユーザーまたはプリンシパル (グループ、ロールなど) はそのドキュメントへのアクセス権を持っています。

セキュリティ フィルター処理を実現するには、`Id eq 'id1' or Id eq 'id2'` など、等値式の複雑な論理和演算を使用する方法があります。 この方法は誤りが発生しやすく、保守が困難です。また、一覧の値の数が数百、数千単位の場合、クエリの応答時間が何秒も遅くなります。 

簡単で高速なアプローチは、`search.in` 関数を使用する方法です。 等値式ではなく `search.in(Id, 'id1, id2, ...')` を使用する場合、1 秒未満の応答時間を期待できます。

この記事では、次の手順でセキュリティ フィルター処理を実現する方法について説明します。
> [!div class="checklist"]
> * プリンシパルの識別子を含むフィールドを作成する 
> * 関連するプリンシパルの識別子を含む既存のドキュメントをプッシュまたは更新する
> * `search.in` `filter` を含む検索要求を発行する

>[!NOTE]
> プリンシパルの識別子を取得するプロセスについては、このドキュメントでは説明しません。 この情報については、ID サービス プロバイダーから入手してください。

## <a name="prerequisites"></a>前提条件

この記事では、[Azure サブスクリプション](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F)、[Azure Search Cognitive Search サービス](search-create-service-portal.md)、および [インデックス](search-what-is-an-index.md)がある前提で説明します。  

## <a name="create-security-field"></a>セキュリティ フィールドを作成する

ドキュメントには、アクセス権を持っているグループを指定するフィールドが含まれている必要があります。 この情報は、発行元に返される結果セットから選択または拒否するドキュメントのフィルター条件になります。
ここでは、セキュリティで保護されたファイルのインデックスがあり、各ファイルにはさまざまなユーザーがアクセスできると想定して説明します。

1. フィールド `group_ids` (ここでは任意の名前を選択できます) を `Collection(Edm.String)` として追加します。 ユーザーが持っているアクセス権に基づいて検索結果がフィルターされるように、フィールドの `filterable` 属性が `true` に設定されていることを確認します。 たとえば、`file_name` が "secured_file_b" のドキュメントで `group_ids` フィールドを `["group_id1, group_id2"]` に設定した場合、グループ ID "group_id1" または "group_id2" に属するユーザーのみが、このファイルに対して読み取りアクセス権を持ちます。
   
   検索要求の一部として返されないように、フィールドの `retrievable` 属性が `false` に設定されていることを確認します。

2. この例のために、`file_id` および `file_name` フィールドも追加します。  

    ```JSON
    {
        "name": "securedfiles",  
        "fields": [
            {"name": "file_id", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false},
            {"name": "file_name", "type": "Edm.String"},
            {"name": "group_ids", "type": "Collection(Edm.String)", "filterable": true, "retrievable": false}
        ]
    }
    ```

## <a name="pushing-data-into-your-index-using-the-rest-api"></a>REST API を使用してインデックスにデータをプッシュする
  
インデックスの URL エンドポイントに HTTP POST 要求を発行します。 HTTP 要求の本文は、追加するドキュメントを含む JSON オブジェクトです。

```http
POST https://[search service].search.windows.net/indexes/securedfiles/docs/index?api-version=2020-06-30  
Content-Type: application/json
api-key: [admin key]
```

要求の本文に、ドキュメントの内容を指定します。

```JSON
{
    "value": [
        {
            "@search.action": "upload",
            "file_id": "1",
            "file_name": "secured_file_a",
            "group_ids": ["group_id1"]
        },
        {
            "@search.action": "upload",
            "file_id": "2",
            "file_name": "secured_file_b",
            "group_ids": ["group_id1", "group_id2"]
        },
        {
            "@search.action": "upload",
            "file_id": "3",
            "file_name": "secured_file_c",
            "group_ids": ["group_id5", "group_id6"]
        }
    ]
}
```

グループの一覧を使用して既存のドキュメントを更新する必要がある場合は、`merge` または `mergeOrUpload` アクションを使用できます。

```JSON
{
    "value": [
        {
            "@search.action": "mergeOrUpload",
            "file_id": "3",
            "group_ids": ["group_id7", "group_id8", "group_id9"]
        }
    ]
}
```

セキュリティの追加または更新の詳細については、[セキュリティの編集](/rest/api/searchservice/addupdate-or-delete-documents)に関するページを参照してください。

## <a name="apply-the-security-filter"></a>セキュリティ フィルターを適用する

`group_ids` のアクセス権に基づいてドキュメントをトリミングするには、`group_ids/any(g:search.in(g, 'group_id1, group_id2,...'))` フィルターを含む検索クエリを発行する必要があります (この "group_id1, group_id2,..." は、検索要求の発行元が属するグループです)。

このフィルターは、指定された識別子のいずれかが `group_ids` フィールドに含まれるすべてのドキュメントと一致します。
Azure Cognitive Search を使用してドキュメントを検索する方法の詳細については、[ドキュメントの検索](/rest/api/searchservice/search-documents)に関するページを参照してください。
このサンプルでは、POST 要求を使用してドキュメントを検索する方法について説明している点に注意してください。

HTTP POST 要求を発行します。

```http
POST https://[service name].search.windows.net/indexes/securedfiles/docs/search?api-version=2020-06-30
Content-Type: application/json  
api-key: [admin or query key]
```

要求の本文でフィルターを指定します。

```JSON
{
   "filter":"group_ids/any(g:search.in(g, 'group_id1, group_id2'))"  
}
```

`group_ids` に "group_id1" または "group_id2" が含まれるドキュメントが返されます。 つまり、要求の発行元が読み取りアクセス権を持っているドキュメントが取得されます。

```JSON
{
 [
   {
    "@search.score":1.0,
     "file_id":"1",
     "file_name":"secured_file_a",
   },
   {
     "@search.score":1.0,
     "file_id":"2",
     "file_name":"secured_file_b"
   }
 ]
}
```

## <a name="next-steps"></a>次のステップ

この記事では、ユーザー ID と `search.in()` 関数に基づいて結果をフィルター処理するためのパターンについて説明します。 この関数を使用して、各ターゲット ドキュメントに関連付けられたプリンシパルの識別子と一致する要求元のユーザーについて、プリンシパルの識別子を渡すことができます。 検索要求が処理されると、`search.in` 関数によって、ユーザーのプリンシパルが読み取りアクセス権を持っていない検索結果は除外されます。 プリンシパルの識別子は、セキュリティ グループ、ロール、さらにはユーザー自身の ID を表す場合があります。

Active Directory に基づく代替パターン、または他のセキュリティ機能を再確認する方法については、次のリンクを参照してください。

* [Active Directory ID を使用して結果をトリミングするためのセキュリティ フィルター](search-security-trimming-for-azure-search-with-aad.md)
* [Azure Cognitive Search のセキュリティ](search-security-overview.md)