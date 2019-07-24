---
title: Azure における IP アドレスの種類
titlesuffix: Azure Virtual Network
description: Azure でのパブリックおよびプライベート IP アドレスについて説明します。
services: virtual-network
documentationcenter: na
author: jimdial
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/05/2019
ms.author: jdial
ms.openlocfilehash: 929c8808721140d5275cba4bcf3fbaa567f961e0
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58652027"
---
# <a name="ip-address-types-and-allocation-methods-in-azure"></a>Azure における IP アドレスの種類と割り当て方法

Azure リソースには、他の Azure リソース、オンプレミス ネットワーク、およびインターネットと通信するために IP アドレスを割り当てることができます。 Azure で使用できる IP アドレスには、次の 2 種類があります。

* **パブリック IP アドレス**:Azure の公開されたサービスを含め、インターネットとの通信に使用します。
* **プライベート IP アドレス**:VPN ゲートウェイまたは ExpressRoute 回線を使用してネットワークを Azure に拡張するときに、Azure 仮想ネットワーク (VNet)、およびオンプレミスのネットワーク内での通信に使用します。

パブリック IP プレフィックスから静的パブリック IP アドレスの連続する範囲を作成することもできます。 [パブリック IP プレフィックスについて確認してください。](public-ip-address-prefix.md)

> [!NOTE]
> Azure には、リソースの作成と操作に関して、2 種類のデプロイ モデルがあります。[Resource Manager とクラシック](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json)です。  この記事では、Resource Manager デプロイ モデルの使用方法について取り上げていますが、最新のデプロイでは、[クラシック デプロイ モデル](virtual-network-ip-addresses-overview-classic.md)ではなくこのモデルをお勧めします。
> 

