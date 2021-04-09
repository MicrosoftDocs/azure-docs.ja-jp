---
title: Azure Cosmos DB の JavaScript クエリ API を使用してストアドプロシージャとトリガーを記述する
description: Azure Cosmos DB で Javascript クエリ API を使用してストアド プロシージャおよびトリガーを記述する方法について説明します
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 05/07/2020
ms.author: tisande
ms.custom: devx-track-js
ms.openlocfilehash: 449af63ef3409b8c8173edef7a5d77074cca02b9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93335842"
---
# <a name="how-to-write-stored-procedures-and-triggers-in-azure-cosmos-db-by-using-the-javascript-query-api"></a>Javascript クエリ API を使用して Azure Cosmos DB でストアド プロシージャおよびトリガーを記述する方法
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB では、ストアド プロシージャまたはトリガーの記述に使用できる SQL 言語の知識がなくても、円滑な JavaScript インターフェイスを使用して最適化されたクエリを実行できます。 Azure Cosmos DB のJavaScript クエリ API サポートについての詳しくは、 [Azure Cosmos DB のJavaScript 言語統合クエリ API の操作](javascript-query-api.md)に関する記事を参照してください。

## <a name="stored-procedure-using-the-javascript-query-api"></a><a id="stored-procedures"></a>JavaScript クエリ API を使用したストアド プロシージャ

次のコード サンプルでは、ストアド プロシージャで JavaScript クエリ API を使用する方法の例を示します。 ストアド プロシージャは入力パラメーターによって指定された Azure Cosmos 項目を挿入し、`__.filter()` メソッドでメタデータ ドキュメントを更新します。このメソッドと共に入力項目のサイズ プロパティに基づき、minSize、maxSize、totalSize が指定されます。

> [!NOTE]
> `__` (2 つのアンダースコア) は JavaScript クエリ API を使用する場合の `getContext().getCollection()` のエイリアスです。

```javascript
/**
 * Insert an item and update metadata doc: minSize, maxSize, totalSize based on item.size.
 */
function insertDocumentAndUpdateMetadata(item) {
  // HTTP error codes sent to our callback function by CosmosDB server.
  var ErrorCode = {
    RETRY_WITH: 449,
  }

  var isAccepted = __.createDocument(__.getSelfLink(), item, {}, function(err, item, options) {
    if (err) throw err;

    // Check the item (ignore items with invalid/zero size and metadata itself) and call updateMetadata.
    if (!item.isMetadata && item.size > 0) {
      // Get the metadata. We keep it in the same container. it's the only item that has .isMetadata = true.
      var result = __.filter(function(x) {
        return x.isMetadata === true
      }, function(err, feed, options) {
        if (err) throw err;

        // We assume that metadata item was pre-created and must exist when this script is called.
        if (!feed || !feed.length) throw new Error("Failed to find the metadata item.");

        // The metadata item.
        var metaItem = feed[0];

        // Update metaDoc.minSize:
        // for 1st document use doc.Size, for all the rest see if it's less than last min.
        if (metaItem.minSize == 0) metaItem.minSize = item.size;
        else metaItem.minSize = Math.min(metaItem.minSize, item.size);

        // Update metaItem.maxSize.
        metaItem.maxSize = Math.max(metaItem.maxSize, item.size);

        // Update metaItem.totalSize.
        metaItem.totalSize += item.size;

        // Update/replace the metadata item in the store.
        var isAccepted = __.replaceDocument(metaItem._self, metaItem, function(err) {
          if (err) throw err;
          // Note: in case concurrent updates causes conflict with ErrorCode.RETRY_WITH, we can't read the meta again
          //       and update again because due to Snapshot isolation we will read same exact version (we are in same transaction).
          //       We have to take care of that on the client side.
        });
        if (!isAccepted) throw new Error("replaceDocument(metaItem) returned false.");
      });
      if (!result.isAccepted) throw new Error("filter for metaItem returned false.");
    }
  });
  if (!isAccepted) throw new Error("createDocument(actual item) returned false.");
}
```

## <a name="next-steps"></a>次のステップ

Azure Cosmos DB のストアド プロシージャ、トリガー、およびユーザー定義関数については、以下の記事を参照してください。

* [Azure Cosmos DB でストアド プロシージャ、トリガー、およびユーザー定義関数を操作する方法](how-to-use-stored-procedures-triggers-udfs.md)

* [Azure Cosmos DB でストアド プロシージャを登録して使用する方法](how-to-use-stored-procedures-triggers-udfs.md#stored-procedures)

* Azure Cosmos DB で[プリトリガー](how-to-use-stored-procedures-triggers-udfs.md#pre-triggers)と[ポストトリガー](how-to-use-stored-procedures-triggers-udfs.md#post-triggers)を登録して使用する方法

* [Azure Cosmos DB でユーザー定義関数を登録して使用する方法](how-to-use-stored-procedures-triggers-udfs.md#udfs)

* [Azure Cosmos DB での合成パーティション キー](synthetic-partition-keys.md)
