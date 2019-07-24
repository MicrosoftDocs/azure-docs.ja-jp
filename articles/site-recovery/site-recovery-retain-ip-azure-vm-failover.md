---
title: Azure Site Recovery を使用した Azure VM フェールオーバー時に IP アドレスを保持する | Microsoft Docs
description: ディザスター リカバリーのために、Azure Site Recovery を使用して Azure VM をセカンダリ リージョンへフェールオーバーする際に、IP アドレスを保持する方法について説明します
ms.service: site-recovery
ms.date: 4/9/2019
author: mayurigupta13
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: 618d60417aa6b582eaef94bf75dcf16c74750f83
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59788873"
---
# <a name="retain-ip-addresses-during-failover"></a>フェールオーバー時に IP アドレスを保持する

[Azure Site Recovery](site-recovery-overview.md) では、VM を別の Azure リージョンにレプリケートし、障害が発生した場合にフェールオーバーして、正常な状態に戻ったらプライマリ リージョンにフェールバックすることで、Azure VM のディザスター リカバリーが可能です。

フェールオーバー中に、ターゲット リージョンで、ソース リージョンと同じ IP アドレス指定を維持したい場合があります。

- 既定では、Azure VM のディザスター リカバリーを有効にすると、Site Recovery によって、ソース リソースの設定に基づいてターゲット リソースが作成されます。 Azure VM が静的 IP アドレスで構成されている場合、Site Recovery は、使用中でなければ同じ IP アドレスをターゲット VM にプロビジョニングしようとします。 Site Recovery でアドレス指定がどのように処理されるかの詳しい説明については、[この記事](azure-to-azure-network-mapping.md#set-up-ip-addressing-for-target-vms)を参照してください。
- シンプルなアプリケーションの場合は、既定の構成で十分です。 複雑なアプリの場合は、フェールオーバー後に、接続が予期した通り確実に機能するように、追加のリソースをプロビジョニングする必要が生じることもあります。


この記事では、より複雑なシナリオの例で、IP アドレスを保持する例をいくつか示します。 以下のような例が含まれています。

- Azure ですべてのリソース実行されている会社のフェールオーバー
- ハイブリッド展開で、オンプレミスと Azure の両方でリソースが実行されている会社のフェールオーバー

## <a name="resources-in-azure-full-failover"></a>Azure 内のリソース: 完全フェールオーバー

A 社は、Azure ですべてのアプリを実行しています。

### <a name="before-failover"></a>フェールオーバー前

フェールオーバー前のアーキテクチャを次に示します。

- 会社 A は、ソースとターゲットの Azure リージョン内に同一のネットワークとサブネットを設けています。
- 目標復旧時間 (RTO) を短縮するため、この会社は、SQL Server Always On やドメイン コント ローラーなどのレプリカ ノードを使用しています。ソース リージョンとターゲット リージョンの間に VPN のサイト間接続を確立できるように、これらのレプリカ ノードは、ターゲット リージョン内では異なる VNet 内に置かれています。 これは、ソースとターゲットで同じ IP アドレス空間が使用されている場合は不可能です。  
- フェールオーバー前のネットワーク アーキテクチャは次のとおりです。
    - プライマリ リージョンは Azure 東アジアです
        - 東アジアに、アドレス空間 10.1.0.0/16 の VNet (**Source VNet**) があります。
        - 東アジアでは、VNet 内の次の 3 つのサブネットにわたってワークロードを分割しています。
            - **サブネット 1**:10.1.1.0/24
            - **サブネット 2**:10.1.2.0/24
            - **サブネット 3**:10.1.3.0/24
    - セカンダリ (ターゲット) リージョンは Azure 東南アジアです
        - 東南アジアには、**Source VNet** と同一の復旧用 VNet (**Recovery VNet**) があります。
        - 東南アジアには、アドレス空間が 10.2.0.0/16 の、追加の VNet (**Azure VNet**) があります。
        - **Azure VNet** には、アドレス空間が 10.2.4.0/24 のサブネット (**Subnet 4**) が含まれています。
        - SQL Server Always On やドメイン コントローラーなどのレプリカ ノードは、**Subnet 4** 内に配置されています。
    - **Source VNet** と **Azure VNet** は、VPN サイト間接続で接続されています。
    - **Recovery VNet** は他の仮想ネットワークには接続されていません。
    - **A 社**は、レプリケートされる項目について、ターゲット IP アドレスの割り当てと確認を行っています。 ターゲット IP は、各 VM のソース IP と同じです。

![完全フェールオーバー前の Azure 内のリソース](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-before-failover2.png)

### <a name="after-failover"></a>フェールオーバー後

ソース リージョンで障害が発生した場合、A 社は、すべてのリソースをターゲット リージョンにフェールオーバーできます。

- フェールオーバーの前にターゲット IP アドレスが既に用意されているため、A 社はフェールオーバーを連携させて、**Recovery VNet** および **Azure VNet** 間のフェールオーバー後に接続を自動的に確立できます。 それを示したのが次の図です。
- ターゲット リージョンにある 2 つの VNet (**Recovery VNet** と **Azure VNet**) 間の接続は、アプリの要件に応じて、フェールオーバー前、フェールオーバー中 (中間ステップとして)、またはフェールオーバー後に確立できます。
  - 会社は[復旧計画](site-recovery-create-recovery-plans.md)を使用して、接続がいつ確立されるかを指定できます。
  - VNet ピアリングまたはサイト間 VPN を使用して Vnet 間を接続できます。
      - VNet ピアリングは VPN ゲートウェイを使用せず、さまざまな制約があります。
      - VNet ピアリングの[料金](https://azure.microsoft.com/pricing/details/virtual-network)は、VNet 間 VPN Gateway の[料金](https://azure.microsoft.com/pricing/details/vpn-gateway)とは計算方法が異なります。 フェールオーバーについては、一般に、予測不能のネットワーク インシデントを最小限にするため、接続の種類を含め、ソース ネットワークと同じ接続方法を使用することをお勧めします。

    ![Azure 内のリソースの完全フェールオーバー](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-full-region-failover2.png)



## <a name="resources-in-azure-isolated-app-failover"></a>Azure 内のリソース: 分離されているアプリのフェールオーバー

アプリ レベルでフェールオーバーすることが必要な場合があります。 たとえば、専用サブネット内に置かれた特定のアプリまたはアプリ階層をフェールオーバーする場合です。

- このシナリオでは、IP アドレス指定は保持できますが、接続性の不整合が発生する可能性が高まるため、一般に推奨されません。 同じ Azure VNet 内の、他のサブネットとの接続も失われます。
- サブネット レベルでアプリをフェールオーバーする場合、より適切なのは、フェールオーバーには異なるターゲット IP アドレスを使用する方法 (ソース VNet で他のサブネットへの接続性が必要な場合) か、ソース リージョン内で各アプリを独自の専用 VNet 内に分離する方法です。 後者の方法では、ソース リージョンでネットワーク間に接続性を確立し、ターゲット リージョンにフェールオーバーするときに同じ動作をエミュレートすることができます。  

この例では、A 社はソース リージョン内で、アプリを専用 Vnet 内に配置し、それらの Vnet 間に接続性を確立しています。 この設計では、分離されたアプリのフェールオーバーを実行して、ターゲット ネットワークではソースのプライベート IP アドレスを保持することができます。

### <a name="before-failover"></a>フェールオーバー前

フェールオーバー前のアーキテクチャは次のとおりです。

- アプリケーション VM は、プライマリの Azure 東アジア リージョンでホストされています。
    - **App1** VM は、次の VNet **Source VNet 1** に配置されています:10.1.0.0/16。
    - **App2** VM は、次の VNet **Source VNet 2** に配置されています:10.2.0.0/16。
    - **Source VNet 1** にはサブネットが 2 つあります。
    - **Source VNet 2** にはサブネットが 2 つあります。
- セカンダリ (ターゲット) リージョンは Azure 東南アジアです。東南アジアには、**Source VNet 1** および **Source VNet 2** と同一の、復旧用の Vnet (**Recovery VNet 1** と **Recovery VNet 2**) があります。
        - **Recovery VNet 1** と **Recovery VNet 2** にはそれぞれ、**Souce VNet 1** と **Souce VNet 2** に対応する 2 つのサブネットがあります。東南アジアには、アドレス空間が 10.3.0.0/16 の、追加の VNet (**Azure VNet**) があります。
        - **Azure VNet** には、アドレス空間が 10.3.4.0/24 のサブネット (**Subnet 4**) が含まれています。
        - SQL Server Always On やドメイン コントローラーなどのレプリカ ノードは、**Subnet 4** 内に配置されています。
- いくつかのサイト間 VPN 接続が存在します。 
    - **Source VNet 1** と **Azure VNet**
    - **Source VNet 2** と **Azure VNet**
    - **Source VNet 1** と **Source VNet 2** はサイト間 VPN で接続されています
- **Recovery VNet 1** と **Recovery VNet 2** は、他のどの VNet にも接続されていません。
- **A 社**は RTO を短縮するため、**Recovery VNet 1** と **Recovery VNet 2** に VPN ゲートウェイを構成しています。  
- **Recovery VNet1** と **Recovery VNet2** は他の仮想ネットワークには接続されていません。
- 目標復旧時間 (RTO) を短縮するために、フェールオーバー前に **Recovery VNet1** と **Recovery VNet2** に VPN Gateway が構成されています。

    ![アプリ フェールオーバー前の Azure 内のリソース](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-isolated-application-before-failover2.png)

### <a name="after-failover"></a>フェールオーバー後

(この例では **Source VNet 2 内の) 1 つのアプリに影響する障害や問題が発生した場合、A 社は、影響を受けたアプリを次のように復旧できます。


- **Source VNet1** と **Source VNet2** の間と、**Source VNet2** と **Azure VNet** の間の VPN 接続を切断します。
- **Source VNet1** と **Recovery VNet2** の間と、**Recovery VNet2** と **Azure VNet** の間で、VPN 接続を確立します。
- **Source VNet2** 内の VM を **Recovery VNet2** にフェールオーバーします。

![Azure アプリ フェールオーバにおけるリソース](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-isolated-application-after-failover2.png)


- この例を拡張し、より多くのアプリケーションとネットワーク接続を含めることができます。 ソースからターゲットにフェールオーバーする際は、できるだけ似た構成の接続を使用することをお勧めします。
- VPN Gateway ではパブリック IP アドレスとゲートウェイ ホップを使用して接続を確立します。 パブリック IP アドレスを使用しない場合や、ホップの増加を避けたい場合は、[Azure VNet ピアリング](../virtual-network/virtual-network-peering-overview.md)を使用し、[サポートされる Azure リージョン](../virtual-network/virtual-network-manage-peering.md#cross-region)間で仮想ネットワークをピアリングできます。

## <a name="hybrid-resources-full-failover"></a>ハイブリッド リソース: 完全フェールオーバー

このシナリオでは、**B 社**はハイブリッド型の事業を運用しており、アプリケーション インフラストラクチャの一部を Azure で実行し、残りをオンプレミスで実行しています。 

### <a name="before-failover"></a>フェールオーバー前

フェールオーバー前のネットワーク アーキテクチャは、次のとおりです。

- アプリケーション VM は、Azure 東アジアでホストされています。
- 東アジアに、アドレス空間 10.1.0.0/16 の VNet (**Source VNet**) があります。
  - 東アジアでは、**Source VNet** 内の次の 3 つのサブネットにわたってワークロードを分割しています。
    - **サブネット 1**:10.1.1.0/24
    - **サブネット 2**:10.1.2.0/24
    - **サブネット 3**:10.1.0.0/16 の Azure 仮想ネットワークを使用する 10.1.3.0/24。 この仮想ネットワークの名前は **Source VNet** です
      - セカンダリ (ターゲット) リージョンは Azure 東南アジアです。
  - 東南アジアには、**Source VNet** と同一の復旧用 VNet (**Recovery VNet**) があります。
- 東アジアの VM は、Azure ExpressRoute またはサイト間 VPN で、オンプレミス データセンターに接続されています。
- RTO を短縮するために、B 社は、フェールオーバー前に Azure 東南アジアにある Recovery VNet にゲートウェイをプロビジョニングします。
- B 社は、レプリケートされた VM のターゲット IP アドレスの割り当てと確認を行っています。 ターゲット IP アドレスは、各 VM のソース IP アドレスと同じです。


![フェールオーバー前のオンプレミスと Azure の接続](./media/site-recovery-retain-ip-azure-vm-failover/on-premises-to-azure-connectivity-before-failover2.png)

### <a name="after-failover"></a>フェールオーバー後


ソース リージョンで障害が発生した場合、B 社は、すべてのリソースをターゲット リージョンにフェールオーバーできます。

- フェールオーバーの前にターゲット IP アドレスが既に用意されているため、B 社はフェールオーバーを連携させて、**Recovery VNet** および **Azure VNet** 間のフェールオーバー後に接続を自動的に確立できます。
- ターゲット リージョンにある 2 つの VNet (**Recovery VNet** と **Azure VNet**) 間の接続は、アプリの要件に応じて、フェールオーバー前、フェールオーバー中 (中間ステップとして)、またはフェールオーバー後に確立できます。 会社は[復旧計画](site-recovery-create-recovery-plans.md)を使用して、接続がいつ確立されるかを指定できます。
- Azure 東南アジアとオンプレミス データセンターの接続を確立する前に、Azure 東アジアとオンプレミス データセンターの元の接続を切断する必要があります。
- オンプレミスのルーティングは、フェールオーバー後にターゲット リージョンとゲートウェイを指し示すように再構成されます。

![フェールオーバー後のオンプレミスと Azure の接続](./media/site-recovery-retain-ip-azure-vm-failover/on-premises-to-azure-connectivity-after-failover2.png)

## <a name="hybrid-resources-isolated-app-failover"></a>ハイブリッド リソース: 分離されているアプリのフェールオーバー

B 社は、サブネット レベルで分離されているアプリをフェールオーバーできません。 これは、ソース VNet と復旧用 VNet のアドレス空間が同一で、ソースからオンプレミスへの元の接続がアクティブであるためです。

 - アプリの回復性のためには、B 社は各アプリを独自の専用 Azure VNet 内に配置する必要があります。
 - 各アプリが別個の VNet 内にあれば、B 社は分離されたアプリをフェールオーバーし、ソースの接続をターゲット リージョンにルーティングできます。

## <a name="next-steps"></a>次の手順

[復旧計画](site-recovery-create-recovery-plans.md)について学習します。
