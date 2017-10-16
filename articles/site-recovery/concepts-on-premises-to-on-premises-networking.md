---
title: "Azure Site Recovery を使用したセカンダリ サイトへのフェールオーバー後の VM 接続 |Microsoft ドキュメント"
description: "Azure Site Recovery を使用したセカンダリ サイトへのフェールオーバー後の Azure VM への接続用のネットワーク ガイダンス。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 67d73590-185c-49b2-a097-597bf54747a9
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2017
ms.author: raynew
ms.openlocfilehash: 7b27fc568c77b44ab2366d297ca9e7685439143e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="vm-connectivity-after-failover-to-a-secondary-site"></a>セカンダリ サイトへのフェールオーバー後の VM 接続

デプロイの前提条件を確認したら、この記事を参照し、System Center Virtual Machine Manager (VMM) クラウドで管理されている Hyper-V 仮想マシン (VM) を、Azure Portal の [Azure Site Recovery](site-recovery-overview.md) を使用してセカンダリ サイトにレプリケートする場合のネットワークを計画します。 

この記事に関するコメントは、この記事の末尾、または [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)で投稿してください。

## <a name="overview"></a>概要

レプリケーションとフェールオーバー戦略を計画する際の重要な問題の 1 つとして、フェールオーバー後のレプリカへの接続方法があります。 これには次のいくつかのオプションがあります。 

- **異なる IP アドレスを使用する**: レプリケートされた VM に対して異なる IP アドレスを使用することを選択できます。 このシナリオでは、VM はフェールオーバー後に新しい IP アドレスを取得するため、DNS の更新が必要になります。
- **同じ IP アドレスを維持する**: レプリカ VM に同じ IP アドレスを使用することができます。 同じ IP アドレスを維持することで、フェールオーバー後のネットワーク関連の問題が少なくなるため、復旧が簡単になります。 

## <a name="retaining-ip-addresses"></a>IP アドレスの保持

セカンダリ サイトへのフェールオーバー後にプライマリ サイトからの IP アドレスを維持する場合は、完全なサブネット フェールオーバーを実行し、ルートを更新して IP アドレスの新しい場所を示すか、プライマリと復旧サイト間に拡張サブネットをデプロイすることができます。

### <a name="stretched-subnet"></a>拡張サブネット

拡張サブネットでは、プライマリとセカンダリ サイトの両方で同時にサブネットを使用することができます。 サーバーとその IP (レイヤー 3) 構成をセカンダリ サイトに移動する場合、トラフィックはネットワークによって新しい場所に自動的にルーティングされます。 

レイヤー 2 (データ リンク層) の場合、拡張 VLAN を管理できるネットワーク機器が必要になります。 さらに、VLAN を拡張すると、障害の可能性のあるドメインが両方のサイトに広がります (実質的に単一障害点になります)。 ただし、ブロードキャスト ストームが始まっても、それを分離できなくなる可能性は低いと考えられます。 


### <a name="subnet-failover"></a>Subnet failover

サブネットのフェールオーバーを実行することで、実際に拡張せずに拡張サブネットの利点を得ることができます。 このソリューションでは、サブネットをソースまたはターゲット サイトで使用できますが、同時に両方で使用することはできません。 フェールオーバーの発生時に IP アドレス空間を維持するために、サイト間でサブネットを移動するようにルーター インフラストラクチャをプログラムで制御できます。 フェールオーバーの発生後、サブネットは関連する VM と共に移動します。 主な欠点は、障害が発生した場合にサブネット全体を移動する必要があることです。

### <a name="example"></a>例

ここでは、完全なサブネットのフェールオーバー例を示します。 プライマリ サイトでは、アプリケーションがサブネット 192.168.1.0/24 で実行されます。 フェールオーバー時に、このサブネット内のすべての VM がセカンダリ サイトにフェールオーバーされ、その IP アドレスは保持されます。 サブネット 192.168.1.0/24 に属しているすべての VM がセカンダリ サイトに移動されたことを反映するためにルートを変更する必要があります。

次の図に、フェールオーバーの前と後のサブネットを示します。

- フェールオーバーの前は、サブネット 192.168.0.1/24 はソース サイトでアクティブで、フェールオーバー後にセカンダリ サイトでアクティブになります。
- プライマリ サイトと復旧サイトとの間、第 3 のサイトとプライマリ サイトとの間、さらに第 3 のサイトと復旧サイトとの間のルートに適宜変更を加える必要があります。

