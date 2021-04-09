---
title: MongoDB 用 Azure Cosmos DB API の整合性レベルのマッピング
description: MongoDB 用 Azure Cosmos DB API の整合性レベルをマッピングする。
author: sivethe
ms.author: sivethe
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 10/12/2020
ms.reviewer: sngun
ms.openlocfilehash: e8ac3e376c8d67e82def3a57910707c6b1433912
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93333156"
---
# <a name="consistency-levels-for-azure-cosmos-db-and-the-api-for-mongodb"></a>Azure Cosmos DB と MongoDB 用 API の整合性レベル
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

ネイティブ MongoDB は、Azure Cosmos DB とは異なり、整合性の保証がネイティブに正確に定義されていません。 代わりに、ネイティブ MongoDB では、必要な整合性レベルを実現するためにプライマリまたはセカンダリ レプリカのいずれかを読み取り操作の対象とする、書き込み保証、読み込み保証、isMaster ディレクティブの整合性保証を設定することをユーザーに許可しています。

MongoDB で Azure Cosmos DB の API を使用する場合、MongoDB ドライバーではお使いの書き込みリージョンをプライマリ レプリカとして扱い、その他のすべてのリージョンを読み取りレプリカとして扱います。 お使いの Azure Cosmos アカウントと関連付けられているどのリージョンをプライマリ レプリカとするかは、選択することが可能です。

> [!NOTE]
> Azure Cosmos DB の既定の整合性モデルはセッションです。 セッションは、Cassandra や MongoDB ではネイティブにサポートされていない、クライアント中心の整合性モデルです。 どの整合性モデルを選択するかの詳細については、「[Azure Cosmos DB の整合性レベル](consistency-levels.md)」を参照してください

MongoDB で Azure Cosmos DB の API を使用する場合:

* 書き込み保証は、お使いの Azure Cosmos アカウントに構成された既定の整合性レベルにマップされます。

* Azure Cosmos DB は、MongoDB クライアント ドライバーによって指定された読み込み保証を、読み取り要求に動的に構成された Azure Cosmos DB 整合性レベルの 1 つに動的にマップします。  

* お使いの Azure Cosmos アカウントに関連付けられた特定のリージョンを最初の書き込み可能なリージョンとすることによって "Primary" と注釈を付けることができます。 

## <a name="mapping-consistency-levels"></a>整合性レベルのマッピング

次の表では、MongoDB で Azure Cosmos DB の API を使用する場合、ネイティブ MongoDB 書き込み/読み込み保証が Azure Cosmos 整合性レベルにどのようにマップされるかを示しています。

:::image type="content" source="./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png" alt-text="MongoDB の整合性モデル マッピング" lightbox= "./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png":::

Azure Cosmos アカウントが、強固な整合性以外の任意の整合性レベルで構成されている場合、*確率論的有界整合性制約* (PBS) メトリックを調べることによって、ワークロードの厳密な整合性を持つ読み取りをクライアントが取得する確率を調べることができます。 このメトリックは、Azure portal で公開されます。詳しくは、「[確率的有界整合性制約 (PBS) メトリックを監視する](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric)」をご覧ください。

確率論的有界整合性制約は、最終的な整合性が、どれほど最終的であるかを示します。 このメトリックでは、Azure Cosmos アカウントで現在構成されている整合性レベルより強力な整合性をどのくらいの頻度で得られるかについての分析情報が提供されます。 つまり、書き込みと読み取りのリージョンの組み合わせに対して厳密な整合性の読み取りを取得する確率 (ミリ秒で測定) を表示できます。

## <a name="next-steps"></a>次のステップ

Azure Cosmos DB でのグローバル配信と整合性レベルについて学習します。

* [グローバル配信の概要](distribute-data-globally.md)
* [整合性レベルの概要](consistency-levels.md)
* [高可用性](high-availability.md)
