---
title: Azure のプライベート IP アドレス
titlesuffix: Azure Virtual Network
description: Azure でのプライベート IP アドレスについて説明します。
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/28/2020
ms.author: allensu
ms.openlocfilehash: 36db885cab734c037b0032c714de28b905595ef0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98223263"
---
# <a name="private-ip-addresses"></a>プライベート IP アドレス
プライベート IP を使用すると、Azure 内のリソース間の通信が可能になります。 

次のリソースがあります。
* 次のような Azure サービス。
    * 仮想マシン ネットワーク インターフェイス
    * 内部ロード バランサー (ILB)
    * アプリケーション ゲートウェイ
* [仮想ネットワーク](virtual-networks-overview.md)内。
* VPN ゲートウェイまたは ExpressRoute 回線を介したオンプレミス ネットワーク。

プライベート IP を使用すると、パブリック IP アドレスを使用せずに、これらのリソースと通信できます。

## <a name="allocation-method"></a>割り当て方法

Azure では、プライベート IP アドレスは、リソースの存在する仮想ネットワーク サブネットのアドレス範囲からリソースに割り当てられます。

各サブネット アドレス範囲の先頭から 4 つのアドレスは Azure によって予約されています。 それらのアドレスがリソースに割り当てられることはありません。 たとえばサブネットのアドレス範囲が 10.0.0.0/16 である場合、10.0.0.0 から 10.0.0.3 および 10.0.255.255 のアドレスは利用できません。 サブネットのアドレス範囲内の IP アドレスを割り当てることのできるリソースは一度に 1 つだけです。 

プライベート IP アドレスを割り当てる方法は 2 種類あります。

- **動的**:サブネットのアドレス範囲内で、次に利用可能な (まだ割り当ても予約もされていない) アドレスが Azure により割り当てられます。 たとえばアドレス 10.0.0.4 ～ 10.0.0.9 が既に他のリソースに割り当てられている場合、10.0.0.10 が新しいリソースに割り当てられます。 

    動的割り当てが既定の割り当て方法となります。 割り当てられると、ネットワーク インターフェイスが次の状態になった場合に動的 IP アドレスが解放されます。
    
    * Deleted
    * 同じ仮想ネットワーク内の別のサブネットに再割り当てされた。
    * 割り当て方法が "静的" に変更され、別の IP アドレスが指定された。 
    
    既定では、割り当て方法を "動的" から"静的" に変更すると、動的に割り当てられていた以前のアドレスが Azure によって静的アドレスとして割り当てられます。

- **静的**:サブネットのアドレス範囲内で、まだ割り当ても予約もされていない IP アドレスを自分で選んで割り当てます。 

    たとえばサブネットのアドレス範囲が 10.0.0.0/16 であり、10.0.0.4 ～ 10.0.0.9 のアドレスは別のリソースに割り当てられているとします。 10.0.0.10 ～ 10.0.255.254 の任意のアドレスを割り当てることができます。 静的アドレスが解放されるのは、ネットワーク インターフェイスが削除された場合だけです。 
    
    割り当て方法が変更されると、Azure によって静的 IP が動的 IP として割り当てられます。 再割り当ては、アドレスがサブネットで次の使用可能なアドレスではない場合でも行われます。 ネットワーク インターフェイスが別のサブネットに割り当てられると、アドレスが変わります。
    
    ネットワーク インターフェイスを別のサブネットに割り当てるには、割り当て方法を "静的" から "動的" に変更します。 ネットワーク インターフェイスを別のサブネットに割り当ててから、割り当て方法を "静的" に戻します。 新しいサブネットのアドレス範囲から IP アドレスを割り当てます。
    
## <a name="virtual-machines"></a>仮想マシン

1 つ以上のプライベート IP アドレスが 1 つ以上の **ネットワーク インターフェイス** に割り当てられます。 ネットワーク インターフェイスは、[Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) または [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 仮想マシンに割り当てられます。 それぞれのプライベート IP アドレスについて、動的または静的のどちらかの割り当て方法を指定できます。

### <a name="internal-dns-hostname-resolution-for-virtual-machines"></a>内部 DNS ホスト名の解決 (仮想マシンの場合)

Azure 仮想マシンは、既定で [Azure で管理される DNS サーバー](virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution)を使用して構成されます。 カスタム DNS サーバーを明示的に構成できます。 これらの DNS サーバーは、同じ仮想ネットワーク内に存在する仮想マシンの内部名前解決を可能にします。

仮想マシンのプライベート IP アドレスへのホスト名のマッピングが、Azure で管理される DNS サーバーに追加されます。 

VM で次のように設定されている場合に、ホスト名がメイン ネットワーク インターフェイスのプライマリ IP にマップされます。

* 複数のネットワーク インターフェイス
* 複数の IP アドレス
* 両方

Azure で管理される DNS を使用して構成された VM は、同じ仮想ネットワーク内のホスト名を解決します。 接続されている仮想ネットワーク内の VM のホスト名を解決するには、カスタム DNS サーバーを使用します。

## <a name="internal-load-balancers-ilb--application-gateways"></a>内部ロード バランサー (ILB) と Application Gateway

次の **フロント エンド** 構成には、プライベート IP アドレスを割り当てることができます。

* [Azure 内部ロード バランサー](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (ILB)
* [Azure Application Gateway](../application-gateway/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 

このプライベート IP アドレスは、内部エンドポイントとして機能します。 内部エンドポイントは、同じ仮想ネットワーク内のリソースおよびその仮想ネットワークに接続されたリモート ネットワークからのみアクセスできます。 動的または静的 IP を割り当てることができます。

## <a name="at-a-glance"></a>早見表
次の表は、プライベート IP をリソースに関連付けるために使用されるプロパティを示しています。 

使用可能な割り当て方法も示されます。

* 動的
* 静的

| 最上位リソース | IP アドレスの関連付け | 動的 | 静的 |
| --- | --- | --- | --- |
| 仮想マシン |ネットワーク インターフェイス |はい |はい |
| Load Balancer |フロント エンド構成 |はい |はい |
| Application gateway |フロント エンド構成 |はい |はい |

## <a name="limits"></a>制限
IP アドレス指定に対する制限は、Azure の[ネットワークの制限](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits)の完全なセットに示されています。 この制限は、リージョンとサブスクリプションごとに存在します。 ビジネス上のニーズに基づいて既定の制限を上限まで引き上げるには、[サポートにお問い合わせください](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

## <a name="next-steps"></a>次のステップ
[Azure でのパブリック IP アドレス](public-ip-addresses.md)について説明します
* Azure ポータルを使用して、[静的プライベート IP アドレスを持つ VM をデプロイ](virtual-networks-static-private-ip-arm-pportal.md)します。