クラシック デプロイ モデルの知識がある場合は、[クラシックと Resource Manager での IP アドレス指定の相違点](virtual-network-ip-addresses-overview-classic.md#differences-between-resource-manager-and-classic-deployments)に関するページを確認してください。

## <a name="public-ip-addresses"></a>パブリック IP アドレス

インターネット リソースから Azure リソースに入ってくる通信には、パブリック IP アドレスが使用されます。 また、Azure リソースからインターネットに出て行く通信や、Azure リソースから公開 Azure サービス (リソースに IP アドレスが割り当てられている Azure サービス) に出て行く通信にも、パブリック IP アドレスが使用されます。 このアドレスは、特定のリソース専用に確保され、明示的に割り当てが解除されない限り維持されます。 パブリック IP アドレスが割り当てられていなくても、リソースはインターネットに向けて通信することはできます。ただしその場合、そのリソース専用ではない空いている IP アドレスが Azure によって動的に割り当てられます。 Azure での送信接続の詳細については、[送信用接続の詳細](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に関するページを参照してください。

Azure リソース マネージャーで、 [パブリック IP](virtual-network-public-ip-address.md) アドレスは、独自のプロパティを持つリソースです。 パブリック IP アドレスのリソースは、次のリソースのいずれかと関連付けることができます。

* 仮想マシン ネットワーク インターフェイス
* インターネットに接続するロード バランサー
* VPN ゲートウェイ
* アプリケーション ゲートウェイ

### <a name="ip-address-version"></a>IP アドレスのバージョン

パブリック IP アドレスは、IPv4 または IPv6 アドレスを使用して作成されます。 パブリックの IPv6 アドレスは、インターネットに接続するロード バランサーにのみ割り当てることができます。

### <a name="sku"></a>SKU

パブリック IP アドレスは、次の SKU のいずれかを使用して作成されます。

>[!IMPORTANT]
> ロード バランサー リソースとパブリック IP リソースには一致する SKU を使用する必要があります。 Basic SKU リソースと Standard SKU リソースを組み合わせることはできません。 スタンドアロン仮想マシン、可用性セット リソース内の仮想マシン、または仮想マシン スケール セット リソースを、両方の SKU に同時にアタッチすることはできません。  新しい設計では、Standard SKU リソースの使用を検討する必要があります。  詳しくは、[Standard ロード バランサー](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に関するページをご覧ください。

#### <a name="basic"></a>Basic

SKU の導入前に作成されたすべてのパブリック IP アドレスは、Basic SKU のパブリック IP アドレスです。 SKU を導入すると、どの SKU をパブリック IP アドレスにするかを設定できます。 Basic SKU のアドレスは次のとおりです。

- 静的または動的な割り当て方法を使用して割り当てられます。
- インバウンドから発生するフローの調整可能なアイドル タイムアウトとして4分から30 分 (既定値は 4 分) が、またアウトバウンドから発生するフローの固定アイドル タイムアウトとして 4 分が割り当てられます。
- 既定で開いています。  ネットワーク セキュリティ グループは推奨されますが、受信または送信トラフィックを制限する場合は省略可能です。
- ネットワーク インターフェイス、VPN ゲートウェイ、アプリケーション ゲートウェイ、およびインターネットに接続するロード バランサーなどのパブリック IP アドレスを割り当てることができる Azure のリソースに割り当てられます。
- 可用性ゾーンのシナリオはサポートしません。  可用性ゾーンのシナリオには、Standard SKU のパブリック IP を使用する必要があります。 可用性ゾーンに関する詳細については、[可用性ゾーンの概要](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に関するページと「[Standard Load Balancer と可用性ゾーン](../load-balancer/load-balancer-standard-availability-zones.md?toc=%2fazure%2fvirtual-network%2ftoc.json)」を参照してください。

#### <a name="standard"></a>Standard

Standard SKU のパブリック IP アドレスは次のとおりです。

- 必ず静的割り当て方法を使用してください。
- インバウンドから発生するフローの調整可能なアイドル タイムアウトとして4分から30 分 (既定値は 4 分) が、またアウトバウンドから発生するフローの固定アイドル タイムアウトとして 4 分が割り当てられます。
- 既定でセキュリティ保護され、受信トラフィックに対して閉じられています。 [ネットワーク セキュリティ グループ](security-overview.md#network-security-groups)で、許可された受信トラフィックを明示的なホワイト リストに登録する必要があります。
- ネットワーク インターフェイス、Standard パブリック ロード バランサー、アプリケーション ゲートウェイ、VPN ゲートウェイに割り当てられます。 Standard Load Balancer の詳細については、[Azure Standard Load Balancer](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) に関するページを参照してください。
- 既定ではゾーン冗長ですが、必要に応じてゾーンのパブリック IP にすることができます (ゾーンのパブリック IP アドレスとして作成でき、特定の可用性ゾーンで保証できます)。 可用性ゾーンに関する詳細については、[可用性ゾーンの概要](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に関するページと「[Standard Load Balancer と可用性ゾーン](../load-balancer/load-balancer-standard-availability-zones.md?toc=%2fazure%2fvirtual-network%2ftoc.json)」を参照してください。
 
> [!NOTE]
> [ネットワーク セキュリティ グループ](security-overview.md#network-security-groups)を作成して関連付け、目的のインバウンド トラフィックを明示的に許可するまで、Standard SKU リソースとのインバウンド通信は失敗します。

### <a name="allocation-method"></a>割り当て方法

Basic と Standard の両方の SKU のパブリック IP アドレスでは *静的*割り当て方法をサポートしています。  リソースの作成時にリソースに IP アドレスが割り当てられ、リソースの削除時に IP アドレスが解放されます。

Basic SKU のパブリック IP アドレスは、*動的*割り当て方法もサポートしており、この方法は、割り当て方法が指定されていない場合の既定です。  Basic パブリック IP アドレス リソースに*動的*割り当て方法を選択することは、IP アドレスがリソースの作成時に割り当てられ**ない**ことを意味します。  パブリック IP アドレスを仮想マシンに関連付けたとき、または基本ロード バランサーのバックエンド プールに、最初の仮想マシン インスタンスを配置したときに、パブリック IP アドレスが割り当てられます。   IP アドレスは、リソースを停止 (または削除) すると解放されます。  たとえば、リソース A から解放された IP アドレスは別のリソースに関連付けられます。 リソース A が停止している間に別のリソースに IP アドレスが割り当てられた場合、リソース A を再起動したときに、別の IP アドレスが割り当てられます。 基本パブリック IP アドレス リソースの割り当て方法を*静的*から*動的*に変更すると、アドレスが解放されます。 関連付けられたリソースの IP アドレスが変わらないようにするため、割り当て方法を明示的に *静的*に設定できます。 この場合、静的 IP アドレスが即座に割り当てられます。

> [!NOTE]
> 割り当て方法を*静的*に設定しても、パブリック IP アドレス リソースに割り当てられる実際の IP アドレスは指定できません。 リソースが作成される Azureの場所で使用可能な IP アドレスのプールから IP アドレスが割り当てられます。
>

次のようなシナリオでは、静的なパブリック IP アドレスが一般的に使用されます。

* Azure リソースと通信するためにファイアウォール規則を更新する必要がある。
* DNS 名の解決で、IP アドレスの変更によりレコードを更新する必要がある。
* Azure のリソースが、IP アドレス ベースのセキュリティ モデルを使用する他のアプリまたはサービスと通信する。
* IP アドレスにリンクされている SSL 証明書を使用する。

> [!NOTE]
> Azure では、各 Azure クラウドの各リージョンに一意の範囲からパブリック IP アドレスが割り当てられます。 Azure [Public](https://www.microsoft.com/download/details.aspx?id=56519)、[US Government](https://www.microsoft.com/download/details.aspx?id=57063)、[China](https://www.microsoft.com/download/details.aspx?id=57062)、および [Germany](https://www.microsoft.com/download/details.aspx?id=57064) クラウドの範囲 (プレフィックス) の一覧をダウンロードできます。
>

### <a name="dns-hostname-resolution"></a>DNS ホスト名の解決
パブリック IP リソースに DNS ドメイン名ラベルを指定して、Azure で管理される DNS サーバーのパブリック IP アドレスに対する *domainnamelabel*.*location*.cloudapp.azure.com のマッピングを作成できます。 たとえば、Azure の *location* が **West US** で、*domainnamelabel* が **contoso** のパブリック IP リソースを作成した場合、完全修飾ドメイン名 (FQDN) **contoso.westus.cloudapp.azure.com** がリソースのパブリック IP アドレスに解決されます。 この FQDN を使用して、Azure 内のパブリック IP アドレスをポイントするカスタム ドメイン CNAME レコードを作成できます。 代わりに、既定のサフィックスを持つ DNS 名ラベルを使用することで、Azure DNS サービスを使用して、パブリック IP アドレスに解決されるカスタム サフィックスを持つ DNS 名を構成することもできます。 詳細については、[Azure パブリック IP アドレスを使用した Azure DNS の使用](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address)に関するページを参照してください。

> [!IMPORTANT]
> 作成された各ドメイン名ラベルは、Azure の location 内で一意である必要があります。  
>

### <a name="virtual-machines"></a>仮想マシン

パブリック IP アドレスは、その**ネットワーク インターフェイス**に割り当てることで、[Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) または [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 仮想マシンに関連付けることができます。 仮想マシンには、動的または静的のどちらかのパブリック IP アドレスを割り当てることができます。 [IP アドレスをネットワーク インターフェイスに割り当てる方法](virtual-network-network-interface-addresses.md)を参照してください。

### <a name="internet-facing-load-balancers"></a>インターネットに接続するロード バランサー

パブリック IP アドレスをロード バランサーの **フロントエンド**構成に割り当てることで、パブリック IP アドレスと [SKU](#sku) あるいは [Azure Load Balancer](../load-balancer/load-balancer-overview.md) を関連付けることができます。 このパブリック IP アドレスは、負荷分散された仮想 IP アドレス (VIP) として機能します。 ロード バランサーのフロント エンドには、動的または静的のどちらかのパブリック IP アドレスを割り当てることができます。 複数のパブリック IP アドレスをロード バランサーのフロント エンドに割り当てて、SSL ベースの Web サイトを含むマルチテナント環境のような [マルチ VIP](../load-balancer/load-balancer-multivip-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) シナリオを有効にすることもできます。 Azure Load Balancer SKU の詳細については、「[Azure Load Balancer の Standard SKU](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)」を参照してください。

### <a name="vpn-gateways"></a>VPN ゲートウェイ

[Azure VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json) は、Azure 仮想ネットワーク を他の Azure 仮想ネットワークまたはオンプレミスのネットワークに接続します。 パブリック IP アドレスは、リモート ネットワークとの通信を有効にするために VPN Gateway に割り当てられます。 VPN ゲートウェイに割り当てることができるのは、*動的*基本パブリック IP アドレスのみです。

### <a name="application-gateways"></a>アプリケーション ゲートウェイ

パブリック IP アドレスをゲートウェイの [フロント エンド](../application-gateway/application-gateway-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)構成に割り当てることで、Azure **Application Gateway** に関連付けることができます。 このパブリック IP アドレスは、負荷分散された VIP として機能します。 アプリケーション ゲートウェイ V1 フロントエンド構成に割り当てることができるのは "*動的*" Basic パブリック IP アドレスのみであり、V2 フロントエンド構成には "*静的*" または Standard SKU アドレスのみです。

### <a name="at-a-glance"></a>早見表
下の表は、パブリック IP アドレスを最上位リソースに関連付けることができる特定のプロパティと、使用できる割り当て方法 (動的または静的) を示しています。

| 最上位リソース | IP アドレスの関連付け | 動的 | 静的 |
| --- | --- | --- | --- |
| 仮想マシン |Linux |はい |はい |
| インターネットに接続するロード バランサー |フロント エンド構成 |はい |はい |
| VPN Gateway |ゲートウェイ IP の構成 |はい |はい |
| Application gateway |フロント エンド構成 |はい (V1 のみ) |はい (V2 のみ) |

## <a name="private-ip-addresses"></a>プライベート IP アドレス
プライベート IP アドレスを使用すると、Azure リソースは、インターネットが到達可能な IP アドレスを使用せずに、 [仮想ネットワーク](virtual-networks-overview.md) の他のリソース、あるいはオンプレミスのネットワーク (VPN ゲートウェイまたは ExpressRoute 回線経由) と通信することができます。

Azure Resource Manager デプロイ モデルでは、プライベート IP アドレスは次の種類の Azure リソースに関連付けられます。

* 仮想マシン ネットワーク インターフェイス
* 内部ロード バランサー (ILB)
* アプリケーション ゲートウェイ

### <a name="ip-address-version"></a>IP アドレスのバージョン

プライベート IP アドレスは、IPv4 または IPv6 アドレスで作成されます。 プライベート IPv6 アドレスは、動的な割り当て方法を使用してのみ割り当てることができます。 仮想ネットワーク上のプライベート IPv6 アドレス間で通信することはできません。 インターネットに接続するロード バランサーを経由すると、インターネットからプライベート IPv6 アドレスへ受信通信できます。 詳細については、[IPv6 を使用してインターネットに接続するロード バランサーを作成する](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json)を参照してください。

### <a name="allocation-method"></a>割り当て方法

プライベート IP アドレスは、リソースのデプロイ先となる仮想ネットワーク サブネットのアドレス範囲から割り当てられます。 各サブネット アドレス範囲の先頭から 4 つのアドレスは Azure によって予約されており、それらのアドレスがリソースに割り当てられることはありません。 たとえばサブネットのアドレス範囲が 10.0.0.0/16 である場合、10.0.0.0 ～ 10.0.0.3 のアドレスをリソースに割り当てることはできません。 サブネットのアドレス範囲内の IP アドレスを割り当てることのできるリソースは一度に 1 つだけです。 

プライベート IP アドレスを割り当てる方法は 2 種類あります。

- **動的**:サブネットのアドレス範囲内で、次に利用可能な (まだ割り当ても予約もされていない) アドレスが Azure により割り当てられます。 たとえばアドレス 10.0.0.4 ～ 10.0.0.9 が既に他のリソースに割り当てられている場合、10.0.0.10 が新しいリソースに割り当てられます。 動的割り当てが既定の割り当て方法となります。 いったん割り当てられた動的 IP アドレスが解放されるのは、ネットワーク インターフェイスが削除されるか、同じ仮想ネットワーク内の別のサブネットに割り当てられた場合、または割り当て方法が "静的" に変更された際に異なる IP アドレスが指定された場合に限られます。 既定では、割り当て方法を "動的" から"静的" に変更すると、動的に割り当てられていた以前のアドレスが Azure によって静的アドレスとして割り当てられます。
- **静的**:サブネットのアドレス範囲内で、まだ割り当ても予約もされていない IP アドレスを自分で選んで割り当てます。 たとえば、サブネットのアドレス範囲が 10.0.0.0/16 で、アドレス 10.0.0.4 ～ 10.0.0.9 が既に他のリソースに割り当てられている場合、10.0.0.10 ～ 10.0.255.254 の任意のアドレスを割り当てることができます。 静的アドレスが解放されるのは、ネットワーク インターフェイスが削除された場合だけです。 割り当て方法を "動的" に変更した場合、サブネット アドレス範囲の先頭から空いている順に割り当てられるのではなく、それまで割り当てられていた静的 IP アドレスが動的アドレスとして Azure によって割り当てられます。 同じ仮想ネットワーク内の異なるサブネットにネットワーク インターフェイスを割り当てた場合にも、アドレスは変化します。ただし、ネットワーク インターフェイスを別のサブネットに割り当てるためには、最初に割り当て方法を "静的" から "動的" に変更しておく必要があります。 ネットワーク インターフェイスを別のサブネットに割り当てた後、割り当て方法を "静的" に変更し、新しいサブネットのアドレス範囲から IP アドレスを割り当てることができます。

### <a name="virtual-machines"></a>仮想マシン

[Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 仮想マシンまたは [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 仮想マシンには**ネットワーク インターフェイス**が複数存在する場合があり、プライベート IP アドレスも複数割り当てられることがあります。 それぞれのプライベート IP アドレスについて、動的または静的のどちらかの割り当て方法を指定できます。

#### <a name="internal-dns-hostname-resolution-for-virtual-machines"></a>内部 DNS ホスト名の解決 (仮想マシンの場合)

カスタムの DNS サーバーを明示的に構成しない限り、既定ではすべての Azure 仮想マシンが [Azure で管理される DNS サーバー](virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution) で構成されます。 これらの DNS サーバーは、同じ仮想ネットワーク内に存在する仮想マシンの内部名前解決を可能にします。

仮想マシンを作成すると、そのプライベート IP アドレスへのホスト名のマッピングが、Azure で管理される DNS サーバーに追加されます。 1 つの仮想マシンに複数のネットワーク インターフェイスが備わっている場合、または 1 つのネットワーク インターフェイスに対して複数の IP が構成されている場合、ホスト名は、プライマリ ネットワーク インターフェイスのプライマリ IP 構成のプライベート IP アドレスにマップされます。

Azure で管理される DNS サーバーで構成されている仮想マシンは、同じ仮想ネットワーク内のすべての仮想マシンのホスト名をプライベート IP アドレスに解決することができます。 接続されている仮想ネットワーク内の仮想マシンのホスト名を解決するには、カスタム DNS サーバーを使用する必要があります。

### <a name="internal-load-balancers-ilb--application-gateways"></a>内部ロード バランサー (ILB) と Application Gateway

[Azure 内部ロード バランサー](../load-balancer/load-balancer-internal-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (ILB) または [Azure Application Gateway](../application-gateway/application-gateway-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) の**フロントエンド**構成にプライベート IP アドレスを割り当てることができます。 このプライベート IP アドレスは内部エンドポイントとして機能し、同じ仮想ネットワーク内のリソースおよび仮想ネットワークに接続されたリモート ネットワークからのみアクセスできます。 フロント エンド構成には、動的または静的のどちらかのプライベート IP アドレスを割り当てることができます。

### <a name="at-a-glance"></a>早見表
下の表は、プライベート IP アドレスを最上位リソースに関連付けることができる特定のプロパティと、使用できる割り当て方法 (動的または静的) を示しています。

| 最上位リソース | IP アドレスの関連付け | 動的 | 静的 |
| --- | --- | --- | --- |
| 仮想マシン |Linux |はい |はい |
| Load Balancer |フロント エンド構成 |はい |はい |
| Application gateway |フロント エンド構成 |はい |はい |

## <a name="limits"></a>制限
IP アドレス指定に対する制限は、Azure の[ネットワークの制限](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits)の完全なセットに示されています。 この制限は、リージョンとサブスクリプションごとに存在します。 ビジネス上のニーズに基づいて既定の制限を上限まで引き上げるには、 [サポートにお問い合わせください](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) 。

## <a name="pricing"></a>価格
パブリック IP アドレスには、わずかな費用がかかることがあります。 Azure での IP アドレスの料金の詳細については、「[IP アドレスの料金](https://azure.microsoft.com/pricing/details/ip-addresses)」ページをご覧ください。

## <a name="next-steps"></a>次の手順
* [Azure ポータルを使用して静的パブリック IP を持つ VM をデプロイする](virtual-network-deploy-static-pip-arm-portal.md)
* Azure ポータルを使用して、[静的プライベート IP アドレスを持つ VM をデプロイ](virtual-networks-static-private-ip-arm-pportal.md)します。
