---
title: Azure Site Recovery を使用したディザスター リカバリーと Azure へのフェールオーバー後に接続する IP アドレス指定の設定 | Microsoft Docs
description: Azure Site Recovery を使用したディザスター リカバリーとオンプレミスからのフェールオーバー後に、Azure VM へ接続する IP アドレス指定の設定方法を説明します
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 4/15/2019
ms.author: mayg
ms.openlocfilehash: 2e1cbb2446501d0afda29eba179e388b5a22e6a8
ms.sourcegitcommit: b8a8d29fdf199158d96736fbbb0c3773502a092d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2019
ms.locfileid: "59565684"
---
# <a name="set-up-ip-addressing-to-connect-to-azure-vms-after-failover"></a>フェールオーバー後に Azure VM に接続するための IP アドレス指定を設定する

この記事では、Azure へのレプリケーションとフェールオーバーのために [Azure Site Recovery](site-recovery-overview.md) サービスを使用した後、Azure VM に接続するためのネットワーク要件について説明します。

この記事では、次の内容について説明します。

> [!div class="checklist"]
> * 使用できる接続方式
> * レプリケートされた Azure VM に別の IP アドレスを使用する方法
> * フェールオーバー後に Azure VM の IP アドレスを保持する方法

## <a name="connecting-to-replica-vms"></a>レプリカ VM への接続

レプリケーションとフェールオーバー戦略を計画する際の重要な問題の 1 つとして、フェールオーバー後の Azure VM への接続方法があります。 レプリカの Azure VM のネットワーク戦略を設計する場合は、いくつかの選択肢があります。

- **異なる IP アドレスを使用する**:レプリケートされた Azure VM ネットワークに対して異なる IP アドレス範囲を使用することを選択できます。 このシナリオでは、VM はフェールオーバー後に新しい IP アドレスを取得するため、DNS の更新が必要になります。
- **同じ IP アドレスを保持する**:フェールオーバー後、Azure ネットワークに対して、プライマリのオンプレミス サイトと同じ IP アドレス範囲を使用することができます。 同じ IP アドレスを維持することで、フェールオーバー後のネットワーク関連の問題が少なくなるため、復旧が簡単になります。 ただし、Azure にレプリケートする場合は、フェールオーバー後に IP アドレスの新しい場所でルートを更新する必要があります。

## <a name="retaining-ip-addresses"></a>IP アドレスの保持

Site Recovery には、サブネットのフェールオーバーを使用して、Azure へのフェールオーバー時に固定 IP アドレスを保持する機能があります。

- サブネットのフェールオーバーでは、特定のサブネットはサイト 1 とサイト 2 のどちらかにのみ存在し、両方のサイトに同時に存在することはありません。
- フェールオーバーの発生時に IP アドレス空間を維持するには、サイトからサイトへサブネットを移動するよう、ルーター インフラストラクチャをプログラムで制御します。
- フェールオーバー時に、サブネットは、関連付けられた保護対象 VM と共に移動します。 主な欠点は、障害が発生した場合にサブネット全体を移動する必要があることです。


### <a name="failover-example"></a>フェールオーバーの例

架空の会社 Woodgrove Bank を使用した Azure へのフェールオーバーの例を見てみましょう。

- Woodgrove Bank は、オンプレミス サイトでそのビジネス アプリをホストします。 モバイル アプリは Azure でホストします。
- オンプレミス エッジ ネットワークと Azure 仮想ネットワークとの間には、VPN サイト間接続が存在します。 VPN 接続により、Azure 内の仮想ネットワークはオンプレミス ネットワークの拡張として表示されます。
- Woodgrove では、Site Recovery を使用して、オンプレミスのワークロードを Azure にレプリケートしたいと考えています。
  - Woodgrove には、ハード コーディングされた IP アドレスに依存するアプリがあるため、Azure にフェールオーバーした後、アプリの IP アドレスを保持する必要があります。
  - Azure で実行されているリソースが使用する IP アドレスの範囲は、 172.16.1.0/24、172.16.2.0/24 です。

![サブネットのフェールオーバー前](./media/site-recovery-network-design/network-design7.png)

**フェールオーバー前のインフラストラクチャ**


Woodgrove が IP アドレスを維持したまま VM を Azure にレプリケートできるようにするには、次のことを行う必要があります。


1. オンプレミス マシンのフェールオーバーの後に Azure VM が作成される Azure 仮想ネットワークを作成します。 これは、アプリケーションがシームレスにフェールオーバーできるように、オンプレミス ネットワークの拡張機能となります。
2. フェールオーバー前に、Site Recovery で、マシンのプロパティに同じ IP アドレスを割り当てます。 フェールオーバー後、Site Recovery は Azure 仮想マシンにこのアドレスを割り当てます。
3. フェールオーバーが実行され、同じ IP アドレスで Azure VM が作成されると、それらは [Vnet 間接続](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)を使用してネットワークに接続します。 このアクションはスクリプト化することができます。
4. 192.168.1.0/24 が Azure に移動したことを反映させるためには、ルートを変更する必要があります。


**フェールオーバー後のインフラストラクチャ**

![サブネットのフェールオーバー後](./media/site-recovery-network-design/network-design9.png)

#### <a name="site-to-site-connection"></a>サイト間接続

フェールオーバー後に、Woodgrove は、Vnet 間接続だけでなく サイト間 VPN 接続を設定できます。
- サイト間接続をセットアップする際、Azure ネットワークでは、IP アドレス範囲がオンプレミスの IP アドレス範囲と異なる場合のみ、トラフィックをオンプレミスの場所 (ローカル ネットワーク) にルーティングできます。 これは、Azure で拡張サブネットがサポートされていないためです。 そのため、オンプレミス上にサブネット 192.168.1.0/24 がある場合、Azure ネットワークにローカル ネットワーク 192.168.1.0/24 を追加することはできません。 これは予想される状況です。その理由は、サブネットにアクティブな VM がないこととサブネットがディザスター リカバリーの目的でのみ作成されることを Azure が認識しないことにあります。
- Azure ネットワークからネットワーク トラフィックを正しくルーティングできるようにするには、ネットワーク内のサブネットとローカル ネットワーク内のサブネットが競合してはなりません。




## <a name="assigning-new-ip-addresses"></a>新しい IP アドレスの割り当て

フェールオーバー後に IP アドレスを保持する必要がない場合の Azure ネットワーク インフラストラクチャの設定方法については、こちらの[ブログ記事](https://azure.microsoft.com/blog/2014/09/04/networking-infrastructure-setup-for-microsoft-azure-as-a-disaster-recovery-site/)を参照してください。 アプリケーションの説明から始まり、オンプレミスと Azure でのネットワークの設定方法を説明し、最後にフェールオーバーの実行に関する情報が示されています。

## <a name="next-steps"></a>次の手順
[フェールオーバーの実行](site-recovery-failover.md)
