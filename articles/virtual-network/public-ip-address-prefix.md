---
title: Azure パブリック IP アドレス プレフィックス | Microsoft Docs
description: Azure パブリック IP アドレス プレフィックスとはどのようなもので、予測可能なパブリック IP アドレスをリソースに割り当てるのにどのように役立つのかについて説明します。
services: virtual-network
documentationcenter: na
author: anavinahar
manager: narayan
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: anavin
ms.openlocfilehash: fe163fc2370e884bed58bfffe5baffa5642b6d90
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/14/2019
ms.locfileid: "67147994"
---
# <a name="public-ip-address-prefix"></a>パブリック IP アドレス プレフィックス

パブリック IP アドレス プレフィックスとは、Azure でパブリック エンドポイントに対して予約されている IP アドレスの範囲です。 Azure では、ユーザーが指定した数に基づいて、連続した範囲のアドレスがサブスクリプションに割り当てられます。 パブリック アドレスについて詳しくは、「[パブリック IP アドレス](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses)」をご覧ください。

パブリック IP アドレスは、各 Azure リージョン内のアドレスのプールから割り当てられます。 Azure で各リージョンに使用される範囲の一覧を[ダウンロード](https://www.microsoft.com/download/details.aspx?id=56519)できます。 たとえば、40.121.0.0/16 は、米国東部リージョンで使用される 100 を超える範囲の 1 つです。 この範囲には、40.121.0.1 から 40.121.255.254 の使用可能なアドレスが含まれます。

Azure リージョンおよびサブスクリプションでパブリック IP アドレス プレフィックスを作成するには、名前と、プレフィックスに含まれるアドレスの数を指定します。 たとえば、/28 というパブリック IP アドレス プレフィックスを作成すると、Azure の範囲の 1 つからユーザーに 16 個のアドレスが割り当てられます。 Azure によって割り当てられる範囲は、範囲を作成するまでわかりませんが、アドレスは連続しています。 パブリック IP アドレス プレフィックスは有料です。 詳しくは、「[パブリック IP アドレスの料金](https://azure.microsoft.com/pricing/details/ip-addresses)」をご覧ください。

## <a name="why-create-a-public-ip-address-prefix"></a>パブリック IP アドレス プレフィックスを作成する理由

パブリック IP アドレス リソースを作成すると、リージョン内で使用されている範囲のいずれかから、使用可能なパブリック IP アドレスが割り当てられます。 Azure でアドレスが割り当てられた後ならアドレスが何かわかりますが、割り当てられるまでは、どのようなアドレスが割り当てられるかわかりません。 これは、たとえば、ユーザーやユーザーのビジネス パートナーが特定の IP アドレスを許可するファイアウォール規則を設定するときに問題になります。 新しいパブリック IP アドレスをリソースに割り当てるたびに、アドレスをファイアウォール規則に追加する必要があります。 パブリック IP アドレス プレフィックスからリソースにアドレスを割り当てる場合は、範囲全体を規則に追加できるため、アドレスの 1 つを割り当てるたびにファイアウォール規則を更新する必要はありません。

## <a name="benefits"></a>メリット

- 既知の範囲からパブリック IP アドレス リソースを作成できます。
- ユーザーまたはユーザーのビジネス パートナーは、現在割り当ててあるパブリック IP アドレスだけでなく、まだ割り当てていないアドレスも含まれる範囲で、ファイアウォール規則を作成できます。 これにより、新しいリソースに IP アドレスを割り当てるときにファイアウォール規則を変更する必要がなくなります。
- 作成できる範囲の既定のサイズは /28 つまり 16 個の IP アドレスです。
- 作成できる範囲の数に制限はありませんが、Azure サブスクリプションで使用できる静的パブリック IP アドレスの数には上限があります。 その結果、サブスクリプションで使用できる数より多くの静的パブリック IP アドレスが含まれるような数の範囲を作成することはできません。 詳しくは、[Azure での制限](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)に関するページをご覧ください。
- プレフィックスのアドレスを使用して作成したアドレスは、パブリック IP アドレスを割り当てることができる任意の Azure リソースに割り当てることができます。
- 範囲内で割り当てられている IP アドレスおよびまだ割り当てられていない IP アドレスを、簡単に確認することができます。

## <a name="scenarios"></a>シナリオ
プレフィックスの静的パブリック IP アドレスには、次のリソースを関連付けることができます。

|Resource|シナリオ|手順|
|---|---|---|
|Virtual Machines| プレフィックスのパブリック IP アドレスを、Azure の仮想マシンに関連付けると、ファイアウォールで IP アドレスをホワイトリストに登録するときの管理オーバーヘッドが軽減されます。 単一のファイアウォール規則でプレフィックス全体をホワイトリストに簡単に登録できます。 Azure で仮想マシンをスケーリングするときは、同じプレフィックスから IP アドレスを関連付けることで、コスト、時間、管理オーバーヘッドを節約できます。| プレフィックスから仮想マシンに IP アドレスを関連付けるには:1. [プレフィックスを作成します。](manage-public-ip-address-prefix.md) 2. [プレフィックスから IP アドレスを作成します。](manage-public-ip-address-prefix.md) 手順 3. [仮想マシンのネットワーク インターフェイスに IP アドレスを関連付けます。](virtual-network-network-interface-addresses.md#add-ip-addresses)
| ロード バランサー | プレフィックスのパブリック IP アドレスを、ロード バランサーのフロントエンド IP 構成またはアウトバウンド規則に関連付けると、Azure のパブリック IP アドレス空間が単純化されます。 パブリック IP アドレス プレフィックスによって定義されている連続した IP アドレスの範囲から発信されるように送信接続をグルーミングすることで、シナリオを単純化できます。 | プレフィックスからロード バランサーに IP アドレスを関連付けるには:1. [プレフィックスを作成します。](manage-public-ip-address-prefix.md) 2. [プレフィックスから IP アドレスを作成します。](manage-public-ip-address-prefix.md) 手順 3. ロード バランサーを作成するときに、ロード バランサーのフロントエンド IP アドレスとして、上の手順 2 で作成した IP アドレスを選択または更新します。 |
| Azure Firewall | 送信 SNAT に対してプレフィックスのパブリック IP アドレスを使用できます。 これは、すべての送信仮想ネットワーク トラフィックが [Azure Firewall](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) のパブリック IP アドレスに変換されることを意味します。 この IP アドレスは事前に決定されたプレフィックスのものなので、Azure でのパブリック IP アドレスのフットプリントがどのようになるかが、事前に非常に簡単にわかります。 | 1.[プレフィックスを作成します。](manage-public-ip-address-prefix.md) 2. [プレフィックスから IP アドレスを作成します。](manage-public-ip-address-prefix.md) 手順 3. [Azure Firewall をデプロイする](../firewall/tutorial-firewall-deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-the-firewall)ときに、前にプレフィックスから割り当てた IP アドレスを選択します。|

## <a name="constraints"></a>制約

- プレフィックスに対して IP アドレスを指定することはできません。 プレフィックスの IP アドレスは、ユーザーが指定したサイズに基づいて、Azure によって割り当てられます。
- 最大 16 個の IP アドレスまたは /28 のプレフィックスを作成できます。 詳しくは、[Azure での制限](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)に関するページをご覧ください。
- プレフィックスを作成した後で、範囲を変更することはできません。
- 範囲は、IPv4 アドレスに対するもののみです。 範囲には IPv6 アドレスは含まれません。
- プレフィックスの範囲から割り当てることができるのは、Standard SKU で作成された静的パブリック IP アドレスのみです。 パブリック IP アドレスの SKU の詳細については、「[パブリック IP アドレス](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses)」をご覧ください。
- 範囲のアドレスを割り当てることができるのは、Azure Resource Manager リソースのみです。 クラシック デプロイ モデルのリソースにアドレスを割り当てることはできません。
- プレフィックスから作成されるすべてのパブリック IP アドレスは、プレフィックスと同じ Azure リージョンおよびサブスクリプションに存在する必要があり、同じリージョンおよびサブスクリプション内のリソースに割り当てられる必要があります。
- 範囲内のいずれかのアドレスが、リソースに関連付けられているパブリック IP アドレス リソースに割り当てられている場合は、プレフィックスを削除できません。 最初に、プレフィックスの IP アドレスを割り当てられているすべてのパブリック IP アドレス リソースの関連付けを解除する必要があります。


## <a name="next-steps"></a>次の手順

- パブリック IP アドレス プレフィックスを[作成](manage-public-ip-address-prefix.md)する
