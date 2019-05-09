---
title: Azure Cosmos DB で Javascript クエリ API を使用してストアド プロシージャおよびトリガーを記述する方法
description: Azure Cosmos DB で Javascript クエリ API を使用してストアド プロシージャおよびトリガーを記述する方法について説明します
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 12/11/2018
ms.author: mjbrown
ms.openlocfilehash: 94f72b716e149b2fa5c31deabf92a8a443eb0bef
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2019
ms.locfileid: "54043347"
---
# <a name="how-to-write-stored-procedures-and-triggers-in-azure-cosmos-db-by-using-the-javascript-query-api"></a>Javascript クエリ API を使用して Azure Cosmos DB でストアド プロシージャおよびトリガーを記述する方法

Azure Cosmos DB では、ストアド プロシージャまたはトリガーの記述に使用できる SQL 言語の知識がなくても、円滑な JavaScript インターフェイスを使用して最適化されたクエリを実行できます。 Azure Cosmos DB のJavaScript クエリ API サポートについての詳しくは、 [Azure Cosmos DB のJavaScript 言語統合クエリ API の操作](javascript-query-api.md)に関する記事を参照してください。

## <a id="stored-procedures"></a>JavaScript クエリ API を使用したストアド プロシージャ

次のコード サンプルでは、ストアド プロシージャで JavaScript クエリ API を使用する方法の例を示します。 ストアド プロシージャは入力パラメーターによって指定された Azure Cosmos DB 項目を挿入し、`__.filter()` メソッドでメタデータ ドキュメントを更新します。このメソッドと共に入力項目のサイズ プロパティに基づき、minSize、maxSize、totalSize が指定されます。

> [!NOTE]
> `__` (二重下線) は JavaScript クエリ API を使用する場合の `getContext().getCollection()` のエイリアスです。

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

## <a name="next-steps"></a>次の手順

Azure Cosmos DB のストアド プロシージャ、トリガー、およびユーザー定義関数については、以下の記事を参照してください。

* [Azure Cosmos DB でストアド プロシージャ、トリガー、およびユーザー定義関数を操作する方法](how-to-use-stored-procedures-triggers-udfs.md)

* [Azure Cosmos DB でストアド プロシージャを登録して使用する方法](how-to-use-stored-procedures-triggers-udfs.md#stored-procedures)

* Azure Cosmos DB で[プリトリガー](how-to-use-stored-procedures-triggers-udfs.md#pre-triggers)と[ポストトリガー](how-to-use-stored-procedures-triggers-udfs.md#post-triggers)を登録して使用する方法

* [Azure Cosmos DB でユーザー定義関数を登録して使用する方法](how-to-use-stored-procedures-triggers-udfs.md#udfs)

* [Azure Cosmos DB での合成パーティション キー](synthetic-partition-keys.md)
