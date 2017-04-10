---
title: "Azure と Linux の VM ネットワークの概要 |Microsoft Docs"
description: "Azure と Linux の VM ネットワークの概要です。"
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: vlivech
manager: timlt
editor: 
ms.assetid: b5420e35-0463-4456-9803-6142db150f2e
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/25/2016
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: f5f611ce6944d37e0afcc9a14fa79d0356d55347
ms.lasthandoff: 03/17/2017


---
# <a name="azure-and-linux-vm-network-overview"></a>Azure と Linux の VM ネットワークの概要
## <a name="virtual-networks"></a>Virtual Networks
Azure 仮想ネットワーク (VNet) は、クラウド内のユーザー独自のネットワークを表すものです。 サブスクリプション専用に Azure クラウドが論理的に分離されています。 このネットワークでは、IP アドレス ブロック、DNS 設定、セキュリティ ポリシー、およびルート テーブルを完全に制御できます。 VNet をさらにサブネットに分割し、Azure IaaS 仮想マシン (VM) や クラウド サービス (PaaS ロール インスタンス) を起動できます。 また、Azure のいずれかの接続オプションを使用し、仮想ネットワークをオンプレミスのネットワークに接続することができます。 つまり、Azure が提供するエンタープライズ規模のメリットを享受しながら、IP アドレス ブロックを完全に制御して、ネットワークを Azure に拡張できます。

* [Virtual Network の概要](../virtual-network/virtual-networks-overview.md)

Azure CLI を使用して VNet を作成するには、次のチュートリアルに従ってください。

* [Azure CLI を使用して VNet を作成する方法](../virtual-network/virtual-networks-create-vnet-arm-cli.md)

## <a name="network-security-groups"></a>ネットワーク セキュリティ グループ
ネットワーク セキュリティ グループ (NSG) には、Virtual Network の VM インスタンスに対するネットワーク トラフィックを許可または拒否する一連のアクセス制御リスト (ACL) 規則が含まれています。 NSG は、サブネットまたはそのサブネット内の個々の VM インスタンスと関連付けることができます。 NSG がサブネットに関連付けられている場合、ACL 規則はそのサブネット内のすべての VM インスタンスに適用されます。 また、NSG を直接 VM に関連付けることにより、その個々の VM に対するトラフィックをさらに制限できます。

* [ネットワーク セキュリティ グループ (NSG) について](../virtual-network/virtual-networks-nsg.md)
* [Azure CLI で NSG を作成する方法](../virtual-network/virtual-networks-create-nsg-arm-cli.md)

## <a name="user-defined-routes"></a>ユーザー定義のルート
Azure で仮想マシン (VM) を仮想ネットワーク (VNet) に追加すると、VM どうしがネットワークを介して自動的に通信できることがわかります。 VM が異なるサブネットにあったとしても、ゲートウェイを指定する必要はありません。 VM からパブリック インターネットへの通信も同様であり、Azure からユーザー独自のデータセンターにハイブリッド接続が存在する場合は、オンプレミスのネットワークへの通信でも同様です。

* [ユーザー定義のルートおよび IP 転送とは](../virtual-network/virtual-networks-udr-overview.md)
* [Azure CLI で UDR を作成する](../virtual-network/virtual-network-create-udr-arm-cli.md)

## <a name="associating-a-fqdn-to-your-linux-vm"></a>Linux VM に FQDN を関連付ける
Resource Manager デプロイ モデルを使用して Azure Portal で仮想マシン (VM) を作成すると、仮想マシン用のパブリック IP リソースが自動的に作成されます。 この IP アドレスを使用して、VM にリモートでアクセスします。 ポータルの既定では完全修飾ドメイン名 (FQDN) が作成されませんが、VM の作成後に追加できます。

* [Azure ポータルでの完全修飾ドメイン名の作成](virtual-machines-linux-portal-create-fqdn.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="network-interfaces"></a>ネットワーク インターフェイス
ネットワーク インターフェイス (NIC) とは、仮想マシン (VM) と基礎となるソフトウェア ネットワーク間の相互接続です。 この記事では、ネットワーク インターフェイスとはなにか、Azure Resource Manager デプロイメント モデルでどのように使用されているかについて説明します。

* [仮想ネットワーク インターフェイス](../virtual-network/virtual-network-network-interface.md)

## <a name="virtual-nics-and-dns-labeling"></a>仮想 NIC と DNS ラベル付け
永続的にする必要があるサーバーが集団として扱われ、頻繁に破棄およびデプロイされる場合は、NIC で DNS ラベル付けを使用すると VNET 上で名前を永続化できます。  次のチュートリアルでは、静的 IP を持つ永続的な名前の NIC をセットアップします。

* [Azure CLI を使用して、完全な Linux 環境を作成する](virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="virtual-network-gateways"></a>仮想ネットワーク ゲートウェイ
仮想ネットワーク ゲートウェイは、Azure 仮想ネットワークとオンプレミスの場所の間のほか、Azure 内の仮想ネットワーク間 (VNet 間) のネットワーク トラフィックの送信にも使用されます。 VPN ゲートウェイを構成するときに、仮想ネットワーク ゲートウェイ、および仮想ネットワーク ゲートウェイの接続を作成して構成する必要があります。

* [VPN Gateway について](../vpn-gateway/vpn-gateway-about-vpngateways.md)

## <a name="internal-load-balancing"></a>内部負荷分散
Azure Load Balancer は、第 4 層 (TCP、UDP) のロード バランサーです。 ロード バランサーは、ロード バランサー セット内のクラウド サービスまたは仮想マシンの正常なサービス インスタンスに着信トラフィックを分散することによって高可用性を提供します。 さらに、Azure Load Balancer は、これらのサービスを複数のポート、複数の IP アドレス、またはその両方に提供できます。

* [Azure CLI を使用した内部ロード バランサーの作成](../load-balancer/load-balancer-get-started-internet-arm-cli.md)


