---
title: Azure Cosmos DB Gremlin の制限
description: Graph エンジンのランタイムの制限事項に関するリファレンス ドキュメント
author: olignat
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 09/10/2019
ms.author: olignat
ms.openlocfilehash: f1fe4cfac22a651f44338986d0a767fe3e1a360b
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/11/2019
ms.locfileid: "70910731"
---
# <a name="azure-cosmos-db-gremlin-limits"></a>Azure Cosmos DB Gremlin の制限
この記事では Azure Cosmos DB Gremling エンジンの制限事項について説明し、それらが顧客トラバーサルに与える影響について説明します。

Cosmos DB Gremlin は Cosmos DB インフラストラクチャの上に構築されているため、[Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/concepts-limits) のすべての制限が適用されます。 

## <a name="limits"></a>制限

Gremlin の制限に達すると、調整エラーを示す **x-ms-status-code** = 429 でトラバーサルが取り消されます。

**リソース**    | **既定の制限** | **説明**
--- | --- | ---
*要求あたりのメモリ* | **2 GB** | 処理中に要求で使用できる最大メモリ。 大規模なデータセットを計算する必要がある要求では、追加のメモリが消費されます。 この制限を超えないように、要求の範囲を小規模なデータセットに設定することを検討するか、OLAP ソリューションを使用してください。
*スクリプトの長さ* | **64 KB** | 要求あたりの Gremlin トラバーサル スクリプトの最大長。
*演算子の深さ* | **400** |  トラバーサルの一意のステップの総数。 たとえば、```g.V().out()``` の演算子の数は 2(V() と out()) です。```g.V('label').repeat(out()).times(100)``` の演算子の深さは 3(V()、repeat()、out()) です。これは、```.times(100)``` が ```.repeat()``` 演算子のパラメーターであるためです。
*並列処理の次数* | **32** | ストレージ レイヤーに対する 1 つの要求でクエリの対象となるストレージ パーティションの最大数。 数百個ものパーティションを含むグラフは、この制限の影響を受けます。
*繰り返しの制限* | **32** | ```.repeat()``` 演算子が実行できる繰り返しの最大数。 ほとんどの場合、```.repeat()``` ステップを繰り返すたびに幅優先トラバーサルが実行されます。つまり、トラバーサルは頂点間で最大 32 ホップに制限されます。
*トラバーサル タイムアウト* | **30 秒** | この時間を超えると、トラバーサルは取り消されます。 Cosmos DB Graph は、トラバーサルの大半が数ミリ秒以内に完了する OLTP データベースです。 Cosmos DB Graph で OLAP クエリを実行するには、[Apache Spark](https://azure.microsoft.com/services/cosmos-db/) と [Graph Data Frames](https://spark.apache.org/docs/latest/sql-programming-guide.html#datasets-and-dataframes) および [Cosmos DB Spark コネクタ](https://github.com/Azure/azure-cosmosdb-spark)を使用してください。
*述語の制限* | **20** | 1 つの頂点または辺に適用される ```.has()``` ステップまたは ```.hasNot()``` ステップの数。 この制限に達すると、アプリケーションに ```The SQL query exceeded the maximum number of joins. The allowed limit is 20``` というエラーが表示されます。 チームはこの制限の解除に取り組んでいます。そのため、この不便さは一時的なものです。 
*アイドル接続タイムアウト* | **5 時間** | Graph サーバーが、トラフィックがない状態で WebSocket 接続を開いたままにする時間。 TCP キープアライブ パケットまたは HTTP キープアライブ要求では、接続の有効期間はこの制限を超えて延長されませんが、それらが送信されていない場合は、基になる Azure インフラストラクチャによって接続がより早く切断される可能性があります。 Cosmos DB Graph エンジンは、そこで実行されている Gremlin トラバーサルがない場合、アイドル状態と見なされます。
*1 時間あたりのリソース トークン* | **100** | リージョン内の Gremlin アカウントに接続するために、Gremlin クライアントによって使用される一意のリソース トークンの数。 アプリケーションが 1 時間あたりの一意トークンの制限を超えた場合、`"Exceeded allowed resource token limit of 100 that can be used concurrently"` が次回の認証要求で返されます。

## <a name="next-steps"></a>次の手順
* [Azure Cosmos DB Gremlin 応答ヘッダー](gremlin-headers.md) 
* [Gremlin での Azure Cosmos DB リソース トークン](how-to-use-resource-tokens-gremlin.md)
