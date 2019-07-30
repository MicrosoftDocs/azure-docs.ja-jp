---
title: Azure Virtual Network に関する FAQ
titlesuffix: Azure Virtual Network
description: Microsoft Azure 仮想ネットワークについてよく寄せられる質問に回答します。
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/12/2019
ms.author: kumud
ms.openlocfilehash: fcc26d0d42576e8d39407f2af5bafe6de24db19f
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/17/2019
ms.locfileid: "67154497"
---
# <a name="azure-virtual-network-frequently-asked-questions-faq"></a>Azure 仮想ネットワークについてよく寄せられる質問 (FAQ)

## <a name="virtual-network-basics"></a>仮想ネットワークの基礎

### <a name="what-is-an-azure-virtual-network-vnet"></a>Azure 仮想ネットワーク (VNet) とは何ですか。
Azure 仮想ネットワーク (VNet) は、クラウド内のユーザー独自のネットワークを表すものです。 サブスクリプション専用に Azure クラウドが論理的に分離されています。 VNet を使用してプロビジョニングして Azure で仮想プライベート ネットワーク (VPN) を管理および、必要に応じて、VNet を Azure で他の VNet とリンクさせたり、オンプレミス IT インフラストラクチャで、ハイブリッドまたはクロスプレミス ソリューションを作成します。 作成したそれぞれの VNet には独自の CIDR ブロックがあり、CIDR ブロックが競合しない限り、他の VNet やオンプレミス ネットワークにリンクすることができます。 VNet の DNS サーバー設定と、サブネットへの VNet の分割も制御できます。

VNet を使用して次のことが行えます。

* プライベート クラウドのみの専用 VNet を作成します。ソリューションがクロスプレミス構成が必要ない場合もあります。 VNet を作成するとき、ご使用のサービスと VNet 内の VMが、クラウド内で互いに直接かつ安全に通信することができます。 VM と、ソリューションの一部としてインターネット通信を必要とするサービスとのエンドポイント接続を構成することができます。
* データ センターを安全に拡張します。VNet を使用すると、従来のサイト間 (S2S) VPN を構築して、データセンターの容量を安全に拡張できます。 S2S VPN は、IPSEC を使用して、社内の VPN ゲートウェイと Azure 間の安全な接続を提供します。
* ハイブリッド クラウドのシナリオを有効にします。VNet は、さまざまなハイブリッド クラウド シナリオをサポートする柔軟性を提供します。 メインフレームなどのオンプレミス システムと Unix システムの任意の型へのクラウド ベースのアプリケーションを安全に接続することができます。

