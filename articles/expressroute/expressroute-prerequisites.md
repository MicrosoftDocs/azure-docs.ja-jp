---
title: 'Azure ExpressRoute: 前提条件'
description: このページでは、Azure ExpressRoute 回線を注文する前に満たす必要がある要件を一覧します。 ここにはチェックリストが含まれています。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: duau
ms.openlocfilehash: 1fad1bca18d16ac3b6a654a3c289d0a14e3cd2e2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92204794"
---
# <a name="expressroute-prerequisites--checklist"></a>ExpressRoute の前提条件とチェック リスト
ExpressRoute を使用して Microsoft クラウド サービスに接続する場合は、次のセクションにある前提条件を満たしていることを確認する必要があります。

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

## <a name="azure-account"></a>Azure アカウント
* 有効でアクティブな Microsoft Azure アカウント。 このアカウントは ExpressRoute 回線のセットアップに必要です。 ExpressRoute 回線は Azure サブスクリプション内のリソースです。 Microsoft 365 など、Azure 以外の Microsoft クラウド サービスに接続が限定される場合でも、Azure サブスクリプションが要件となります。
* アクティブな Microsoft 365 サブスクリプション (Microsoft 365 サービスを使用している場合)。 詳細については、この記事の Microsoft 365 固有の要件に関するセクションを参照してください。

## <a name="connectivity-provider"></a>接続プロバイダー

* [ExpressRoute 接続パートナー](expressroute-locations.md#partners) と連携して Microsoft Cloud に接続することができます。 [3 とおりの方法](expressroute-introduction.md)でオンプレミス ネットワークと Microsoft の間に接続をセットアップできます。
* プロバイダーが ExpressRoute 接続パートナーではない場合でも、 [クラウド エクスチェンジ プロバイダー](expressroute-locations.md#connectivity-through-exchange-providers)を通じて Microsoft Cloud に接続できます。

## <a name="network-requirements"></a>ネットワークの要件
* **ピアリングの場所ごとの冗長性**: Microsoft から、([クラウド エクスチェンジへの物理接続が 1 つ](expressroute-faqs.md#onep2plink)しかない場合でも) 各 ExpressRoute 回線上の Microsoft のルーターとピアリング ルーターの間に冗長 BGP セッションを設定することが求められます。
* **ディザスター リカバリーの冗長性**: Microsoft は、単一障害点を回避するために別々のピアリングの場所に少なくとも 2 つの ExpressRoute 回線を設定することを強くお勧めします。
* **ルーティング**: Microsoft Cloud への接続方法に応じて、自社またはプロバイダーで [ルーティング ドメイン](expressroute-circuit-peerings.md)用の BGP セッションを設定および管理する必要があります。 一部のイーサネット接続プロバイダーまたはクラウド エクスチェンジ プロバイダーでは、BGP 管理を付加価値サービスとして提供している場合があります。
* **NAT**:Microsoft では、パブリック IP アドレスのみを Microsoft ピアリング経由で受け入れます。 オンプレミス ネットワークでプライベート IP アドレスを使用している場合、自社またはプロバイダーで [NAT を使用して](expressroute-nat.md)プライベート IP アドレスをパブリック IP アドレスに変換する必要があります。
* **QoS**:Skype for Business にはさまざまなサービス (音声、ビデオ、テキストなど) があり、それぞれに特化した QoS 処理を必要とします。 自社またはプロバイダーで [QoS 要件](expressroute-qos.md)に従うことが必要です。
* **ネットワーク セキュリティ**: ExpressRoute を介して Microsoft Cloud に接続する場合は、[ネットワーク セキュリティ](/azure/cloud-adoption-framework/reference/networking-vdc)について考慮してください。

## <a name="microsoft-365"></a>Microsoft 365
ExpressRoute で Microsoft 365 を有効にする予定がある場合は、以下のドキュメントで Microsoft 365 の要件の詳細について確認してください。

* [Microsoft 365 向け Azure ExpressRoute](/microsoft-365/enterprise/azure-expressroute)
* [Microsoft 365 向け ExpressRoute を使用したルーティング](/microsoft-365/enterprise/routing-with-expressroute)
* [ExpressRoute による高可用性とフェールオーバー](/microsoft-365/enterprise/network-planning-with-expressroute)
* [Microsoft 365 URL および IP アドレス範囲](/microsoft-365/enterprise/urls-and-ip-address-ranges)
* [Microsoft 365 のネットワーク計画とパフォーマンスのチューニング](/microsoft-365/enterprise/network-planning-and-performance)
* [Microsoft 365 のネットワークおよび移行の計画](/microsoft-365/enterprise/network-and-migration-planning)
* [Microsoft 365 とオンプレミス環境との統合](/microsoft-365/enterprise/microsoft-365-integration)
* [Office 365 での ExpressRoute に関する高度なトレーニング ビデオ](https://channel9.msdn.com/series/aer/)

## <a name="next-steps"></a>次のステップ
* ExpressRoute の詳細については、「 [ExpressRoute のFAQ](expressroute-faqs.md)」をご覧ください。
* ExpressRoute 接続プロバイダーを探します。 「 [ExpressRoute パートナーとピアリングの場所](expressroute-locations.md)」を参照してください。
* [ルーティング](expressroute-routing.md)、[NAT](expressroute-nat.md)、[QoS](expressroute-qos.md) の要件を参照してください。
* ExpressRoute 接続を構成します。
  * [ExpressRoute 回線の作成](expressroute-howto-circuit-arm.md)
  * [ルーティングの構成](expressroute-howto-routing-arm.md)
  * [ExpressRoute 回線への VNet のリンク](expressroute-howto-linkvnet-arm.md)