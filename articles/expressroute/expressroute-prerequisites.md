---
title: "Azure ExpressRoute 導入の前提条件 | Microsoft Docs"
description: "このページでは、Azure ExpressRoute 回線を注文する前に満たす必要がある要件を一覧します。"
documentationcenter: na
services: expressroute
author: cherylmc
manager: timlt
editor: 
ms.assetid: f872d25e-acfd-405d-9d1b-dcb9f323a2ff
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: a56749a5ac1098db3970140a76b71c77c1f74c07
ms.lasthandoff: 03/28/2017


---
# <a name="expressroute-prerequisites--checklist"></a>ExpressRoute の前提条件とチェック リスト
ExpressRoute を使用して Microsoft クラウド サービスに接続する場合は、次のセクションにある前提条件を満たしていることを確認する必要があります。

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

## <a name="azure-account"></a>Azure アカウント
* 有効でアクティブな Microsoft Azure アカウント。 このアカウントは ExpressRoute 回線のセットアップに必要です。 ExpressRoute 回線は Azure サブスクリプション内のリソースです。 Azure サブスクリプションは、Office 365 や CRM Online など、非 Azure Microsoft クラウド サービスに接続が制限される場合でも、要件となります。
* アクティブな Office 365 サブスクリプション (Office 365 サービスを利用する場合)。 詳細については、この記事の [Office 365 固有の要件](#office-365-specific-requirements)に関するセクションを参照してください。

## <a name="connectivity-provider"></a>接続プロバイダー

* [ExpressRoute 接続パートナー](expressroute-locations.md#partners) と連携して Microsoft Cloud に接続することができます。 [3 とおりの方法](expressroute-introduction.md)でオンプレミス ネットワークと Microsoft の間に接続をセットアップできます。 
* プロバイダーが ExpressRoute 接続パートナーではない場合でも、 [クラウド エクスチェンジ プロバイダー](expressroute-locations.md#c1partners)を通じて Microsoft Cloud に接続できます。

## <a name="network-requirements"></a>ネットワークの要件
* **接続の冗長性**: プロバイダーとの物理接続に対する冗長性の要件はありません。 Microsoft の要件では、 [クラウド エクスチェンジへの物理接続が 1 つ](expressroute-faqs.md#onep2plink)しかない場合でも、Microsoft のルーターとピアリング ルーターの間に冗長 BGP セッションを設定することが必要です。
* **ルーティング**: Microsoft Cloud への接続方法に応じて、自社またはプロバイダーで [ルーティング ドメイン](expressroute-circuit-peerings.md)用の BGP セッションを設定および管理する必要があります。 一部のイーサネット接続プロバイダーまたはクラウド エクスチェンジ プロバイダーでは、BGP 管理を付加価値サービスとして提供している場合があります。
* **NAT**: Microsoft では、パブリック IP アドレスのみを Microsoft ピアリング経由で受け入れます。 オンプレミス ネットワークでプライベート IP アドレスを使用している場合、自社またはプロバイダーで [NAT を使用して](expressroute-nat.md)プライベート IP アドレスをパブリック IP アドレスに変換する必要があります。
* **QoS**: Skype for Business にはさまざまなサービス (音声、ビデオ、テキストなど) があり、それぞれに特化した QoS 処理を必要とします。 自社またはプロバイダーで [QoS 要件](expressroute-qos.md)に従うことが必要です。
* **ネットワーク セキュリティ**: ExpressRoute を介して Microsoft Cloud に接続する場合は、[ネットワーク セキュリティ](../best-practices-network-security.md)について考慮してください。

## <a name="office-365"></a>Office 365
ExpressRoute で Office 365 を有効にする予定がある場合は、以下のドキュメントで Office 365 の要件の詳細について確認してください。

* [Office 365 向け ExpressRoute の概要](https://support.office.com/en-us/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd)
* [Office 365 向け ExpressRoute を使用したルーティング](https://support.office.com/en-us/article/Routing-with-ExpressRoute-for-Office-365-e1da26c6-2d39-4379-af6f-4da213218408)
* [Office 365 URL および IP アドレス範囲](https://support.office.com/en-us/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)
* [Office 365 のネットワーク計画とパフォーマンスのチューニング](https://support.office.com/en-us/article/Network-planning-and-performance-tuning-for-Office-365-e5f1228c-da3c-4654-bf16-d163daee8848)
* [ネットワーク帯域幅の計算ツールと各種ツール](https://support.office.com/en-us/article/Network-and-migration-planning-for-Office-365-f5ee6c33-bcd7-4b0b-b0f8-dc1d9fb8d132)
* [Office 365 とオンプレミス環境との統合](https://support.office.com/en-us/article/Office-365-integration-with-on-premises-environments-263faf8d-aa21-428b-aed3-2021837a4b65)
* [Office 365 での ExpressRoute に関する高度なトレーニング ビデオ](https://channel9.msdn.com/series/aer/)

## <a name="crm-online"></a>CRM Online
ExpressRoute で CRM Online を有効にする予定がある場合は、以下のドキュメントで CRM Online の詳細について確認してください。

* [CRM Online URL](https://support.microsoft.com/kb/2655102) と [IP アドレス範囲](https://support.microsoft.com/kb/2728473)

## <a name="next-steps"></a>次のステップ
* ExpressRoute の詳細については、「 [ExpressRoute のFAQ](expressroute-faqs.md)」をご覧ください。
* ExpressRoute 接続プロバイダーを探します。 「 [ExpressRoute パートナーとピアリングの場所](expressroute-locations.md)」を参照してください。
* [ルーティング](expressroute-routing.md)、[NAT](expressroute-nat.md)、[QoS](expressroute-qos.md) の要件を参照してください。
* ExpressRoute 接続を構成します。
  * [ExpressRoute 回線の作成](expressroute-howto-circuit-classic.md)
  * [ルーティングの構成](expressroute-howto-routing-classic.md)
  * [ExpressRoute 回線への VNet のリンク](expressroute-howto-linkvnet-classic.md)

