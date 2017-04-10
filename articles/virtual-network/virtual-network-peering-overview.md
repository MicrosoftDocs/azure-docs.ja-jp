---
title: "Azure の仮想ネットワーク ピアリング | Microsoft Docs"
description: "Azure の仮想ネットワーク ピアリングについて説明します。"
services: virtual-network
documentationcenter: na
author: NarayanAnnamalai
manager: jefco
editor: tysonn
ms.assetid: eb0ba07d-5fee-4db0-b1cb-a569b7060d2a
ms.service: virtual-network
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/17/2016
ms.author: narayan
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 6fbcdcf77f46a3c643e8fedc1d112588cbd7befc
ms.lasthandoff: 04/03/2017


---
# <a name="virtual-network-peering"></a>仮想ネットワーク ピアリング
仮想ネットワーク (VNet) ピアリングにより、同じリージョン内の 2 つの VNet を Azure のバックボーン ネットワークを介して接続できます。 ピアリングされた 2 つの VNet は、接続において見かけ上 1 つのネットワークとして機能します。 2 つの VNet はあくまで個別のリソースとして管理されますが、ピアリングされた VNet 内の仮想マシン (VM) は、プライベート IP アドレスを使用して互いに直接通信を行うことができます。

ピアリングされた VNet に存在する VM 間のトラフィックは、同一 VNet 内の VM 間でルーティングされるトラフィックと同じように、Azure インフラストラクチャを介してルーティングされます。 以下、VNET ピアリングを使う利点をいくつか挙げます。

* 異なる VNet 内のリソース間で、待ち時間が短く、広帯域幅の接続が可能である。
* ネットワーク アプライアンスや VPN ゲートウェイなどのリソースを、ピアリングされた VNET のトランジット ポイントとして使用できる。
* Azure Resource Manager デプロイメント モデルを使って作成された 2 つの VNet をピアリングできることに加え、Resource Manager を使って作成された VNet とクラシック デプロイメント モデルを使って作成された VNet をピアリングできる。 Azure の 2 つのデプロイメント モデルの違いの詳細については、[Azure のデプロイメント モデルの概要](../azure-resource-manager/resource-manager-deployment-model.md)に関する記事を参照してください。

VNET ピアリングの要件と主な特徴は次のとおりです。

* ピアリングする VNet は同じ Azure リージョンに存在する必要があります。
* ピアリングする VNet の IP アドレス空間は、重複していてはなりません。
* VNet ピアリングは 2 つの VNet 間の関係です。ピアリングを越えて派生する推移的な関係は存在しません。 たとえば、VNetA が VNetB とピアリングされ、VNetB が VNetC とピアリングされているからといって、VNetA が VNetC とピアリングされていることには "*なりません*"。
* 2 つの異なるサブスクリプションに存在する VNet をピアリングできます。そのためには、両方のサブスクリプションの特権ユーザーがピアリングを承認しており、サブスクリプションが同じ Active Directory テナントに関連付けられていることが必要となります。
* ピアリングは、Resource Manager デプロイメント モデルを使って作成された VNet どうしか、Resource Manager デプロイメント モデルを使って作成された VNet とクラシック デプロイメント モデルを使って作成された VNet の間で行うことができます。 クラシック デプロイメント モデルを使って作成された 2 つの VNet を互いにピアリングすることはできません。 異なるデプロイメント モデルを使って作成された VNet をピアリングする場合、VNet が "*同じ*" サブスクリプションに存在している必要があります。 **プレビュー** リリースでは、"*異なる*" サブスクリプションに存在する、異なるデプロイメント モデルを使って作成された VNet をピアリングすることができます。 詳細については、[PowerShell を使用した仮想ネットワーク ピアリングの作成](virtual-networks-create-vnetpeering-arm-ps.md)に関するページを参照してください。
* ピアリングされた VNet に存在する VM マシン間の通信には帯域幅上の特別な制限はありませんが、VM サイズに基づく帯域幅の上限は依然として適用されます。 さまざまな VM サイズの最大ネットワーク帯域幅の詳細については、[Windows](../virtual-machines/windows/sizes.md) または [Linux](../virtual-machines/linux/sizes.md) の VM サイズに関する記事を参照してください。

![Basic VNET peering](./media/virtual-networks-peering-overview/figure01.png)

## <a name="connectivity"></a>接続
2 つの VNet をピアリングすると、VNet 内の VM または Cloud Services ロールは、ピアリングされている VNet に接続されている他のリソースに直接接続できるようになります。 その 2 つの VNet 間には、IP レベルの完全な接続が確立されます。

ピアリングされた VNet に存在する 2 つの VM 間におけるネットワークのラウンド トリップの待ち時間は、単一の VNet 内のラウンド トリップと変わりません。 ネットワーク スループットは、VM に許可された帯域幅を基準としています。許可されている帯域幅は、VM のサイズに比例するものです。 ピアリング内の帯域幅に関してそれ以外の制限は一切ありません。

ピアリングされた VNet に存在する VM 間のトラフィックは、ゲートウェイを介さず、Azure のバックエンド インフラストラクチャを介して直接ルーティングされます。

VNet に接続されている VM は、ピアリングされた VNet 内の内部負荷分散 (ILB) エンドポイントにアクセスすることができます。 どちらかの VNet にネットワーク セキュリティ グループ (NSG) を適用すると、もう一方の VNet やサブネットへのアクセスを適宜ブロックすることができます。

