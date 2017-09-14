---
title: "Site Recovery を使用した Hyper-V VM レプリケーションのネットワーク マッピングの計画 | Microsoft Docs"
description: "オンプレミスのデータセンターから Azure またはセカンダリ サイトに Hyper-V 仮想マシンをレプリケートするためのネットワーク マッピングを設定します。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: tysonn
ms.assetid: fcaa2f52-489d-4c1c-865f-9e78e000b351
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 05/23/2017
ms.author: raynew
ms.translationtype: Human Translation
ms.sourcegitcommit: db18dd24a1d10a836d07c3ab1925a8e59371051f
ms.openlocfilehash: b1b8b1ebc013a5dfb69528f9353369e18f84e61f
ms.contentlocale: ja-jp
ms.lasthandoff: 06/15/2017

---


# <a name="plan-network-mapping-for-hyper-v-vm-replication-with-site-recovery"></a>Site Recovery を使用した Hyper-V VM レプリケーションのネットワーク マッピングの計画



この記事は、[Azure Site Recovery サービス](site-recovery-overview.md)を使用した、Azure またはセカンダリ サイトへの Hyper-V VM のレプリケーション中のネットワーク マッピングを理解し、計画する際に役立ちます。

この記事に関するコメントは、この記事の末尾に投稿してください。また、技術的な質問は、[Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)に投稿してください。

## <a name="network-mapping-for-replication-to-azure"></a>Azure へのレプリケーションのネットワーク マッピング

Hyper-V VM (VMM で管理) を Azure にレプリケートするときに使用されるネットワーク マッピングです。 このネットワーク マッピングでは、ソース VMM サーバーの VM ネットワークと、ターゲット Azure ネットワークがマップされます。 マッピングでは次が行われます。

- **ネットワーク接続** - レプリケートされた Azure VM が、マップされたネットワークに確実に接続されます。 同じネットワーク上でフェールオーバーするすべてのマシンが、別々の復旧計画でフェールオーバーされた場合でも、相互に接続できます。
- **ネットワーク ゲートウェイ** - ターゲット Azure ネットワークでネットワーク ゲートウェイをセットアップすると、VM は、他のオンプレミスの仮想マシンに接続できます。

以下の点に注意してください。

- ソース VMM VM ネットワークを Azure 仮想ネットワークをマップします。
- フェールオーバー後、ソース ネットワークの Azure VM は、マップされたターゲット仮想ネットワークに接続されます。
- ソース VM ネットワークに追加された新しい VM は、レプリケーションが実行されると、マップされた Azure ネットワークに接続されます。
- ターゲット ネットワークに複数のサブネットがあり、そのサブネットのいずれかが、ソースの仮想マシンが配置されているサブネットと同じ名前である場合、フェールオーバー後、レプリカの仮想マシンはそのターゲット サブネットに接続します。
- ターゲットのサブネットで名前が一致するものがなければ、仮想マシンはネットワークの最初のサブネットに接続します。


## <a name="network-mapping-for-replication-to-a-secondary-datacenter"></a>セカンダリ データセンターへのレプリケーションのネットワーク マッピング

Hyper-V VM (System Center Virtual Machine Manager (VMM) で管理) をセカンダリ データセンターにレプリケートするときに使用されるネットワーク マッピングです。 このネットワーク マッピングでは、ソース VMM サーバーの VM ネットワークと、ターゲット VMM サーバーの VM ネットワークがマップされます。 マッピングでは次が行われます。

- **ネットワーク接続** - フェールオーバー後に VM が適切なネットワークに接続されます。 レプリカ VM は、ソース ネットワークにマップされているターゲット ネットワークに接続されます。
- **最適な配置** - Hyper-V ホスト サーバーのレプリカ VM が最適に配置されます。 レプリカ VM は、マップされた VM ネットワークにアクセスできるホストに配置されます。
- **ネットワーク マッピングを構成しない** - ネットワーク マッピングを構成しない場合、レプリカ VM は、フェールオーバー後に VM ネットワークに接続されません。

以下の点に注意してください。

- ネットワーク マッピングは、2 つの VMM サーバー上の VM ネットワーク間、または 2 つのサイトが同じサーバーによって管理されている場合は、単一の VMM サーバー上の VM ネットワーク間に構成できます。
- マッピングを正しく構成し、レプリケーションを有効にすると、1 次拠点にある VM はネットワークに接続され、ターゲットの拠点にあるそのレプリカは、マップされたネットワークに接続されます。
-
- VMM でネットワークが正しくセットアップされている場合、ネットワーク マッピング中に、ターゲット VM ネットワークを選択すると、ソース VM ネットワークを使用する VMM ソース クラウドが表示されます。また、保護に使用されるターゲット クラウド上の使用可能なターゲット VM ネットワークも表示されます。
- ターゲット ネットワークに複数のサブネットがあり、そのサブネットのいずれかが、ソースの仮想マシンが配置されているサブネットと同じ名前である場合、レプリカの仮想マシンは、フェールオーバー後にそのターゲット サブネットに接続されます。 ターゲットのサブネットで名前が一致するものがなければ、仮想マシンはネットワークの最初のサブネットに接続されます。



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



## <a name="next-steps"></a>次のステップ

[ネットワーク インフラストラクチャの計画](site-recovery-network-design.md)の詳細を確認する。

