---
title: Azure でのパブリック IP アドレス
titlesuffix: Azure Virtual Network
description: Azure でのパブリック IP アドレスについて説明します。
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
ms.openlocfilehash: ffdd673cc8a357a7156fb3b3e932c524c831db15
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103418064"
---
# <a name="public-ip-addresses"></a>パブリック IP アドレス

インターネット リソースから Azure リソースに入ってくる通信には、パブリック IP アドレスが使用されます。 パブリック IP アドレスにより、Azure リソースからインターネットへの通信と、公開されている Azure サービスへの通信が可能になります。 このアドレスは、特定のリソース専用に確保され、明示的に割り当てが解除されない限り維持されます。 パブリック IP が割り当てられていないリソースは、送信通信できます。 Azure では、リソース専用ではない使用可能な IP アドレスが動的に割り当てられます。 Azure での送信接続の詳細については、[送信用接続の詳細](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に関するページを参照してください。

Azure リソース マネージャーで、 [パブリック IP](virtual-network-public-ip-address.md) アドレスは、独自のプロパティを持つリソースです。 パブリック IP アドレスのリソースは、次のリソースのいずれかと関連付けることができます。

* 仮想マシン ネットワーク インターフェイス
* インターネットに接続するロード バランサー
* VPN ゲートウェイ
* アプリケーション ゲートウェイ
* Azure Firewall

## <a name="ip-address-version"></a>IP アドレスのバージョン

パブリック IP アドレスは、IPv4 または IPv6 アドレスを使用して作成されます。 

## <a name="sku"></a>SKU

SKU のアップグレードについては、[パブリック IP のアップグレード](../virtual-network/virtual-network-public-ip-address-upgrade.md)に関するページをご覧ください。

パブリック IP アドレスは、次の SKU のいずれかを使用して作成されます。

>[!IMPORTANT]
> ロード バランサー リソースとパブリック IP リソースには一致する SKU を使用する必要があります。 Basic SKU リソースと Standard SKU リソースを組み合わせることはできません。 スタンドアロン仮想マシン、可用性セット リソース内の仮想マシン、または仮想マシン スケール セット リソースを、両方の SKU に同時にアタッチすることはできません。  新しい設計では、Standard SKU リソースの使用を検討する必要があります。  詳しくは、[Standard ロード バランサー](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に関するページをご覧ください。

### <a name="standard"></a>Standard

Standard SKU のパブリック IP アドレス:

- 必ず静的割り当て方法を使用してください。
- インバウンドから発生するフローの調整可能なアイドル タイムアウトとして4分から30 分 (既定値は 4 分) が、またアウトバウンドから発生するフローの固定アイドル タイムアウトとして 4 分が割り当てられます。
- 既定でセキュリティ保護され、受信トラフィックに対して閉じられています。 [ネットワーク セキュリティ グループ](./network-security-groups-overview.md#network-security-groups)を使用した受信トラフィックの一覧表示を許可します。
- ネットワーク インターフェイス、Standard パブリック ロード バランサー、またはアプリケーション ゲートウェイに割り当てられます。 Standard Load Balancer の詳細については、[Azure Standard Load Balancer](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) に関するページを参照してください。
- ゾーン冗長 (3 つすべてのゾーンからアドバタイズ)、ゾーン ベース (特定の事前に選択された可用性ゾーンで保証)、またはゾーンなし (特定の事前に選択された可用性ゾーンに関連付けられていない) にすることができます。 可用性ゾーンに関する詳細については、[可用性ゾーンの概要](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に関するページと「[Standard Load Balancer と可用性ゾーン](../load-balancer/load-balancer-standard-availability-zones.md?toc=%2fazure%2fvirtual-network%2ftoc.json)」を参照してください。 **ゾーン冗長 IP は、[3 つの可用性ゾーン](../availability-zones/az-region.md)が有効になっているリージョンでのみ作成できます。** ゾーンが有効になる前に作成された IP は、ゾーン冗長にはなりません。
- [リージョン間ロード バランサー ](../load-balancer/cross-region-overview.md)(プレビュー機能) のためのエニーキャスト フロントエンド IP として使用できます。
 
> [!NOTE]
> [ネットワーク セキュリティ グループ](./network-security-groups-overview.md#network-security-groups)を作成して関連付け、目的のインバウンド トラフィックを明示的に許可するまで、Standard SKU リソースとのインバウンド通信は失敗します。

> [!NOTE]
> [インスタンス メタデータ サービス (IMDS)](../virtual-machines/windows/instance-metadata-service.md) を使用している場合は、Basic SKU のパブリック IP アドレスのみを使用できます。 Standard SKU はサポートされていません。

> [!NOTE]
> Standard SKU のパブリック IP アドレスを使用している場合、診断設定は [リソース] ブレードに表示されません。 標準パブリック IP アドレス リソースのログ記録を有効にするには、[Azure Monitor] ブレードの [診断設定] に移動し、IP アドレス リソースを選択します。

### <a name="basic"></a>Basic

SKU の導入前に作成されたすべてのパブリック IP アドレスは、Basic SKU のパブリック IP アドレスです。 

SKU を導入するときは、どの SKU をパブリック IP アドレスにするかを指定します。 

Basic SKU のアドレス:

- 静的または動的な割り当て方法を使用して割り当てられます。
- インバウンドから発生するフローの調整可能なアイドル タイムアウトとして4分から30 分 (既定値は 4 分) が、またアウトバウンドから発生するフローの固定アイドル タイムアウトとして 4 分が割り当てられます。
- 既定で開いています。  ネットワーク セキュリティ グループは推奨されますが、受信または送信トラフィックを制限する場合は省略可能です。
- パブリック IP アドレスを割り当てることができる次のような任意の Azure リソースに割り当てられます。
    * ネットワーク インターフェイス
    * VPN ゲートウェイ
    * Application Gateway
    * パブリック ロード バランサー
- 可用性ゾーンのシナリオはサポートしません。 可用性ゾーンのシナリオには、Standard SKU のパブリック IP を使用します。 可用性ゾーンに関する詳細については、[可用性ゾーンの概要](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に関するページと「[Standard Load Balancer と可用性ゾーン](../load-balancer/load-balancer-standard-availability-zones.md?toc=%2fazure%2fvirtual-network%2ftoc.json)」を参照してください。

## <a name="allocation-method"></a>割り当て方法

Basic および Standard のパブリック IP では、**静的** な割り当てがサポートされています。  リソースには、作成時に IP アドレスが割り当てられます。 この IP アドレスは、リソースが削除されたときに解放されます。

Basic SKU のパブリック IP アドレスは、**動的** な割り当てをサポートします。 動的割り当てが既定の割り当て方法です。 "動的" を選択すると、IP アドレスは作成時にリソースに割り当てられ **ません**。

IP は、パブリック IP アドレス リソースを次のものに関連付けるときに割り当てられます。

* 仮想マシン 
* 1 つ目の仮想マシンは、ロード バランサーのバックエンド プールに関連付けられます。

IP アドレスは、リソースを停止 (または削除) すると解放されます。  

たとえば、パブリック IP リソースが **リソース A** という名前のリソースから解放されます。**リソース A** は、パブリック IP リソースが再割り当てされる場合、起動時に別の IP アドレスを受信します。

IP アドレスは、割り当て方法が **静的** から **動的** に変更されたときに解放されます。 関連付けられたリソースの IP アドレスが変わらないようにするには、割り当て方法を明示的に **静的** に設定します。 この場合、静的 IP アドレスが即座に割り当てられます。

> [!NOTE]
> 割り当て方法を **静的** に設定しても、パブリック IP アドレス リソースに割り当てられる実際の IP アドレスは指定できません。 リソースが作成される Azureの場所で使用可能な IP アドレスのプールから IP アドレスが割り当てられます。
>

次のようなシナリオでは、静的なパブリック IP アドレスが一般的に使用されます。

* Azure リソースと通信するためにファイアウォール規則を更新する必要がある。
* DNS 名の解決で、IP アドレスの変更によりレコードを更新する必要がある。
* Azure のリソースが、IP アドレス ベースのセキュリティ モデルを使用する他のアプリまたはサービスと通信する。
* IP アドレスにリンクされている TLS/SSL 証明書を使用する。

> [!NOTE]
> Azure では、各 Azure クラウドの各リージョンに一意の範囲からパブリック IP アドレスが割り当てられます。 Azure [Public](https://www.microsoft.com/download/details.aspx?id=56519)、[US Government](https://www.microsoft.com/download/details.aspx?id=57063)、[China](https://www.microsoft.com/download/details.aspx?id=57062)、および [Germany](https://www.microsoft.com/download/details.aspx?id=57064) クラウドの範囲 (プレフィックス) の一覧をダウンロードできます。
>

## <a name="dns-hostname-resolution"></a>DNS ホスト名の解決

パブリック IP リソースの DNS ドメイン名ラベルを指定するオプションを選択します。 

この選択により、**domainnamelabel**.**location**.cloudapp.azure.com から Azure で管理される DNS のパブリック IP へのマッピングが作成されます。 

たとえば、次のようなパブリック IP を作成します。

* **domainnamelabel** が **contoso**
* Azure の **location** が **West US**

完全修飾ドメイン名 (FQDN) **contoso.westus.cloudapp.azure.com** は、リソースのパブリック IP アドレスに解決されます。

> [!IMPORTANT]
> 作成された各ドメイン名ラベルは、Azure の location 内で一意である必要があります。  
>

## <a name="dns-recommendations"></a>DNS に関する推奨事項

リージョンの移動が必要な場合は、パブリック IP の FQDN を移行できません。 パブリック IP アドレスをポイントするカスタム CNAME レコードを作成するには、この FQDN を使用します。 

別のパブリック IP への移動が必要な場合は、FQDN を更新する代わりに CNAME レコードを更新します。

DNS レコードには [Azure DNS](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address) または外部の DNS プロバイダーを使用できます。 

## <a name="virtual-machines"></a>仮想マシン

パブリック IP アドレスは、その **ネットワーク インターフェイス** に割り当てることで、[Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) または [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 仮想マシンに関連付けることができます。 

パブリック IP アドレスには、**動的** または **静的** を選択します。 [IP アドレスをネットワーク インターフェイスに割り当てる方法](virtual-network-network-interface-addresses.md)を参照してください。

## <a name="internet-facing-load-balancers"></a>インターネットに接続するロード バランサー

パブリック IP アドレスをロード バランサーの **フロントエンド** 構成に割り当てることで、パブリック IP アドレスと [SKU](#sku) あるいは [Azure Load Balancer](../load-balancer/load-balancer-overview.md) を関連付けることができます。 このパブリック IP は、負荷分散された IP として機能します。 

ロード バランサーのフロント エンドには、動的または静的のどちらかのパブリック IP アドレスを割り当てることができます。 ロード バランサーのフロントエンドには、複数のパブリック IP アドレスを割り当てることができます。 この構成により、TLS ベースの Web サイトを含むマルチテナント環境のような[マルチ VIP](../load-balancer/load-balancer-multivip-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) シナリオを実現できます。 

Azure Load Balancer SKU の詳細については、「[Azure Load Balancer の Standard SKU](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)」を参照してください。

## <a name="vpn-gateways"></a>VPN ゲートウェイ

[Azure VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json) は、Azure 仮想ネットワークを次に接続します。

* Azure 仮想ネットワーク
* オンプレミス ネットワーク。 

パブリック IP アドレスは、リモート ネットワークとの通信を有効にするために VPN Gateway に割り当てられます。 

* **動的** な Basic パブリック IP を VPNGw 1-5 SKU フロントエンド構成に割り当てます。
* **静的** な Standard パブリック IP アドレスを VPNGwAZ 1-5 SKU フロントエンド構成に割り当てます。

## <a name="application-gateways"></a>アプリケーション ゲートウェイ

パブリック IP アドレスをゲートウェイの **フロント エンド** 構成に割り当てることで、Azure [Application Gateway](../application-gateway/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) に関連付けることができます。 

* **動的** な Basic パブリック IP アドレスは、Application Gateway の V1 フロントエンド構成に割り当てます。 
* **静的** な Standard パブリック IP アドレスは、V2 フロントエンド構成に割り当てます。

## <a name="azure-firewall"></a>Azure Firewall

[Azure Firewall](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) では、アプリケーションとネットワークの接続ポリシーを、サブスクリプションと仮想ネットワークをまたいで作成、適用、記録することができます。

**静的** な標準パブリック IP アドレスは、ファイアウォールにのみ関連付けることができます。 これにより、対象の仮想ネットワークから送信されるトラフィックを外部のファイアウォールで識別できます。 


## <a name="at-a-glance"></a>早見表

下の表は、パブリック IP を最上位リソースに関連付けることができるプロパティと、使用できる割り当て方法を示しています。

| 最上位リソース | IP アドレスの関連付け | 動的 | 静的 |
| --- | --- | --- | --- |
| 仮想マシン |ネットワーク インターフェイス |はい |はい |
| インターネットに接続するロード バランサー |フロント エンド構成 |はい |はい |
| VPN Gateway |ゲートウェイ IP の構成 |はい |いいえ |
| Application gateway |フロント エンド構成 |はい (V1 のみ) |はい (V2 のみ) |
| Azure Firewall | フロント エンド構成 | いいえ | はい|

## <a name="limits"></a>制限

IP アドレス指定に対する制限は、Azure の[ネットワークの制限](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits)の完全なセットに示されています。 

この制限は、リージョンとサブスクリプションごとに存在します。 ビジネス上のニーズに基づいて既定の制限を上限まで引き上げるには、[サポートにお問い合わせください](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

## <a name="pricing"></a>価格

パブリック IP アドレスには、わずかな費用がかかることがあります。 Azure での IP アドレスの料金の詳細については、「[IP アドレスの料金](https://azure.microsoft.com/pricing/details/ip-addresses)」ページをご覧ください。

## <a name="next-steps"></a>次のステップ
* [Azure でのプライベート IP アドレス](private-ip-addresses.md)について学習する
* [Azure ポータルを使用して静的パブリック IP を持つ VM をデプロイする](virtual-network-deploy-static-pip-arm-portal.md)