### <a name="how-do-i-get-started"></a>開始するには?
「[Virtual Network のドキュメント](https://docs.microsoft.com/azure/virtual-network/)」にアクセスし、開始します。 このコンテンツには、すべての VNet 機能の概要とデプロイに関する情報が示されています。

### <a name="can-i-use-vnets-without-cross-premises-connectivity"></a>クロスプレミス接続を使用しないで VNet を使用できますか。
はい。 プレミスに接続せずに VNet を使用することができます。 たとえば、Microsoft Windows Server Active Directory ドメイン コントローラーと SharePoint ファームを Azure VNet 内のみで実行できます。

### <a name="can-i-perform-wan-optimization-between-vnets-or-a-vnet-and-my-on-premises-data-center"></a>VNet 間または VNet とオンプレミスのデータ センター間で WAN の最適化を実行することはできますか。
はい。 Azure Marketplace を通じて複数のベンダーから提供されている [WAN 最適化ネットワーク仮想アプライアンス](https://azure.microsoft.com/marketplace/?term=wan+optimization)をデプロイできます。

## <a name="configuration"></a>構成

### <a name="what-tools-do-i-use-to-create-a-vnet"></a>VNet を作成するのには、どのようなツールを使用でしょうか。
次のツールを使用して、VNet を作成または構成できます。

* Azure ポータル
* PowerShell
* Azure CLI
* ネットワーク構成ファイル (netcfg - クラシック VNet のみ)。 「[Configure a VNet using a network configuration file (ネットワーク構成ファイルを使用した VNet の構成)](virtual-networks-using-network-configuration-file.md)」を参照してください。

### <a name="what-address-ranges-can-i-use-in-my-vnets"></a>VNet でどのアドレス範囲が使用できるでしょうか。
[RFC 1918](https://tools.ietf.org/html/rfc1918) で定義されている任意の IP アドレス範囲 (例: 10.0.0.0/16)。 次のアドレス範囲は追加できません。
* 224.0.0.0/4 (マルチキャスト)
* 255.255.255.255/32 (ブロードキャスト)
* 127.0.0.0/8 (ループバック)
* 169.254.0.0/16 (リンク ローカル)
* 168.63.129.16/32 (内部 DNS)

### <a name="can-i-have-public-ip-addresses-in-my-vnets"></a>VNet 内でパブリック IP アドレスを持つことができますか。
はい。 パブリック IP アドレス範囲の詳細については、[仮想ネットワークの作成](manage-virtual-network.md#create-a-virtual-network)に関する記事をご覧ください。 パブリック IP アドレスは、インターネットから直接アクセスできません。

### <a name="is-there-a-limit-to-the-number-of-subnets-in-my-vnet"></a>VNet 内のサブネットの数に制限はありますか。
はい。 詳細については、[Azure の制限](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits)に関する記事をご覧ください。 サブネットのアドレス空間は、互いに重複することはできません。

### <a name="are-there-any-restrictions-on-using-ip-addresses-within-these-subnets"></a>これらのサブネット内の IP アドレスの使用に関する制限はありますか。
はい。 Azure では、各サブネット内で 5 つの IP アドレスが予約されています。 これらは x.x.x.0 から x.x.x.3 とサブネットの最後のアドレスです。    
- x.x.x.0 とサブネットの最後のアドレスは、プロトコル準拠用に予約されています。
- 各サブネットの x.x.x.1 から x.x.x.3 は Azure サービス用に予約されています。

### <a name="how-small-and-how-large-can-vnets-and-subnets-be"></a>VNet およびサブネットは、どれくらい小規模に、また、大規模になるのでしょうか。
サポートされる最小のサブネットは /29、最大は /8 です (CIDR サブネット定義を使用)。

### <a name="can-i-bring-my-vlans-to-azure-using-vnets"></a>VNet を使用して、VLAN を Azure に接続できるでしょうか。
いいえ。 VNet はレイヤー 3 のオーバーレイです。 Azure では、任意のレイヤー 2 のセマンティクスはサポートされません。

### <a name="can-i-specify-custom-routing-policies-on-my-vnets-and-subnets"></a>私の Vnet とサブネットをカスタム ルーティング ポリシーを指定できますか。
はい。 ルート テーブルを作成してサブネットに関連付けることができます。 Azure でのルーティングの詳細については、[ルーティングの概要](virtual-networks-udr-overview.md#custom-routes)に関する記事をご覧ください。

### <a name="do-vnets-support-multicast-or-broadcast"></a>VNet はマルチキャストやブロードキャストをサポートしますか。
いいえ。 マルチキャストとブロードキャストはサポートされていません。

### <a name="what-protocols-can-i-use-within-vnets"></a>VNet 内はどのようなプロトコルを使用できますか。
VNet では、TCP、UDP、および ICMP TCP/IP プロトコルを使用することができます。 ユニキャストは VNet 内でサポートされますが、ユニキャスト (ソース ポート UDP/68 / 宛先ポート UDP/67) を経由した動的ホスト構成プロトコル (DHCP) は例外です。 マルチキャスト、ブロードキャスト、IP-in-IP のカプセル化されたパケット、Generic Routing Encapsulation (GRE) のパケットは、VNet 内でブロックされます。 

### <a name="can-i-ping-my-default-routers-within-a-vnet"></a>VNet 内には、既定ルーターを ping できるでしょうか。
いいえ。

### <a name="can-i-use-tracert-to-diagnose-connectivity"></a>Tracert を使用して、接続を診断することができますか。
いいえ。

### <a name="can-i-add-subnets-after-the-vnet-is-created"></a>VNet を作成した後のサブネットを追加できますか。
はい。 サブネットのアドレス範囲が VNet 内の別のサブネットの一部でなく、仮想ネットワークのアドレス範囲に空き領域があれば、いつでもサブネットを VNet に追加できます。

### <a name="can-i-modify-the-size-of-my-subnet-after-i-create-it"></a>作成した後、サブネットのサイズを変更できますか。
はい。 VM またはサービスがサブネット内にデプロイされていない場合は、サブネットを追加、削除、拡張、または縮小できます。

### <a name="can-i-modify-subnets-after-i-created-them"></a>サブネットを作成後に変更できますか。
はい。 VNet で使用された CIDR ブロックを追加、削除、変更することができます。

### <a name="if-i-am-running-my-services-in-a-vnet-can-i-connect-to-the-internet"></a>VNet でサービスを実行している場合、インターネットに接続できますか。
はい。 VNet 内にデプロイされているすべてのサービスは、インターネットに送信接続できます。 Azure での送信インターネット接続について詳しくは、[送信接続](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に関する記事をご覧ください。 Resource Manager を使用してデプロイされたリソースに受信接続するには、リソースにパブリック IP アドレスが割り当てられている必要があります。 パブリック IP アドレスについて詳しくは、[パブリック IP アドレス](virtual-network-public-ip-address.md)に関する記事をご覧ください。 Azure にデプロイされたすべての Azure クラウド サービスには、パブリックにアドレス指定可能な VIP が割り当てられています。 PaaS ロールの入力エンドポイントと仮想マシンのエンドポイントを定義して、これらのサービスがインターネットからの接続を承諾できるようにします。

### <a name="do-vnets-support-ipv6"></a>VNet は IPv6 をサポートするでしょうか。
いいえ。 この時点で、VNet で IPv6 を使用できません。 ただし、Azure ロード バランサーに IPv6 アドレスを割り当てて、仮想マシンを負荷分散することはできます。 詳細については、「[Azure Load Balancer の IPv6 の概要](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)」をご覧ください。

### <a name="can-a-vnet-span-regions"></a>VNet は複数のリージョンで広がるでしょうか。
いいえ。 VNet は、1 つのリージョンに制限されます。 ただし、仮想ネットワークは複数の可用性ゾーンにまたがります。 可用性ゾーンの詳細については、「[可用性ゾーンの概要](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)」を参照してください。 仮想ネットワーク ピアリングを使用して、別々のリージョンにある仮想ネットワークを接続できます。 詳細については、[仮想ネットワーク ピアリングの概要](virtual-network-peering-overview.md)に関する記事をご覧ください。

### <a name="can-i-connect-a-vnet-to-another-vnet-in-azure"></a>VNet を Azure での別の VNet に接続できますか。
はい。 次のいずれかの方法で VNet どうしを接続できます。
- **仮想ネットワーク ピアリング**:詳細については、[VNet ピアリングの概要](virtual-network-peering-overview.md)に関する記事をご覧ください
- **Azure VPN Gateway**:詳細については、[VNet 間接続の構成](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に関する記事をご覧ください。 

## <a name="name-resolution-dns"></a>名前解決 (DNS)

### <a name="what-are-my-dns-options-for-vnets"></a>VNet の DNS オプションとは何でしょうか。
[VM とロール インスタンスの名前解決](virtual-networks-name-resolution-for-vms-and-role-instances.md) ページでディシジョン テーブルを使用して、使用できるすべての DNS オプションを行うことができます。

### <a name="can-i-specify-dns-servers-for-a-vnet"></a>VNet の DNS サーバーを指定できますか。
はい。 VNet 設定では、DNS サーバーの IP アドレスを指定できます。 設定は、VNet 内のすべての VM の既定の DNS サーバーとして適用されます。

### <a name="how-many-dns-servers-can-i-specify"></a>DNS サーバーの数を指定できますか。
[Azure の制限](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits)に関する記事をご覧ください。

### <a name="can-i-modify-my-dns-servers-after-i-have-created-the-network"></a>ネットワークを作成した後、DNS サーバーを変更できますか。
はい。 いつでも、VNet の DNS サーバーの一覧を変更できます。 DNS サーバーの一覧を変更する場合は、新しい DNS サーバーを取得するために VNet で VM をそれぞれ再起動する必要があります。

### <a name="what-is-azure-provided-dns-and-does-it-work-with-vnets"></a>Azure で提供される DNS と Vnet と組み合わせて使用について
Azure で提供される DNS は、Microsoft によって提供されるマルチ テナント DNS サービスです。 Azure は、すべての VM とクラウド サービス ロール インスタンスをこのサービスに登録します。 このサービスは、同じクラウド サービス内に含まれる VM とロール インスタンスのホスト名によって、また、同じ VNet 内の VM とロール インスタンスの FQDN によって名前解決を提供します。 DNS の詳細については、[VM と Cloud Services ロール インスタンスの名前解決](virtual-networks-name-resolution-for-vms-and-role-instances.md)に関する記事をご覧ください。

Azure で提供される DNS を使用したテナント間名前解決は、VNet の最初の 100 個のクラウド サービスまでに制限されます。 独自の DNS サーバーを使用している場合は、この制限は適用されません。

### <a name="can-i-override-my-dns-settings-on-a-per-vm-or-cloud-service-basis"></a>VM またはクラウド サービスごとに、DNS 設定をオーバーライドできますか。
はい。 VM またはクラウド サービスごとに DNS サーバーを設定し、既定のネットワーク設定をオーバーライドすることができます。 ただし、できるだけネットワーク全体の DNS を使用することをお勧めします。

### <a name="can-i-bring-my-own-dns-suffix"></a>独自の DNS サフィックスを取り込むことができますか。
いいえ。 ご使用の VNet のカスタム DNS サフィックスを指定することはできません。

## <a name="connecting-virtual-machines"></a>仮想マシンの接続

### <a name="can-i-deploy-vms-to-a-vnet"></a>VNet に VM をデプロイできますか。
はい。 Resource Manager デプロイ モデルを使用してデプロイされた VM に接続されているすべてのネットワーク インターフェイス (NIC) は、VNet に接続されている必要があります。 クラシック デプロイ モデルを使用してデプロイされた VM は、必要に応じて VNet に接続できます。

### <a name="what-are-the-different-types-of-ip-addresses-i-can-assign-to-vms"></a>VM に割り当てることができる IP アドレスにはどのような種類がありますか。
* **プライベート:** 各 VM 内の各 NIC に割り当てられます。 アドレスは、静的または動的な方法を使用して割り当てられます。 プライベート IP アドレスは、VNet のサブネット設定で指定した範囲から割り当てられます。 クラシック デプロイ モデルを使用してデプロイされたリソースは、VNet に接続されていなくてもプライベート IP アドレスが割り当てられます。 割り当て方法の動作は、リソースが Resource Manager デプロイ モデルとクラシック デプロイ モデルのどちらを使用してデプロイされたかによって異なります。 

  - **Resource Manager**:動的または静的な方法を使用して割り当てられたプライベート IP アドレスは、リソースが削除されるまで、仮想マシン (Resource Manager) に割り当てられたままになります。 異なるのは、静的な方法の場合はユーザーが割り当てるアドレスを選択し、動的な方法の場合は Azure が選択するという点です。 
  - **クラシック**:動的な方法を使用して割り当てられたプライベート IP アドレスは、仮想マシン (クラシック) VM が停止 (割り当て解除) 状態になった後で再起動されたときに変更される可能性があります。 クラシック デプロイ モデルを使用してデプロイされるリソースのプライベート IP アドレスを固定する必要がある場合は、静的な方法を使用してプライベート IP アドレスを割り当ててください。

* **パブリック:** 必要に応じて、Azure Resource Manager デプロイ モデルを使用してデプロイされた VM に接続されている NIC に割り当てられます。 アドレスは、静的または動的な割り当て方法を使用して割り当てることができます。 クラシック デプロイ モデルを使用してデプロイされたすべての VM および Cloud Services ロール インスタンスは、"*動的な*" パブリック仮想 IP (VIP) アドレスが割り当てられたクラウド サービス内に存在します。 パブリックな "*静的*" IP アドレスは、[予約済み IP アドレス](virtual-networks-reserved-public-ip.md)と呼ばれ、必要に応じて VIP として割り当てることができます。 パブリック IP アドレスは、クラシック デプロイ モデルを使用してデプロイされた個々の VM または Cloud Services ロール インスタンスに割り当てることができます。 これらのアドレスは、[インスタンス レベルのパブリック IP (ILPIP)](virtual-networks-instance-level-public-ip.md) アドレスと呼ばれ、動的に割り当てることができます。

### <a name="can-i-reserve-a-private-ip-address-for-a-vm-that-i-will-create-at-a-later-time"></a>後で作成する VM 用にプライベート IP アドレスを予約することはできますか。
いいえ。 プライベート IP アドレスを予約することはできません。 プライベート IP アドレスが使用可能な場合は、DHCP サーバーによって VM またはロール インスタンスに割り当てられます。 その VM は、プライベート IP アドレスを割り当てたい VM である場合も、そうでない場合もあります。 ただし、既に作成した VM のプライベート IP アドレスを、使用可能なプライベート IP アドレスに変更することができます。

### <a name="do-private-ip-addresses-change-for-vms-in-a-vnet"></a>VNet 内の VM のプライベート IP アドレスは変更されますか。
一概には言えません。 VM が Resource Manager を使用してデプロイされた場合は、静的または動的な割り当て方法のどちらを使用して割り当てられていても、変更されることはありません。 VM がクラシック デプロイ モデルを使用してデプロイされた場合は、VM が停止 (割り当て解除) 状態になった後で起動された場合に、動的 IP アドレスが変更される可能性があります。 このアドレスは、いずれかのデプロイメント モデルを使用してデプロイされた VM が削除されると、その VM から解放されます。

### <a name="can-i-manually-assign-ip-addresses-to-nics-within-the-vm-operating-system"></a>VM オペレーティング システム内の NIC に手動で IP アドレスを割り当てることはできますか。
はい。ただし、必要な場合 (仮想マシンに複数の IP アドレスを割り当てる場合など) 以外はお勧めしません。 詳細については、[仮想マシンに複数の IP アドレスを追加する方法](virtual-network-multiple-ip-addresses-portal.md#os-config)に関する記事をご覧ください。 VM にアタッチされた Azure NIC に割り当てられている IP アドレスが変更され、VM オペレーティング システム内の IP アドレスと異なる場合は、VM への接続が失われます。

### <a name="if-i-stop-a-cloud-service-deployment-slot-or-shutdown-a-vm-from-within-the-operating-system-what-happens-to-my-ip-addresses"></a>クラウド サービス デプロイ スロットを停止した場合や、オペレーティング システム内から VM をシャットダウンした場合、IP アドレスはどうなりますか。
何もありません。 IP アドレス (パブリック VIP、パブリック、プライベート) は、クラウド サービス デプロイ スロットまたは VM に割り当てられたままとなります。

### <a name="can-i-move-vms-from-one-subnet-to-another-subnet-in-a-vnet-without-redeploying"></a>VNet 内の別のサブネットへ、再デプロイせずに移動できますか。
はい。 詳細については、「[VM またはロール インスタンスを別のサブネットに移動する方法](virtual-networks-move-vm-role-to-subnet.md)」を参照してください。

### <a name="can-i-configure-a-static-mac-address-for-my-vm"></a>VM に静的な MAC アドレスを構成できますか。
いいえ。 MAC アドレスを静的に構成することはできません。

### <a name="will-the-mac-address-remain-the-same-for-my-vm-once-its-created"></a>MAC アドレスは、一度作成されると、VM で同じものとして残りますか。
はい。MAC アドレスは、Resource Manager デプロイ モデルを使用してデプロイされた VM とクラシック デプロイ モデルを使用してデプロイされた VM のどちらの場合も、削除されるまで同じままです。 以前は、VM が停止 (割り当て解除) された場合に MAC アドレスが解放されました。現在、MAC アドレスは、VM が割り当て解除状態であっても保持されます。 MAC アドレスがネットワーク インターフェイスに割り当てられると、そのネットワーク インターフェイスが削除されるか、プライマリ ネットワーク インターフェイスのプライマリ IP 構成に割り当てられたプライベート IP アドレスが変更されるまで、その状態は変わりません。 

### <a name="can-i-connect-to-the-internet-from-a-vm-in-a-vnet"></a>VNet 内の VM からインターネットに接続できますか。
はい。 VNet 内にデプロイされているすべての VM および Cloud Services ロール インスタンスは、インターネットに接続できます。

## <a name="azure-services-that-connect-to-vnets"></a>VNet に接続する Azure サービス

### <a name="can-i-use-azure-app-service-web-apps-with-a-vnet"></a>Azure App Service Web Apps を VNet で使用することはできますか。
はい。 ASE (App Service Environment) を使用して VNet 内に Web Apps をデプロイし、VNet 統合を使用してアプリのバックエンドを VNet に接続し、サービス エンドポイントを使用してインバウンド トラフィックをアプリにロックダウンできます。 詳細については、次の記事を参照してください。

* [App Service のネットワーク機能](../app-service/networking-features.md)
* [App Service 環境で Web Apps を作成する](../app-service/environment/app-service-web-how-to-create-a-web-app-in-an-ase.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
* [アプリを Azure 仮想ネットワークに統合する](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
* [App Service のアクセス制限](../app-service/app-service-ip-restrictions.md)

### <a name="can-i-deploy-cloud-services-with-web-and-worker-roles-paas-in-a-vnet"></a>Web ロールと worker ロール (PaaS) を持つ Cloud Services を VNet にデプロイすることはできますか。
はい。 Cloud Services ロール インスタンスは、VNet 内に (オプションで) デプロイできます。 そのためには、サービス構成のネットワーク構成セクションで、VNet の名前と、ロールとサブネットのマッピングを指定します。 どのバイナリも更新する必要はありません。

### <a name="can-i-connect-a-virtual-machine-scale-set-to-a-vnet"></a>仮想マシン スケール セットを VNet に接続することはできますか。
はい。 仮想マシン スケール セットは VNet に接続する必要があります。

### <a name="is-there-a-complete-list-of-azure-services-that-can-i-deploy-resources-from-into-a-vnet"></a>VNet 内にリソースをデプロイできる Azure サービスの完全な一覧はありますか。
はい。詳細については、「[Azure サービスの仮想ネットワーク統合](virtual-network-for-azure-services.md)」をご覧ください。

### <a name="which-azure-paas-resources-can-i-restrict-access-to-from-a-vnet"></a>どの Azure PaaS リソースで、VNet からのアクセスのみにアクセスを制限できますか。

Azure Storage、Azure SQL Database などの一部の Azure PaaS サービスを使用してデプロイされたリソースは、仮想ネットワーク サービス エンドポイントの使用によって、VNet 内のリソースのみにネットワーク アクセスを制限できます。 詳細については、[仮想ネットワーク サービス エンドポイントの概要](virtual-network-service-endpoints-overview.md)に関する記事をご覧ください。

### <a name="can-i-move-my-services-in-and-out-of-vnets"></a>サービスを VNet 内外で移動できますか。
いいえ。 サービスを VNet 内外で移動することはできません。 リソースを別の VNet に移動するには、リソースを削除して再デプロイする必要があります。

## <a name="security"></a>セキュリティ

### <a name="what-is-the-security-model-for-vnets"></a>VNet のセキュリティ モデルとは何ですか。
Vnet は、他の VNet から、および Azure インフラストラクチャでホストされている他のサービスから、完全に分離されています。 VNet は、トラスト バウンダリです。

### <a name="can-i-restrict-inbound-or-outbound-traffic-flow-to-vnet-connected-resources"></a>受信または送信トラフィック フローを VNet に接続されたリソースに制限することはできますか。
はい。 VNet 内の個々のサブネット、VNet に接続された NIC、またはその両方に[ネットワーク セキュリティ グループ](security-overview.md)を適用できます。

### <a name="can-i-implement-a-firewall-between-vnet-connected-resources"></a>VNet に接続されたリソースの間にファイアウォールを実装することはできますか。
はい。 Azure Marketplace を通じて複数のベンダーから提供されている[ファイアウォール ネットワーク仮想アプライアンス](https://azure.microsoft.com/marketplace/?term=firewall)をデプロイできます。

### <a name="is-there-information-available-about-securing-vnets"></a>VNet のセキュリティ保護に関する情報はありますか。
はい。 詳細については、「[Azure のネットワーク セキュリティの概要](../security/security-network-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)」をご覧ください。

## <a name="apis-schemas-and-tools"></a>API、スキーマ、およびツール

### <a name="can-i-manage-vnets-from-code"></a>VNet をコードから管理できますか。
はい。 [Azure Resource Manager](/rest/api/virtual-network) デプロイ モデルと[クラシック](https://go.microsoft.com/fwlink/?LinkId=296833) デプロイ モデルで、VNet 用の REST API を使用できます。

### <a name="is-there-tooling-support-for-vnets"></a>VNet に対するツール サポートはありますか。
はい。 次のツールを使用できます。
- Azure Portal。[Azure Resource Manager](manage-virtual-network.md#create-a-virtual-network) および[クラシック](virtual-networks-create-vnet-classic-pportal.md) デプロイメント モデルを使用して VNet をデプロイするために使用します。
- PowerShell。[Resource Manager](/powershell/module/az.network) および[クラシック](/powershell/module/servicemanagement/azure/?view=azuresmps-3.7.0) デプロイメント モデルを使用してデプロイされた VNet を管理するために使用します。
- Azure コマンド ライン インターフェイス (CLI)。[Resource Manager](/cli/azure/network/vnet) デプロイメント モデルおよび[クラシック](../virtual-machines/azure-cli-arm-commands.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-network-commands-to-manage-network-resources) デプロイメント モデルを使用してデプロイされた VNet をデプロイおよび管理するために使用します。  

## <a name="vnet-peering"></a>VNET ピアリング

### <a name="what-is-vnet-peering"></a>VNet ピアリングとは
VNet ピアリング (仮想ネットワーク ピアリング) を使用して、仮想ネットワークに接続できます。 仮想ネットワーク間の VNet ピアリング接続では、IPv4 アドレスによってそれらの間でトラフィックをプライベートにルーティングできます。 ピアリングされた VNet 内の仮想マシンは、同じネットワーク内にあるかのように相互に通信できます。 これらの仮想ネットワークは、同じリージョン内にあっても異なるリージョン内にあってもかまいません (グローバル VNet ピアリングとも呼ばれます)。 VNet ピアリング接続は、Azure サブスクリプション間で作成することもできます。

### <a name="can-i-create-a-peering-connection-to-a-vnet-in-a-different-region"></a>別のリージョンの VNet へのピアリング接続を作成できますか。
はい。 グローバル VNet ピアリングを使用すると、別のリージョンの VNet にピアリングできます。 グローバル VNet ピアリングは、すべての Azure パブリック リージョン、China Cloud リージョン、Government Cloud リージョンで使用できます。 Azure パブリック リージョンから国内クラウド リージョンに、グローバルにピアリングすることはできません。

### <a name="what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers"></a>グローバル VNet ピアリングとロード バランサーに関連する制約は何ですか?
2 つの仮想ネットワークが異なるリージョンにある場合 (グローバル VNet ピアリング)、Basic Load Balancer を使用するリソースには接続できません。 Standard Load Balancer を使用するリソースには接続できます。
次のリソースは Basic Load Balancer を使用しています。つまり、グローバル VNet ピアリングを介してそれらと通信することはできません。
- Basic Load Balancer の背後にある VM
- Basic Load Balancer を使用する仮想マシン スケール セット 
- Redis Cache 
- Application Gateway (v1) SKU
- Service Fabric
- SQL MI
- API Management
- Active Directory Domain Service (ADDS)
- Logic Apps
- HDInsight
-   Azure Batch
- AKS
- App Service 環境

ExpressRoute、または VNet ゲートウェイ経由の VNet 対 VNet を介してこのようなリソースに接続できます。

### <a name="can-i-enable-vnet-peering-if-my-virtual-networks-belong-to-subscriptions-within-different-azure-active-directory-tenants"></a>仮想ネットワークが別の Azure Active Directory テナント内のサブスクリプションに属する場合、VNet ピアリングを有効にできますか。
はい。 サブスクリプションが別の Azure Active Directory テナントに属する場合、(ローカルまたはグローバルのどちらでも) VNet ピアリング を確立できます。 これは、PowerShell または CLI を使用して行うことができます。 portal はまだサポートされていません。

### <a name="my-vnet-peering-connection-is-in-initiated-state-why-cant-i-connect"></a>VNet ピアリング接続が*開始済み*の状態にあります。接続できないのはなぜですか。
ピアリング接続が開始済みの状態にある場合、これは、リンクを 1 つだけ作成していることを意味します。 正常な接続を確立するには、双方向のリンクを作成する必要があります。 VNet A から VNet B にピアリングするには、VNetA から VNetB、および VNetB から VNetAへのリンクを作成する必要があります。 両方のリンクを作成すると、状態が*接続済み*に変更されます。

### <a name="my-vnet-peering-connection-is-in-disconnected-state-why-cant-i-create-a-peering-connection"></a>VNet ピアリング接続が "*切断*" 状態にあります。ピアリング接続を作成できないのはなぜですか。
VNet ピアリング接続が切断状態にある場合、それは作成されたリンクの 1 つが削除されていることを意味します。 ピアリング接続を再確立するためには、リンクを削除して再作成する必要があります。

### <a name="can-i-peer-my-vnet-with-a-vnet-in-a-different-subscription"></a>VNet を別のサブスクリプションにある VNet とピアリングできますか。
はい。 VNet はサブスクリプションやリージョンを越えてピアリングできます。

### <a name="can-i-peer-two-vnets-with-matching-or-overlapping-address-ranges"></a>一致するまたは重複するアドレス範囲にある 2 つの VNet をピアリングできますか。
いいえ。 VNet ピアリングを有効にするには、アドレス空間がオーバーラップしない必要があります。

### <a name="how-much-do-vnet-peering-links-cost"></a>VNet ピアリング リンクの料金をどれくらいですか。
VNet ピアリング接続を作成するのに料金はかかりません。 ピアリング接続経由でのデータ転送には料金が発生します。 [こちらを参照](https://azure.microsoft.com/pricing/details/virtual-network/)してください。

### <a name="is-vnet-peering-traffic-encrypted"></a>VNet ピアリングのトラフィックは暗号化されますか。
いいえ。 ピアリングされた VNet のリソース間のトラフィックはプライベートで分離されています。 マイクロソフトのバックボーンに完全に残ります。

### <a name="why-is-my-peering-connection-in-a-disconnected-state"></a>ピアリング接続が切断状態にあるのはなぜですか。
1 つの VNet ピアリング リンクが削除されると、VNet ピアリングは*切断*状態になります。 ピアリング接続を再度確立するには、両方のリンクを削除する必要があります。

### <a name="if-i-peer-vneta-to-vnetb-and-i-peer-vnetb-to-vnetc-does-that-mean-vneta-and-vnetc-are-peered"></a>VNetA から VNetB にピアリングし、VNetB から VNetC にピアリングすると、VNetA と VNetC がピアリングされたことになりますか。
いいえ。 推移的なピアリングはサポートされません。 VNetA と VNetC をピアリングする必要があります。

### <a name="are-there-any-bandwidth-limitations-for-peering-connections"></a>ピアリング接続に帯域幅制限はありますか。
いいえ。 ローカルであってもグローバルであっても、VNet ピアリングで帯域幅制限は課されません。 帯域幅は VM やコンピューティング リソースによってのみ制限されます。

### <a name="how-can-i-troubleshoot-vnet-peering-issues"></a>VNet ピアリングの問題をトラブルシューティングするにはどうすればよいですか?
こちらの[トラブルシューティング ガイド](https://support.microsoft.com/en-us/help/4486956/troubleshooter-for-virtual-network-peering-issues)を試すことができます。

## <a name="virtual-network-tap"></a>仮想ネットワーク TAP

### <a name="which-azure-regions-are-available-for-virtual-network-tap"></a>仮想ネットワーク TAP はどの Azure リージョンで利用できますか。
仮想ネットワーク TAP プレビューは、すべての Azure リージョンで利用できます。 監視対象のネットワーク インターフェイス、仮想ネットワーク TAP リソース、およびコレクターまたは分析ソリューションは、同じリージョンにデプロイする必要があります。

### <a name="does-virtual-network-tap-support-any-filtering-capabilities-on-the-mirrored-packets"></a>仮想ネットワーク TAP では、ミラー化されたパケットに対するフィルター処理機能がサポートされていますか。
仮想ネットワーク TAP のプレビュー版では、フィルター処理機能はサポートされていません。 TAP 構成をネットワーク インターフェイスに追加すると、ネットワーク インターフェイス上のすべてのイングレス トラフィックとエグレス トラフィックのディープ コピーが、TAP のターゲットにストリーミングされます。

### <a name="can-multiple-tap-configurations-be-added-to-a-monitored-network-interface"></a>監視対象のネットワーク インターフェイスに、複数の TAP 構成を追加できますか。
監視対象のネットワーク インターフェイスに追加できる TAP 構成は 1 つだけです。 ユーザーが選択した分析ツールに TAP トラフィックの複数コピーをストリーミングする機能については、個別の[パートナー ソリューション](virtual-network-tap-overview.md#virtual-network-tap-partner-solutions)を確認してください。

### <a name="can-the-same-virtual-network-tap-resource-aggregate-traffic-from-monitored-network-interfaces-in-more-than-one-virtual-network"></a>同じ仮想ネットワーク TAP リソースで、複数の仮想ネットワーク内の監視対象ネットワーク インターフェイスからのトラフィックを集約できますか。
はい。 同じ仮想ネットワーク TAP リソースを使用して、同じサブスクリプションまたは異なるサブスクリプションのピアリングされた仮想ネットワーク内の監視対象ネットワーク インターフェイスからのミラー化されたトラフィックを集約できます。 仮想ネットワーク TAP リソースと、ターゲット ロード バランサーまたはターゲット ネットワーク インターフェイスは、同じサブスクリプションに含まれる必要があります。 すべてのサブスクリプションが同じ Azure Active Directory テナントに存在する必要がある。

### <a name="are-there-any-performance-considerations-on-production-traffic-if-i-enable-a-virtual-network-tap-configuration-on-a-network-interface"></a>ネットワーク インターフェイスで仮想ネットワーク TAP 構成を有効にする場合、運用環境のトラフィックのパフォーマンスについて考慮することがありますか。

仮想ネットワーク TAP はプレビュー段階です。 プレビュー期間中は、サービス レベル アグリーメントはありません。 運用環境のワークロードでは機能を使用しないでください。 TAP 構成で仮想マシンのネットワーク インターフェイスを有効にすると、運用トラフィックを送信するために仮想マシンに割り当てられている Azure ホスト上の同じリソースを使用して、ミラーリング機能が実行され、ミラー化されたパケットが送信されます。 運用トラフィックとミラー化されたトラフィックを送信するのに十分なリソースを仮想マシンが利用できるように、[Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) または [Windows](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) の適切な仮想マシン サイズを選択してください。

### <a name="is-accelerated-networking-for-linuxcreate-vm-accelerated-networking-climd-or-windowscreate-vm-accelerated-networking-powershellmd-supported-with-virtual-network-tap"></a>仮想ネットワーク TAP では、[Linux](create-vm-accelerated-networking-cli.md) または [Windows](create-vm-accelerated-networking-powershell.md) に対する高速ネットワークはサポートされていますか。

高速ネットワークが有効になっている仮想マシンにアタッチされたネットワーク インターフェイスで、TAP 構成を追加することができます。 ただし、現在 Azure の高速ネットワークではミラーリング トラフィックのオフロードがサポートされていないため、TAP 構成を追加することにより、仮想マシンでのパフォーマンスと待機時間が影響を受けます。

## <a name="virtual-network-service-endpoints"></a>仮想ネットワーク サービス エンドポイント

### <a name="what-is-the-right-sequence-of-operations-to-set-up-service-endpoints-to-an-azure-service"></a>Azure サービスに対するサービス エンドポイントを設定するにはどのような操作手順が正しいですか。
サービス エンドポイントを介して Azure サービス リソースをセキュリティ保護するには 2 つのステップがあります。
1. Azure サービスに対するサービス エンドポイントを有効にします。
2. Azure サービスで VNet ACL を設定します。

最初のステップはネットワーク側の操作であり、2 番目のステップはサービス リソース側の操作です。 どちらのステップも、管理者ロールに付与されている RBAC のアクセス許可に基づいて、同じ管理者または異なる管理者が実行できます。 Azure サービス側で VNet ACL をセットアップする前に、まず仮想ネットワークに対してサービス エンドポイントを有効にすることをお勧めします。 そのためには、上で示した順序で手順を実行し、VNet サービス エンドポイントを設定する必要があります。

>[!NOTE]
> 上で説明されているどちらの操作も、許可される VNet とサブネットに Azure サービスへのアクセスを制限する前に、完了する必要があります。 ネットワーク側で Azure サービスに対してサービス エンドポイントを有効にするだけでは、制限付きアクセスは提供されません。 さらに、Azure サービス側で VNet ACL も設定する必要があります。

一部のサービス (SQL、CosmosDB など) では、**IgnoreMissingVnetServiceEndpoint** フラグを使用して上記の手順に対する例外を設定できます。 フラグを **True** に設定すると、ネットワーク側でサービス エンドポイントを設定する前に、Azure サービスで VNet ACL を設定できます。 Azure サービスでこのフラグが提供されているのは、Azure サービスで特定の IP ファイアウォールが構成されている場合に、ネットワーク側でサービス エンドポイントを有効にすると、パブリック IPv4 アドレスからプライベート アドレスへのソース IP アドレスの変更によって、接続が断たれる可能性があるためです。 ネットワーク側でサービス エンドポイントを設定する前に、Azure サービス側で VNet ACL を設定すると、接続が断たれるのを防ぐことができます。

### <a name="do-all-azure-services-reside-in-the-azure-virtual-network-provided-by-the-customer-how-does-vnet-service-endpoint-work-with-azure-services"></a>すべての Azure サービスは、顧客が提供する Azure 仮想ネットワーク内に存在しますか。 VNet サービス エンドポイントは Azure サービスでどのように動作しますか。

いいえ、すべての Azure サービスが顧客の仮想ネットワーク内に存在するわけではありません。 Azure Storage、Azure SQL、Azure Cosmos DB など、Azure データ サービスの大部分は、パブリック IP アドレス経由でアクセスできるマルチテナント サービスです。 Azure サービスの仮想ネットワーク統合について詳しくは、[こちら](virtual-network-for-azure-services.md)をご覧ください。 

VNet サービス エンドポイント機能 (ネットワーク側で VNet サービス エンドポイントを有効にして、Azure サービス側で適切な VNet ACL を設定する) を使用すると、Azure サービスへのアクセスは、許可されている VNet とサブネットからに制限されます。

### <a name="how-does-vnet-service-endpoint-provide-security"></a>VNet サービス エンドポイントでは、セキュリティはどのように提供されますか。

VNet サービス エンドポイント機能 (ネットワーク側で VNet サービス エンドポイントを有効にして、Azure サービス側で適切な VNet ACL を設定する) では、Azure サービスへのアクセスが許可された VNet とサブネットに制限されるので、ネットワーク レベルの セキュリティと Azure サービス トラフィックの分離が提供されます。 VNet サービス エンドポイントを使用するすべてのトラフィックは Microsoft のバックボーンを経由するので、パブリック インターネットからのもう 1 つの分離レイヤーが提供されます。 さらに、お客様は、Azure サービス リソースへのパブリック インターネット アクセスを完全に削除し、IP ファイアウォールと VNet ACL の組み合わせを経由する仮想ネットワークからのトラフィックだけを許可できるので、承認されていないアクセスから Azure サービス リソースが保護されます。      

### <a name="what-does-the-vnet-service-endpoint-protect---vnet-resources-or-azure-service"></a>VNet サービス エンドポイントで保護されるのは、VNet リソースですか、Azure サービスですか。
VNet サービス エンドポイントは、Azure サービス リソースを保護するのに役立ちます。 VNet リソースは、ネットワーク セキュリティ グループ (NSG) によって保護されます。

### <a name="is-there-any-cost-for-using-vnet-service-endpoints"></a>VNet サービス エンドポイントを使用するにはコストがかかりますか。

いいえ、VNet サービス エンドポイントを使用するための追加コストはありません。

### <a name="can-i-turn-on-vnet-service-endpoints-and-set-up-vnet-acls-if-the-virtual-network-and-the-azure-service-resources-belong-to-different-subscriptions"></a>仮想ネットワークと Azure サービス リソースが異なるサブスクリプションに属している場合、VNet サービス エンドポイントを有効にして、VNet ACL を設定できますか。

はい、できます。 仮想ネットワークと Azure サービス リソースのサブスクリプションは、同じでも異なっていてもかまいません。 唯一の要件は、仮想ネットワークと Azure サービス リソースの両方が、同じ Active Directory (AD) テナントの下に存在する必要があることです。

### <a name="can-i-turn-on-vnet-service-endpoints-and-set-up-vnet-acls-if-the-virtual-network-and-the-azure-service-resources-belong-to-different-ad-tenants"></a>仮想ネットワークと Azure サービス リソースが異なる AD テナントに属している場合、VNet サービス エンドポイントを有効にして、VNet ACL を設定できますか。
いいえ、VNet サービス エンドポイントと VNet ACL は、AD テナントをまたがってはサポートされません。

### <a name="can-an-on-premises-devices-ip-address-that-is-connected-through-azure-virtual-network-gateway-vpn-or-express-route-gateway-access-azure-paas-service-over-vnet-service-endpoints"></a>Azure Virtual Network ゲートウェイ (VPN) または ExpressRoute ゲートウェイを介して接続されているオンプレミス デバイスの IP アドレスでは、VNet サービス エンドポイント経由で Azure PaaS サービスにアクセスできますか。
既定では、仮想ネットワークからのアクセスに限定された Azure サービス リソースは、オンプレミスのネットワークからはアクセスできません。 オンプレミスからのトラフィックを許可する場合は、オンプレミスまたは ExpressRoute からのパブリック IP アドレス (通常は NAT) を許可する必要もあります。 これらの IP アドレスは、Azure サービス リソースの IP ファイアウォール構成を通じて追加できます。

### <a name="can-i-use-vnet-service-endpoint-feature-to-secure-azure-service-to-multiple-subnets-with-in-a-virtual-network-or-across-multiple-virtual-networks"></a>VNet サービス エンドポイントの機能を使用して、1 つの仮想ネットワーク内の複数のサブネットまたは複数の仮想ネットワークにまたがるサブネットに Azure サービスを限定できますか。
Azure サービスへのアクセスを 1 つの仮想ネットワーク内または複数の仮想ネットワークにまたがる複数のサブネットに限定するには、サブネットごとに個別にネットワーク側でサービス エンドポイントを有効にしてから、Azure サービス側で適切な VNet ACL を設定することによって、Azure サービス リソースへのアクセスをすべてのサブネットに限定します。
 
### <a name="how-can-i-filter-outbound-traffic-from-a-virtual-network-to-azure-services-and-still-use-service-endpoints"></a>仮想ネットワークから Azure サービスへの送信トラフィックをフィルター処理しながら、サービス エンドポイントを使用するには、どうすればよいですか。
仮想ネットワークから Azure サービスへのトラフィックを検査またはフィルター処理する場合は、仮想ネットワーク内にネットワーク仮想アプライアンスをデプロイします。 その後、ネットワーク仮想アプライアンスがデプロイされているサブネットにサービス エンドポイントを適用し、VNet ACL を使用してこのサブネットのみに Azure サービス リソースへのアクセスを限定します。 また、このシナリオは、ネットワーク仮想アプライアンス フィルタリングを使用して、ご利用の仮想ネットワークから Azure サービスへのアクセスを特定の Azure リソースにのみ制限する場合に役立ちます。 詳細については、[ネットワーク仮想アプライアンスを持つエグレス](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha)に関するページを参照してください。

### <a name="what-happens-when-you-access-an-azure-service-account-that-has-virtual-network-access-control-list-acl-enabled-from-outside-the-vnet"></a>仮想ネットワーク アクセス制御リスト (ACL) が有効になっている Azure サービス アカウントに、VNet の外部からアクセスするとどうなりますか。
HTTP 403 または HTTP 404 エラーが返されます。

### <a name="are-subnets-of-a-virtual-network-created-in-different-regions-allowed-to-access-an-azure-service-account-in-another-region"></a>他のリージョンで作成された仮想ネットワークのサブネットは、別のリージョンの Azure サービス アカウントにアクセスできますか。 
はい、ほとんどの Azure サービスについて、異なるリージョンで作成された仮想ネットワークから、VNet サービス エンドポイント経由で別のリージョンの Azure サービスにアクセスできます。 たとえば、Azure Cosmos DB アカウントが米国西部または米国東部にあり、仮想ネットワークが複数のリージョンにある場合、仮想ネットワークは Azure Cosmos DB にアクセスできます。 ストレージと SQL は例外であり、本質的にリージョン対応なので、仮想ネットワークと Azure サービスの両方が同じリージョンに存在する必要があります。
  
### <a name="can-an-azure-service-have-both-vnet-acl-and-an-ip-firewall"></a>Azure サービスで VNet ACL と IP ファイアウォールの両方を使用できますか。
はい、VNet ACL と IP ファイアウォールは共存できます。 両方の機能が相互に補完して、分離とセキュリティを確実にします。
 
### <a name="what-happens-if-you-delete-a-virtual-network-or-subnet-that-has-service-endpoint-turned-on-for-azure-service"></a>Azure サービス用のサービス エンドポイントが有効になっている仮想ネットワークまたはサブネットを削除するとどうなりますか。
VNet とサブネットの削除は独立した操作であり、サービス エンドポイントが Azure サービスに対して有効になっている場合でもサポートされます。 Azure サービスで VNet ACL が設定されている場合、それらの VNet とサブネットについては、VNet サービス エンドポイントが有効になっている VNet またはサブネットを削除すると、その Azure サービスに関連付けられている VNet ACL 情報が無効になります。
 
### <a name="what-happens-if-azure-service-account-that-has-vnet-service-endpoint-enabled-is-deleted"></a>VNet サービス エンドポイントが有効になっている Azure サービス アカウントを削除するとどうなりますか。
Azure サービス アカウントの削除は独立した操作であり、ネットワーク側でサービス エンドポイントが有効になっていて、Azure サービス側で VNet ACL が設定されている場合でもサポートされます。 

### <a name="what-happens-to-the-source-ip-address-of-a-resource-like-a-vm-in-a-subnet-that-has-vnet-service-endpoint-enabled"></a>VNet サービス エンドポイントが有効になったリソース (サブネット内の VM など) の送信元 IP アドレスはどうなりますか。
仮想ネットワーク サービス エンドポイントが有効である場合、仮想ネットワークのサブネット内にあるリソースの送信元 IP アドレスは、Azure サービスへのトラフィックで、パブリック IPV4 アドレスから Azure 仮想ネットワークのプライベート IP アドレスに切り替えられます。 Azure サービスで以前にパブリック IPV4 アドレスに対して設定されている特定の IP ファイアウォールが失敗する可能性があることに注意してください。 

### <a name="does-service-endpoint-route-always-take-precedence"></a>サービス エンドポイントのルートは常に優先されますか。
サービス エンドポイントでは、BGP ルートより優先されるシステム ルートが追加され、サービス エンドポイントのトラフィックに対して最適なルーティングが提供されます。 サービス エンドポイントでは常に、ユーザーの仮想ネットワークから Microsoft Azure のバックボーン ネットワーク上のサービスへのサービス トラフィックが、直接受け取られます。 Azure でのルートの選択方法の詳細については、「[仮想ネットワーク トラフィックのルーティング](virtual-networks-udr-overview.md)」を参照してください。
 
### <a name="how-does-nsg-on-a-subnet-work-with-service-endpoints"></a>サービス エンドポイントではサブネット上の NSG はどのように動作しますか。
Azure サービスに到達するには、NSG で送信接続を許可する必要があります。 NSG がすべてのインターネット送信トラフィックに対して開かれている場合、サービス エンドポイントのトラフィックは動作するはずです。 サービス タグを使用して、サービス IP にみに送信トラフィックを制限することもできます。  
 
### <a name="what-permissions-do-i-need-to-set-up-service-endpoints"></a>サービス エンドポイントを設定するにはどのようなアクセス許可が必要ですか。
サービス エンドポイントは、仮想ネットワークへの書き込みアクセス権を持つユーザーが仮想ネットワーク上で個別に構成できます。 Azure サービス リソースへのアクセスを VNet に限定するには、ユーザーが、追加されるサブネットのアクセス許可 "**Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action**" を持っている必要があります。 このアクセス許可は、既定では組み込みのサービス管理者のロールに含まれ、カスタム ロールを作成することで変更できます。 組み込みロールと、特定のアクセス許可を[カスタム ロール](https://docs.microsoft.com/azure/role-based-access-control/custom-roles?toc=%2fazure%2fvirtual-network%2ftoc.json)に割り当てる方法の詳細をご覧ください。
 

### <a name="can-i-filter-virtual-network-traffic-to-azure-services-allowing-only-specific-azure-service-resources-over-vnet-service-endpoints"></a>VNet サービス エンドポイント経由での Azure サービスへの仮想ネットワーク トラフィックをフィルター処理し、特定の Azure サービス リソースのみを許可することはできますか。 

仮想ネットワーク (VNet) のサービス エンドポイント ポリシーでは、サービス エンドポイント経由での Azure サービスへの仮想ネットワーク トラフィックをフィルター処理し、特定の Azure サービス リソースのみを許可することができます。 エンドポイント ポリシーでは、Azure サービスへの仮想ネットワーク トラフィックから詳細なアクセス制御が提供されます。 サービス エンドポイント ポリシーについて詳しくは、[こちら](virtual-network-service-endpoint-policies-overview.md)をご覧ください。

### <a name="does-azure-active-directory-azure-ad-support-vnet-service-endpoints"></a>Azure Active Directory (Azure AD) は VNet サービス エンドポイントをサポートしますか。

サービス エンドポイントは Azure Active Directory (Azure AD) によってネイティブにサポートされていません。 VNet サービス エンドポイントをサポートする Azure サービスの完全な一覧は[こちら](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)で確認できます。 サービス エンドポイントをサポートするサービスの下にリストされている "Microsoft.AzureActiveDirectory" タグは、ADLS Gen 1 へのサービス エンドポイントをサポートするために使用されます。 ADLS Gen 1 の場合、Azure Data Lake Storage Gen1 の仮想ネットワーク統合では、仮想ネットワークと Azure Active Directory (Azure AD) との間で仮想ネットワーク サービス エンドポイント セキュリティを利用して、アクセス トークン内に追加のセキュリティ要求が生成されます。 これらの要求は、ご利用の Data Lake Storage Gen1 アカウントに対して仮想ネットワークを認証し、アクセスを許可するために使用されます。 [Azure Data Lake Store Gen 1 VNet 統合]の詳細(../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json

### <a name="are-there-any-limits-on-how-many-vnet-service-endpoints-i-can-set-up-from-my-vnet"></a>VNet から設定できる VNet サービス エンドポイントの数に制限はありますか。
仮想ネットワーク内の VNet サービス エンドポイントの合計数に制限はありません。 Azure サービス リソース (Azure Storage アカウントなど) の場合、リソースへのアクセスに使用されるサブネットの数がサービスによって制限される場合があります。 制限の例を次の表に示します。 

|||
|---|---|
|Azure サービス| VNet ルールでの制限|
|Azure Storage| 100|
|Azure SQL| 128|
|Azure SQL Data Warehouse|  128|
|Azure KeyVault|    127|
|Azure Cosmos DB|   64|
|Azure Event Hub|   128|
|Azure Service Bus| 128|
|Azure Data Lake Store V1|  100|
 
>[!NOTE]
> 制限は、Azure サービスの判断で変更される可能性があります。 サービスについて詳しくは、それぞれのサービス ドキュメントをご覧ください。 




  



