---
title: Azure ExpressRoute を使用したリモート ユーザーのサポート
description: このページでは、COVID-19 の世界的流行により、Azure ExpressRoute を利用してリモートで作業できるようにする方法について説明します。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 03/22/2020
ms.author: duau
ms.openlocfilehash: d51f47b73fe572ce81d3e7b54f902f94fcd11b5b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89567675"
---
# <a name="using-azure-expressroute-to-create-hybrid-connectivity-to-support-remote-users"></a>リモート ユーザーをサポートするためのハイブリッド接続を Azure ExpressRoute を使用して作成する

この記事では、ExpressRoute、Azure、Microsoft ネットワーク、および Azure パートナー エコシステムを利用してリモートで勤務する方法について説明します。

## <a name="connecting-to-azure-services-with-expressroute"></a>ExpressRoute を使用した Azure サービスへの接続

>[!NOTE]
>この記事では、ExpressRoute、Azure、Microsoft ネットワーク、および Azure パートナー エコシステムを利用してリモートで作業し、COVID-19 の危機によって直面しているネットワークの問題を軽減する方法について説明します。
>

[ExpressRoute](expressroute-introduction.md) は、Microsoft のデータセンターと、オンプレミスや共用施設にあるインフラストラクチャの間でプライベート接続を可能にする Azure サービスです。 ExpressRoute 接続では、公共のインターネットを利用できません。 接続はセキュリティで保護され、信頼性が高く、高速で、インターネット経由の一般的な接続よりも待機時間が短く、安定しています。

## <a name="useful-links"></a>便利なリンク

* [既存の ExpressRoute 回線の帯域幅を増やす方法](expressroute-howto-circuit-portal-resource-manager.md#modify)
* [ExpressRoute の監視、メトリック、およびアラート](expressroute-monitoring-metrics-alerts.md#expressroute-gateway-connections-in-bitsseconds)
* [ExpressRoute 経由のルート最適化](expressroute-optimize-routing.md)
* [Microsoft 365 向け Azure ExpressRoute](/microsoft-365/enterprise/azure-expressroute)
* [非対称ルーティングに関する考慮事項](expressroute-asymmetric-routing.md)
* [Azure portal でサポート リクエストをオープンする方法](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)

## <a name="troubleshoot"></a>トラブルシューティング

* [ExpressRoute 接続を確認する](expressroute-troubleshooting-expressroute-overview.md)
* [Resource Manager](expressroute-troubleshooting-arp-resource-manager.md) と [Classic](expressroute-troubleshooting-arp-classic.md) での ARP テーブルの取得
* [失敗した回線のリセット](reset-circuit.md)
* [ネットワーク パフォーマンスのトラブルシューティング](expressroute-troubleshooting-network-performance.md)

## <a name="next-steps"></a>次の手順

* [ExpressRoute 接続モデルについて](expressroute-connectivity-models.md)
* ExpressRoute 接続とルーティング ドメインについて説明します。 「[ExpressRoute 回線とルーティング ドメイン](expressroute-circuit-peerings.md)」を参照します
* サービス プロバイダーを検索します。 「[ExpressRoute パートナーとピアリングの場所](expressroute-locations.md)」を参照します
* すべての前提条件を満たしていることを確認します。 「 [Azure ExpressRoute の前提条件](expressroute-prerequisites.md)」を参照してください。
* [ExpressRoute 回線の作成と変更](expressroute-howto-circuit-portal-resource-manager.md)
* [ExpressRoute 回線のピアリングの作成と変更](expressroute-howto-routing-portal-resource-manager.md)
* [ExpressRoute 回線に仮想ネットワークを接続する](expressroute-howto-linkvnet-portal-resource-manager.md)
