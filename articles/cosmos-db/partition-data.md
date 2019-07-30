---
title: Azure Cosmos DB でのパーティション分割と水平スケーリング
description: Azure Cosmos DB でのパーティション分割のしくみ、パーティション分割とパーティション キーを構成する方法、アプリケーションに適したパーティション キーを選ぶ方法について説明します。
ms.author: rimman
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: 1b624270fe22004a6ae64affe87b2fc22a2e9a66
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68467679"
---
# <a name="partitioning-and-horizontal-scaling-in-azure-cosmos-db"></a>Azure Cosmos DB でのパーティション分割と水平スケーリング

この記事では、Azure Cosmos DB の物理パーティションと論理パーティションについて説明します。 スケーリングとパーティション分割のベスト プラクティスについても説明します。 

## <a name="logical-partitions"></a>論理パーティション

論理パーティションは、同じパーティション キーを持つ一連のアイテムで構成されます。 たとえば、すべてのアイテムに `City` プロパティが含まれているコンテナーでは、`City` は、コンテナーのパーティション キーとして使用できます。 `London`、`Paris`、`NYC` など、`City` に特定の値を持つアイテムのグループが、個別の論理パーティションを形成します。 基になるデータを削除するときに、パーティションの削除方法を心配する必要はありません。

Azure Cosmos DB では、コンテナーがスケーラビリティの基本単位です。 コンテナーに追加されたデータと、コンテナーに対してプロビジョニングしたスループットは、一連の論理パーティション全体で自動的に (水平方向に) パーティション分割されます。 データとスループットは、Azure Cosmos コンテナーに指定したパーティション キーに基づいてパーティション分割されます。 詳細については、「[Azure Cosmos コンテナーを作成する](how-to-create-container.md)」を参照してください。

論理パーティションでは、データベース トランザクションのスコープも定義します。 論理パーティション内のアイテムは、[スナップショット分離が指定されたトランザクション](database-transactions-optimistic-concurrency.md)を使用して更新することができます。 コンテナーに新しい項目が追加されると、新しい論理パーティションがシステムによって透過的に作成されます。

## <a name="physical-partitions"></a>物理パーティション

Azure Cosmos コンテナーは、多数の論理パーティションを超えてデータとスループットを分散することでスケーリングされます。 内部的には、1 つまたは複数の論理パーティションが、[*レプリカ セット*](global-dist-under-the-hood.md)とも呼ばれる一連のレプリカで構成される物理パーティションにマップされます。 各レプリカ セットは、Azure Cosmos DB データベース エンジンのインスタンスをホストします。 レプリカ セットにより、物理パーティション内に格納されたデータの耐久性、高可用性、一貫性が確保されます。 物理パーティションでは、最大量のストレージと要求ユニット (RU) がサポートされます。 物理パーティションを構成する各レプリカは、そのパーティションのストレージ クォータを継承します。 物理パーティションのすべてのレプリカは、物理パーティションに割り当てられたスループットを一元的にサポートします。 

次の図は、グローバルに分散されている物理パーティションに論理パーティションがどのようにマップされているかを示しています。

![Azure Cosmos DB パーティション分割を示す図](./media/partition-data/logical-partitions.png)

コンテナーに対してプロビジョニングされたスループットは、物理パーティション間で均等に分割されます。 スループット要求を均等に分散しないパーティション キーの仕様により、"ホット" パーティションが作成される可能性があります。 ホット パーティションによって、レート制限が発生し、プロビジョニングされたスループットを効率的に使用できなくなり、コストが高くなる場合があります。

論理パーティションとは異なり、物理パーティションはシステムの内部実装です。 物理パーティションのサイズ、配置、または数は制御できません。また、論理パーティションと物理パーティションの間のマッピングは制御できません。 ただし、[適切な論理パーティション キーを選択する](partitioning-overview.md#choose-partitionkey)ことによって、論理パーティションの数、データ、ワークロード、スループットの分散を制御することができます。

## <a name="next-steps"></a>次の手順

* [パーティション キーの選択](partitioning-overview.md#choose-partitionkey)について確認します。
* [Azure Cosmos DB におけるスループットのプロビジョニング](request-units.md)について理解します。
* [Azure Cosmos DB の世界規模での分散](distribute-data-globally.md)について理解します。
* [Azure Cosmos コンテナーのスループットをプロビジョニングする](how-to-provision-container-throughput.md)方法を確認する。
* [Azure Cosmos データベースのスループットをプロビジョニングする](how-to-provision-database-throughput.md)方法を確認する。
