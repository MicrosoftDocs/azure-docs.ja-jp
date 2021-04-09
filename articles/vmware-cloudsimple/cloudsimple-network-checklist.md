---
title: Azure VMware Solution by CloudSimple - ネットワーク チェックリスト
description: Azure VMware Solution by CloudSimple でネットワーク CIDR を割り当てるためのチェックリスト
author: sharaths-cs
ms.author: dikamath
ms.date: 09/25/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: bfb170036293dc9f519259dc92737f30380aa84a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "77025012"
---
# <a name="networking-prerequisites-for-azure-vmware-solution-by-cloudsimple"></a>Azure VMware Solution by CloudSimple のネットワーク前提条件

Azure VMware Solution by CloudSimple によって提供される VMware のプライベート クラウド環境には、オンプレミス環境、企業が管理するデバイス、Azure リソースのユーザーやアプリケーションがアクセスできます。 接続は、VPN や Azure ExpressRoute 接続などのネットワーク サービスによって提供されます。 一部のネットワーク サービスでは、サービスを有効にするためにネットワーク アドレス範囲を指定する必要があります。 

この記事の表には、一連のアドレス範囲とそれに対応し、指定のアドレスを使用するサービスがまとめてあります。 アドレスは、必須の場合と、展開するサービスに依存する場合があります。 アドレス空間は、オンプレミス サブネット、Azure Virtual Network サブネット、予定されている CloudSimple ワークロード サブネットのいずれとも、重複しないようにする必要があります。

## <a name="network-address-ranges-required-for-creating-a-private-cloud"></a>プライベート クラウドを作成するために必要なネットワーク アドレス範囲

CloudSimple サービスとプライベート クラウドを作成するときは、次のように、指定されたネットワーク クラスレス ドメイン間ルーティング (CIDR) の範囲に従う必要があります。

| 名前/使用目的     | 説明                                                                                                                            | アドレス範囲            |
|-------------------|----------------------------------------------------------------------------------------------------------------------------------------|--------------------------|
| ゲートウェイ CIDR      | エッジ サービスに必須 (VPN ゲートウェイ)。  この CIDR は CloudSimple サービスの作成時に必要であり、RFC 1918 空間のものにする必要があります。 | /28                      |
| vSphere/vSAN CIDR | VMware 管理ネットワークに必須。 この CIDR は、プライベート クラウドの作成時に指定する必要があります。                                    | /24 または /23 または /22 または /21 |

## <a name="network-address-range-required-for-azure-network-connection-to-an-on-premises-network"></a>オンプレミス ネットワークへの Azure ネットワーク接続に必要なネットワーク アドレス範囲

[ExpressRoute 経由でプライベート クラウド ネットワークにオンプレミス ネットワーク](on-premises-connection.md)から接続すると、Global Reach 接続が確立されます。  その接続では、オンプレミス ネットワーク、プライベート クラウド ネットワーク、Azure ネットワークの間でルートを交換するために、Border Gateway Protocol (BGP) が使用されます。

| 名前/使用目的             | 説明                                                                                                                                                                             | アドレス範囲 |
|---------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|
| ExpressRoute ピアリング CIDR | オンプレミス接続に ExpressRoute Global Reach を使用するときに必要。 サポート チケットを通して Global Reach 接続要求が行われるときは、この CIDR を提供する必要があります。 | /29           |

## <a name="network-address-range-required-for-using-a-site-to-site-vpn-connection-to-an-on-premises-network"></a>オンプレミス ネットワークへのサイト間 VPN 接続を使用するために必要なネットワーク アドレス範囲

[サイト間 VPN を使用してプライベート クラウド ネットワークにオンプレミス ネットワーク](vpn-gateway.md)から接続するには、次の IP アドレス、オンプレミス ネットワーク、ID が必要です。 

| アドレス/アドレス範囲 | 説明                                                                                                                                                                                                                                                           |
|-----------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ピア IP               | オンプレミス VPN ゲートウェイ パブリック IP アドレス。 オンプレミス データセンターと CloudSimple サービス リージョンの間でサイト間 VPN を確立するために必要。 この IP アドレスは、サイト間 VPN ゲートウェイを作成するときに必要です。                                         |
| ピア識別子       | オンプレミス VPN ゲートウェイのピア識別子。 これは通常、**ピア IP** と同じです。  オンプレミス VPN ゲートウェイに一意の識別子が指定されている場合、その識別子を指定する必要があります。  ピア ID は、サイト間 VPN ゲートウェイを作成するときに必要です。   |
| オンプレミス ネットワーク   | リージョンで CloudSimple へのアクセスを必要とするオンプレミス プレフィックス。  ユーザーがネットワークにアクセスするクライアント ネットワークなど、CloudSimple ネットワークにアクセスするオンプレミス ネットワークからのすべてのプレフィックスが含まれます。                                         |

## <a name="network-address-range-required-for-using-point-to-site-vpn-connections"></a>ポイント対サイト VPN 接続の使用に必要なネットワーク アドレス範囲

ポイント対サイト VPN 接続では、クライアント コンピューターから CloudSimple ネットワークにアクセスできます。  [ポイント対サイト VPN を設定するには](vpn-gateway.md)、次のネットワーク アドレス範囲を指定する必要があります。

| アドレス/アドレス範囲 | 説明                                                                                                                                                                                                                                                                                                  |
|-----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| クライアント サブネット         | DHCP アドレスは、ポイント対サイト VPN を使用して接続するときに、クライアント サブネットによって提供されます。 このサブネットは、CloudSimple ポータルでポイント対サイト VPN ゲートウェイを作成するときに必要です。  ネットワークは 2 つのサブネットに分割されます。1 つは UDP 接続用で、もう 1 つは TCP 接続用です。 |

## <a name="next-steps"></a>次のステップ

* [プライベート クラウドにアクセスするためのオンプレミス ファイアウォールのセットアップ](on-premises-firewall-configuration.md)
* [クイックスタート - CloudSimple サービスを作成する](quickstart-create-cloudsimple-service.md)
* [クイックスタート - プライベート クラウドを構成する](quickstart-create-private-cloud.md)
* Azure ネットワーク接続の詳細については[こちら](cloudsimple-azure-network-connection.md)をご覧ください
* VPN ゲートウェイの詳細については[こちら](cloudsimple-vpn-gateways.md)をご覧ください
