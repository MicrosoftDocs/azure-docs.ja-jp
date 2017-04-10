---
title: "Azure における IP アドレスの種類 | Microsoft Docs"
description: "Azure でのパブリックおよびプライベート IP アドレスについて説明します。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn
tags: azure-resource-manager
ms.assetid: 610b911c-f358-4cfe-ad82-8b61b87c3b7e
ms.service: virtual-network
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/27/2016
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 0506de98119576aae8f55cc6067f0bee95334c87
ms.lasthandoff: 03/31/2017


---
# <a name="ip-address-types-and-allocation-methods-in-azure"></a>Azure における IP アドレスの種類と割り当て方法
Azure リソースには、他の Azure リソース、オンプレミス ネットワーク、およびインターネットと通信するために IP アドレスを割り当てることができます。 Azure で使用できる IP アドレスには、次の 2 種類があります。

* **パブリック IP アドレス**: Azure の公開されたサービスを含め、インターネットとの通信に使用します。
* **プライベート IP アドレス**: VPN ゲートウェイまたは ExpressRoute 回線を使用してネットワークを Azure に拡張するときに、Azure 仮想ネットワーク (VNet)、およびオンプレミスのネットワーク内での通信に使用します。

> [!NOTE]
> Azure には、リソースの作成と操作に関して、[Resource Manager とクラシックの](../resource-manager-deployment-model.md) 2 種類のデプロイメント モデルがあります。  この記事では、Resource Manager デプロイメント モデルの使用方法について取り上げていますが、最新のデプロイでは、[クラシック デプロイメント モデル](virtual-network-ip-addresses-overview-classic.md)ではなくこのモデルをお勧めします。
> 

