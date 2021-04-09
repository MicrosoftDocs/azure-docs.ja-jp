---
title: Site Recovery を使用した Hyper-V (VMM 使用) のネットワーク マッピングについて
description: Azure Site Recovery を使用して、(VMM クラウドで管理されている) Hyper-V VM を Azure にディザスター リカバリーするためにネットワーク マッピングを設定する方法について説明します。
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: 6b68b4c943ec96620427978c2309f27e1fb1f217
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "74082557"
---
# <a name="prepare-network-mapping-for-hyper-v-vm-disaster-recovery-to-azure"></a>Hyper-V VM の Azure へのディザスター リカバリーのためにネットワーク マッピングを準備する


この記事は、[Azure Site Recovery サービス](site-recovery-overview.md)を使用して System Center Virtual Machine Manager (VMM) クラウド内の Hyper-V VM を Azure (またはセカンダリ サイト) にレプリケートする際に、ネットワーク マッピングを理解し、その準備をするうえで役立ちます。


## <a name="prepare-network-mapping-for-replication-to-azure"></a>Azure へのレプリケーションのネットワーク マッピングを準備する

Azure にレプリケートすると、ネットワーク マッピングによって、ソース VMM サーバーの VM ネットワークとターゲット Azure 仮想ネットワーク間がマップされます。 マッピングでは次が行われます。
-  **ネットワーク接続** - レプリケートされた Azure VM が、マップされたネットワークに確実に接続されます。 同じネットワーク上でフェールオーバーするすべてのマシンが、別々の復旧計画でフェールオーバーされた場合でも、相互に接続できます。
- **ネットワーク ゲートウェイ** - ターゲット Azure ネットワークでネットワーク ゲートウェイをセットアップすると、VM は、他のオンプレミスの仮想マシンに接続できます。

ネットワーク マッピングは次のように動作します。

- ソース VMM VM ネットワークを Azure 仮想ネットワークをマップします。
- フェールオーバー後、ソース ネットワークの Azure VM は、マップされたターゲット仮想ネットワークに接続されます。
- ソース VM ネットワークに追加された新しい VM は、レプリケーションが実行されると、マップされた Azure ネットワークに接続されます。
- ターゲット ネットワークに複数のサブネットがあり、そのサブネットのいずれかが、ソースの仮想マシンが配置されているサブネットと同じ名前である場合、フェールオーバー後、レプリカの仮想マシンはそのターゲット サブネットに接続します。
- ターゲットのサブネットで名前が一致するものがなければ、仮想マシンはネットワークの最初のサブネットに接続します。

## <a name="prepare-network-mapping-for-replication-to-a-secondary-site"></a>セカンダリ サイトに対するレプリケーションのネットワーク マッピングを準備する

セカンダリサイトにレプリケートする場合、ネットワーク マッピングによって、ソース VMM サーバー上の VM ネットワークとターゲット VMM サーバー上の VM ネットワーク間がマップされます。 マッピングでは次が行われます。

- **ネットワーク接続** - フェールオーバー後に VM が適切なネットワークに接続されます。 レプリカ VM は、ソース ネットワークにマップされているターゲット ネットワークに接続されます。
- **最適な VM 配置** - Hyper-V ホスト サーバーのレプリカ VM が最適に配置されます。 レプリカ VM は、マップされた VM ネットワークにアクセスできるホストに配置されます。
- **ネットワーク マッピングを構成しない** - ネットワーク マッピングを構成しない場合、レプリカ VM は、フェールオーバー後に VM ネットワークに接続されません。

ネットワーク マッピングは次のように動作します。

- ネットワーク マッピングは、2 つの VMM サーバー上の VM ネットワーク間、または 2 つのサイトが同じサーバーによって管理されている場合は、単一の VMM サーバー上の VM ネットワーク間に構成できます。
- マッピングを正しく構成し、レプリケーションを有効にすると、1 次拠点にある VM はネットワークに接続され、ターゲットの拠点にあるそのレプリカは、マップされたネットワークに接続されます。
- Site Recovery でネットワーク マッピング中に、ターゲット VM ネットワークを選択すると、ソース VM ネットワークを使用する VMM ソース クラウドが表示されます。また、保護に使用されるターゲット クラウド上の使用可能なターゲット VM ネットワークも表示されます。
- ターゲット ネットワークに複数のサブネットがあり、そのサブネットのいずれかが、ソースの VM が配置されているサブネットと同じ名前である場合、レプリカの仮想マシンは、フェールオーバー後にそのターゲット サブネットに接続されます。 ターゲット サブネットで名前が一致するものがなければ、VM はネットワークの最初のサブネットに接続されます。

