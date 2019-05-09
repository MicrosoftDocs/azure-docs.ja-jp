---
title: Azure インフラストラクチャの可用性
description: この記事では、顧客のデータの最大の可用性を提供する冗長性のレベルについて説明します。
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/06/2018
ms.author: terrylan
ms.openlocfilehash: a9a55f61f032512be032897d5f21ece405844634
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2019
ms.locfileid: "56105877"
---
# <a name="azure-infrastructure-availability"></a>Azure インフラストラクチャの可用性
Azure は、仮想化テクノロジで実現される広範囲の冗長性に基づいた堅牢な可用性を提供します。 Azure には、顧客のデータの最大の可用性を提供するさまざまなレベルの冗長性が用意されています。

## <a name="temporary-outages-and-natural-disaster"></a>一時的な停止および自然災害
Microsoft Cloud Infrastructure and Operations チームは、クラウド インフラストラクチャの設計、構築、運用、およびセキュリティの改善を行っています。 このチームは、Azure インフラストラクチャが、高可用性と信頼性、高効率、およびスマート スケーラビリティを提供していることを保証します。 チームは、さらにセキュリティで保護された、プライベートな信頼できるクラウドを提供します。

無停電電源装置とバッテリーの広大なバンクにより、短期間の停電が発生した場合でも、電気が途切れないことが保証されます。 非常発電機は、長時間にわたる停止や計画メンテナンスのためのバックアップ電源を提供します。 自然災害が発生した場合、データセンターはオンサイトの予備燃料を使用できます。

高速で堅牢な光ファイバー ネットワークは、データセンターを他の主要なハブやインターネット ユーザーと接続します。 コンピューティング ノードは、ユーザーに近いワークロードをホストすることによって待機時間を短縮し、geo 冗長性を提供し、さらに全体的なサービスの回復性を向上させます。 エンジニアのチームは 24 時間休みなしで作業して、サービスを永続的に使用できることを保証します。

Microsoft は、高度な監視およびインシデント対応、サービスのサポート、およびバックアップ フェールオーバー機能を通して高可用性を確保します。 地理的に分散した Microsoft オペレーション センターは、365 日 24 時間体制で運用しています。 Azure ネットワークは、世界最大のネットワークの 1 つです。 光ファイバーおよびコンテンツ配信ネットワークは、データセンターとエッジ ノードを接続して高パフォーマンスと信頼性を確保します。

## <a name="disaster-recovery"></a>障害復旧
Azure では、2 つの場所でデータを持続的に保持します。 バックアップ サイトの場所を選択できます。 Azure は常に、両方の場所でお客様のデータの 3 つの正常なレプリカを保持します。

## <a name="database-availability"></a>データベースの可用性
Azure では、データベースがインターネット ゲートウェイ経由でインターネットにアクセスでき、それによりデータベースの可用性が持続されることを保証します。 監視では、アクティブなデータベースの正常性と状態を 5 分の時間間隔で評価します。

## <a name="storage-availability"></a>ストレージの可用性
Azure は、高度にスケーラブルな、持続性のあるストレージ サービスを通してストレージを提供します。これにより、接続エンドポイントが提供されます。 これは、アプリケーションがストレージ サービスに直接アクセスできることを意味します。 ストレージ サービスは、受信したストレージ要求を、トランザクションの整合性を使用して効率的に処理します。

## <a name="next-steps"></a>次の手順
Microsoft が Azure インフラストラクチャのセキュリティ保護を支援するために行っていることの詳細については、以下を参照してください。

- [Azure の施設、建物、および物理上のセキュリティ](azure-physical-security.md)
- [Azure 情報システムのコンポーネントと境界](azure-infrastructure-components.md)
- [Azure ネットワーク アーキテクチャ](azure-infrastructure-network.md)
- [Azure 実稼働環境のネットワーク](azure-production-network.md)
- [Azure SQL Database のセキュリティ機能](azure-infrastructure-sql.md)
- [Azure の実稼働環境の運用と管理](azure-infrastructure-operations.md)
- [Azure インフラストラクチャの監視](azure-infrastructure-monitoring.md)
- [Azure インフラストラクチャの整合性](azure-infrastructure-integrity.md)
- [Azure での顧客データの保護](azure-protection-of-customer-data.md)
