---
title: Azure Site Recovery を使用して Azure ExpressRoute を Azure VM のディザスター リカバリーと統合する
description: Azure Site Recovery と Azure ExpressRoute を使用して、Azure VM のディザスター リカバリーを設定する方法を説明します
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mayg
ms.openlocfilehash: 0e1f670f2ba5ad31f29d56b2de40acd6e2bf18a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88654380"
---
# <a name="integrate-expressroute-with-disaster-recovery-for-azure-vms"></a>ExpressRoute を Azure VM のディザスター リカバリーと統合する


この記事では、セカンダリ Azure リージョンに Azure VM のディザスター リカバリーを設定するときに、Azure ExpressRoute を [Azure Site Recovery](site-recovery-overview.md) と統合する方法について説明します。

Site Recovery では、Azure VM のデータを Azure にレプリケートすることで、Azure VM のディザスター リカバリーを行うことができます。

- Azure VM で [Azure Managed Disks](../virtual-machines/managed-disks-overview.md) を使用する場合、VM データが、セカンダリ リージョン内のレプリケートされているマネージド ディスクにレプリケートされます。
- Azure VM でマネージド ディスクを使用しない場合、VM データは Azure ストレージ アカウントにレプリケートされます。
- レプリケーション エンドポイントはパブリックですが、Azure VM のレプリケーション トラフィックはインターネットを経由しません。

ExpressRoute を利用すると、接続プロバイダーが提供するプライベート接続を介して、オンプレミスのネットワークを Microsoft Azure クラウドに拡張できます。 ExpressRoute が構成されている場合は、次のように Site Recovery と統合されます。

- **Azure リージョン間のレプリケーション中**: Azure VM ディザスター リカバリーのレプリケーション トラフィックは Azure 内にのみ存在し、レプリケーションで ExpressRoute が必要になることも、使用されることもありません。 しかし、オンプレミス サイトからプライマリ Azure サイト内の Azure VM に接続する場合は、これらの Azure VM のディザスター リカバリーを設定する際に注意すべきさまざまな問題があります。
- **Azure リージョン間のフェールオーバー**: 障害の発生時に、プライマリからセカンダリの Azure リージョンに Azure VM をフェールオーバーします。 セカンダリ リージョンへのフェールオーバー後に、ExpressRoute を使用して、セカンダリ リージョン内の Azure VM にアクセスするために行うさまざまな手順があります。


## <a name="before-you-begin"></a>開始する前に

開始する前に、次の概念を理解していることを確認してください。