## <a name="example"></a>例

このメカニズムを説明するために、次に例を示します。 ニューヨークとシカゴという 2 つの拠点がある組織を例にします。

**場所** | **VMM サーバー** | **VM ネットワーク** | **マップ先**
---|---|---|---
ニューヨーク | VMM-NewYork| VMNetwork1-NewYork | VMNetwork1-Chicago にマップされています
 |  | VMNetwork2-NewYork | マッピングなし
シカゴ | VMM-Chicago| VMNetwork1-Chicago | VMNetwork1-NewYork にマップされています
 | | VMNetwork2-Chicago | マッピングなし

次の点に注意してください。

- VMNetwork1-NewYork に接続されている VM に対してレプリカ VM が作成されると、そのレプリカ VM は VMNetwork1-Chicago に接続されます。
- VMNetwork2-NewYork または VMNetwork2-Chicago 対してレプリカ VM が作成されると、そのレプリカ VM はどのネットワークにも接続されません。

サンプル組織で VMM クラウドがどのように設定され、論理ネットワークがクラウドにどのように関連付けられるかを次に示します。

### <a name="cloud-protection-settings"></a>クラウドの保護設定

**保護されたクラウド** | **保護するクラウド** | **論理ネットワーク (ニューヨーク)**  
---|---|---
GoldCloud1 | GoldCloud2 |
SilverCloud1| SilverCloud2 |
GoldCloud2 | <p>NA</p><p></p> | <p>LogicalNetwork1-NewYork</p><p>LogicalNetwork1-Chicago</p>
SilverCloud2 | <p>NA</p><p></p> | <p>LogicalNetwork1-NewYork</p><p>LogicalNetwork1-Chicago</p>

### <a name="logical-and-vm-network-settings"></a>論理設定と VM ネットワークの設定

**場所** | **論理ネットワーク** | **関連付けられた VM ネットワーク**
---|---|---
ニューヨーク | LogicalNetwork1-NewYork | VMNetwork1-NewYork
シカゴ | LogicalNetwork1-Chicago | VMNetwork1-Chicago
 | LogicalNetwork2-Chicago | VMNetwork2-Chicago

### <a name="target-network-settings"></a>ターゲット ネットワークの設定

これらの設定に基づき、次の表は、ターゲット VM ネットワークを選択する場合に使用可能な選択肢を示します。

**Select** | **保護されたクラウド** | **保護するクラウド** | **使用可能なターゲット ネットワーク**
---|---|---|---
VMNetwork1-Chicago | SilverCloud1 | SilverCloud2 | 利用可能
 | GoldCloud1 | GoldCloud2 | 利用可能
VMNetwork2-Chicago | SilverCloud1 | SilverCloud2 | 使用不可
 | GoldCloud1 | GoldCloud2 | 利用可能


ターゲット ネットワークに複数のサブネットがあり、そのサブネットのいずれかが、ソースの仮想マシンが配置されているサブネットと同じ名前である場合、レプリカの仮想マシンは、フェールオーバー後にそのターゲット サブネットに接続されます。 ターゲットのサブネットで名前が一致するものがなければ、仮想マシンはネットワークの最初のサブネットに接続されます。


### <a name="failback-behavior"></a>フェールバックの動作

フェールバック (レプリケーションの反転) の場合の動作を確認するために、次の設定で VMNetwork1-NewYork が VMNetwork1-Chicago にマップされていると仮定します。


**VM** | **接続先の VM ネットワーク**
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



## <a name="next-steps"></a>次のステップ

- セカンダリ VMM サイトへのフェールオーバー後の IP アドレス指定の詳細については、[こちら](hyper-v-vmm-networking.md)を参照してください。
- Azure へのフェールオーバー後の IP アドレス指定について詳しくは、[こちら](concepts-on-premises-to-azure-networking.md)をご覧ください。