クラシック デプロイメント モデルの知識がある場合は、[クラシック デプロイメントと Resource Manager での IP アドレス指定の相違点](virtual-network-ip-addresses-overview-classic.md#differences-between-resource-manager-and-classic-deployments)に関するページを確認してください。

## <a name="public-ip-addresses"></a>パブリック IP アドレス
パブリック IP アドレスを使用すると、Azure リソースはインターネットのほか、[Azure Redis Cache](https://azure.microsoft.com/services/cache/)、[Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)、[SQL Database](../sql-database/sql-database-technical-overview.md)、[Azure Storage](../storage/storage-introduction.md) など、Azure の公開されたサービスと通信できます。

Azure リソース マネージャーで、 [パブリック IP](resource-groups-networking.md#public-ip-address) アドレスは、独自のプロパティを持つリソースです。 パブリック IP アドレスのリソースは、次のリソースのいずれかと関連付けることができます。

* 仮想マシン (VM)
* インターネットに接続するロード バランサー
* VPN ゲートウェイ
* アプリケーション ゲートウェイ

### <a name="allocation-method"></a>割り当て方法
IP アドレスを*パブリック* IP リソースに割り当てる方法には、*動的*と*静的*の 2 種類があります。 既定の割り当て方法は *動的*で、IP アドレスの作成時に割り当ては **行われません** 。 代わりに、関連付けられたリソース (VM やロード バランサーなど) を開始 (または作成) するときに、パブリック IP アドレスが割り当てられます。 IP アドレスは、リソースを停止 (または削除) すると解放されます。 これにより、リソースの停止および開始の際に IP アドレスが変更されます。

関連付けられたリソースの IP アドレスが変わらないようにするため、割り当て方法を明示的に *静的*に設定できます。 この場合、IP アドレスが即座に割り当てられます。 IP アドレスはリソースを削除するか、その割り当て方法を *動的*に変更した場合にのみ解放されます。

> [!NOTE]
> 割り当て方法を*静的*に設定しても、*パブリック IP リソース*に割り当てられる実際の IP アドレスは指定できません。 代わりに、リソースが作成される Azure の location に使用可能な IP アドレスのプールから割り当てられます。
>

次のようなシナリオでは、静的なパブリック IP アドレスが一般的に使用されます。

* エンドユーザーが Azure リソースと通信するためのファイアウォール規則を更新する必要がある。
* DNS 名の解決で、IP アドレスの変更によりレコードを更新する必要がある。
* Azure のリソースが、IP アドレス ベースのセキュリティ モデルを使用する他のアプリまたはサービスと通信する。
* IP アドレスにリンクされている SSL 証明書を使用する。

> [!NOTE]
> Azure リソースへのパブリック IP アドレス (動的/静的) の割り当てに使用する IP アドレス範囲のリストは、「 [Azure データセンターの IP アドレス範囲](https://www.microsoft.com/download/details.aspx?id=41653)」で公開されています。
>

### <a name="dns-hostname-resolution"></a>DNS ホスト名の解決
パブリック IP リソースに DNS ドメイン名ラベルを指定して、Azure で管理される DNS サーバーのパブリック IP アドレスに対する *domainnamelabel*.*location*.cloudapp.azure.com のマッピングを作成できます。 たとえば、Azure の *location* が **West US** で、*domainnamelabel* が **contoso** のパブリック IP リソースを作成した場合、完全修飾ドメイン名 (FQDN) **contoso.westus.cloudapp.azure.com** がリソースのパブリック IP アドレスに解決されます。 この FQDN を使用して、Azure 内のパブリック IP アドレスをポイントするカスタム ドメイン CNAME レコードを作成できます。

> [!IMPORTANT]
> 作成された各ドメイン名ラベルは、Azure の location 内で一意である必要があります。  
>

### <a name="virtual-machines"></a>仮想マシン
パブリック IP アドレスは、その**ネットワーク インターフェイス**に割り当てることで、[Windows](../virtual-machines/windows/about.md) または [Linux](../virtual-machines/virtual-machines-linux-about.md) の VM に関連付けることができます。 複数のネットワーク インターフェイスを備える VM の場合、*プライマリ* ネットワーク インターフェイスのみに割り当てることができます。 VM には、動的または静的のどちらかのパブリック IP アドレスを割り当てることができます。

### <a name="internet-facing-load-balancers"></a>インターネットに接続するロード バランサー
パブリック IP アドレスをロード バランサーの [フロント エンド](../load-balancer/load-balancer-overview.md)構成に割り当てることで、 **Azure Load Balancer** に関連付けることができます。 このパブリック IP アドレスは、負荷分散された仮想 IP アドレス (VIP) として機能します。 ロード バランサーのフロント エンドには、動的または静的のどちらかのパブリック IP アドレスを割り当てることができます。 複数のパブリック IP アドレスをロード バランサーのフロント エンドに割り当てて、SSL ベースの Web サイトを含むマルチテナント環境のような [マルチ VIP](../load-balancer/load-balancer-multivip.md) シナリオを有効にすることもできます。

### <a name="vpn-gateways"></a>VPN ゲートウェイ
[Azure VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) は、Azure 仮想ネットワーク (VNet) を他の Azure VNet またはオンプレミスのネットワークに接続するために使用されます。 リモート ネットワークとの通信を有効にするには、パブリック IP アドレスをその **IP 構成** に割り当てる必要があります。 現時点で VPN ゲートウェイに割り当てることができるのは、 *動的* パブリック IP アドレスのみです。

### <a name="application-gateways"></a>アプリケーション ゲートウェイ
パブリック IP アドレスをゲートウェイの [フロント エンド](../application-gateway/application-gateway-introduction.md)構成に割り当てることで、Azure **Application Gateway** に関連付けることができます。 このパブリック IP アドレスは、負荷分散された VIP として機能します。 現時点で Application Gateway のフロント エンド構成に割り当てることができるのは、 *動的* パブリック IP アドレスのみです。

### <a name="at-a-glance"></a>早見表
下の表は、パブリック IP アドレスを最上位リソースに関連付けることができる特定のプロパティと、使用できる割り当て方法 (動的または静的) を示しています。

| 最上位リソース | IP アドレスの関連付け | 動的 | 静的 |
| --- | --- | --- | --- |
| 仮想マシン |Linux |はい |はい |
| Load Balancer |フロントエンドの構成 |はい |はい |
| VPN Gateway |ゲートウェイ IP の構成 |はい |いいえ |
| フロント エンド |フロントエンドの構成 |はい |いいえ |

## <a name="private-ip-addresses"></a>プライベート IP アドレス
プライベート IP アドレスを使用すると、Azure リソースは、インターネットが到達可能な IP アドレスを使用せずに、 [仮想ネットワーク](virtual-networks-overview.md) の他のリソース、あるいはオンプレミスのネットワーク (VPN ゲートウェイまたは ExpressRoute 回線経由) と通信することができます。

Azure Resource Manager デプロイメント モデルでは、プライベート IP アドレスは次の種類の Azure リソースに関連付けられます。

* VM
* 内部ロード バランサー (ILB)
* アプリケーション ゲートウェイ

### <a name="allocation-method"></a>割り当て方法
プライベート IP アドレスは、リソースが関連付けられているサブネットのアドレス範囲から割り当てられます。 サブネット自体のアドレス範囲は、VNet のアドレス範囲に含まれます。

プライベート IP アドレスを割り当てる方法には、*動的*と*静的*の 2 種類があります。 既定の割り当て方法は*動的*です。IP アドレスは (DHCP を使用して) リソースのサブネットから自動的に割り当てられます。 リソースを停止して起動すると、この IP アドレスが変更される場合があります。

IP アドレスが変わらないようにするため、割り当て方法を *静的* に設定することができます。 この場合、リソースのサブネットの一部として有効な IP アドレスも指定する必要があります。

静的プライベート IP アドレスは、通常は次のものに使用されます。

* ドメイン コントローラーまたは DNS サーバーとして機能する VM。
* IP アドレスを使用するファイアウォール規則を必要とするリソース。
* IP アドレスを使用して他のアプリ/リソースからアクセスされるリソース。

### <a name="virtual-machines"></a>仮想マシン
プライベート IP アドレスは、[Windows](../virtual-machines/windows/about.md) または [Linux](../virtual-machines/virtual-machines-linux-about.md) の VM の**ネットワーク インターフェイス**に割り当てます。 複数のネットワーク インターフェイスを備える VM の場合、各インターフェイスにプライベート IP アドレスが割り当てられます。 ネットワーク インターフェイスに対して動的または静的のどちらかの割り当て方法を指定できます。

#### <a name="internal-dns-hostname-resolution-for-vms"></a>内部 DNS ホスト名の解決 (VM の場合)
カスタムの DNS サーバーを明示的に構成しない限り、既定ではすべての Azure VM が [Azure で管理される DNS サーバー](virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution) で構成されます。 これらの DNS サーバーは、同じ VNet 内に存在する VM の内部名前解決を可能にします。

VM を作成すると、そのプライベート IP アドレスへのホスト名のマッピングが、Azure で管理される DNS サーバーに追加されます。 複数のネットワーク インターフェイスを備える VM の場合、ホスト名はプライマリ ネットワーク インターフェイスのプライベート IP アドレスにマップされます。

Azure で管理される DNS サーバーで構成されている VM は、その VNet 内のすべての VM のホスト名をプライベート IP アドレスに解決することができます。

### <a name="internal-load-balancers-ilb--application-gateways"></a>内部ロード バランサー (ILB) と Application Gateway
[Azure 内部ロード バランサー](../load-balancer/load-balancer-internal-overview.md) (ILB) または [Azure Application Gateway](../application-gateway/application-gateway-introduction.md) の**フロント エンド**構成にプライベート IP アドレスを割り当てることができます。 このプライベート IP アドレスは内部エンドポイントとして機能し、その仮想ネットワーク (VNet) 内のリソースおよび VNet に接続されたリモート ネットワークからのみアクセスできます。 フロント エンド構成には、動的または静的のどちらかのプライベート IP アドレスを割り当てることができます。

### <a name="at-a-glance"></a>早見表
下の表は、プライベート IP アドレスを最上位リソースに関連付けることができる特定のプロパティと、使用できる割り当て方法 (動的または静的) を示しています。

| 最上位リソース | IP アドレスの関連付け | 動的 | 静的 |
| --- | --- | --- | --- |
| 仮想マシン |Linux |はい |はい |
| Load Balancer |フロントエンドの構成 |はい |はい |
| フロント エンド |フロントエンドの構成 |はい |はい |

## <a name="limits"></a>制限
IP アドレス指定に対する制限は、Azure の[ネットワークの制限](../azure-subscription-service-limits.md#networking-limits)の完全なセットに示されています。 これらの制限は、リージョンとサブスクリプションごとに存在します。 ビジネス上のニーズに基づいて既定の制限を上限まで引き上げるには、 [サポートにお問い合わせください](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) 。

## <a name="pricing"></a>価格
パブリック IP アドレスには、わずかな費用がかかることがあります。 Azure での IP アドレスの料金の詳細については、「[IP アドレスの料金](https://azure.microsoft.com/pricing/details/ip-addresses)」ページをご覧ください。

## <a name="next-steps"></a>次のステップ
* [Azure ポータルを使用して静的パブリック IP を持つ VM をデプロイする](virtual-network-deploy-static-pip-arm-portal.md)
* [テンプレートを使用した静的パブリック IP を持つ VM のデプロイ](virtual-network-deploy-static-pip-arm-template.md)
* Azure ポータルを使用して、[静的プライベート IP アドレスを持つ VM をデプロイ](virtual-networks-static-private-ip-arm-pportal.md)します。