- ExpressRoute [回線](../expressroute/expressroute-circuit-peerings.md)
- ExpressRoute の[ルーティング ドメイン](../expressroute/expressroute-circuit-peerings.md#routingdomains)
- ExpressRoute の[場所](../expressroute/expressroute-locations.md)。
- Azure VM の[レプリケーション アーキテクチャ](azure-to-azure-architecture.md)
- Azure VM の[レプリケーションを設定する](azure-to-azure-tutorial-enable-replication.md)方法。
- Azure VM を[フェールオーバーする](azure-to-azure-tutorial-failover-failback.md)方法。


## <a name="general-recommendations"></a>一般的な推奨事項

ベスト プラクティスとして、また、ディザスター リカバリーの効率的な目標復旧時間 (RTO) を確保するために、ExpressRoute と統合するように Site Recovery を設定する場合は、次のようにすることをお勧めします。

- セカンダリ リージョンにフェールオーバーする前に、ネットワーク コンポーネントをプロビジョニングします。
    - Azure VM のレプリケーションを有効にすると、Site Recovery では、ソース ネットワーク設定に基づいて、ターゲットの Azure リージョンでネットワーク、サブネット、ゲートウェイなどのネットワーク リソースを自動的にデプロイできます。
    - Site Recovery では、VNet ゲートウェイなどのネットワーク リソースを自動的に設定することはできません。
    - フェールオーバーの前に、これらの追加のネットワーク リソースをプロビジョニングすることをお勧めします。 このデプロイには短時間のダウンタイムが関連しており、デプロイ計画中にそれを考慮しなかった場合、復旧時間全体に影響する可能性があります。
- 通常のディザスター リカバリー訓練を行います。
    - 訓練ではデータ損失やダウンタイムなしでレプリケーション戦略を検証します。これは運用環境には影響しません。 RTO に悪影響を及ぼす可能性のある最終段階での構成の問題を回避するのに役立ちます。
    - 訓練でテスト フェールオーバーを実行する場合は、レプリケーションを有効にしたときに設定された既定のネットワークではなく、別個の Azure VM ネットワークを使用することをお勧めします。
- 持っている ExpressRoute 回線が 1 つの場合は、異なる IP アドレス空間を使用します。
    - ターゲット仮想ネットワークに対して異なる IP アドレス空間を使用することをお勧めします。 これにより、リージョンでの障害発生時に接続を確立する場合に問題を回避できます。
    - 別のアドレス空間を使用できない場合は、必ず、IP アドレスが異なる別のテスト ネットワークでディザスター リカバリー訓練のテスト フェールオーバーを実行するようにしてください。 同じ ExpressRoute 回線に IP アドレス空間が重複している 2 つの VNet を接続することはできません。

## <a name="replicate-azure-vms-when-using-expressroute"></a>ExpressRoute の使用時に Azure VM をレプリケートする


プライマリ サイトの Azure VM に対してレプリケーションを設定する必要があり、ExpressRoute 経由でオンプレミス サイトからこれらの VM に接続する場合は、以下のようにする必要があります。

1. 各 Azure VM の[レプリケーションを有効にします](azure-to-azure-tutorial-enable-replication.md)。
2. 必要に応じて、Site Recovery でネットワークが設定されるようにします。
    - レプリケーションを構成して有効にすると、Site Recovery によって、ターゲット Azure リージョン内のネットワーク、サブネット、およびゲートウェイ サブネットが、ソース リージョン内のものと一致するように設定されます。 また、Site Recovery によって、ソースとターゲットの仮想ネットワークの間のマッピングも行われます。
    - これを Site Recovery で自動的に行わない場合は、レプリケーションを有効にする前に、ターゲット側のネットワーク リソースを作成します。
3. 他のネットワーク要素を作成します。
    - Site Recovery では、セカンダリ リージョンにルート テーブル、VNet ゲートウェイ、VNet ゲートウェイ接続、VNet ピアリング、およびその他のネットワーク リソースと接続が作成されません。
    - プライマリ リージョンからフェールオーバーを実行する前のある時点で、セカンダリ リージョンにこれらの追加のネットワーク要素を作成する必要があります。
    - [復旧計画](site-recovery-create-recovery-plans.md)と自動スクリプトを使用して、これらのネットワーク リソースを設定して接続することができます。
1. ネットワーク トラフィックのフローを制御するためにネットワーク仮想アプライアンス (NVA) をデプロイしている場合は、次のことに注意してください。
    - Azure VM レプリケーションの Azure の既定のシステム ルートは、0.0.0.0/0 です。
    - 通常は、NVA デプロイでも、送信インターネット トラフィックが NVA を通過する、既定のルート (0.0.0.0/0) が定義されます。 他の特定のルート構成が見つからない場合は、既定のルートが使用されます。
    - このような場合に、すべてのレプリケーション トラフィックが NVA を経由すると、NVA がオーバーロードになる可能性があります。
    - すべての Azure VM トラフィックをオンプレミス デプロイにルーティングする既定のルートを使用した場合も、同じ制限が適用されます。
    - このシナリオでは、レプリケーション トラフィックが Azure 境界から外に出ないように、Microsoft.Storage サービス用の仮想ネットワーク内に[ネットワーク サービス エンドポイントを作成する](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage)ことをお勧めします。

## <a name="replication-example"></a>レプリケーションの例

通常、企業のデプロイでは、複数の Azure VNet にわたってワークロードが分割され、インターネットとオンプレミス サイトに対する外部接続のために中央の接続ハブが使用されます。 ハブとスポークのトポロジは通常、ExpressRoute と共に使用されます。

![フェールオーバー前の ExpressRoute を使用したオンプレミスから Azure への接続](./media/azure-vm-disaster-recovery-with-expressroute/site-recovery-with-expressroute-before-failover.png)

- **リージョン**。 アプリは Azure 東アジア リージョンにデプロイされます。
- **スポーク vNet**。 アプリは、次の 2 つのスポーク vNet にデプロイされます。
    - **ソース vNet1**: 10.1.0.0/24。
    - **ソース vNet2**: 10.2.0.0/24。
    - 各スポークの仮想ネットワークは、**ハブ vNet** に接続されます。
- **ハブ vNet**。 1 つのハブ vNet があります。**ソース ハブ vNet**: 10.10.10.0/24。
  - このハブ vNet はゲートキーパーとして機能します。
  - サブネット間でのすべての通信は、このハブを通過します。
    - **ハブ vNet のサブネット**。 ハブ vNet には、次の 2 つのサブネットがあります。
    - **NVA サブネット**: 10.10.10.0/25。 このサブネットには NVA (10.10.10.10) が含まれています。
    - **ゲートウェイ サブネット**: 10.10.10.128/25。 このサブネットには、ExpressRoute 接続に接続された ExpressRoute ゲートウェイがあります。これは、プライベート ピアリング ルーティング ドメインを通じてオンプレミス サイトにルーティングされます。
- オンプレミス データ センターでは、香港特別行政区のパートナー エッジを介した ExpressRoute 回線接続が使用されています。
- すべてのルーティングは、Azure ルート テーブル (UDR) によって制御されます。
- vNet 間のアウトバウンド トラフィックや、オンプレミス データ センターへのアウトバウンド トラフィックはすべて、NVA を介してルーティングされます。

### <a name="hub-and-spoke-peering-settings"></a>ハブとスポークのピアリング設定

#### <a name="spoke-to-hub"></a>スポークからハブへ

**方向** | **設定** | **State**
--- | --- | ---
スポークからハブへ | 仮想ネットワーク アドレスを許可する | Enabled
スポークからハブへ | 転送されたトラフィックを許可する | Enabled
スポークからハブへ | ゲートウェイ転送を許可する | 無効
スポークからハブへ | リモート ゲートウェイを使用する | Enabled

 ![スポークからハブへのピアリング構成](./media/azure-vm-disaster-recovery-with-expressroute/spoke-to-hub-peering-configuration.png)

#### <a name="hub-to-spoke"></a>ハブからスポークへ

**方向** | **設定** | **State**
--- | --- | ---
ハブからスポークへ | 仮想ネットワーク アドレスを許可する | Enabled
ハブからスポークへ | 転送されたトラフィックを許可する | Enabled
ハブからスポークへ | ゲートウェイ転送を許可する | Enabled
ハブからスポークへ | リモート ゲートウェイを使用する | 無効

 ![ハブからスポークへのピアリング構成](./media/azure-vm-disaster-recovery-with-expressroute/hub-to-spoke-peering-configuration.png)

### <a name="example-steps"></a>手順の例

この例では、ソース ネットワーク内の Azure VM に対してレプリケーションを有効にする場合、次のようにする必要があります。

1. VM の[レプリケーションを有効にします](azure-to-azure-tutorial-enable-replication.md)。
2. Site Recovery によって、ターゲット リージョンにレプリカ vNet、サブネット、およびゲートウェイ サブネットが作成されます。
3. Site Recovery によって、ソース ネットワークと、作成されるレプリカのターゲット ネットワークの間のマッピングが作成されます。
4. 仮想ネットワーク ゲートウェイ、仮想ネットワーク ゲートウェイ接続、仮想ネットワーク ピアリング、またはその他のネットワーク リソースや接続を手動で作成します。


## <a name="fail-over-azure-vms-when-using-expressroute"></a>ExpressRoute の使用時に Azure VM をフェールオーバーする

Site Recovery を使用してターゲット Azure リージョンに Azure VM をフェールオーバーした後、ExpressRoute の[プライベート ピアリング](../expressroute/expressroute-circuit-peerings.md#privatepeering)を使用してアクセスすることができます。

- 新しい接続でターゲット vNet に ExpressRoute を接続する必要があります。 既存の ExpressRoute 接続は自動的に転送されません。
- ターゲット vNet への ExpressRoute 接続を設定する方法は、ExpressRoute のトポロジによって異なります。


### <a name="access-with-two-circuits"></a>2 つの回線によるアクセス

#### <a name="two-circuits-with-two-peering-locations"></a>2 つの回線と 2 つのピアリング場所

この構成は、リージョンの障害から ExpressRoute 回線を保護するのに役立ちます。 プライマリのピアリング場所がダウンしても、他の場所から接続を継続できます。

- 運用環境に接続されている回線は、通常、プライマリです。 通常、セカンダリ回線の帯域幅は低くなっており、障害が発生した場合に増やすことができます。
- フェールオーバー後に、セカンダリ ExpressRoute 回線からターゲット vNet への接続を確立できます。 また、障害が発生した場合に備えて接続を設定し、復旧時間全体を短くすることができます。
- プライマリおよびターゲットの両方の vNet に対する同時接続を使用する場合は、オンプレミスのルーティングでフェールオーバー後にセカンダリの回線と接続のみが使用されるようにしてください。
- ソースとターゲットの vNet では、フェールオーバー後に、新しい IP アドレスを受け取ることも、同じものを保持することもできます。 どちらの場合も、セカンダリ接続はフェールオーバーの前に確立できます。


#### <a name="two-circuits-with-single-peering-location"></a>2 つの回線と単一のピアリング場所

この構成はプライマリの ExpressRoute 回線の障害から保護するのに役立ちますが、両方の回線に影響する単一の ExpressRoute のピアリング場所がダウンした場合を除きます。

- オンプレミス データ センターから、ソースの vNet (プライマリ回線を使用) と、ターゲットの vNet (セカンダリ回線を使用) に同時に接続することができます。
- プライマリとターゲットに対する同時接続を使用する場合は、オンプレミスのルーティングでフェールオーバー後にセカンダリの回線と接続のみが使用されるようにしてください。
-   回線が同じピアリング場所で作成されている場合、両方の回線を同じ vNet に接続することはできません。

### <a name="access-with-a-single-circuit"></a>単一回線によるアクセス

この構成では、Expressroute 回線は 1 つだけです。 回線は 1 つがダウンした場合に冗長接続されますが、ピアリング リージョンでダウンした場合、単一のルート回線では回復させることはできません。 以下の点に注意してください。

- [同じ地理的場所](azure-to-azure-support-matrix.md#region-support)の任意の Azure リージョンには、Azure VM をレプリケーションすることができます。 ターゲット Azure リージョンがソースと同じ場所にない状態で、単一の ExpressRoute 回線を使用している場合は、ExpressRoute Premium を有効にする必要があります。 詳細については、[ExpressRoute の場所](../expressroute/expressroute-locations.md)に関する記述と「[ExpressRoute の価格](https://azure.microsoft.com/pricing/details/expressroute/)」を参照してください。
- ターゲット リージョンで同じ IP アドレス空間が使用されている場合、回線にソースとターゲットの vNet を同時に接続することはできません。 このシナリオでは:    
    -  ソース側の接続を切断し、ターゲット側の接続を確立します。 この接続変更は、Site Recovery の復旧計画の一部としてスクリプト化することができます。 以下の点に注意してください。
        - 地域障害の発生時には、プライマリ リージョン へのアクセスができないと、切断操作が失敗する可能性があります。 これが、ターゲット リージョンへの接続の作成に影響する可能性があります。
        - ターゲット リージョンで接続を作成し、その後でプライマリ リージョンが回復しても、2 つの同時接続で同じアドレス空間への接続が試行された場合、パケット ドロップが発生する可能性があります。
        - これを回避するには、プライマリ接続をすぐに終了します。
        - VM がプライマリ リージョンにフェールバックされた後、セカンダリ接続が切断されたら、プライマリ接続を再度確立できます。
-   ターゲット vNet で別のアドレス空間が使用されている場合は、同じ ExpressRoute 回線から、ソースとターゲットの vNet に同時に接続できます。


## <a name="failover-example"></a>フェールオーバーの例

この例では、次のトポロジを使用します。

- 2 つの異なるピアリング場所に 2 つの異なる ExpressRoute 回線を使用します。
- フェールオーバー後に Azure VM のプライベート IP アドレスを保持します。
- ターゲットの復旧リージョンは Azure 東南アジアです。
- セカンダリ ExpressRoute 回線接続は、シンガポールにあるパートナー エッジを通じて確立されます。

フェールオーバー後に同じ IP アドレスを使い、単一の ExpressRoute 回線を使用するシンプルなトポロジについては、[こちらの記事を参照してください](site-recovery-retain-ip-azure-vm-failover.md#hybrid-resources-full-failover)。

### <a name="example-steps"></a>手順の例
この例で復旧を自動化するには、次のようにする必要があります。

1. レプリケーションを設定する手順に従います。
2. [Azure VM をフェールオーバーします](azure-to-azure-tutorial-failover-failback.md)。フェールオーバー中またはフェールオーバー後に以下の追加の手順を行います。

    a. ターゲット リージョンのハブ vNet に、Azure ExpressRoute ゲートウェイを作成します。 この場合、ターゲットのハブ vNet を ExpressRoute 回線に接続する必要があります。

    b. ターゲットのハブ vNet からターゲットの ExpressRoute 回線への接続を作成します。

    c. ターゲット リージョンのハブ アンド スポーク仮想ネットワーク間に、VNet ピアリングを設定します。 ターゲット リージョンのピアリング プロパティは、ソース リージョンのものと同じになります。

    d. ハブ VNet と、2 つのスポーク Vnet に、UDR を設定します。

    - ターゲット側の UDR のプロパティは、同じ IP アドレスを使用している場合、ソース側のものと同じです。
    - 異なるターゲット IP アドレスを使用する場合は、UDR を適切に変更する必要があります。


上記の手順は、[回復計画](site-recovery-create-recovery-plans.md)の一部としてスクリプト化することができます。 アプリケーションの接続性や回復時間の要件によっては、上記の手順をフェールオーバーの開始前に完了することもできます。

#### <a name="after-recovery"></a>復旧後

VM の復旧と接続の完了後の復旧環境は次のようになります。

![フェールオーバー後の ExpressRoute を使用したオンプレミスと Azure の接続](./media/azure-vm-disaster-recovery-with-expressroute/site-recovery-with-expressroute-after-failover.png)



## <a name="next-steps"></a>次のステップ

アプリのフェールオーバーを自動化するための[復旧計画](site-recovery-create-recovery-plans.md)の使用について詳しく確認します。
