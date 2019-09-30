---
title: Azure Cosmos DB Graph を使用したシステム ドキュメントのプロパティへのアクセス
description: Gremlin API を使用して Cosmos DB システム ドキュメントのプロパティの読み取りと書き込みを行う方法について学習します
author: olignat
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: overview
ms.date: 09/10/2019
ms.author: olignat
ms.openlocfilehash: 4354d37a32bde006a9bee70c39df1fee9b269365
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/11/2019
ms.locfileid: "70911305"
---
# <a name="system-document-properties"></a>システム ドキュメントのプロパティ

Azure Cosmos DB では、すべてのドキュメントに[システム プロパティ](https://docs.microsoft.com/rest/api/cosmos-db/databases) (```_ts```、```_self```、```_attachments```、```_rid```、```_etag``` など) があります。 さらに、Gremlin エンジンによって、辺の ```inVPartition``` および ```outVPartition``` プロパティが追加されます。 既定では、これらのプロパティはトラバーサルに使用できます。 ただし、特定のプロパティ、またはすべてのプロパティを Gremlin トラバーサルに含めることは可能です。

```
g.withStrategies(ProjectionStrategy.build().IncludeSystemProperties('_ts').create())
```

## <a name="e-tag"></a>E-Tag

このプロパティは、オプティミスティック コンカレンシー制御に使用されます。 アプリケーションで操作をいくつかの別個のトラバーサルに分割する必要がある場合、eTag プロパティを使用して、同時書き込み時のデータ損失を回避できます。

```
g.withStrategies(ProjectionStrategy.build().IncludeSystemProperties('_etag').create()).V('1').has('_etag', '"00000100-0000-0800-0000-5d03edac0000"').property('test', '1')
```

## <a name="time-to-live-ttl"></a>Time-to-live (TTL)

コレクションでドキュメントの有効期限が有効になっており、ドキュメントに ```ttl``` プロパティが設定されている場合、このプロパティは通常の頂点または辺プロパティとして Gremlin トラバーサルで使用できるようになります。 Time-to-live プロパティの公開を有効にするために ```ProjectionStrategy``` は必要ありません。

次のトラバーサルで作成された頂点は、**123 秒**以内に自動的に削除されます。

```
g.addV('vertex-one').property('ttl', 123)
```

## <a name="next-steps"></a>次の手順
* [Cosmos DB のオプティミスティック コンカレンシー制御](faq.md#how-does-the-sql-api-provide-concurrency)
* Azure Cosmos DB の [Time to Live (TTL)](time-to-live.md)
