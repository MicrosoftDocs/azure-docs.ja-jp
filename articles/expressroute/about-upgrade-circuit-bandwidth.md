---
title: 回線の帯域幅のアップグレードについて | Azure ExpressRoute
description: この記事では、ExpressRoute 回線の帯域幅をアップグレードするためのベスト プラクティスについて説明します
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: duau
ms.openlocfilehash: 7831e7944321e074c312853e1534c47970ebdfdb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89397952"
---
# <a name="about-upgrading-expressroute-circuit-bandwidth"></a>ExpressRoute 回線の帯域幅のアップグレードについて

ExpressRoute により、Microsoft のグローバル ネットワークへの専用接続性とプライベート接続性が可能になります。 接続は、ExpressRoute パートナーのネットワーク、または Microsoft Enterprise Edge (MSEE) デバイスへの直接接続によって容易になります。 物理接続を構成してテストしたら、ExpressRoute 回線を作成し、ピアリングを構成することによって、レイヤー 2 とレイヤー 3 の接続を有効にすることができます。

## <a name="upgrade-circuit-bandwidth"></a><a name="upgrade"></a>回線の帯域幅をアップグレードする

回線の帯域幅をアップグレードするには、ExpressRoute Direct または ExpressRoute パートナーに、アップグレードを成功させるために[十分な使用可能な帯域幅](#considerations)がある必要があります。

容量が使用可能な場合は、次の方法を使用して回線をアップグレードできます。

* [Azure Portal](expressroute-howto-circuit-portal-resource-manager.md#modify)
* [PowerShell](expressroute-howto-circuit-arm.md#modify)
* [Azure CLI](howto-circuit-cli.md#modify)

## <a name="capacity-considerations"></a><a name="considerations"></a>容量に関する考慮事項

### <a name="insufficient-expressroute-partner-bandwidth"></a><a name="bandwidth"></a>不十分な ExpressRoute パートナーの帯域幅

ExpressRoute パートナーに十分な容量がない場合は、必要な帯域幅に構成された新しい回線を作成する必要があります。 接続性を維持するために、新しく作成された回線がプロビジョニングされ、ピアリングが構成され、(プライベート ピアリングに関しては) ExpressRoute 仮想ネットワーク ゲートウェイへの接続オブジェクトがプロビジョニングされるまでは、古い回線を削除しないでください。

ExpressRoute パートナーに使用可能な容量が十分にない場合は、希望するピアリングの場所の追加容量を要求する必要があります。 新しい容量がプロビジョニングされたら、記事の「[回線の帯域幅をアップグレードする](#upgrade)」セクションに記載されている手順を使用して、新しい回線の作成、接続の構成、および古い回線の削除を行うことができます。


### <a name="insufficient-expressroute-direct-bandwidth"></a><a name="bandwidth"></a>不十分な ExpressRoute Direct の帯域幅

ExpressRoute Direct に十分な容量がない場合は、不要になった ExpressRoute Direct リソースに関連付けられている回線を削除するか、新しい ExpressRoute Direct リソースを作成することができます。 ExpressRoute Direct リソースの管理に関するガイダンスについては、[ExpressRoute Direct を構成する方法](how-to-expressroute-direct-portal.md)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

* [回線の作成と変更](expressroute-howto-circuit-portal-resource-manager.md)
* [ピアリング構成の作成と変更](expressroute-howto-routing-portal-resource-manager.md)
* [ExpressRoute 回線への仮想ネットワークのリンク](expressroute-howto-linkvnet-portal-resource-manager.md)
