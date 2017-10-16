---
title: "Azure における IP アドレスの種類 | Microsoft Docs"
description: "Azure でのパブリックおよびプライベート IP アドレスについて説明します。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 610b911c-f358-4cfe-ad82-8b61b87c3b7e
ms.service: virtual-network
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/18/2017
ms.author: jdial
ms.openlocfilehash: 8ddd582ed159e10add896252c40feb19780c42fb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="ip-address-types-and-allocation-methods-in-azure"></a>Azure における IP アドレスの種類と割り当て方法

Azure リソースには、他の Azure リソース、オンプレミス ネットワーク、およびインターネットと通信するために IP アドレスを割り当てることができます。 Azure で使用できる IP アドレスには、次の 2 種類があります。

* **パブリック IP アドレス**: Azure の公開されたサービスを含め、インターネットとの通信に使用します。
* **プライベート IP アドレス**: VPN ゲートウェイまたは ExpressRoute 回線を使用してネットワークを Azure に拡張するときに、Azure 仮想ネットワーク (VNet)、およびオンプレミスのネットワーク内での通信に使用します。

> [!NOTE]
> Azure には、リソースの作成と操作に関して、[Resource Manager とクラシックの](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 2 種類のデプロイメント モデルがあります。  この記事では、Resource Manager デプロイメント モデルの使用方法について取り上げていますが、最新のデプロイでは、[クラシック デプロイメント モデル](virtual-network-ip-addresses-overview-classic.md)ではなくこのモデルをお勧めします。
> 

クラシック デプロイメント モデルの知識がある場合は、[クラシック デプロイメントと Resource Manager での IP アドレス指定の相違点](virtual-network-ip-addresses-overview-classic.md#differences-between-resource-manager-and-classic-deployments)に関するページを確認してください。

## <a name="public-ip-addresses"></a>パブリック IP アドレス

パブリック IP アドレスを使用すると、Azure リソースはインターネットのほか、[Azure Redis Cache](https://azure.microsoft.com/services/cache)、[Azure Event Hubs](https://azure.microsoft.com/services/event-hubs)、[SQL Database](../sql-database/sql-database-technical-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)、[Azure Storage](../storage/common/storage-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) など、Azure の公開されたサービスと通信できます。

Azure リソース マネージャーで、 [パブリック IP](virtual-network-public-ip-address.md) アドレスは、独自のプロパティを持つリソースです。 パブリック IP アドレスのリソースは、次のリソースのいずれかと関連付けることができます。

* 仮想マシン ネットワーク インターフェイス
* インターネットに接続するロード バランサー
* VPN ゲートウェイ
* アプリケーション ゲートウェイ

### <a name="ip-address-version"></a>IP アドレスのバージョン

パブリック IP アドレスは、IPv4 または IPv6 アドレスを使用して作成されます。 パブリックの IPv6 アドレスは、インターネットに接続するロード バランサーにのみ割り当てることができます。

### <a name="sku"></a>SKU

パブリック IP アドレスは、次の SKU のいずれかを使用して作成されます。

#### <a name="basic"></a>基本

SKU の導入前に作成されたすべてのパブリック IP アドレスは、Basic SKU のパブリック IP アドレスです。 SKU を導入すると、どの SKU をパブリック IP アドレスにするかを設定できます。 Basic SKU のアドレスは次のとおりです。

- 静的または動的な割り当て方法を使用して割り当てられます。
- ネットワーク インターフェイス、VPN ゲートウェイ、アプリケーション ゲートウェイ、およびインターネットに接続するロード バランサーなどのパブリック IP アドレスを割り当てることができる Azure のリソースに割り当てられます。
- 特定のゾーンに割り当てることができます。
- ゾーン冗長はありません。 可用性ゾーンの詳細については、「[可用性ゾーンの概要](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)」を参照してください。

#### <a name="standard"></a>標準

Standard SKU のパブリック IP アドレスは次のとおりです。

- 静的割り当て方法のみを使用して割り当てられます。
- ネットワーク インターフェイス、または標準のインターネットに接続するロード バランサーに割り当てられます。 Azure ロード バランサー SKU の詳細については、[Azure ロード バランサー Standard SKU](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)を参照してください。
- ゾーンは既定で冗長です。 ゾーンで作成し、特定の可用性ゾーンで保証できます。  可用性ゾーンの詳細については、[可用性ゾーンの概要](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)を参照してください。
 
> [!NOTE]
> 標準 SKU のパブリック IP アドレスを仮想マシンのネットワーク インターフェイスに割り当てるときは、[ネットワーク セキュリティ グループ](security-overview.md#network-security-groups)で、特定のトラフィックを明示的に許可する必要があります。  ネットワーク セキュリティ グループを作成して関連付け、特定のトラフィックを明示的に許可するまでは、リソースと通信できません。

Standard SKU はプレビュー リリースです。 Standard SKU のパブリック IP アドレスを作成する前に、プレビューを登録し、サポートされている場所にアドレスを作成する必要があります。 プレビューを登録するには、[Standard SKU プレビューを登録する](virtual-network-public-ip-address.md#register-for-the-standard-sku-preview)を参照してください。 サポートされている場所 (リージョン) の一覧については、[[利用可能なリージョン]](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#region-availability) を参照し、追加のリージョンのサポートについて [[Azure Virtual Network updates] (Azure Virtual Network の更新)](https://azure.microsoft.com/updates/?product=virtual-network) ページを監視してください。


### <a name="allocation-method"></a>割り当て方法

IP アドレスをパブリック IP リソースに割り当てる方法には、*動的*と*静的*の 2 種類があります。 既定の割り当て方法は *動的*で、IP アドレスの作成時に割り当ては **行われません** 。 代わりに、関連付けられたリソース (VM やロード バランサーなど) を開始 (または作成) するときに、パブリック IP アドレスが割り当てられます。 IP アドレスは、リソースを停止 (または削除) すると解放されます。 たとえば、リソース A から解放された IP アドレスは別のリソースに関連付けられます。 リソース A が停止している間に別のリソースに IP アドレスが割り当てられた場合、リソース A を再起動したときに、別の IP アドレスが割り当てられます。

関連付けられたリソースの IP アドレスが変わらないようにするため、割り当て方法を明示的に *静的*に設定できます。 この場合、静的 IP アドレスが即座に割り当てられます。 アドレスは、リソースを削除するか、その割り当て方法を *動的*に変更した場合にのみ解放されます。

> [!NOTE]
> 割り当て方法を*静的*に設定しても、パブリック IP アドレス リソースに割り当てられる実際の IP アドレスは指定できません。 リソースが作成される Azureの場所で使用可能な IP アドレスのプールから IP アドレスが割り当てられます。
>

次のようなシナリオでは、静的なパブリック IP アドレスが一般的に使用されます。

* Azure リソースと通信するためにファイアウォール規則を更新する必要がある。
* DNS 名の解決で、IP アドレスの変更によりレコードを更新する必要がある。
* Azure のリソースが、IP アドレス ベースのセキュリティ モデルを使用する他のアプリまたはサービスと通信する。
* IP アドレスにリンクされている SSL 証明書を使用する。

> [!NOTE]
> Azure では、各 Azure リージョンに一意の範囲からパブリック IP アドレスが割り当てられます。 詳細については、[Azure データ センターの IP 範囲](https://www.microsoft.com/download/details.aspx?id=41653)を参照してください。
>

### <a name="dns-hostname-resolution"></a>DNS ホスト名の解決
パブリック IP リソースに DNS ドメイン名ラベルを指定して、Azure で管理される DNS サーバーのパブリック IP アドレスに対する *domainnamelabel*.*location*.cloudapp.azure.com のマッピングを作成できます。 たとえば、Azure の *location* が **West US** で、*domainnamelabel* が **contoso** のパブリック IP リソースを作成した場合、完全修飾ドメイン名 (FQDN) **contoso.westus.cloudapp.azure.com** がリソースのパブリック IP アドレスに解決されます。 この FQDN を使用して、Azure 内のパブリック IP アドレスをポイントするカスタム ドメイン CNAME レコードを作成できます。

> [!IMPORTANT]
> 作成された各ドメイン名ラベルは、Azure の location 内で一意である必要があります。  
>

### <a name="virtual-machines"></a>仮想マシン

パブリック IP アドレスは、その**ネットワーク インターフェイス**に割り当てることで、[Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) または [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 仮想マシンに関連付けることができます。 仮想マシンには、動的または静的のどちらかのパブリック IP アドレスを割り当てることができます。 [IP アドレスをネットワーク インターフェイスに割り当てる方法](virtual-network-network-interface-addresses.md)を参照してください。

### <a name="internet-facing-load-balancers"></a>インターネットに接続するロード バランサー

パブリック IP アドレスをロード バランサーの **フロントエンド**構成に割り当てることで、パブリック IP アドレスと [SKU](#SKU) あるいは [Azure Load Balancer](../load-balancer/load-balancer-overview.md) を関連付けることができます。 このパブリック IP アドレスは、負荷分散された仮想 IP アドレス (VIP) として機能します。 ロード バランサーのフロント エンドには、動的または静的のどちらかのパブリック IP アドレスを割り当てることができます。 複数のパブリック IP アドレスをロード バランサーのフロント エンドに割り当てて、SSL ベースの Web サイトを含むマルチテナント環境のような [マルチ VIP](../load-balancer/load-balancer-multivip.md?toc=%2fazure%2fvirtual-network%2ftoc.json) シナリオを有効にすることもできます。 Azure Load Balancer SKU の詳細については、「[Azure Load Balancer の Standard SKU](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)」を参照してください。

### <a name="vpn-gateways"></a>VPN ゲートウェイ

[Azure VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json) は、Azure 仮想ネットワーク を他の Azure 仮想ネットワークまたはオンプレミスのネットワークに接続します。 パブリック IP アドレスは、リモート ネットワークとの通信を有効にするために VPN Gateway に割り当てられます。 VPN ゲートウェイに割り当てることができるのは、*動的*パブリック IP アドレスのみです。

### <a name="application-gateways"></a>アプリケーション ゲートウェイ

パブリック IP アドレスをゲートウェイの [フロント エンド](../application-gateway/application-gateway-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)構成に割り当てることで、Azure **Application Gateway** に関連付けることができます。 このパブリック IP アドレスは、負荷分散された VIP として機能します。 Application Gateway のフロント エンド構成に割り当てることができるのは、*動的*パブリック IP アドレスのみです。

### <a name="at-a-glance"></a>早見表
下の表は、パブリック IP アドレスを最上位リソースに関連付けることができる特定のプロパティと、使用できる割り当て方法 (動的または静的) を示しています。

| 最上位リソース | IP アドレスの関連付け | 動的 | 静的 |
| --- | --- | --- | --- |
| 仮想マシン |Linux |はい |あり |
| インターネットに接続するロード バランサー |フロント エンド構成 |あり |はい |
| VPN Gateway |ゲートウェイ IP の構成 |はい |いいえ |
| フロント エンド |フロントエンドの構成 |はい |いいえ |

## <a name="private-ip-addresses"></a>プライベート IP アドレス
プライベート IP アドレスを使用すると、Azure リソースは、インターネットが到達可能な IP アドレスを使用せずに、 [仮想ネットワーク](virtual-networks-overview.md) の他のリソース、あるいはオンプレミスのネットワーク (VPN ゲートウェイまたは ExpressRoute 回線経由) と通信することができます。

Azure Resource Manager デプロイメント モデルでは、プライベート IP アドレスは次の種類の Azure リソースに関連付けられます。

* 仮想マシン ネットワーク インターフェイス
* 内部ロード バランサー (ILB)
* アプリケーション ゲートウェイ

### <a name="ip-address-version"></a>IP アドレスのバージョン

プライベート IP アドレスは、IPv4 または IPv6 アドレスで作成されます。 プライベート IPv6 アドレスは、動的な割り当て方法を使用してのみ割り当てることができます。 仮想ネットワーク上のプライベート IPv6 アドレス間で通信することはできません。 インターネットに接続するロード バランサーを経由すると、インターネットからプライベート IPv6 アドレスへ受信通信できます。 詳細については、[IPv6 を使用してインターネットに接続するロード バランサーを作成する](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json)を参照してください。

### <a name="allocation-method"></a>割り当て方法

プライベート IP アドレスは、リソースが関連付けられているサブネットのアドレス範囲から割り当てられます。 サブネット自体のアドレス範囲は、仮想ネットワークのアドレス範囲に含まれます。

プライベート IP アドレスを割り当てる方法には、*動的*と*静的*の 2 種類があります。 既定の割り当て方法は*動的*です。IP アドレスは (DHCP を使用して) リソースのサブネットから自動的に割り当てられます。 リソースを停止して起動すると、この IP アドレスが変更される場合があります。

IP アドレスが変わらないようにするため、割り当て方法を *静的* に設定することができます。 *静的*に設定した場合、リソースのサブネットの一部として有効な IP アドレスも指定する必要があります。

静的プライベート IP アドレスは、通常は次のものに使用されます。

* ドメイン コントローラーまたは DNS サーバーとして機能する仮想マシン。
* IP アドレスを使用するファイアウォール規則を必要とするリソース。
* IP アドレスを使用して他のアプリ/リソースからアクセスされるリソース。

### <a name="virtual-machines"></a>仮想マシン

プライベート IP アドレスは、[Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) または [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 仮想マシンの**ネットワーク インターフェイス**に割り当てます。 仮想マシンに複数のネットワーク インターフェイスがある場合は、各ネットワーク インターフェイスに、プライベート IP アドレスが割り当てられます。 ネットワーク インターフェイスに対して動的または静的のどちらかの割り当て方法を指定できます。

#### <a name="internal-dns-hostname-resolution-for-virtual-machines"></a>内部 DNS ホスト名の解決 (仮想マシンの場合)

カスタムの DNS サーバーを明示的に構成しない限り、既定ではすべての Azure 仮想マシンが [Azure で管理される DNS サーバー](virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution) で構成されます。 これらの DNS サーバーは、同じ仮想ネットワーク内に存在する仮想マシンの内部名前解決を可能にします。

仮想マシンを作成すると、そのプライベート IP アドレスへのホスト名のマッピングが、Azure で管理される DNS サーバーに追加されます。 複数のネットワーク インターフェイスを備える仮想マシンの場合、ホスト名はプライマリ ネットワーク インターフェイスのプライベート IP アドレスにマップされます。

Azure で管理される DNS サーバーで構成されている仮想マシンは、同じ仮想ネットワーク内のすべての仮想マシンのホスト名をプライベート IP アドレスに解決することができます。

### <a name="internal-load-balancers-ilb--application-gateways"></a>内部ロード バランサー (ILB) と Application Gateway

[Azure 内部ロード バランサー](../load-balancer/load-balancer-internal-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (ILB) または [Azure Application Gateway](../application-gateway/application-gateway-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) の**フロント エンド**構成にプライベート IP アドレスを割り当てることができます。 このプライベート IP アドレスは内部エンドポイントとして機能し、同じ仮想ネットワーク内のリソースおよび仮想ネットワークに接続されたリモート ネットワークからのみアクセスできます。 フロント エンド構成には、動的または静的のどちらかのプライベート IP アドレスを割り当てることができます。

### <a name="at-a-glance"></a>早見表
下の表は、プライベート IP アドレスを最上位リソースに関連付けることができる特定のプロパティと、使用できる割り当て方法 (動的または静的) を示しています。

| 最上位リソース | IP アドレスの関連付け | 動的 | 静的 |
| --- | --- | --- | --- |
| 仮想マシン |Linux |はい |はい |
| Load Balancer |フロントエンドの構成 |はい |はい |
| フロント エンド |フロントエンドの構成 |はい |はい |

## <a name="limits"></a>制限
IP アドレス指定に対する制限は、Azure の[ネットワークの制限](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits)の完全なセットに示されています。 この制限は、リージョンとサブスクリプションごとに存在します。 ビジネス上のニーズに基づいて既定の制限を上限まで引き上げるには、 [サポートにお問い合わせください](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) 。

## <a name="pricing"></a>価格
パブリック IP アドレスには、わずかな費用がかかることがあります。 Azure での IP アドレスの料金の詳細については、「[IP アドレスの料金](https://azure.microsoft.com/pricing/details/ip-addresses)」ページをご覧ください。

## <a name="next-steps"></a>次のステップ
* [Azure ポータルを使用して静的パブリック IP を持つ VM をデプロイする](virtual-network-deploy-static-pip-arm-portal.md)
* [テンプレートを使用した静的パブリック IP を持つ VM のデプロイ](virtual-network-deploy-static-pip-arm-template.md)
* Azure ポータルを使用して、[静的プライベート IP アドレスを持つ VM をデプロイ](virtual-networks-static-private-ip-arm-pportal.md)します。
