---
title: Azure Cosmos DB でのスループットのスケーリング
description: この記事では、Azure Cosmos DB で、Azure Cosmos アカウントがプロビジョニングされている異なるリージョン間でスループットをスケーリングする方法について説明します。
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: 4cc8f2c90e74b5b3ab6df3169df0524f1134b66e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85113604"
---
# <a name="globally-scale-provisioned-throughput"></a>プロビジョニングされたスループットのグローバルなスケーリング 

Azure Cosmos DB では、プロビジョニングされたスループットは要求ユニット/秒 (RU/s、複数形は RUs) として表現されます。 次の図に示すように、RU は Cosmos コンテナーに対する読み書き両方の操作のコストを測定します。

:::image type="content" source="./media/scaling-throughput/request-unit-charge-of-read-and-write-operations.png" alt-text="要求ユニット" border="false":::

RU は Cosmos コンテナーまたは Cosmos データベースにプロビジョニングできます。 コンテナーにプロビジョニングされた RU は、そのコンテナーに対して実行される操作についてのみ使用できます。 データベースにプロビジョニングされた RU は、そのデータベース内のすべてのコンテナー間で共有されます (排他的に割り当てられた RU を持つコンテナーを除く)。

プロビジョニングされたスループットを弾力的にスケーリングするために、プロビジョニングされた RU/秒をいつでも増減できます。 詳細については、[スループットのプロビジョニングに関する方法](set-throughput.md)、および Cosmos コンテナーとデータベースを柔軟にスケーリングする方法に関するページを参照してください。 スループットをグローバルにスケーリングするために、Cosmos アカウントからリージョンをいつでも追加したり削除したりすることができます。 詳細については、「[データベース アカウントのリージョンの追加/削除](how-to-manage-database-account.md#addremove-regions-from-your-database-account)」を参照してください。 Cosmos アカウントに複数のリージョンを関連付けることは、短い待機時間と[高い可用性](high-availability.md)を世界中で実現するために多くのシナリオで重要です。

## <a name="how-provisioned-throughput-is-distributed-across-regions"></a>プロビジョニングされたスループットのリージョン間での分散方法

Cosmos コンテナー (またはデータベース) に *'R'* 個の RU をプロビジョニングする場合、Cosmos DB により、Cosmos アカウントに関連付けられている*それぞれ*のリージョンで *'R'* 個の RU が利用可能であることが確保されます。 アカウントに新しいリージョンを追加するたびに、Cosmos DB では、新しく追加されたリージョンに *'R'* 個の RU を自動的にプロビジョニングされます。 Cosmos コンテナーに対して実行される操作は、各リージョンで *'R'* 個の RU を取得することが確保されます。 特定のリージョンに RU を選択的に割り当てることはできません。 Cosmos コンテナー (またはデータベース) でプロビジョニングされた RU は、Cosmos アカウントに関連付けられているすべてのリージョンでプロビジョニングされます。

Cosmos コンテナーに *'R'* 個の RU が構成され、Cosmos アカウントに *'N'* 個のリージョンが関連付けられていると仮定すると、次のようになります。

- Cosmos アカウントに単一の書き込みリージョンが構成されている場合、コンテナーでグローバルに利用可能な RU の合計は *R* x *N* 個です。

- Cosmos アカウントに複数の書き込みリージョンが構成されている場合、コンテナーでグローバルに利用可能な RU の合計は *R* x (*N*+1) 個です。 追加の *R* 個の RU は、更新の競合およびリージョン間でのアンチエントロピ トラフィックを処理するために自動的にプロビジョニングされます。

選択した[整合性モデル](consistency-levels.md)もスループットに影響します。 整合性レベルが比較的緩やかな場合 (*セッション*、*一貫性のあるプレフィックス*、*最終的*など)、比較的強固な場合 (*有界整合性制約*や*厳密*など) と比べ、ほぼ 2 倍の読み取りスループットを得ることができます。

## <a name="next-steps"></a>次のステップ

次のステップでは、コンテナーまたはデータベースのスループットを構成する方法を学習できます。

* [コンテナーとデータベースのスループットの取得と設定](set-throughput.md) 

