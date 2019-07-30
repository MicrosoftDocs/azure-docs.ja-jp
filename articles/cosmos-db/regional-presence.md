---
title: Azure Cosmos DB のリージョン プレゼンス
description: この記事では、Azure Cosmos DB のリージョン プレゼンスとさまざまなクラウド環境について説明します。
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: rimman
ms.custom: seodec18
ms.openlocfilehash: 775ca8c57a9a444df2f7d0a4df4224b7eba9d677
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2019
ms.locfileid: "68384046"
---
# <a name="regional-presence-with-azure-cosmos-db"></a>Azure Cosmos DB のリージョン プレゼンス

Azure Cosmos DB は Azure の基礎を成すサービスであり、既定では、Azure が利用できるすべてのリージョンで常に利用できます。 現時点で、世界中の [54 のリージョン](https://azure.microsoft.com/global-infrastructure/regions/)で Azure を利用できます。 

[![Azure Cosmos DB を利用できるリージョン](./media/regional-presence/regional-presence.png)](./media/regional-presence/regional-presence.png#lightbox)

Cosmos DB は、お客様が利用できる 5 つの異なる Azure クラウド環境のすべてで利用できます。

* **Azure パブリック** クラウドは、グローバルに利用できます。

* **Azure China 21Vianet** は、Microsoft と中国最大のインターネット プロバイダーの 1 つである 21Vianet との間の独自のパートナーシップを通して利用できます。

* **Azure Germany** では、データ保護受託者モデルに沿ってサービスが提供され、お客様のデータは、ドイツ国内でデータ保護受託者の役目を務める T-Systems International GmbH (Deutsche Telekom の子会社) の管理下で、ドイツ国外に移転されないことが保証されます。

* **Azure Government** は、米国内の 4 つのリージョンで米国政府機関とそのパートナーが利用できます。 

* **米国国防総省 (DoD) 向け Azure Government** は、米国内の 2 つのリージョンで米国国防総省が利用できます。

## <a name="regional-presence-with-global-distribution"></a>グローバル分散によるリージョン プレゼンス

既定では、すべての Azure リージョンで、Azure Cosmos DB によって公開されるすべての API シリーズ (SQL、MongoDB、Cassandra、Gremlin、Table など) を利用できます。 たとえば、Azure Cosmos DB によって公開される MongoDB と Cassandra の API シリーズを、世界中のすべての Azure リージョンだけではなく、ソブリン クラウド (China、Germany、Government、米国国防総省 (DoD) のリージョン) でも利用できます。

Azure Cosmos DB は、[グローバルに分散される](distribute-data-globally.md)データベース サービスです。 任意の数の Azure リージョンをお客様の Azure Cosmos アカウントに関連付けることができ、お客様のデータは、自動的かつ透過的にレプリケートされます。 Azure Cosmos アカウントへのリージョンの追加と削除は、いつでも実行できます。 Azure Cosmos DB では、ターンキー グローバル分散機能とマルチマスター レプリケーション プロトコルによって、読み取りと書き込みが 10 ミリ秒未満の待機時間で 99 パーセンタイルで実行され、読み取りと書き込みに対して 99.999 % の可用性が提供され、お客様の Azure Cosmos アカウントに関連付けられたすべてのリージョン間でプロビジョニング スループットを柔軟にスケーリングすることができます。 Azure Cosmos DB では、5 つの明確に定義された整合性モデルも用意されており、お客様は特定の整合性モデルをお客様のデータに適用できます。 最後に、Azure Cosmos DB は、プロビジョニング スループット、99 パーセンタイルの待ち時間、高可用性、および一貫性を含む包括的な[サービス レベル アグリーメント (SLA)](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/) を提供する業界唯一のデータベース サービスです。 上記の機能は、すべての Azure クラウドで使用できます。

## <a name="next-steps"></a>次の手順

次に、以下の記事で Azure Cosmos DB のコアとなる概念を確認できます。

* [グローバルなデータの分散](distribute-data-globally.md)
* [Azure Cosmos DB アカウントの管理方法](manage-account.md)
* [Azure Cosmos のコンテナーとデータベースのスループットをプロビジョニングする](set-throughput.md)
* [Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
