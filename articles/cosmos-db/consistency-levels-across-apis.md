---
title: 整合性レベルと Azure Cosmos DB API
description: Azure Cosmos DB、Apache Cassandra、および MongoDB の異なる API 間の整合性レベルのマッピングについての理解
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/6/2020
ms.reviewer: sngun
ms.openlocfilehash: af777efda769315019ecee41d4053f5ab82f3047
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2020
ms.locfileid: "87920434"
---
# <a name="consistency-levels-and-azure-cosmos-db-apis"></a>整合性レベルと Azure Cosmos DB API

Azure Cosmos DB は、一般的なデータベース用のワイヤ プロトコルに対応する API をネイティブにサポートしています。 これらは、MongoDB、Apache Cassandra、Gremlin、および Azure Table Storage などです。 これらのデータベースには、厳密に定義された整合性モデルや SLA で裏付けられた整合性レベルに対する保証がありません。 通常、これらのデータベースでは、Azure Cosmos DB によって提供される 5 つの整合性モデルのサブセットのみが提供されます。

SQL API、Gremlin API、および Table API を使用する場合、Azure Cosmos アカウントに構成されている既定の整合性レベルが使用されます。 

Casandra API または Azure Cosmos DB の Mongo DB 用 API を使用すると、Apache Cassandra と MongoDB がそれぞれ提供しているものよりも強い整合性と耐久性が保証された、整合性レベルの完全なセットがアプリケーションで取得されます。 このドキュメントでは、Apache Cassandra と MongoDB の整合性レベルに対応する Azure Cosmos DB の整合性レベルを示します。

> [!NOTE]
> Azure Cosmos DB の既定の整合性モデルはセッションです。 セッションは、Cassandra や MongoDB ではネイティブにサポートされていない、クライアント中心の整合性モデルです。 どの整合性モデルを選択するかの詳細については、「[Azure Cosmos DB の整合性レベル](consistency-levels.md)」を参照してください

## <a name="mapping-between-apache-cassandra-and-azure-cosmos-db-consistency-levels"></a><a id="cassandra-mapping"></a>Apache Cassandra と Azure Cosmos DB の間の整合性レベルのマッピング

Apache Cassandra は、Azure Cosmos DB とは異なり、整合性の保証がネイティブで正確に定義されていません。  代わりに Apache Cassandra には、高可用性、整合性および待機時間とのトレードオフを可能にする、書き込みの整合性レベルと読み取りの整合性レベルがあります。 Azure Cosmos DB の Cassandra API を使用する場合: 

* Apache Cassandra の書き込み整合性レベルは、お使いの Azure Cosmos アカウントに構成された既定の整合性レベルにマップされます。 書き込み操作 (CL) の整合性は、要求ごとに変更することはできません。

* Azure Cosmos DB は、Cassandra クライアント ドライバーによって指定された読み取り整合性レベルを、読み取り要求に動的に構成された Azure Cosmos DB 整合性レベルの 1 つに動的にマップします。 

次の表に、Cassandra API を使用した場合に、ネイティブ Cassandra 整合性レベルがどのように Azure Cosmos DB の整合性レベルにマップされるかを示しています。  

:::image type="content" source="./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png" alt-text="Cassandra の整合性モデル マッピング" lightbox="./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png" :::

## <a name="mapping-between-mongodb-and-azure-cosmos-db-consistency-levels"></a><a id="mongo-mapping"></a>MongoDB と Azure Cosmos DB の間の整合性レベルのマッピング

ネイティブ MongoDB は、Azure Cosmos DB とは異なり、整合性の保証がネイティブに正確に定義されていません。 代わりに、ネイティブ MongoDB では、必要な整合性レベルを実現するためにプライマリまたはセカンダリ レプリカのいずれかを読み取り操作の対象とする、書き込み保証、読み込み保証、isMaster ディレクティブの整合性保証を設定することをユーザーに許可しています。

MongoDB で Azure Cosmos DB の API を使用する場合、MongoDB ドライバーではお使いの書き込みリージョンをプライマリ レプリカとして扱い、その他のすべてのリージョンを読み取りレプリカとして扱います。 お使いの Azure Cosmos アカウントと関連付けられているどのリージョンをプライマリ レプリカとするかは、選択することが可能です。 

MongoDB で Azure Cosmos DB の API を使用する場合:

* 書き込み保証は、お使いの Azure Cosmos アカウントに構成された既定の整合性レベルにマップされます。

* Azure Cosmos DB は、MongoDB クライアント ドライバーによって指定された読み込み保証を、読み取り要求に動的に構成された Azure Cosmos DB 整合性レベルの 1 つに動的にマップします。  

* お使いの Azure Cosmos アカウントに関連付けられた特定のリージョンを最初の書き込み可能なリージョンとすることによって "Master" と注釈を付けることができます。 

次の表では、MongoDB で Azure Cosmos DB の API を使用する場合、ネイティブ MongoDB 書き込み/読み込み保証が Azure Cosmos 整合性レベルにどのようにマップされるかを示しています。

:::image type="content" source="./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png" alt-text="MongoDB の整合性モデル マッピング" lightbox= "./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png":::

## <a name="next-steps"></a>次のステップ

Azure Cosmos DB API とオープン ソースの API の間の整合性レベルおよび互換性についての詳細を参照してください。 次の記事をご覧ください。

* [さまざまな整合性レベルでの可用性およびパフォーマンスのトレードオフ](consistency-levels-tradeoffs.md)
* [Azure Cosmos DB の MongoDB 用 API でサポートされている MongoDB の機能](mongodb-feature-support.md)
* [Azure Cosmos DB Cassandra API でサポートされる Apache Cassandra の機能](cassandra-support.md)
