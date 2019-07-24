---
title: グラフ検索メソッド - Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Academic Knowledge API のグラフ検索メソッドを使用して、特定のグラフ パターンに基づいてアカデミック エンティティのセットを返します。
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 5d47b938560fb1bd15adfe1a1c2d35b7359d47a3
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57977413"
---
# <a name="graph-search-method"></a>グラフ検索メソッド

**グラフ検索** REST API を使用して、特定のグラフ パターンに基づいてアカデミック エンティティのセットを返します。  応答は、ユーザー指定の制約を満たすグラフ パスのセットです。 グラフ パスは、_v0, e0, v1, e1, ..., vn_ (_v0_ はパスの開始ノード) の形式のグラフのノードとエッジのインターリーブ シーケンスです。
<br>

**REST エンドポイント:**  
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/graph/search?
```   
<br>

## <a name="request-parameters"></a>要求パラメーター  

Name     | 値 | 必須  | 説明
-----------|-----------|---------|--------
**mode**       | テキスト文字列 | はい | 使用するモードの名前。 値は *json* または *lambda* です。

グラフ検索メソッドは、HTTP POST 要求を介して呼び出す必要があります。 post 要求には、コンテンツ タイプ ヘッダー **application/json** が含まれている必要があります。

##### <a name="json-search"></a>JSON 検索 

*json* 検索では、POST の本文は JSON オブジェクトです。 JSON オブジェクトはユーザー指定の制約を伴うパス パターンを記述します (*json* 検索については、[JSON オブジェクトの仕様](JSONSearchSyntax.md)に関する記事をご覧ください)。


##### <a name="lambda-search"></a>ラムダ検索

"*ラムダ*" 検索では、POST の本文はプレーン テキスト文字列です。 POST の本文は 1 つの C# ステートメントである LIKQ ラムダ クエリ文字列です ("*ラムダ*" 検索については、[クエリ文字列の仕様](LambdaSearchSyntax.md)に関する記事をご覧ください)。 

<br>

## <a name="response-json"></a>応答 (JSON)

Name | 説明
-------|-----   
**results** | クエリ式に一致する 0 個以上のエンティティの配列。 各エンティティには、要求された属性の値が含まれています。 このフィールドは、要求が正常に処理された場合に存在します。
**error** | HTTP 状態コード。 このフィールドは、要求が失敗した場合に存在します。
**message** | エラー メッセージ。 このフィールドは、要求が失敗した場合に存在します。

クエリを "_800 ミリ秒_" 以内に処理できない場合は、"_タイムアウト_" エラーが返されます。 

<br>

#### <a name="example"></a>例:

##### <a name="json-search"></a>JSON 検索
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/graph/search?mode=json
```
<br>
*json* 検索では、タイトルに "graph engine" が含まれ、"bin shao" によって記述された論文を取得する場合、クエリを次のように指定できます。

```JSON
{
  "path": "/paper/AuthorIDs/author",
  "paper": {
    "type": "Paper",
    "NormalizedTitle": "graph engine",
    "select": [
      "OriginalTitle"
    ]
  },
  "author": {
    "return": {
      "type": "Author",
      "Name": "bin shao"
    }
  }
}
```

クエリの出力は、グラフ パスの配列です。 グラフ パスは、クエリ パスに指定されたノードに対応するノード オブジェクトの配列です。 これらのノード オブジェクトには少なくとも 1 つのプロパティ *CellID* があります。これはエンティティ ID を表します。 その他のプロパティは、[*トラバーサル アクション オブジェクト*](JSONSearchSyntax.md)の select 演算子を介してプロパティ名を指定することで取得できます。

```JSON
{
  "Results": [
    [
      {
        "CellID": 2160459668,
        "OriginalTitle": "Trinity: a distributed graph engine on a memory cloud"
      },
      {
        "CellID": 2093502026
      }
    ],
    [
      {
        "CellID": 2171539317,
        "OriginalTitle": "A distributed graph engine for web scale RDF data"
      },
      {
        "CellID": 2093502026
      }
    ],
    [
      {
        "CellID": 2411554868,
        "OriginalTitle": "A distributed graph engine for web scale RDF data"
      },
      {
        "CellID": 2093502026
      }
    ],
    [
      {
        "CellID": 73304046,
        "OriginalTitle": "The Trinity graph engine"
      },
      {
        "CellID": 2093502026
      }
    ]
  ]
}
 ```

##### <a name="lambda-search"></a>ラムダ検索 

```
https://westus.api.cognitive.microsoft.com/academic/v1.0/graph/search?mode=lambda
```
<br>
"*ラムダ*" 検索では、特定の論文の著者 ID を取得する場合、次のようなクエリを記述できます。

```
MAG.StartFrom(@"{
    type  : ""Paper"",
    match : {
        NormalizedTitle : ""trinity: a distributed graph engine on a memory cloud""
    }
}").FollowEdge("AuthorIDs").VisitNode(Action.Return)
```

"*ラムダ*" 検索クエリの出力も、グラフ パスの配列です。

```JSON
{
  "Results": [
    [
      {
        "CellID": 2160459668
      },
      {
        "CellID": 2142490828
      }
    ],
    [
      {
        "CellID": 2160459668
      },
      {
        "CellID": 2116756368
      }
    ],
    [
      {
        "CellID": 2160459668
      },
      {
        "CellID": 2093502026
      }
    ]
  ]
}
```
 
## <a name="graph-schema"></a>グラフ スキーマ

グラフ スキーマは、グラフ検索クエリを記述するのに役立ちます。 これを次の図に示します。

![Microsoft Academic Graph スキーマ](./Images/AcademicGraphSchema.png)
