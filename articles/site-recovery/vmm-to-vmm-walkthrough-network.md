---
title: "Azure Site Recovery を使用したセカンダリ VMM サイトへの Hyper-V レプリケーション用のネットワークを計画する | Microsoft Docs"
description: "この記事では、Azure Site Recovery を使用して Hyper-V VM をセカンダリ System Center VMM サイトにレプリケートする場合のネットワークの計画について説明します。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 095f2d73-994e-4fc0-96f2-e03a7d72e492
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: fff84ee45818e4699df380e1536f71b2a4003c71
ms.openlocfilehash: a1f3f6e6cba074647195e2b0cbcdc7b4f3dec475
ms.contentlocale: ja-jp
ms.lasthandoff: 08/01/2017

---
# <a name="step-3-plan-networking-for-hyper-v-vm-replication-to-a-secondary-vmm-site"></a>手順 3: セカンダリ VMM サイトへの Hyper-V VM レプリケーション用のネットワークを計画する

デプロイの前提条件を確認したら、この記事を参照し、System Center Virtual Machine Manager (VMM) クラウドで管理されている Hyper-V 仮想マシン (VM) を、Azure Portal の [Azure Site Recovery](site-recovery-overview.md) を使用してセカンダリ サイトにレプリケートする場合のネットワークを計画します。 

この記事に関するコメントは、この記事の末尾、または [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)で投稿してください。

## <a name="network-mapping-overview"></a>ネットワーク マッピングの概要

ネットワーク マッピングは、Hyper-V VM (VMM で管理) をセカンダリ データセンターにレプリケートするときに使用されます。 このネットワーク マッピングでは、ソース VMM サーバーの VM ネットワークと、ターゲット VMM サーバーの VM ネットワークがマップされます。 マッピングでは次が行われます。

- **ネットワーク接続** - フェールオーバー後に VM が適切なネットワークに接続されます。 レプリカ VM は、ソース ネットワークにマップされているターゲット ネットワークに接続されます。
- **最適な配置** - Hyper-V ホスト サーバーのレプリカ VM が最適に配置されます。 レプリカ VM は、マップされた VM ネットワークにアクセスできるホストに配置されます。
- **ネットワーク マッピングを構成しない** - ネットワーク マッピングを構成しない場合、レプリカ VM は、フェールオーバー後に VM ネットワークに接続されません。


### <a name="example"></a>例

このメカニズムを説明するために、次に例を示します。 ニューヨークとシカゴという 2 つの拠点がある組織を例にします。

**場所** | **VMM サーバー** | **VM ネットワーク** | **マップ先**
---|---|---|---
ニューヨーク | VMM-NewYork| VMNetwork1-NewYork | VMNetwork1-Chicago にマップされています
 |  | VMNetwork2-NewYork | マッピングなし
シカゴ | VMM-Chicago| VMNetwork1-Chicago | VMNetwork1-NewYork にマップされています
 | | VMNetwork1-Chicago | マッピングなし

次の点に注意してください。

- VMNetwork1-NewYork に接続されている仮想マシンに対してレプリカ仮想マシンが作成されると、そのレプリカ仮想マシンは VMNetwork1-Chicago に接続されます。
- VMNetwork2-NewYork または VMNetwork2-Chicago 対してレプリカ仮想マシンが作成されると、そのレプリカ仮想マシンはどのネットワークにも接続されません。

サンプル組織で VMM クラウドがどのように設定され、論理ネットワークがクラウドにどのように関連付けられるかを次に示します。

#### <a name="cloud-protection-settings"></a>クラウドの保護設定

**保護されたクラウド** | **保護するクラウド** | **論理ネットワーク (ニューヨーク)**  
---|---|---
GoldCloud1 | GoldCloud2 |
SilverCloud1| SilverCloud2 |
GoldCloud2 | <p>該当なし</p><p></p> | <p>LogicalNetwork1-NewYork</p><p>LogicalNetwork1-Chicago</p>
SilverCloud2 | <p>該当なし</p><p></p> | <p>LogicalNetwork1-NewYork</p><p>LogicalNetwork1-Chicago</p>

#### <a name="logical-and-vm-network-settings"></a>論理設定と VM ネットワークの設定

**場所** | **論理ネットワーク** | **関連付けられた VM ネットワーク**
---|---|---
ニューヨーク | LogicalNetwork1-NewYork | VMNetwork1-NewYork
シカゴ | LogicalNetwork1-Chicago | VMNetwork1-Chicago
 | LogicalNetwork2-Chicago | VMNetwork2-Chicago

#### <a name="target-network-settings"></a>ターゲット ネットワークの設定

これらの設定に基づき、次の表は、ターゲット VM ネットワークを選択する場合に使用可能な選択肢を示します。

