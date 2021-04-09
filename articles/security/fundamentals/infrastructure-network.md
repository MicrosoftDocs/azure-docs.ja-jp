---
title: Azure ネットワーク アーキテクチャ
description: この記事では、Microsoft Azure インフラストラクチャ ネットワークの一般的な説明を行います。
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/08/2020
ms.author: terrylan
ms.openlocfilehash: 3b047489f9cfa3623c11e324cf58114b707c10b7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89567871"
---
# <a name="azure-network-architecture"></a>Azure ネットワーク アーキテクチャ
Azure ネットワーク アーキテクチャは、インターネットから Azure データセンターへの接続を提供します。 Azure でデプロイされたあらゆるワークロード (IaaS、PaaS、SaaS) は、Azure データセンター ネットワークを活用しています。

## <a name="network-topology"></a>ネットワーク トポロジ
Azure データセンターのネットワーク アーキテクチャは、以下のコンポーネントで構成されています。

- 境界ネットワーク
- ワイド エリア ネットワーク
- リージョン ゲートウェイ ネットワーク
- データセンター ネットワーク

![Azure ネットワークの図](./media/infrastructure-network/network-arch.png)

## <a name="network-components"></a>ネットワーク コンポーネント
ネットワーク コンポーネントの簡単な説明。

- 境界ネットワーク

   - Microsoft ネットワークと他のネットワーク (インターネット、エンタープライズ ネットワークなど) の間の責任分界点
   - Azure へのインターネットおよび [ExpressRoute](../../expressroute/expressroute-introduction.md) ピアリングを提供します

- ワイド エリア ネットワーク

   - 世界中をカバーする Microsoft のインテリジェントなバックボーン ネットワーク
   - [Azure リージョン](https://azure.microsoft.com/global-infrastructure/geographies/)間の接続を提供します

- リージョン ゲートウェイ

   - Azure リージョン内のすべてのデータセンターの集計ポイント
   - Azure リージョン内のデータセンター間に大規模な接続を提供します (たとえば、データセンターあたり数百テラビット)

- データセンター ネットワーク

   - 低オーバーサブスクライブ帯域幅で、データセンター内のサーバー間に接続を提供します

上記のネットワーク コンポーネントは、常にオンで常時利用可能なクラウド ビジネスをサポートするために、最大限の可用性を提供するように設計されています。 冗長性は、物理的な側面から制御プロトコルに至るまでのあらゆる面を考慮して設計され、ネットワークに組み込まれています。

## <a name="datacenter-network-resiliency"></a>データセンター ネットワークの回復性
データセンター ネットワークを使用した回復性の設計原則について説明します。

データセンター ネットワークは、[Clos ネットワーク](https://en.wikipedia.org/wiki/Clos_network)の変更バージョンであり、クラウド規模のトラフィックに対して高いバイセクション帯域幅を提供します。 このネットワークは、個々のハードウェア障害による影響を軽減するために、多数の汎用デバイスを使用して構築されています。 これらのデバイスを物理的に異なる場所に戦略的に配置し、電源と冷却領域を分離することで、環境イベントの影響を軽減しています。  コントロール プレーンでは、すべてのネットワーク デバイスが OSI モデルのレイヤー 3 のルーティング モードとして実行されます。これにより、トラフィック ループの過去の問題が解消されます。 異なる階層間のすべてのパスがアクティブになり、等コスト マルチパス (ECMP) ルーティングを使用して高い冗長性と帯域幅が提供されます。

次の図は、データセンター ネットワークがさまざまな階層のネットワーク デバイスによって構築されていることを示しています。 図中の棒線は、冗長性と高帯域幅接続を提供するネットワーク デバイスのグループを示しています。

![データセンター ネットワーク](./media/infrastructure-network/datacenter-network.png)

## <a name="next-steps"></a>次のステップ
Microsoft が Azure インフラストラクチャのセキュリティ保護を支援するために行っていることの詳細については、以下を参照してください。

- [Azure の施設、建物、および物理上のセキュリティ](physical-security.md)
- [Azure インフラストラクチャの可用性](infrastructure-availability.md)
- [Azure 情報システムのコンポーネントと境界](infrastructure-components.md)
- [Azure 実稼働環境のネットワーク](production-network.md)
- [Azure SQL Database のセキュリティ機能](infrastructure-sql.md)
- [Azure の実稼働環境の運用と管理](infrastructure-operations.md)
- [Azure インフラストラクチャの監視](infrastructure-monitoring.md)
- [Azure インフラストラクチャの整合性](infrastructure-integrity.md)
- [Azure での顧客データの保護](protection-customer-data.md)
