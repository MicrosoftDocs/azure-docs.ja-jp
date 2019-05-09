---
title: JSON 検索構文 - Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Academic Knowledge API で利用できる JSON 検索構文について説明します。
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: fddd2291fe7fbb46c57d31e9aebc7fc6244df971
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/07/2019
ms.locfileid: "55884802"
---
# <a name="json-search-syntax"></a>JSON 検索の構文

```javascript
/* Query Object:
   Suppose we have a query path /v0/e0/v1/e1/...
   A query object contains constraints to be applied to graph nodes in a path.
   Constraints are given in <"node identifier", {constraint object}> key-value pairs: 
*/
{
    /* query path can also be set in the query object */
    path: "/v0/e0/v1/e1/.../vn/",
    v0: { /* A Constraint Object */ },
    v1: { /* A Constraint Object */ },
}
```

クエリ パス内のノード名 (_v0、v1、..._) は、クエリ オブジェクトで参照できるノード識別子として機能します。パス内のエッジ名 (_e0、e1、..._) は、対応するエッジの種類を表します。 アスタリスク _*_ をノードまたはエッジ名として使用して (開始ノードは例外で、これは指定する必要があります)、このような要素に制約がないことを宣言できます。 たとえば、クエリ パス `/v0/*/v1/e1/*/` は、エッジ _(v0、v1)_ の種類を制限することなくグラフからパスを取得します。 その一方で、クエリにはパスの行き先 (最終ノード) に対する制約はありません。

パスにノードが 1 つだけ含まれている場合 (たとえば _v0_)、クエリは制約を満たすすべてのエンティティを単純に返します。 開始ノードに適用される制約オブジェクトは "*開始クエリ オブジェクト*" と呼ばれ、次のように指定されます。

```javascript
/* Starting Query Object:
   Specifies constraints on the starting node
*/
{
    /* "match" operator searches for entities with the specified properties. 
       All properties defined in the graph schema be queried such as "Name" and "NormalizedTitle".
     */
    "match": { 
        "Name" : "some name",
        ...
    },
    /* "id" operator directly specifies the IDs of the starting nodes. 
       When it is present, the "match" operator is ignored. 
     */
    "id": [ id1, id2, id3... ],
    /* "type" operator limits results to a certain type of entities,
       for example, "Author" or "Paper".
     */
    "type": "Author",
    /* "select" operator pulls properties from the database and 
       returns them to the client.
     */
    "select": ["PaperRank", ...]
}
```

パスに開始ノード以外のノードも含まれている場合、クエリ プロセッサは、指定されたパス パターンに従ってグラフ トラバーサルを実行します。 ノードに到達すると、ユーザー指定のトラバーサル アクションがトリガーされます。つまり、現在のノードで停止して戻るか、グラフの探索を続けます。 トラバーサル アクションが指定されていない場合は、既定のアクションが実行されます。 中間ノードの場合、既定のアクションではグラフの探索が続けられます。 パスの最後のノードの場合、既定のアクションでは停止して戻ります。 トラバーサル アクションを指定する制約オブジェクトを "*トラバーサル アクション オブジェクト*" と呼びます。 これは次のように指定します。

```javascript
/* Traversal Action Object:
   Specifies graph traversal actions on a node (except for the starting node).
 */
{
    /* Set the continue condition here. */
    continue: { 
        /* simple property exact match */
        "property_key" : "property_value", 
        /* Advanced query operators */
        /* -- Numerical comparisons */
        "property_key_2" : { "gt" /* or simply ">" */ : 123 },
        /* -- Substring query */
        "property_key_3" : { "substring" : "456" },
        /* -- CellID query */
        "id": [ 111, 222, 333... ],
        /* -- Entity type query */
        "type": "cell_type",
        /* -- Property existance query */
        "has" : "property_key_4",
        /* -- Logical operators */
        /* ---- Note that, by default the operators are combined with AND semantics */
        
        /* -- OR operator */
        "or": {
          /* Query operators... */
        },
        /* -- NOT operator */
        "not": {
            /* Query operators... */
        }
    },
    /* Set the return condition here. */
    return: {
        /* Same operators as the continue object */
    },
    /* The selected properties to return. */
    select: ["property_key_1", ...]
}
```

*json* 検索クエリの POST 本文には、少なくとも 1 つの "*パス*" パターンが含まれている必要があります。 トラバーサル アクション オブジェクトは省略可能です。 次に 2 つの例を示します。

```JSON
{
  "path": "/series/ConferenceInstanceIDs/conference/FieldOfStudyIDs/field",
  "series": {
    "type": "ConferenceSeries",
    "FullName": "graph",
    "select": [ "FullName", "ShortName" ]
  },
  "conference": {
    "type": "ConferenceInstance",
    "select": [ "FullName" ]
  },
  "field": {
    "type": "FieldOfStudy",
    "select": [ "Name" ],
    "return": { "Name": { "substring" : "World Wide Web" } }
  }
}
```

```JSON
{
  "path": "/author/PaperIDs/paper",
  "author": {
    "type": "Author",
    "select": [ "DisplayAuthorName" ],
    "match": { "Name": "leslie lamport" }
  },
  "paper": {
    "type": "Paper",
    "select": [ "OriginalTitle", "CitationCount" ],
    "return": { "CitationCount": { "gt": 100 } }
  }
}
```