ピアリングを構成するときに、VNet 間の NSG の規則を有効にするか、無効にするかを選択できます。 ピアリングされた VNet 間で完全な接続を開いた場合 (既定の選択肢)、特定のサブネットまたは VM に NSG を適用して、特定のアクセスをブロック (拒否) することができます。 NSG の詳細については、[ネットワーク セキュリティ グループ](virtual-networks-nsg.md)に関する記事を参照してください。

Azure には、VM に対する内部的な DNS 名前解決の機構が備わっていますが、ピアリングされた VNet 越しには機能しません。 VM の内部 DNS 名を解決できるのは、ローカルの VNet 内に限られます。 ただし、ピアリングされた VNet に接続された VM を VNet の DNS サーバーとして構成することはできます。 詳細については、「[独自 DNS サーバー使用の名前解決](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server)」を参照してください。

## <a name="service-chaining"></a>サービス チェイニング
後の図に示すように、ピアリングされた VNet 内の VM を指し示すユーザー定義ルート (UDR) を "次ホップ" の IP アドレスとして構成することができます。 これにより、一方の VNet からのトラフィックを、UDR を介して、ピアリングされた VNet で実行されている仮想アプライアンスに転送するサービス チェイニングが可能になります。

また、ハブ アンド スポーク型の環境を効果的に構築することもできます。この場合、ハブでインフラストラクチャ コンポーネント (ネットワーク仮想アプライアンスなど) をホストできます。 その後、すべてのスポーク VNet は、それとピアリングしたり、ハブ VNet で実行されているアプライアンスへのトラフィックのサブセットとピアリングしたりすることができます。 つまり、VNet ピアリングにより、UDR 上の次ホップ IP アドレスを、ピアリングされた VNet 内の VM の IP アドレスにすることができます。 UDR の詳細については、[ユーザー定義ルート](virtual-networks-udr-overview.md)に関する記事を参照してください。

## <a name="gateways-and-on-premises-connectivity"></a>ゲートウェイとオンプレミスの接続
各 VNet には、別の VNet とピアリングされているかどうかに関係なく独自のゲートウェイを設けることができ、そのゲートウェイを使用してオンプレミス ネットワークに接続できます。 ゲートウェイを使用して [VNet 対 VNet 接続](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)を構成するときに、それらの VNet がピアリングされていてもかまいません。

VNet 間の接続に両方の方法が構成されているときは、ピアリング構成を介して (つまり Azure バックボーンを介して) VNet 間のトラフィックが流れます。

VNet がピアリングされているとき、そのピアリングされた VNet のゲートウェイを、オンプレミス ネットワークへのトランジット ポイントとして構成することもできます。 この場合、リモート ゲートウェイを使用する VNet に独自のゲートウェイを設けることはできません。 1 つの VNet に設定できるゲートウェイは 1 つだけです。 ゲートウェイは、以下の図に示すように、ローカル ゲートウェイと (ピアリングされた VNet の) リモート ゲートウェイのどちらかになります。

![VNET peering transit](./media/virtual-networks-peering-overview/figure02.png)

ゲートウェイ トランジットは、異なるデプロイメント モデルを使って作成された VNet 間のピアリング関係ではサポートされません。 ゲートウェイ トランジットが機能するためには、ピアリング関係にある両方の VNet が Resource Manager を介して作成されている必要があります。

1 本の Azure ExpressRoute 接続を共有する VNet どうしをピアリングした場合、両者間のトラフィックは、そのピアリング関係を通過することになります (つまり Azure バックボーン ネットワークが使用されます)。 この場合でも、各 VNet のローカル ゲートウェイを使用してオンプレミス回線に接続できます。 また、共有ゲートウェイを使用して、オンプレミス接続用のトランジットを構成することもできます。

## <a name="provisioning"></a>プロビジョニング
VNET ピアリングは、特権の下で許可される操作であり、 VirtualNetworks 名前空間に独立した機能として存在します。 ユーザーに特定の権限を付与することによってピアリングを承認することができます。 仮想ネットワークに対する読み取り/書き込みアクセス権を持つユーザーには、この権限が自動的に継承されます。

VNET に対するピアリング操作は、管理者権限を持つユーザーまたはピアリング機能の特権を与えられたユーザーが開始できます。 対応するピアリング要求がもう一方の側に存在し、その他各種の要件が満たされた場合にピアリングは確立されます。

2 つの VNet 間に VNet ピアリングを確立する方法の詳細については、この記事の「[次のステップ](#next-steps)」セクションで紹介されている記事を参照してください。

## <a name="limits"></a>制限
1 つの仮想ネットワークで許容されるピアリングの数には制限があります。 詳しくは、 [Azure ネットワークの制限](../azure-subscription-service-limits.md#networking-limits) に関するセクションをご覧ください。

## <a name="pricing"></a>価格
VNet ピアリングを利用するイグレス トラフィックとエグレス トラフィックに少額の料金が発生します。 詳細については、 [価格のページ](https://azure.microsoft.com/pricing/details/virtual-network)を参照してください。

## <a name="next-steps"></a>次のステップ
次の項目を使用して VNet ピアリングを作成する方法を説明します。

* [Azure ポータル](virtual-networks-create-vnetpeering-arm-portal.md)
* [Azure PowerShell](virtual-networks-create-vnetpeering-arm-ps.md)
* [Azure Resource Manager テンプレート](virtual-networks-create-vnetpeering-arm-template-click.md)

