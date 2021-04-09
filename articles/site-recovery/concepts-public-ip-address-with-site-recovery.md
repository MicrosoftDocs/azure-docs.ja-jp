---
title: Azure Site Recovery を使用したフェールオーバー後にパブリック IP アドレスを割り当てる
description: ディザスター リカバリーと移行のために、Azure Site Recovery と Azure Traffic Manager を使用してパブリック IP アドレスを設定する方法について説明します
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mayg
ms.openlocfilehash: 01c2f61dcf024e8c9dbbd5b2ee11a479b3c16305
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "86130283"
---
# <a name="set-up-public-ip-addresses-after-failover"></a>フェールオーバー後にパブリック IP アドレスを設定する

インターネット リソースから Azure リソースに入ってくる通信には、パブリック IP アドレスが使用されます。 また、Azure リソースからインターネットに出て行く通信や、Azure リソースから公開 Azure サービス (リソースに IP アドレスが割り当てられている Azure サービス) に出て行く通信にも、パブリック IP アドレスが使用されます。
- インターネットから、Azure Virtual Machines (VM)、Azure Application Gateway、Azure Load Balancer、Azure VPN Gateway などのリソースへの受信。 パブリック IP アドレスが割り当てられていないリソース (VM など) でも、インターネットから一部のリソースと通信することができます。ただし、そのリソースがロード バランサーのバックエンド プールの一部であり、ロード バランサーにパブリック IP アドレスが割り当てられている必要があります。
- 予測できる IP アドレスを使ったインターネットへの送信方向の接続。 たとえば、仮想マシンは、パブリック IP アドレスが割り当てられていなくてもインターネットに対して送信方向の通信を行うことができますが、既定でそのアドレスは Azure によって予測できないパブリック アドレスに変換されたネットワーク アドレスです。 パブリック IP アドレスをリソースに割り当てると、送信方向の接続に使われる IP アドレスがわかります。 アドレスは、予測することはできますが、選ばれている割り当て方法によっては変化する可能性があります。 詳細については、「[パブリック IP アドレスの作成](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address)」を参照してください。 Azure リソースからの送信接続の詳細については、[送信用接続の詳細](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に関する記事を参照してください。

Azure Resource Manager では、 パブリック IP アドレスは、独自のプロパティを持つリソースです。 パブリック IP アドレスのリソースは、次のリソースのいずれかと関連付けることができます。

* 仮想マシン ネットワーク インターフェイス
* インターネットに接続するロード バランサー
* VPN ゲートウェイ
* アプリケーション ゲートウェイ

この記事では、Site Recovery を使ってパブリック IP アドレスを使用する方法について説明しています。

## <a name="public-ip-address-assignment-using-recovery-plan"></a>復旧計画を使用したパブリック IP アドレスの割り当て

生産アプリケーションのパブリック IP アドレスは **フェールオーバー時に保持されません**。 フェールオーバー プロセスの一部として起動したワークロードは、ターゲット リージョンで利用可能なAzure パブリック IP リソースを割り当てる必要があります。 この手順は、手動でまたは復旧計画に基づき自動で行われます。 復旧計画は、マシンを復旧グループに収集します。 これは、体系的な回復プロセスを定義するのに役立ちます。 復旧計画は順序を指定し、各ステップで必要とされるアクションを自動化します。Azure へのフェールオーバーには Azure Automation の Runbook かスクリプトを使用します。

セットアップは次のとおりです。
- [復旧計画](../site-recovery/site-recovery-create-recovery-plans.md#create-a-recovery-plan)を作成し、計画の必要性に応じて、ワークロードをグループ化します。
- フェールオーバーした VMに、[Azure Automation runbook](../site-recovery/site-recovery-runbook-automation.md#customize-the-recovery-plan)スクリプトを使用したパブリック IP アドレスをアタッチする手順を追加して、計画をカスタマイズします。

 
## <a name="public-endpoint-switching-with-dns-level-routing"></a>DNS レベルのルーティングでのパブリック エンドポイントの切り替え

Azure Traffic Manager により DNS レベルのエンドポイント間のルーティングが可能になり、DR シナリオへの[RTOをドライブダウン](../site-recovery/concepts-traffic-manager-with-site-recovery.md#recovery-time-objective-rto-considerations)して、アシストできます。 

Traffic Manager でのフェールオーバーシナリオの詳細については:
1. Traffic Manager による[オンプレミスから Azure へのフェールオーバー](../site-recovery/concepts-traffic-manager-with-site-recovery.md#on-premises-to-azure-failover) 
2. Traffic Manager による[Azure フェールオーバー](../site-recovery/concepts-traffic-manager-with-site-recovery.md#azure-to-azure-failover) 

セットアップは次のとおりです。
- [Traffic Manager プロファイル](../traffic-manager/quickstart-create-traffic-manager-profile.md)を作成します。
- **優先度** ルーティングメゾットを活用しながら、2 つのエンドポイントを作成します ― ソース用の **プライマリ** と Azure 用の **フェールオーバー** です。 **Primary** には優先順位 1 を割り当て、**Failover** には優先順位 2 を割り当てます。
- **プライマリ** エンドポイントは、ソース環境がAzure の内部または外部にあるかどうかによって、[Azure](../traffic-manager/traffic-manager-endpoint-types.md#azure-endpoints)または [外部](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints)どちらにもなり得ます。
- **フェールオーバー** エンドポイントが **Azure** エンドポイントとして作成されます。 **静的パブリック IP アドレス** を使用し、これが災害時に Traffic Manager 用の外部に接続するエンドポイントとなります。

## <a name="next-steps"></a>次のステップ
- [Azure Site Recovery を使ったAzure Traffic Manager](../site-recovery/concepts-traffic-manager-with-site-recovery.md)について詳しく見る
- Traffic Manager の[ルーティング方法](../traffic-manager/traffic-manager-routing-methods.md)の詳細を確認する。
- アプリケーションのフェールオーバーを自動化するための[復旧計画](site-recovery-create-recovery-plans.md)の詳細を確認する。
