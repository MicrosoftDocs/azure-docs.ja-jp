---
title: Azure Cosmos DB の MongoDB 用 API でデータのクエリを実行する
description: Azure Cosmos DB の MongoDB 用 API から MongoDB シェル コマンドを使用してデータを照会する方法について説明します
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: tutorial
ms.date: 12/03/2019
ms.reviewer: sngun
ms.openlocfilehash: f93ec39e7a2e3b5829c0d6205404c6c5c4af6189
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93074320"
---
# <a name="query-data-by-using-azure-cosmos-dbs-api-for-mongodb"></a>Azure Cosmos DB の MongoDB 用 API を使用してデータのクエリを実行する
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

[Azure Cosmos DB の MongoDB 用 API](mongodb-introduction.md) では、[MongoDB のクエリ](https://docs.mongodb.com/manual/tutorial/query-documents/)がサポートされています。 

この記事に含まれるタスクは次のとおりです。 

> [!div class="checklist"]
> * MongoDB シェルを使用して Cosmos データベースに格納されているデータのクエリを実行する

まずは、このドキュメントの例を使用したり、[MongoDB シェルでの Azure Cosmos DB に対するクエリの実行](https://azure.microsoft.com/resources/videos/query-azure-cosmos-db-data-by-using-the-mongodb-shell/)に関するビデオを見ることから始めます。

## <a name="sample-document"></a>サンプル ドキュメント

この記事のクエリは、次のサンプル ドキュメントを使用します。

```json
{
  "id": "WakefieldFamily",
  "parents": [
      { "familyName": "Wakefield", "givenName": "Robin" },
      { "familyName": "Miller", "givenName": "Ben" }
  ],
  "children": [
      {
        "familyName": "Merriam", 
        "givenName": "Jesse", 
        "gender": "female", "grade": 1,
        "pets": [
            { "givenName": "Goofy" },
            { "givenName": "Shadow" }
        ]
      },
      { 
        "familyName": "Miller", 
         "givenName": "Lisa", 
         "gender": "female", 
         "grade": 8 }
  ],
  "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
  "creationDate": 1431620462,
  "isRegistered": false
}
```
## <a name="example-query-1"></a><a id="examplequery1"></a> サンプル クエリ 1 

上記の家族に関するサンプル ドキュメントに対して、次のクエリは ID フィールドが `WakefieldFamily` と一致するドキュメントを返します。

**クエリ**

```bash
db.families.find({ id: "WakefieldFamily"})
```

**結果**

```json
{
    "_id": "ObjectId(\"58f65e1198f3a12c7090e68c\")",
    "id": "WakefieldFamily",
    "parents": [
      {
        "familyName": "Wakefield",
        "givenName": "Robin"
      },
      {
        "familyName": "Miller",
        "givenName": "Ben"
      }
    ],
    "children": [
      {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [
          { "givenName": "Goofy" },
          { "givenName": "Shadow" }
        ]
      },
      {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
      }
    ],
    "address": {
      "state": "NY",
      "county": "Manhattan",
      "city": "NY"
    },
    "creationDate": 1431620462,
    "isRegistered": false
}
```

## <a name="example-query-2"></a><a id="examplequery2"></a>サンプル クエリ 2 

次のクエリでは、家族のすべての子が返されます。 

**クエリ**

```bash 
db.families.find( { id: "WakefieldFamily" }, { children: true } )
``` 

**結果**

```json
{
    "_id": "ObjectId("58f65e1198f3a12c7090e68c")",
    "children": [
      {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [
          { "givenName": "Goofy" },
          { "givenName": "Shadow" }
        ]
      },
      {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
      }
    ]
}
```

## <a name="example-query-3"></a><a id="examplequery3"></a>サンプル クエリ 3 

次のクエリでは、登録されているすべての家族が返されます。 

**クエリ**

```bash
db.families.find( { "isRegistered" : true })
``` 

**結果**

ドキュメントは返されません。 

## <a name="example-query-4"></a><a id="examplequery4"></a>サンプル クエリ 4

次のクエリでは、登録されていないすべての家族が返されます。 

**クエリ**

```bash
db.families.find( { "isRegistered" : false })
``` 

**結果**

```json
{
    "_id": ObjectId("58f65e1198f3a12c7090e68c"),
    "id": "WakefieldFamily",
    "parents": [{
        "familyName": "Wakefield",
        "givenName": "Robin"
    }, {
        "familyName": "Miller",
        "givenName": "Ben"
    }],
    "children": [{
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [{
            "givenName": "Goofy"
        }, {
            "givenName": "Shadow"
        }]
    }, {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
    }],
    "address": {
        "state": "NY",
        "county": "Manhattan",
        "city": "NY"
    },
    "creationDate": 1431620462,
    "isRegistered": false
}
```

## <a name="example-query-5"></a><a id="examplequery5"></a>サンプル クエリ 5

次のクエリでは、登録されていない、州が NY のすべての家族が返されます。 

**クエリ**

```bash
db.families.find( { "isRegistered" : false, "address.state" : "NY" })
``` 

**結果**

```json
{
    "_id": ObjectId("58f65e1198f3a12c7090e68c"),
    "id": "WakefieldFamily",
    "parents": [{
        "familyName": "Wakefield",
        "givenName": "Robin"
    }, {
        "familyName": "Miller",
        "givenName": "Ben"
    }],
    "children": [{
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [{
            "givenName": "Goofy"
        }, {
            "givenName": "Shadow"
        }]
    }, {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
    }],
    "address": {
        "state": "NY",
        "county": "Manhattan",
        "city": "NY"
    },
    "creationDate": 1431620462,
    "isRegistered": false
}
```

## <a name="example-query-6"></a><a id="examplequery6"></a>サンプル クエリ 6

次のクエリでは、子が 8 年生のすべての家族が返されます。

**クエリ**

```bash
db.families.find( { children : { $elemMatch: { grade : 8 }} } )
```

**結果**

```json
{
    "_id": ObjectId("58f65e1198f3a12c7090e68c"),
    "id": "WakefieldFamily",
    "parents": [{
        "familyName": "Wakefield",
        "givenName": "Robin"
    }, {
        "familyName": "Miller",
        "givenName": "Ben"
    }],
    "children": [{
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [{
            "givenName": "Goofy"
        }, {
            "givenName": "Shadow"
        }]
    }, {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
    }],
    "address": {
        "state": "NY",
        "county": "Manhattan",
        "city": "NY"
    },
    "creationDate": 1431620462,
    "isRegistered": false
}
```

## <a name="example-query-7"></a><a id="examplequery7"></a>サンプル クエリ 7

次のクエリでは、子の配列サイズが 3 のすべての家族が返されます。

**クエリ**

```bash
db.Family.find( {children: { $size:3} } )
```

**結果**

子が 2 人より多い家族はないため、返される結果はありません。 パラメーターが 2 の場合のみ、このクエリは成功し、ドキュメント全体を返します。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、次の手順を行いました。

> [!div class="checklist"]
> * Azure Cosmos DB の MongoDB 用 API を使用してクエリを実行する方法を理解しました。

次のチュートリアルに進んで、データをグローバルに分散する方法について学習できます。

> [!div class="nextstepaction"]
> [データをグローバルに分散する](tutorial-global-distribution-sql-api.md)