**フェールオーバー前**

![フェールオーバー前](./media/vmm-to-vmm-walkthrough-network/network-design2.png)

**フェールオーバー後**

![フェールオーバー後](./media/vmm-to-vmm-walkthrough-network/network-design3.png)

フェールオーバー後は次のようになります。

- Site Recovery は VM 上のネットワーク インターフェイスごとに IP アドレスを割り当てます。IP アドレスは、各 VMM インスタンスの関連するネットワークの静的 IP アドレス プールから取得されます。
- セカンダリ サイトの IP アドレス プールがソース サイトのものと同じである場合、Site Recovery はレプリカ VM に (ソース VM の) 同じ IP アドレスを割り当てます。 IP アドレスは VMM では予約されますが、Hyper-V ホストではフェールオーバー IP アドレスとして設定されません。 Hyper-V ホストのフェールオーバー IP アドレスは、フェールオーバーの直前に設定されます。
- 同じ IP アドレスが使用できない場合、Site Recovery はプールから別の使用可能な IP アドレスを割り当てます。
- VM が DHCP を使用する場合、Site Recovery は IP アドレスを管理しません。 セカンダリ サイト上の DHCP サーバーがソース サイトと同じ範囲からアドレスを割り当てられることを確認する必要があります。

### <a name="validate-the-ip-address"></a>IP アドレスを検証する

VM の保護を有効にしたら、以下のサンプル スクリプトを使用して、VM に割り当てられているアドレスを検証することができます。 同じ IP アドレスがフェールオーバー IP アドレスとして設定され、フェールオーバー時に VM に割り当てられます。

    ```
    $vm = Get-SCVirtualMachine -Name <VM_NAME>
    $na = $vm[0].VirtualNetworkAdapters>
    $ip = Get-SCIPAddress -GrantToObjectID $na[0].id
    $ip.address 
    ```

## <a name="changing-ip-addresses"></a>IP アドレスの変更

このシナリオでは、フェールオーバーする VM の IP アドレスが変更されます。 このソリューションの欠点は、メンテナンスが必要であることです。 通常は、レプリカ VM の起動後に DNS が更新されます。 ネットワークで DNS エントリを変更またはフラスターし、キャッシュ エントリを更新する必要がある場合があります。 その際にダウンタイムが生じることがあります。 ダウンタイムは次のように軽減できます。

- イントラネット アプリケーションに低 TTL 値を使用する。
- Site Recovery の復旧計画で以下のスクリプトを使用して、DNS サーバーを更新し、タイムリーに更新が行われるようにする。 動的 DNS 登録を使用する場合、スクリプトは必要ありません。

    ```
    param(
    string]$Zone,
    [string]$name,
    [string]$IP
    )
    $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
    $newrecord = $record.clone()
    $newrecord.RecordData[0].IPv4Address  =  $IP
    Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord
    ```
    
### <a name="example"></a>例 

プライマリと復旧サイト間で異なる IP アドレスの使用を予定しているとします。この例では、プライマリとセカンダリ サイト間で異なる IP アドレスがあり、プライマリまたは復旧サイトでホストされているアプリケーションにアクセス可能な第 3 のサイトがあります。

- フェールオーバーの前は、アプリはプライマリ サイトのサブネット 192.168.1.0/24 でホストされており、フェールオーバー後は、セカンダリ サイトのサブネット 172.16.1.0/24 に配置されるように構成されています。
- VPN 接続/ネットワーク ルートは、3 つのサイトすべてが相互にアクセスできるように適切に構成されています。
- フェールオーバー後に、アプリは復旧サブネットで復元されます。 このシナリオでは、サブネット全体をフェールオーバーする必要はなく、VPN またはネットワーク ルートを再構成するための変更も必要ありません。 フェールオーバーと何回かの DNS 更新で、アプリケーションにアクセスできる状態が維持されます。
- 動的更新を許可するように DNS が構成されている場合、VM は、フェールオーバー後に起動すると、新しい IP アドレスを使用して自身を登録します。

**フェールオーバー前**

![Different IP - Before Failover](./media/vmm-to-vmm-walkthrough-network/network-design10.png)

**フェールオーバー後**

![Different IP - After Failover](./media/vmm-to-vmm-walkthrough-network/network-design11.png)




