---
title: Azure Cosmos DB でのストレージ コストを最適化する
description: この記事では、Azure Cosmos DB に格納されているデータのストレージ コストを管理する方法について説明します。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 92bd3ff925080def4b2f074d07e662dfdbdbee01
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93080852"
---
# <a name="optimize-storage-cost-in-azure-cosmos-db"></a>Azure Cosmos DB でのストレージ コストを最適化する
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB では、無制限のストレージとスループットを提供します。 Azure Cosmos コンテナーまたはデータベース上でプロビジョニング/構成する必要のあるスループットとは異なり、ストレージは使用料に基づいて課金されます。 使用する論理ストレージに対してのみ課金され、ストレージを事前に予約する必要はありません。 ストレージは、Azure Cosmos コンテナーに対して追加または削除したデータに基づいて、自動的にスケール アップ/ダウンします。

## <a name="storage-cost"></a>Storage コスト

ストレージは GB 単位で課金されます。 ローカル SSD を使用したストレージは、データおよびインデックス作成で使用されます。 使用される合計ストレージは、Azure Cosmos DB を使用しているすべてのリージョンで使用される、データおよびインデックスに必要なストレージと等しくなります。 Azure Cosmos アカウントを 3 つのリージョン間でグローバルにレプリケートする場合は、それらの 3 つのリージョンでの合計ストレージ コストに対して支払います。 ストレージ要件を見積もるには、[容量プランナー](https://www.documentdb.com/capacityplanner) ツールを参照してください。 Azure Cosmos DB でのストレージのコストは $0.25 GB/月です。最新の更新情報については、[価格設定ページ](https://azure.microsoft.com/pricing/details/cosmos-db/)を参照してください。 アラートを設定して、Azure Cosmos コンテナーによって使用されるストレージを判断したり、ストレージを監視するには、[Azure Cosmos DB の監視](./monitor-cosmos-db.md)に関する記事を参照してください。

## <a name="optimize-cost-with-item-size"></a>アイテムのサイズによってコストを最適化する

Azure Cosmos DB では、最適なパフォーマンスとコストの利点を得るために、アイテムのサイズが 2 MB 以下であることが想定されています。 2 MB を超えるデータを格納するアイテムが必要な場合は、アイテムのスキーマを再設計することを検討してください。 スキーマを再設計できないというまれなケースでは、アイテムをサブアイテムに分割し、それらを一般的な識別子 (ID) で論理的にリンクすることができます。 Azure Cosmos DB のすべての機能は、その論理識別子に固定することで一貫して動作します。

## <a name="optimize-cost-with-indexing"></a>インデックス作成によってコストを最適化する

既定では、データに自動的にインデックスが付けられるので、使用される合計ストレージが増大する可能性があります。 ただし、このオーバーヘッドを低減するために、カスタムのインデックス ポリシーを適用できます。 ポリシーによって調整されていない自動インデックス作成は、アイテムのサイズの約 10 から 20% になります。 インデックス ポリシーを削除またはカスタマイズすることで、書き込みに対して追加のコストは発生せず、追加のスループット容量は必要ありません。 カスタムのインデックス作成ポリシーを構成するには、[Azure Cosmos DB でのインデックス作成](index-policy.md)に関するページを参照してください。 前にリレーショナル データベースを使用したことがあれば、"すべてにインデックスを作成" するには、2 倍以上のストレージが必要だと思うかもしれません。 しかし、Azure Cosmos DB での平均的なケースでは、それよりもかなり小さくて済みます。 Azure Cosmos DB は、低ストレージ フットプリント用に設計されているので、自動インデックス作成の場合にも、インデックスのストレージ オーバーヘッドは一般的に低くなります (10 から 20%)。 インデックス作成ポリシーを管理することで、インデックス フットプリントとクエリ パフォーマンスのトレードオフを、よりきめ細かく制御できます。

## <a name="optimize-cost-with-time-to-live-and-change-feed"></a>有効時間および変更フィードによってコストを最適化する

データが不要になったら、[有効期限](time-to-live.md)や[変更フィード](change-feed.md)を使用して Azure Cosmos アカウントから適切に削除するか、または Azure BLOB Storage や Azure データ ウェアハウスなどの別のデータ ストアに古いデータを移行することができます。 Azure Cosmos DB は、Time to Live (TTL) を使用して、一定の期間が経過したらアイテムをコンテナーから自動的に削除する機能を提供します。 既定では、コンテナー レベルで Time to Live を設定し、項目ごとに値をオーバーライドできます。 コンテナー レベルまたはアイテム レベルで TTL を設定すると、アイテムが最後に変更されてからその期間が経過した後で、Azure Cosmos DB によってそれらのアイテムが自動的に削除されます。 変更フィードを使用すると、Azure Cosmos DB 内の別のコンテナーまたは外部データ ストアにデータを移行できます。 移行ではダウン タイムは発生せず、移行が完了したら、有効期限を削除するか、またはソース Azure Cosmos コンテナーを削除するように構成できます。

## <a name="optimize-cost-with-rich-media-data-types"></a>リッチ メディア データ タイプによってコストを最適化する 

ビデオやイメージなどのリッチ メディア タイプを格納する場合、Azure Cosmos DB にはさまざまなオプションがあります。 1 つのオプションでは、これらのリッチ メディア タイプを Azure Cosmos アイテムとして格納します。 アイテムあたり 2 MB の制限があり、データ アイテムを複数のサブアイテムにチェーニングすることにより、この制限を回避できます。 または、これらを Azure BLOB Storage に格納し、メタデータを使用して、Azure Cosmos アイテムから参照することができます。 この方法にはさまざまな長所と短所があります。 1 つ目の方法では、通常の Azure Cosmos アイテムに加えてリッチ メディア データ タイプに対して、待機時間、スループット SLA、ターンキー グローバル配信機能の観点から最高のパフォーマンスを実現します。 ただし、サポート価格は高くなります。 メディアを Azure BLOB Storage に格納することで、全体的なコストを削減できます。 待機時間が重要な場合は、Azure Cosmos アイテムから参照されるリッチ メディア ファイルに対して Premium Storage を使用できます。 これは、低コストでエッジ サーバーのイメージを処理して geo 制限を回避するために、CDN とネイティブに統合されています。 このシナリオの短所としては、Azure Cosmos DB と Azure BLOB Storage の 2 つのサービスを処理する必要があるので、運用コストが増大する可能性があります。 

## <a name="check-storage-consumed"></a>使用されたストレージを確認する

Azure Cosmos コンテナーのストレージ使用量を確認するには、コンテナーに対して HEAD 要求または GET 要求を実行し、`x-ms-request-quota` ヘッダーと `x-ms-request-usage` ヘッダーを調べます。 または、.NET SDK を使用する場合、[DocumentSizeQuota](/previous-versions/azure/dn850325(v%3Dazure.100)) プロパティと [DocumentSizeUsage](/previous-versions/azure/dn850324(v=azure.100)) プロパティを使用して、使用されたストレージを取得できます。

## <a name="using-sdk"></a>SDK の使用

```csharp
// Measure the item size usage (which includes the index size)
ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));   

Console.WriteLine("Item size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);
```

## <a name="next-steps"></a>次のステップ

次は、先に進み、以下の各記事で Azure Cosmos DB でのコストの最適化の詳細について学習することができます。

* [開発とテストのための最適化](optimize-dev-test.md)の詳細について学習します
* [Azure Cosmos DB の課金内容の確認](understand-your-bill.md)の詳細について学習します
* [スループット コストの最適化](optimize-cost-throughput.md)の詳細について学習します
* [読み取りと書き込みのコストの最適化](optimize-cost-reads-writes.md)の詳細について学習します
* [クエリ コストの最適化](./optimize-cost-reads-writes.md)の詳細について学習します
* [複数リージョンの Azure Cosmos アカウント コストの最適化](optimize-cost-regions.md)の詳細について学習します
