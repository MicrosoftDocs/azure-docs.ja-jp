---
title: Azure パブリック IP アドレス プレフィックス
description: Azure パブリック IP アドレス プレフィックスとはどのようなもので、予測可能なパブリック IP アドレスをリソースに割り当てるのにどのように役立つのかについて説明します。
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/29/2020
ms.author: allensu
ms.openlocfilehash: 874cb283f0076905393aa529e4ff9e1bad3ae047
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/13/2021
ms.locfileid: "103419679"
---
# <a name="public-ip-address-prefix"></a>パブリック IP アドレス プレフィックス

パブリック IP アドレス プレフィックスとは、Azure での IP アドレスの予約範囲です。 ユーザーが指定した数に基づいて、Azure によって連続した範囲のアドレスがサブスクリプションに指定されます。 

パブリック アドレスについて詳しくは、「[パブリック IP アドレス](./public-ip-addresses.md#public-ip-addresses)」をご覧ください。

パブリック IP アドレスは、各 Azure リージョン内のアドレスのプールから割り当てられます。 Azure で各リージョンに使用される範囲の一覧を[ダウンロード](https://www.microsoft.com/download/details.aspx?id=56519)できます。 たとえば、40.121.0.0/16 は、米国東部リージョンで使用される 100 を超える範囲の 1 つです。 この範囲には、40.121.0.1 から 40.121.255.254 の使用可能なアドレスが含まれます。

Azure リージョンおよびサブスクリプションでパブリック IP アドレス プレフィックスを作成するには、名前と、プレフィックスに含まれるアドレスの数を指定します。 

パブリック IP アドレス範囲は、選択したプレフィックスを使用して割り当てられます。 /28 のプレフィックスを作成した場合、Azure によってその範囲の 1 つから 16 個の IP アドレスが指定されます。

Azure によって割り当てられる範囲は、範囲を作成するまでわかりませんが、アドレスは連続しています。 

パブリック IP アドレスのプレフィックスには料金が発生します。詳細については、[パブリック IP アドレスの料金](https://azure.microsoft.com/pricing/details/ip-addresses)に関するページを参照してください。

## <a name="why-create-a-public-ip-address-prefix"></a>パブリック IP アドレス プレフィックスを作成する理由

パブリック IP アドレス リソースを作成すると、そのリージョン内で使用されている範囲のいずれかから、使用可能なパブリック IP アドレスが割り当てられます。 

Azure によって IP アドレスが割り当てられるまで、正確な IP はわかりません。 このプロセスは、特定の IP アドレスを許可するファイアウォール規則を作成するときに問題になることがあります。 追加されたすべての IP アドレスに対して、対応するファイアウォール規則を追加する必要があります。

パブリック IP アドレスのプレフィックスからご自分のリソースにアドレスを割り当てる場合は、ファイアウォール規則の更新は必要ありません。 範囲全体が規則に追加されます。

## <a name="benefits"></a>メリット

- 既知の範囲からのパブリック IP アドレス リソースの作成。
- 現在割り当てているパブリック IP アドレスとまだ割り当てていないアドレスを含む範囲を持つファイアウォール規則の構成。 この構成により、新しいリソースに IP アドレスを割り当てるときにファイアウォール規則を変更する必要がなくなります。
- 作成できる範囲の既定のサイズは /28 つまり 16 個の IP アドレスです。
- 作成できる範囲の数に制限がない。 Azure サブスクリプションで使用できる静的パブリック IP アドレスの数には上限があります。 サブスクリプションで使用できる数より多くの静的パブリック IP アドレスが含まれるような数の範囲を作成することはできません。 詳しくは、[Azure での制限](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)に関するページをご覧ください。
- プレフィックスのアドレスを使用して作成したアドレスは、パブリック IP アドレスを割り当てることができる任意の Azure リソースに割り当てることができます。
- 範囲内で指定されている IP アドレスと指定されていない IP アドレスを、簡単に確認することができます。

## <a name="scenarios"></a>シナリオ
プレフィックスの静的パブリック IP アドレスには、次のリソースを関連付けることができます。

|リソース|シナリオ|手順|
|---|---|---|
|仮想マシン| プレフィックスのパブリック IP アドレスを、Azure の仮想マシンに関連付けると、ファイアウォールの許可リストに IP アドレスを追加するときの管理オーバーヘッドが軽減されます。 単一のファイアウォール規則でプレフィックス全体を登録できます。 Azure で仮想マシンをスケーリングするときは、同じプレフィックスから IP アドレスを関連付けることで、コスト、時間、管理オーバーヘッドを節約できます。| プレフィックスから仮想マシンに IP アドレスを関連付けるには: </br> 1.[プレフィックスを作成します。](manage-public-ip-address-prefix.md) </br> 2.[プレフィックスから IP アドレスを作成します。](manage-public-ip-address-prefix.md) </br> 3.[仮想マシンのネットワーク インターフェイスに IP アドレスを関連付けます。](virtual-network-network-interface-addresses.md#add-ip-addresses) </br> [仮想マシン スケール セットに IP を関連付ける](https://azure.microsoft.com/resources/templates/101-vmms-with-public-ip-prefix/)こともできます。
| 標準ロード バランサー | プレフィックスのパブリック IP アドレスを、ロード バランサーのフロントエンド IP 構成またはアウトバウンド規則に関連付けると、Azure のパブリック IP アドレス空間が単純化されます。 連続した IP アドレスの範囲からの送信接続をグルーミングすることで、シナリオを簡略化します。 | プレフィックスからロード バランサーに IP アドレスを関連付けるには: </br> 1.[プレフィックスを作成します。](manage-public-ip-address-prefix.md) </br> 2.[プレフィックスから IP アドレスを作成します。](manage-public-ip-address-prefix.md) </br> 3.ロード バランサーを作成するときに、ロード バランサーのフロントエンド IP アドレスとして、上の手順 2 で作成した IP アドレスを選択または更新します。 |
| Azure Firewall | 送信 SNAT に対してプレフィックスのパブリック IP アドレスを使用できます。 すべての送信仮想ネットワーク トラフィックが [Azure Firewall](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) のパブリック IP アドレスに変換されます。 | プレフィックスの IP をファイアウォールに関連付けるには: </br> 1.[プレフィックスを作成します。](manage-public-ip-address-prefix.md) </br> 2.[プレフィックスから IP アドレスを作成します。](manage-public-ip-address-prefix.md) </br> 3.[Azure Firewall をデプロイする](../firewall/tutorial-firewall-deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-the-firewall)ときに、前にプレフィックスから指定した IP アドレスを選択します。|
| VPN Gateway (AZ SKU) または Application Gateway v2 | ゾーン冗長 VPN または Application Gateway v2 に対してプレフィックスのパブリック IP を使用できます。 | プレフィックスの IP をゲートウェイに関連付けるには: </br> 1.[プレフィックスを作成します。](manage-public-ip-address-prefix.md) </br> 2.[プレフィックスから IP アドレスを作成します。](manage-public-ip-address-prefix.md) </br> 3.[VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/tutorial-create-gateway-portal) または [Application Gateway](../application-gateway/quick-create-portal.md#create-an-application-gateway) をデプロイするときに、前にプレフィックスから指定した IP アドレスを選択します。|

## <a name="constraints"></a>制約

- プレフィックスに対して IP アドレスを指定することはできません。 プレフィックスの IP アドレスは、ユーザーが指定したサイズに基づいて、Azure によって指定されます。
- 既定では、最大 16 個の IP アドレスまたは /28 のプレフィックスを作成できます。 詳細については、[ネットワークの上限の引き上げ要求](../azure-portal/supportability/networking-quota-requests.md)に関するページおよび [Azure の制限](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)に関するページを参照してください。
- プレフィックスを作成した後で、範囲を変更することはできません。
- プレフィックスの範囲から割り当てることができるのは、Standard SKU で作成された静的パブリック IP アドレスのみです。 パブリック IP アドレスの SKU の詳細については、「[パブリック IP アドレス](./public-ip-addresses.md#public-ip-addresses)」をご覧ください。
- 範囲のアドレスを割り当てることができるのは、Azure Resource Manager リソースのみです。 クラシック デプロイ モデルのリソースにアドレスを割り当てることはできません。
- プレフィックスから作成されるすべてのパブリック IP アドレスは、プレフィックスと同じ Azure リージョンおよびサブスクリプションに存在する必要があります。 アドレスは、同じリージョンおよびサブスクリプション内のリソースに割り当てる必要があります。
- 範囲内のいずれかのアドレスが、リソースに関連付けられているパブリック IP アドレス リソースに割り当てられている場合は、プレフィックスを削除できません。 最初に、プレフィックスの IP アドレスを割り当てられているすべてのパブリック IP アドレス リソースの関連付けを解除する必要があります。


## <a name="next-steps"></a>次のステップ

- パブリック IP アドレス プレフィックスを[作成](manage-public-ip-address-prefix.md)する