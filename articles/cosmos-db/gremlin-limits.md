---
title: Azure Cosmos DB Gremlin の制限
description: Graph エンジンのランタイムの制限事項に関するリファレンス ドキュメント
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 10/04/2019
ms.author: lbosq
ms.openlocfilehash: 76ad787990c355d29613c05ca9fce31885a2eccc
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029854"
---
# <a name="azure-cosmos-db-gremlin-limits"></a>Azure Cosmos DB Gremlin の制限
この記事では Azure Cosmos DB Gremling エンジンの制限事項について説明し、それらが顧客トラバーサルに与える影響について説明します。

Cosmos DB Gremlin は、Cosmos DB インフラストラクチャの上に構築されています。 このため、[Azure Cosmos DB サービスの制限](https://docs.microsoft.com/azure/cosmos-db/concepts-limits)に関する記事で説明されているすべての制限が当てはまります。 

## <a name="limits"></a>制限

Gremlin の制限に達すると、調整エラーを示す **x-ms-status-code** 429 でトラバーサルが取り消されます。 詳細については、[Gremlin サーバーの応答ヘッダー](gremlin-limits.md)に関する記事を参照してください。

**リソース**    | **既定の制限** | **説明**
--- | --- | ---
*スクリプトの長さ* | **64 KB** | 要求あたりの Gremlin トラバーサル スクリプトの最大長。
*演算子の深さ* | **400** |  トラバーサルの一意のステップの総数。 たとえば、```g.V().out()``` の演算子の数は 2 (V() と out()) です。```g.V('label').repeat(out()).times(100)``` の演算子の深さは 3 (V()、repeat()、out()) です。これは、```.times(100)``` が ```.repeat()``` 演算子のパラメーターであるためです。
*並列処理の次数* | **32** | ストレージ レイヤーに対する 1 つの要求でクエリの対象となるストレージ パーティションの最大数。 数百個ものパーティションを含むグラフは、この制限の影響を受けます。
*繰り返しの制限* | **32** | ```.repeat()``` 演算子が実行できる繰り返しの最大数。 ほとんどの場合、```.repeat()``` ステップを繰り返すたびに幅優先トラバーサルが実行されます。つまり、トラバーサルは頂点間で最大 32 ホップに制限されます。
*トラバーサル タイムアウト* | **30 秒** | この時間を超えると、トラバーサルは取り消されます。 Cosmos DB Graph は、トラバーサルの大半が数ミリ秒以内に完了する OLTP データベースです。 Cosmos DB Graph で OLAP クエリを実行するには、[Apache Spark](https://azure.microsoft.com/services/cosmos-db/) と [Graph Data Frames](https://spark.apache.org/docs/latest/sql-programming-guide.html#datasets-and-dataframes) および [Cosmos DB Spark コネクタ](https://github.com/Azure/azure-cosmosdb-spark)を使用してください。
*アイドル接続のタイムアウト* | **1 時間** | Gremlin サービスがアイドル状態の Websocket 接続を開いたままにしている時間。 TCP キープアライブ パケットまたは HTTP キープアライブ要求では、この制限を超えて接続の有効期間は延長されません。 Cosmos DB Graph エンジンは、アクティブな Gremlin 要求が Websocket 接続で実行されていない場合、その接続をアイドル状態であると見なします。
*1 時間あたりのリソース トークン* | **100** | リージョン内の Gremlin アカウントに接続するために、Gremlin クライアントによって使用される一意のリソース トークンの数。 アプリケーションが 1 時間あたりの一意トークンの制限を超えた場合、次回の認証要求で `"Exceeded allowed resource token limit of 100 that can be used concurrently"` が返されます。

## <a name="next-steps"></a>次の手順
* [Azure Cosmos DB Gremlin 応答ヘッダー](gremlin-headers.md) 
* [Gremlin での Azure Cosmos DB リソース トークン](how-to-use-resource-tokens-gremlin.md)
