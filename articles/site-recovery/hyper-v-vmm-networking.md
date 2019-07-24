---
title: Azure Site Recovery を使用したフェールオーバー後にセカンダリ オンプレミス サイトに接続するように IP アドレス指定を設定する | Microsoft Docs
description: Azure Site Recovery を使用したディザスター リカバリーとフェールオーバーの後に、セカンダリ オンプレミス サイトの VM に接続するように IP アドレス指定を設定する方法について説明します。
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/27/2018
ms.author: raynew
ms.openlocfilehash: c93f80ba93e84c93ddf58b46b4bbcf98c53478d8
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/27/2018
ms.locfileid: "53794228"
---
# <a name="set-up-ip-addressing-to-connect-to-a-secondary-on-premises-site-after-failover"></a>フェールオーバー後にセカンダリ オンプレミス サイトに接続するように IP アドレス指定を設定する

System Center Virtual Machine Manager (VMM) クラウドの Hyper-V VM をセカンダリ サイトにフェールオーバーした後、レプリカ VM に接続できる必要があります。 このような処理を実行する場合にこの記事を参照してください。 

## <a name="connection-options"></a>接続オプション

フェールオーバー後、レプリカ VM の IP アドレス指定を処理するには、いくつかの方法があります。 

- **フェールオーバー後に同じ IP アドレスを保持する**:このシナリオでは、レプリケートされた VM はプライマリ VM と同じ IP アドレスを持ちます。 これにより、フェールオーバー後のネットワーク関連の問題は単純になりますが、いくつかのインフラストラクチャの作業が必要になります。
- **フェールオーバー後に異なる IP アドレスを使用する**:このシナリオでは、VM はフェールオーバー後に新しい IP アドレスを取得します。 
 

## <a name="retain-the-ip-address"></a>IP アドレスを保持する

プライマリ サイトの IP アドレスを保持する場合は、セカンダリ サイトへのフェールオーバー後に次の操作を実行します。

- プライマリ サイトとセカンダリ サイトの間に拡張サブネットを展開します。
- プライマリ サイトからセカンダリ サイトへの完全なサブネット フェールオーバーを実行します。 IP アドレスの新しい場所を示すようにルートを更新する必要があります。


### <a name="deploy-a-stretched-subnet"></a>拡張サブネットを展開する

拡張構成では、プライマリとセカンダリ サイトの両方で同時にサブネットを使用することができます。 拡張サブネットでは、マシンとその IP (レイヤー 3) アドレス構成をセカンダリ サイトに移動すると、ネットワークによって自動的にトラフィックが新しい場所にルーティングされます。 

- レイヤー 2 (データ リンク層) の場合、拡張 VLAN を管理できるネットワーク機器が必要になります。
- VLAN を拡張すると、障害ドメインが発生した場合、両方のサイトにまで拡張されます。 これは単一障害点になります。 このようなシナリオでは、ブロードキャスト ストームなどのインシデントを分離できません。 


### <a name="fail-over-a-subnet"></a>サブネットのフェールオーバー

サブネット全体のフェールオーバーを実行することで、実際に拡張せずに拡張サブネットの利点を得ることができます。 このソリューションでは、サブネットをソースまたはターゲット サイトで使用できますが、同時に両方で使用することはできません。

- フェールオーバーの発生時に IP アドレス空間を維持するために、サイト間でサブネットを移動するようにルーター インフラストラクチャをプログラムで制御できます。
- フェールオーバーが発生すると、サブネットは関連する VM と共に移動します。
- この方法の主な欠点は、障害が発生した場合にサブネット全体を移動する必要があることです。

#### <a name="example"></a>例

ここでは、完全なサブネットのフェールオーバー例を示します。 

- フェールオーバー前のプライマリ サイトでは、アプリケーションがサブネット 192.168.1.0/24 で実行されます。
- フェールオーバー時に、このサブネット内のすべての VM がセカンダリ サイトにフェールオーバーされ、その IP アドレスは保持されます。 
- サブネット 192.168.1.0/24 のすべての VM がセカンダリ サイトに移動されたことを反映するためにルートを変更する必要があります。

