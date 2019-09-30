---
title: Azure Cosmos DB Gremlin の TinkerPop 機能との互換性
description: Graph エンジンの互換性の問題に関するリファレンス ドキュメント
author: olignat
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 09/10/2019
ms.author: olignat
ms.openlocfilehash: d58d1ea50251c3568161ce008db2672ff0fd6372
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/11/2019
ms.locfileid: "70910734"
---
# <a name="azure-cosmos-db-gremlin-compatibility"></a>Azure Cosmos DB Gremlin の互換性
Azure Cosmos DB Graph エンジンは [Apache TinkerPop](https://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps) のトラバーサル ステップの仕様に厳密に従っていますが、違いがあります。

## <a name="behavior-differences"></a>動作の違い

* Azure Cosmos DB Graph エンジンでは***幅優先***トラバーサルが実行されますが、TinkerPop Gremlin では深さ優先になります。 この動作により、Cosmos DB のように水平方向にスケーラブルなシステムでのパフォーマンスが向上します。 

## <a name="unsupported-features"></a>サポートされていない機能

* ***[Gremlin バイトコード](http://tinkerpop.apache.org/docs/current/tutorials/gremlin-language-variants/)*** は、プログラミング言語に依存しない、グラフ トラバーサルの仕様です。 Cosmos DB Graph ではまだサポートされていません。 ```GremlinClient.SubmitAsync()``` を使用して、トラバーサルをテキスト文字列として渡してください。

* ***```property(set, 'xyz', 1)```*** set カーディナリティは現在サポートされていません。 代わりに ```property(list, 'xyz', 1)``` を使用してください

* ***```match()```*** を使用すると、宣言型のパターン一致を使用してグラフのクエリを実行できます。 この機能は使用できません。

* 頂点または辺の***プロパティとしてのオブジェクト***はサポートされていません。 プロパティには、プリミティブ型または配列のみを指定できます。

* ***配列プロパティによる並べ替え*** (```.order().by(<array property>)```) はサポートされていません。 プリミティブ型での並べ替えのみがサポートされています。

* ***非プリミティブ JSON 型***はサポートされていません。 ```string``` 型、```number``` 型、または ```true```/```false``` 型を使用してください。 ```null``` 値はサポートされていません。 

* ***GraphSONv3*** シリアライザーは現在使用できません。

* システムの分散型の性質により、***トランザクション***はサポートされていません。  Gremlin アカウントで "自分の書き込みを読み取る" ように適切な整合性モデルを構成し、オプティミスティック同時実行制御を使用して、競合する書き込みを解決してください。

## <a name="next-steps"></a>次の手順
* [Cosmos DB ユーザーの声](https://feedback.azure.com/forums/263030-azure-cosmos-db)のページにアクセスして、フィードバックを共有し、お客様にとって重要な機能に取り組めるようにチームをサポートしてください。
