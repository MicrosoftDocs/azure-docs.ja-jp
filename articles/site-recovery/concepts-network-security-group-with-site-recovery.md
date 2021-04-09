---
title: Azure Site Recovery でのネットワーク セキュリティ グループ | Microsoft Docs
description: Azure Site Recovery でディザスター リカバリーと移行にネットワーク セキュリティ グループを使用する方法を説明します
author: Harsha-CS
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: harshacs
ms.openlocfilehash: 367aba09f84da1e227c08721077aa1b2132a62bf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92367976"
---
# <a name="network-security-groups-with-azure-site-recovery"></a>Azure Site Recovery でのネットワーク セキュリティ グループ

ネットワーク セキュリティ グループは、仮想ネットワーク内のリソースへのネットワーク トラフィックを制限するために使用されます。 [ネットワーク セキュリティ グループ (NSG)](../virtual-network/network-security-groups-overview.md#network-security-groups) には、ソースまたはターゲット IP アドレス、ポート、およびプロトコルを基に、受信/送信ネットワーク トラフィックを許可または拒否するセキュリティ規則の一覧が含まれています。

Resource Manager デプロイ モデルでは、NSG をサブネットまたは個別のネットワーク インターフェイスに関連付けることができます。 NSG をサブネットに関連付けた場合、そのサブネットに接続されているすべてのリソースにその NSG のルールが適用されます。 既に NSG を関連付けられているサブネット内の個別のネットワーク インターフェイスにも NSG を関連付けることにより、トラフィックをさらに制限することができます。

この記事では、Azure Site Recovery でネットワーク セキュリティ グループを使用する方法について説明します。

## <a name="using-network-security-groups"></a>ネットワーク セキュリティ グループの使用

個別のサブネットには、1 つの NSG を関連付けることができ、NSG をまったく関連付けなくてもかまいません。 個別のネットワーク インターフェイスにも、1 つの NSG を関連付けるか、またはまったく関連付けないことができます。 したがって、最初にサブネットに NSG を関連付けた後、別の NSG を仮想マシンのネットワーク インターフェイスに関連付けることによって、VM のトラフィックを効果的に二重に制限することができます。 このケースでの NSG ルールの適用は、トラフィックの方向と、適用されるセキュリティ規則の優先順位に依存します。

次のような 1 台の仮想マシンによる簡単な例を考えてみます。
-    仮想マシンは、**Contoso サブネット** の内部に配置されています。
-    **Contoso サブネット** は、**サブネット NSG** と関連付けられています。
-    VM ネットワーク インターフェイスは、さらに **VM NSG** と関連付けられています。

![NSG と Site Recovery](./media/concepts-network-security-group-with-site-recovery/site-recovery-with-network-security-group.png)

この例では、受信トラフィックについてはサブネット NSG が最初に評価されます。 サブネット NSG の通過を許可されたすべてのトラフィックは、次に VM NSG によって評価されます。 送信トラフィックに対しては逆の順序が適用され、VM NSG が最初に評価されます。 VM NSG の通過を許可されたすべてのトラフィックは、次にサブネット NSG によって評価されます。

これにより、きめ細かくセキュリティ ルールを適用できます。 たとえば、サブネットの下にある少数のアプリケーション VM (フロントエンド VM など) への受信インターネット アクセスは許可しながら、他の VM (データベース VM や他のバックエンド VM など) への受信インターネット アクセスは制限することができます。 この場合、サブネット NSG では比較的緩いルールを設定してインターネット トラフィックを許可し、VM NSG ではアクセスを拒否することによって特定の VM へのアクセスを制限できます。 送信トラフィックに対しても同じことが当てはまります。

このような NSG 構成を設定するときは、[セキュリティ規則](../virtual-network/network-security-groups-overview.md#security-rules)に適切な優先順位を適用することが重要です。 規則は、優先順位に従って処理され、数値が小さいほど優先順位が高いために、大きい数値の前に小さい数値が処理されます。 トラフィックが規則に一致すると、処理が停止します。 この結果、優先順位低く (数値が大きい)、優先順位が高い規則と同じ属性を持つ規則は処理されません。

ネットワーク セキュリティ グループが、ネットワーク インターフェイスとサブネットの両方に適用されているかわからない場合があります。 ネットワーク インターフェイスの[有効なセキュリティ規則](../virtual-network/virtual-network-network-interface.md#view-effective-security-rules)を表示すると、ネットワーク インターフェイスに適用されている規則の集計を簡単に確認できます。 [Azure Network Watcher](../network-watcher/network-watcher-monitoring-overview.md) の [IP フローの確認](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md)機能を使って、ネットワーク インターフェイスの受信/送信が許可されているかどうかを確認することもできます。 このツールを使うと、通信が許可されているか、どのネットワーク セキュリティ規則でトラフィックが許可/拒否されているかを確認できます。

## <a name="on-premises-to-azure-replication-with-nsg"></a>NSG でのオンプレミスから Azure へのレプリケーション

Azure Site Recovery により、オンプレミスの [HYPER-V 仮想マシン](hyper-v-azure-architecture.md)、[VMware 仮想マシン](vmware-azure-architecture.md)、および[物理サーバー](physical-azure-architecture.md)のディザスター リカバリーと Azure への移行が可能になります。 オンプレミスから Azure へのすべてのシナリオで、レプリケーション データは Azure Storage アカウントに送信され、格納されます。 レプリケーション中に仮想マシンの料金は発生しません。 Azure へのフェールオーバーを実行すると、Site Recovery は Azure IaaS 仮想マシンを自動的に作成します。

Azure へのフェールオーバー後に VM が作成された場合、NSG を使って仮想ネットワークおよび VM へのネットワーク トラフィックを制限することができます。 Site Recovery は、フェールオーバー操作の一部としては NSG を作成しません。 フェールオーバーを開始する前に、必要な Azure NSG を作成することをお勧めします。 そうすれば、Site Recovery の強力な[復旧計画](site-recovery-create-recovery-plans.md)で自動化スクリプトを使って、フェールオーバーの間にフェールオーバーされた VM に NSG を自動的に関連付けることができます。

たとえば、フェールオーバー後の VM 構成が上で詳しく説明した[シナリオ例](concepts-network-security-group-with-site-recovery.md#using-network-security-groups)に似ている場合、次のようになります。
-    ターゲット Azure リージョンでの DR 計画の一部として、**Contoso VNet** および **Contoso サブネット** を作成できます。
-    また、同じ DR 計画の一部として、**サブネット NSG** と **VM NSG** の両方を作成して構成することもできます。
-    NSG とサブネットの両方が既に使用できるので、**サブネット NSG** を **Contoso サブネット** とすぐに関連付けることができます。
-    **VM NSG** は、復旧計画を使用するフェールオーバーの間に VM と関連付けることができます。

NSG を作成して構成した後は、[テスト フェールオーバー](site-recovery-test-failover-to-azure.md)を実行して、スクリプト化した NSG の関連付けとフェールオーバー後の VM の接続を確認することをお勧めします。

## <a name="azure-to-azure-replication-with-nsg"></a>NSG を使用した Azure から Azure へのレプリケーション

Azure Site Recovery によって、[Azure 仮想マシン](azure-to-azure-architecture.md)のディザスター リカバリーが可能になります。 Azure VM のレプリケーションを有効にすると、Site Recovery は、ターゲット リージョンにレプリカ仮想ネットワーク (サブネットとゲートウェイ サブネットを含む) を作成し、ソースとターゲットの仮想ネットワーク間に必要なマッピングを作成することができます。 また、ターゲット側のネットワークとサブネットを事前に作成して、レプリケーションを有効化する際に同じものを使用することもできます。 Site Recovery が[フェールオーバー](azure-to-azure-tutorial-failover-failback.md)の前にターゲット Azure リージョンに VM を作成することはありません。

Azure VM レプリケーションでは、ソース Azure リージョンの NSG ルールで、レプリケーション トラフィックに対して[送信接続](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags)を許可する必要があります。 この [NSG 構成の例](azure-to-azure-about-networking.md#example-nsg-configuration)を使用すると、これらの必要なルールもテストして確認することができます。

Site Recovery は、フェールオーバー操作の一部としては NSG を作成またはレプリケートしません。 フェールオーバーを開始する前に、ターゲット Azure リージョンに必要な NSG を作成しておくことをお勧めします。 そうすれば、Site Recovery の強力な[復旧計画](site-recovery-create-recovery-plans.md)で自動化スクリプトを使って、フェールオーバーの間にフェールオーバーされた VM に NSG を自動的に関連付けることができます。

前に説明した[シナリオ例](concepts-network-security-group-with-site-recovery.md#using-network-security-groups)について考えます。
-    VM に対してレプリケーションが有効になっていると、Site Recovery はターゲット Azure リージョン上に **Contoso VNet** と **Contoso サブネット** のレプリカを作成できます。
-    ターゲット Azure リージョンに **サブネット NSG** と **VM NSG** の目的のレプリカを作成し (たとえば、それぞれ **ターゲット サブネット NSG** および **ターゲット VM NSG** という名前にします)、ターゲット リージョンで必要な追加規則に対応できます。
-    NSG とサブネットの両方が既に使用できるので、**ターゲット サブネット NSG** をターゲット リージョン サブネットとすぐに関連付けることができます。
-    **ターゲット VM NSG** は、復旧計画を使用するフェールオーバーの間に VM と関連付けることができます。

NSG を作成して構成した後は、[テスト フェールオーバー](azure-to-azure-tutorial-dr-drill.md)を実行して、スクリプト化した NSG の関連付けとフェールオーバー後の VM の接続を確認することをお勧めします。

## <a name="next-steps"></a>次のステップ
-    [ネットワーク セキュリティ グループ](../virtual-network/network-security-groups-overview.md#network-security-groups)の詳細を確認する。
-    NSG の[セキュリティ規則](../virtual-network/network-security-groups-overview.md#security-rules)の詳細を確認する。
-    NSG の[効果的なセキュリティ規則](../virtual-network/diagnose-network-traffic-filter-problem.md)の詳細を確認する。
-    アプリケーションのフェールオーバーを自動化するための[復旧計画](site-recovery-create-recovery-plans.md)の詳細を確認する。