次の図に、フェールオーバーの前と後のサブネットを示します。


**フェールオーバー前**

![フェールオーバー前](./media/hyper-v-vmm-networking/network-design2.png)

**フェールオーバー後**

![フェールオーバー後](./media/hyper-v-vmm-networking/network-design3.png)

フェールオーバー後、Site Recovery は VM 上の各ネットワーク インターフェイスに IP アドレスを割り当てます。 このアドレスは、VM インスタンスごとに、関連ネットワークの静的 IP アドレス プールから割り当てられます。

- セカンダリ サイトの IP アドレス プールがソース サイトのものと同じである場合、Site Recovery はレプリカ VM に (ソース VM の) 同じ IP アドレスを割り当てます。 IP アドレスは VMM では予約されますが、Hyper-V ホストではフェールオーバー IP アドレスとして設定されません。 Hyper-V ホストのフェールオーバー IP アドレスは、フェールオーバーの直前に設定されます。
- 同じ IP アドレスが使用できない場合、Site Recovery はプールから別の使用可能な IP アドレスを割り当てます。
- VM が DHCP を使用する場合、Site Recovery は IP アドレスを管理しません。 セカンダリ サイト上の DHCP サーバーがソース サイトと同じ範囲からアドレスを割り当てられることを確認する必要があります。

### <a name="validate-the-ip-address"></a>IP アドレスを検証する

VM の保護を有効にしたら、以下のサンプル スクリプトを使用して、VM に割り当てられているアドレスを検証することができます。 この IP アドレスがフェールオーバー IP アドレスとして設定され、フェールオーバー時に VM に割り当てられます。

    ```
    $vm = Get-SCVirtualMachine -Name <VM_NAME>
    $na = $vm[0].VirtualNetworkAdapters>
    $ip = Get-SCIPAddress -GrantToObjectID $na[0].id
    $ip.address 
    ```

## <a name="use-a-different-ip-address"></a>別の IP アドレスを使用する

このシナリオでは、フェールオーバーする VM の IP アドレスが変更されます。 このソリューションの欠点は、メンテナンスが必要であることです。  場合によっては、DNS とキャッシュ エントリを更新する必要があります。 この場合、ダウンタイムが発生する可能性がありますが、次の方法で軽減できます。

- イントラネット アプリケーションに低 TTL 値を使用する。
- Site Recovery の復旧計画で以下のスクリプトを使用して、タイムリーに DNS サーバーが更新されるようにする。 動的 DNS 登録を使用する場合、スクリプトは必要ありません。

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

この例では、プライマリとセカンダリ サイト間で異なる IP アドレスがあり、プライマリまたは復旧サイトでホストされているアプリケーションにアクセス可能な第 3 のサイトがあります。

- フェールオーバー前は、アプリケーションはプライマリ サイトのサブネット 192.168.1.0/24 でホストされます。
- フェールオーバー後は、アプリケーションはセカンダリ サイトのサブネット 172.16.1.0/24 に構成されます。
- 3 つのサイトはすべて相互にアクセスできます。
- フェールオーバー後に、アプリは復旧サブネットで復元されます。
- このシナリオでは、サブネット全体をフェールオーバーする必要はなく、VPN またはネットワーク ルートを再構成するための変更も必要ありません。 フェールオーバーと何回かの DNS 更新で、アプリケーションにアクセスできる状態が維持されます。
- 動的更新を許可するように DNS が構成されている場合、VM は、フェールオーバー後に起動すると、新しい IP アドレスを使用して自身を登録します。

**フェールオーバー前**

![異なる IP アドレス - フェールオーバー前](./media/hyper-v-vmm-networking/network-design10.png)

**フェールオーバー後**

![異なる IP アドレス - フェールオーバー後](./media/hyper-v-vmm-networking/network-design11.png)


## <a name="next-steps"></a>次の手順

[フェールオーバーの実行](hyper-v-vmm-failover-failback.md)