**選択肢** | **保護されたクラウド** | **保護するクラウド** | **使用可能なターゲット ネットワーク**
---|---|---|---
VMNetwork1-Chicago | SilverCloud1 | SilverCloud2 | 使用可能
 | GoldCloud1 | GoldCloud2 | 使用可能
VMNetwork2-Chicago | SilverCloud1 | SilverCloud2 | 使用できません。
 | GoldCloud1 | GoldCloud2 | 使用可能


ターゲット ネットワークに複数のサブネットがあり、そのサブネットのいずれかが、ソースの仮想マシンが配置されているサブネットと同じ名前である場合、レプリカの仮想マシンは、フェールオーバー後にそのターゲット サブネットに接続されます。 ターゲットのサブネットで名前が一致するものがなければ、仮想マシンはネットワークの最初のサブネットに接続されます。


#### <a name="failback-behavior"></a>フェールバックの動作

フェールバック (レプリケーションの反転) の場合の動作を確認するために、次の設定で VMNetwork1-NewYork が VMNetwork1-Chicago にマップされていると仮定します。


**仮想マシン** | **接続先の VM ネットワーク**
---|---
VM1 | VMNetwork1-NewYork
VM2 (VM1 のレプリカ) | VMNetwork1-Chicago

これらの設定で、いくつかの可能なシナリオでの動作を確認しましょう。

**シナリオ** | **結果**
---|---
フェールオーバー後に VM-2 のネットワークのプロパティの変更がない。 | VM-1 は、ソース ネットワークに接続されたままです。
VM-2 のネットワークのプロパティがフェールオーバー後に変更され、VM-2 が切断される。 | VM-1 が切断されます。
VM-2 のネットワークのプロパティがフェールオーバー後に変更され、VM-2 が VMNetwork2-Chicago に接続される。 | VMNetwork2-Chicago がマップされていない場合、VM-1 は切断されます。
VMNetwork1-Chicago のネットワークのマッピングが変更される。 | VM-1 は VMNetwork1-Chicago にマップされたネットワークに接続されます。



## <a name="prepare-for-network-mapping"></a>ネットワーク マッピングを準備する

1. ソースおよびターゲット VMM サーバー上で、論理ネットワークがソースおよびターゲット クラウドに関連付けられている必要があります。 
2. ソースおよびターゲット サーバー上で、VM ネットワークが論理ネットワークにリンクされている必要があります。
3. ソースの場所にある Hyper-V ホスト上の VM を、ソース VM ネットワークにリンクする必要があります。 単一の VMM サーバーのみを使用する場合は、同じサーバーに VM ネットワーク間のマッピングを構成できます。

Site Recovery のデプロイ時にネットワーク マッピングをセットアップする場合は、次のようになります。

- ネットワーク マッピングをセットアップし、ターゲット VM ネットワークを選択すると、ソース VM ネットワークを使用する VMM ソース クラウドが表示されます。また、ターゲット クラウド上の使用可能なターゲット VM ネットワークも表示されます。
- - マッピングが正しく構成され、レプリケーションが有効になっている場合は、ソース VM がそのソース VM ネットワークに接続され、ターゲットの場所にあるそのレプリカがマップされた VM ネットワークに接続されます。
- ターゲット ネットワークに複数のサブネットがあり、そのサブネットのいずれかが、ソースの仮想マシンが配置されているサブネットと同じ名前である場合、レプリカの仮想マシンは、フェールオーバー後にそのターゲット サブネットに接続されます。 ターゲット サブネットで名前が一致するものがなければ、VM はネットワークの最初のサブネットに接続されます。

## <a name="connect-to-vms-after-failover"></a>フェールオーバー後に VM に接続する

レプリケーションとフェールオーバー戦略を計画する際の重要な問題の 1 つとして、フェールオーバー後のレプリカへの接続方法があります。 これには次のいくつかのオプションがあります。 

- **異なる IP アドレスを使用する**: レプリケートされた VM に対して異なる IP アドレスを使用することを選択できます。 このシナリオでは、VM はフェールオーバー後に新しい IP アドレスを取得するため、DNS の更新が必要になります。
- **同じ IP アドレスを維持する**: レプリカ VM に同じ IP アドレスを使用することができます。 同じ IP アドレスを維持することで、フェールオーバー後のネットワーク関連の問題が少なくなるため、復旧が簡単になります。 

## <a name="retain-ip-addresses"></a>IP アドレスを維持する

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



## <a name="next-steps"></a>次のステップ

[手順 4: VMM と Hyper-V を準備する](vmm-to-vmm-walkthrough-vmm-hyper-v.md)方法に関するページに進みます。



