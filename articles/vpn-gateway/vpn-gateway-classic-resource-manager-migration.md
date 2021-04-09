---
title: VPN Gateway クラシックから Resource Manager への移行 | Microsoft Docs
description: このページでは、VPN Gateway クラシックから Resource Manager への移行の概要を示します。
documentationcenter: na
services: vpn-gateway
author: amsriva
manager: rossort
editor: amsriva
ms.assetid: caa8eb19-825a-4031-8b49-18fbf3ebc04e
ms.service: vpn-gateway
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/06/2020
ms.author: amsriva
ms.openlocfilehash: c9d7fb8be1894ffa5f8c35e16e1ed3aa0949b3ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96488206"
---
# <a name="vpn-gateway-classic-to-resource-manager-migration"></a>VPN Gateway クラシックから Resource Manager への移行
VPN Gateway は クラシック モデル から Resource Manager デプロイ モデルに移行できるようになりました。 詳細については、Azure Resource Manager の[機能と利点](../azure-resource-manager/management/overview.md)をご覧ください。 この記事では、従来のデプロイメントから新しい Resource Manager ベースモデルへの移行方法について説明します。 

VPN Gateway は、クラシックから Resource Manager への VNet の移行の一部として移行されます。 この移行では、一度に 1つの VNet が移行されます。 移行に必要な追加のツールや前提条件はありません。 移行手順は既存の VNet の移行と同じで、詳細は[IaaS リソースの移行](../virtual-machines/migration-classic-resource-manager-ps.md)に関するページに記載されています。 移行中にデータ パスのダウンタイムはないため、既存のワークロードは引き続き動作し、移行中にオンプレミスの接続が失われることはありません。 VPN Gateway に関連付けられているパブリック IP アドレスは、移行プロセス中も変更されません。 そのため、移行後にオンプレミスのルーターを再構成する必要はありません。  

Resource Manager モデルは従来のモデルと異なり、仮想ネットワーク ゲートウェイ、ローカル ネットワーク ゲートウェイ、接続リソースで構成されます。 これらは、それぞれ VPN Gateway、 オンプレミスのアドレス空間であるローカルサイト、 2 つの間の接続を表します。 移行が完了すると、ゲートウェイはクラシック モデルでは利用できなくなり、仮想ネットワーク ゲートウェイ、ローカル ネットワーク ゲートウェイ、接続オブジェクトのすべての管理操作は、Resource Manager モデルを使って行う必要があります。

## <a name="supported-scenarios"></a>サポートされるシナリオ
最も一般的な VPN 接続のシナリオは、クラシックからリソース マネージャーへの移行で対応されています。 サポートされるシナリオは次のとおりです。

* ポイントからサイトへの接続
* オンプレミスの場所に接続している VPN Gateway のサイト間接続
* VPN Gateway を使用している 2 つの VNet 間の接続
* 同じオンプレミスの場所に接続している複数の VNet
* マルチサイト接続
* 強制トンネリングが有効になっている VNet

サポートされていないシナリオ。  

* ExpressRoute ゲートウェイと VPN ゲートウェイの両方を備えた VNet は、現在サポートされていません。
* VM 拡張機能がオンプレミスのサーバーに接続している場合のトランジット シナリオ。 トランジット VPN 接続の制限は次のとおりです。

> [!NOTE]
> Resource Manager モデルの CIDR 検証は、クラシック モデルの検証よりも厳密になっています。 移行を開始する前に、クラシックのアドレス範囲が、有効な CIDR 形式に準拠していることを確認します。 CIDR は、一般的な CIDR バリデーターを使用して検証できます。 無効な CIDR 範囲の VNet やローカル サイトを移行すると、エラー状態になります。
> 
> 

## <a name="vnet-to-vnet-connectivity-migration"></a>VNet 間接続の移行
クラシックでの VNet 間の接続は、接続された VNet を表すローカル サイトを作成することで実現していました。 互いに接続する必要のある 2 つの VNet を表す 2 つのローカル サイトを作成する必要がありました。 これらのローカル サイトは、対応する VNet に IPsec トンネルを使用して接続され、2 つの VNet 間の接続が確立されていました。 このモデルでは、1 つの VNet 内のアドレス範囲が変更されると、対応するローカル サイトにも反映する必要があったため、管理が複雑になるという問題がありました。 Resource Manager モデルでは、この問題が解決されました。 2 つの VNet 間の接続は、接続リソースに "VNet2VNet" の接続の種類を使用することにより直接確立できます。 

![VNet間の移行のスクリーン ショット。](./media/vpn-gateway-migration/migration1.png)

VNet の移行中、現在の VNet の VPN Gateway に接続しているエンティティが、別の VNet であることが検出され、両方の VNet の移行の完了後、他の VNet を表す 2 つのローカル サイトは表示されなくなります。 2 つの VPN Gateway、2 つのローカル サイト、それらを結ぶ2 つの接続といったクラシック モデルは、2 つの VPN Gateway と 2 つの VNet2VNet 接続種類を使用するリソース マネージャー モデルに変換されます。

## <a name="transit-vpn-connectivity"></a>トランジット VPN 接続
オンプレミスに直接接続している別の VNet に接続することによって、VNet へのオンプレミス接続を確立する、というトポロジで、VPN Gateway を構成できます。 これはトランジット VPN 接続であり、1 つ目の VNet のインスタンスは、直接オンプレミスに接続されている Vnet 上にある VPN ゲートウェイへの転送によってオンプレミスリソースに接続されます。 クラシック デプロイ モデルでこの構成を行うには、接続済みの VNet とオンプレミスのアドレス空間の両方を表す集計プレフィックスのあるローカル サイトを作成する必要がありました。 その後、そのローカル サイトを VNet に接続してトランジット接続が確立されていました。 この従来モデルにも、オンプレミスのアドレス範囲を変更した場合には、VNet とオンプレミスの集計を表すローカル サイトにも反映しなければならないという管理上の問題がありました。 Resource Manager がサポートするゲートウェイで BGP がサポートされるようになったことにより、接続されているゲートウェイがオンプレミスからルートを学習することができ、手動でプレフィックスを修正する必要がなくなったため管理が簡素化されます。

![トランジット ルーティングのシナリオのスクリーンショット。](./media/vpn-gateway-migration/migration2.png)

ローカル サイト を必要とせずに、VNet 間接続を変換するため、トランジット シナリオで、間接的にオンプレミスに接続されている VNet のオンプレミス接続が失われることになります。 移行の完了後に、次の 2 つの方法で接続が失われることの影響を軽減することができます。 

* 互いに接続されていて、オンプレミスに接続している VPN Gateway で BGP を有効にします。 BGP を有効にすると、VNet ゲートウェイ間でお互いにルートを学習し、通知し合うため、他の構成変更なしに接続が復元されます。 BGP のオプションは、Standard 以上の SKU でのみ利用できます。
* 影響を受けるVNet からオンプレミスの場所を表すローカル ネットワーク ゲートウェイへの明示的な接続を確立します。 これには、オンプレミスのルーターの構成を変更して、IPsec トンネルを作成、構成する必要もあります。

## <a name="next-steps"></a>次のステップ
VPN Gateway の移行サポートについて学習した後は、[プラットフォームでサポートされる、クラシックから Azure Resource Manager への IaaS リソースの移行](../virtual-machines/migration-classic-resource-manager-ps.md)に関する記事をご覧ください。