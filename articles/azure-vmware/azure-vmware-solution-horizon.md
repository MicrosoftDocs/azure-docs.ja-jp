---
title: Azure VMware Solution 上に Horizon をデプロイする
description: Azure VMware Solution 上に VMware Horizon をデプロイする方法について説明します。
ms.topic: how-to
ms.date: 09/29/2020
ms.openlocfilehash: c34d0ac7806f8284e893cf3ad4f3c82dd404ff41
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102181399"
---
# <a name="deploy-horizon-on-azure-vmware-solution"></a>Azure VMware Solution 上に Horizon をデプロイする 

>[!NOTE]
>このドキュメントでは、VMware Horizon 製品 (旧称 Horizon 7) に焦点を当てています。 Horizon は Horizon Cloud on Azure とは異なるソリューションですが、共有コンポーネントがいくつかあります。 Azure VMware Solution の主な利点を挙げると、サイズ変更方法がより簡単であること、および VMware Cloud Foundation 管理が Azure portal に統合されていることです。

仮想デスクトップおよびアプリケーション プラットフォームである [VMware Horizon](https://www.vmware.com/products/horizon.html)® は、データセンターで実行され、シンプルで一元的な管理を実現します。 どこにいても、あらゆるデバイス上に仮想デスクトップとアプリケーションが提供されます。 Horizon を使用すると、Windows および Linux 仮想デスクトップ、リモート デスクトップ サーバー (RDS) でホストされているアプリケーション、デスクトップ、物理マシンへの接続を作成して仲介することができます。

ここでは、Azure VMware Solution への Horizon のデプロイに焦点を当てます。 VMware Horizon に関する一般的な情報については、以下の Horizon の製品ドキュメントを参照してください。

* [VMware Horizon とは](https://www.vmware.com/products/horizon.html)

* [VMware Horizon の詳細情報](https://docs.vmware.com/en/VMware-Horizon/index.html)

* [Horizon の参照アーキテクチャ](https://techzone.vmware.com/resource/workspace-one-and-horizon-reference-architecture)

Azure VMware Solution に Horizon を導入する場合、Azure プラットフォームには 2 つの仮想デスクトップ インフラストラクチャ (VDI) ソリューションが用意されています。 次の図は、主な相違点の概要を示しています。

:::image type="content" source="media/horizon/difference-horizon-azure-vmware-solution-horizon-cloud-azure.png" alt-text="Azure VMware Solution 上の Horizon と Horizon Cloud on Azure" border="false":::

Horizon 8 リリース ラインの Horizon 2006 以降のバージョンでは、オンプレミス デプロイと Azure VMware Solution デプロイの両方がサポートされています。 オンプレミスでサポートされているものの、Azure VMware Solution ではサポートされていない Horizon の機能がいくつかあります。 また、Horizon エコシステムのその他の製品もサポートされています。 詳細については、[機能パリティと相互運用性](https://kb.vmware.com/s/article/80850)に関するページを参照してください。

## <a name="deploy-horizon-in-a-hybrid-cloud"></a>ハイブリッド クラウドでの Horizon のデプロイ

Horizon Cloud Pod アーキテクチャ (CPA) を使用してオンプレミスおよび Azure のデータ センターを相互接続する場合は、ハイブリッド クラウド環境に Horizon をデプロイできます。 CPA を使用すると、デプロイをスケールアップし、ハイブリッド クラウドを構築し、事業継続とディザスター リカバリーのための冗長性を確保することができます。  詳細については、「[既存の Horizon 7 環境の拡大](https://techzone.vmware.com/resource/business-continuity-vmware-horizon#_Toc41650874)」を参照してください。

>[!IMPORTANT]
>CPA は拡張されたデプロイではなく、各 Horizon ポッドは独立しています。個々のポッドに属する接続サーバーは、すべて 1 つの場所に配置し、ネットワークの観点から同じブロードキャスト ドメインで実行する必要があります。

オンプレミスまたはプライベート データ センターと同様に、Azure VMware Solution のプライベート クラウドに Horizon をデプロイできます。 次のセクションでは、Horizon をオンプレミスと Azure VMware Solution にデプロイする場合の主な違いについて説明します。

Azure プライベート クラウドは、概念的には、Horizon ドキュメントで使用される用語である VMware SDDC と同じです。 このドキュメントでは、これ以降、Azure プライベート クラウドと VMware SDDC を置き換え可能な用語として使用します。

Azure VMware Solution 上の Horizon でサブスクリプション ライセンスを管理するには、Horizon クラウド コネクタが必要です。 クラウド コネクタは、Horizon 接続サーバーと共に Azure Virtual Network にデプロイできます。

>[!IMPORTANT]
>Azure VMware Solution 上の Horizon に対する Horizon コントロール プレーンのサポートはまだご利用いただけません。 必ず VHD バージョンの Horizon クラウド コネクタをダウンロードしてください。

## <a name="vcenter-cloud-admin-role"></a>vCenter クラウド管理者ロール

Azure VMware Solution は SDDC サービスであり、Azure VMware Solution 上の SDDC のライフサイクルは Azure によって管理されるため、Azure VMware Solution 上の vCenter 権限モデルは設計によって制限されます。

お客様は、vCenter 権限の制限付きセットを持つクラウド管理者ロールを使用する必要があります。 Horizon 製品は、Azure VMware Solution でクラウド管理者ロールを使用するように変更されています。具体的には次のとおりです。

* インスタント クローンのプロビジョニングは、Azure VMware Solution で実行するように変更されました。

* 特定の vSAN ポリシー (VMware_Horizon) が、Horizon で動作するように Azure VMware Solution で作成されています。これは、Horizon 用にデプロイされた SDDC で利用可能であり、使用されている必要があります。

* vSphere コンテンツ ベースの読み取りキャッシュ (CBRC) は、ビュー ストレージ アクセラレータとも呼ばれ、Azure VMware Solution で実行する場合は無効になります。

>[!IMPORTANT]
>CBRC を再び有効にすることはできません。

>[!NOTE]
>Azure VMware Solution では、Horizon 2006 (Horizon 8) 以上が Horizon 8 ブランチにデプロイされ、Horizon 接続サーバーのインストーラーで **[Azure]** オプションが選択されている限り、特定の Horizon 設定が自動的に構成されます。

## <a name="horizon-on-azure-vmware-solution-deployment-architecture"></a>Azure VMware Solution 上の Horizon のデプロイ アーキテクチャ

一般的な Horizon アーキテクチャの設計では、ポッドとブロック戦略が使用されます。 ブロックは単一の vCenter ですが、複数のブロックを結合することでポッドが作成されます。 Horizon ポッドは、Horizon のスケーラビリティの制限によって決定される組織の単位です。 各 Horizon ポッドには個別の管理ポータルがあるため、標準的な設計手法はポッドの数を最小限に抑えることです。

すべてのクラウドには独自のネットワーク接続方式があります。 Azure VMware Solution のネットワーク接続は、VMware の SDDC ネットワーク/NSX エッジと組み合わせることで、オンプレミスとは異なる Horizon をデプロイするための独自の要件を提示します。

次のことを前提とすれば、各 Azure プライベート クラウドおよび SDDC によって、それぞれ 4,000 のデスクトップまたはアプリケーションのセッションの処理が可能になります。

* ワークロードのトラフィックが、LoginVSI タスク ワーカー プロファイルと一致する。

* プロトコル トラフィックだけが考慮され、ユーザー データは考慮されない。

* NSX Edge が大きなサイズに構成されている。

>[!NOTE]
>ワークロードのプロファイルとニーズは異なる場合があるため、結果はユース ケースによって異なる場合があります。 ユーザー データ ボリュームでは、ワークロードのコンテキストにおけるスケールの制限が低くなることがあります。 デプロイのサイズを変更し、それに応じて計画します。 詳細については、「[Horizon のデプロイのために Azure VMware Solution ホストのサイズを変更する](#size-azure-vmware-solution-hosts-for-horizon-deployments)」セクションのサイズ変更のガイドラインを参照してください。

Azure プライベート クラウドおよび SDDC の上限を考慮して、Azure Virtual Network 内部で Horizon 接続サーバーと VMware Unified Access Gateway (UAG) が実行されるデプロイ アーキテクチャをお勧めします。 これにより、各 Azure プライベート クラウドおよび SDDC が実質的にブロックになります。 その結果として、Azure VMware Solution 上で実行される Horizon のスケーラビリティが最大になります。

Azure Virtual Network から Azure プライベート クラウド/SDDC への接続は、ExpressRoute FastPath で構成する必要があります。 次の図は、基本的な Horizon ポッドのデプロイを示しています。

:::image type="content" source="media/horizon/horizon-pod-deployment-expresspath-fast-path.png" alt-text="ExpressPath Fast Path を使用した通常の Horizon ポッドのデプロイ" border="false":::

## <a name="network-connectivity-to-scale-horizon-on-azure-vmware-solution"></a>Azure VMware Solution 上の Horizon をスケーリングするためのネットワーク接続

このセクションでは、Azure VMware Solution 上で Horizon をスケーリングするのに役立つように、いくつかの一般的なデプロイ例を使用してネットワーク アーキテクチャの概要を説明します。 重点を置くのは特に重要なネットワーク要素です。 

### <a name="single-horizon-pod-on-azure-vmware-solution"></a>Azure VMware Solution 上の単一の Horizon ポッド

:::image type="content" source="media/horizon/single-horizon-pod-azure-vmware-solution.png" alt-text="Azure VMware Solution 上の単一の Horizon ポッド" border="false":::

単一の Horizon ポッドは最も簡単なデプロイ シナリオです。米国東部リージョンに Horizon ポッドをちょうど 1 つデプロイするだけだからです。  各プライベート クラウドおよび SDDC によって処理されるデスクトップ セッション数は 4,000 と予想されるため、最大の Horizon ポッド サイズをデプロイします。  プライベート クラウドまたは SDDC のデプロイを最大で 3 つ計画することができます。

Azure Virtual Network に Horizon インフラストラクチャの仮想マシン (VM) がデプロイされている場合、Horizon ポッド 1 つあたり 12,000 のセッションに接続できます。 Azure Virtual Network に対する各プライベート クラウドと SDDC との間の接続は ExpressRoute Fast Path です。  プライベート クラウド間の東西トラフィックは必要ありません。 

この基本的なデプロイの例には、次のような重要な前提があります。

* Cloud Pod アーキテクチャ (CPA) を使用してこの新しいポッドに接続するオンプレミスの Horizon ポッドはありません。

* エンド ユーザーは、インターネット経由で自分の仮想デスクトップに接続します (オンプレミスのデータ センター経由で接続することはできません)。

Azure Virtual Network の AD ドメイン コントローラーを、VPN または ExpressRoute 回線経由でオンプレミスの Active Directory に接続します。

基本的な例のバリエーションは、オンプレミスのリソースへの接続をサポートすることになります。 たとえば、ユーザーはデスクトップにアクセスして仮想デスクトップ アプリケーション トラフィックを生成したり、CPA を使用してオンプレミスの Horizon ポッドに接続したりします。

図に、オンプレミスのリソースへの接続をサポートする方法を示しています。 企業ネットワークから Azure Virtual Network に接続するには、ExpressRoute 回線が必要です。  また、ExpressRoute の Global Reach を使用して、プライベート クラウドおよび SDDC のそれぞれに企業ネットワークを接続する必要もあります。  これにより、SDDC から ExpressRoute 回線およびオンプレミスのリソースへの接続が可能になります。 

:::image type="content" source="media/horizon/connect-corporate-network-azure-virtual-network.png" alt-text="企業ネットワークを Azure Virtual Network に接続する" border="false":::

### <a name="multiple-horizon-pods-on-azure-vmware-solution-across-multiple-regions"></a>複数のリージョンにわたる Azure VMware Solution 上の複数の Horizon ポッド

もう 1 つのシナリオでは、複数のポッド間で Horizon をスケーリングします。  このシナリオでは、2 つの異なるリージョンに 2 つの Horizon ポッドをデプロイし、CPA を使用してそれらをフェデレーションします。 これは前の例のネットワーク構成と似ていますが、リージョン間リンクがいくつか追加されています。 

各リージョン内の Azure Virtual Network を、他方のリージョン内のプライベート クラウドまたは SDDC に接続します。 これにより、CPA フェデレーションの一部である Horizon 接続サーバーが、管理下にあるすべてのデスクトップに接続できるようになります。 この構成にプライベート クラウドまたは SDDC を追加すると、全体で 24,000 のセッションにスケーリングできるようになります。 

同じリージョンに 2 つの Horizon ポッドをデプロイすると、同じ原則が適用されます。  2 つ目のホライズン ポッドは必ず "*別の Azure Virtual Network*" にデプロイしてください。 単一ポッドの例と同じように、ExpressRoute と Global Reach を使用して、企業ネットワークとオンプレミスのポッドをこのマルチポッドおよびリージョンの例に接続できます。 

:::image type="content" source="media/horizon/multiple-horizon-pod-azure-vmware-solution.png" alt-text="複数のリージョンにわたる Azure VMware Solution 上の複数の Horizon ポッド" border="false":::

## <a name="size-azure-vmware-solution-hosts-for-horizon-deployments"></a>Horizon のデプロイのために Azure VMware Solution ホストのサイズを変更する 

Azure VMware Solution で実行されているホスト上の Horizon のサイズ設定方法は、オンプレミスの Horizon よりも簡単です。  これは、Azure VMware Solution のホスト インスタンスが標準化されているためです。  ホストのサイズを正確に設定すると、VDI 要件をサポートするために必要なホストの数を特定できます。  これは、デスクトップあたりのコストを決定する上で中心的な役割を果たします。

### <a name="sizing-tables"></a>サイズ設定表

Horizon 仮想デスクトップの具体的な vCPU と vRAM 要件は、お客様固有のワークロード プロファイルによって決まります。   MSFT と VMware の営業チームと協力して、仮想デスクトップの vCPU および vRAM 要件を判断してください。 

| VM 1 台あたりの vCPU | VM 1 台あたりの vRAM (GB) | インスタンス | VM 100 台 | VM 200 台 | VM 300 台 | VM 400 台 | VM 500 台 | VM 600 台 | VM 700 台 | VM 800 台 | VM 900 台 | VM 1,000 台 | VM 2,000 台 | VM 3,000 台 | VM 4,000 台 | VM 5,000 台 | VM 6,000 台 | VM 6,400 台 |
|:-----------:|:----------------:|:--------:|:-------:|:-------:|:-------:|:-------:|:-------:|:-------:|:-------:|:-------:|:-------:|:--------:|:--------:|:--------:|:--------:|:--------:|:--------:|:--------:|
|      2      |        3.5       |    AVS   |    3    |    3    |    4    |    4    |    5    |    6    |    6    |    7    |    8    |     9    |    17    |    25    |    33    |    41    |    49    |    53    |
|      2      |         4        |    AVS   |    3    |    3    |    4    |    5    |    6    |    6    |    7    |    8    |    9    |     9    |    18    |    26    |    34    |    42    |    51    |    54    |
|      2      |         6        |    AVS   |    3    |    4    |    5    |    6    |    7    |    9    |    10   |    11   |    12   |    13    |    26    |    38    |    51    |    62    |    75    |    79    |
|      2      |         8        |    AVS   |    3    |    5    |    6    |    8    |    9    |    11   |    12   |    14   |    16   |    18    |    34    |    51    |    67    |    84    |    100   |    106   |
|      2      |        12        |    AVS   |    4    |    6    |    9    |    11   |    13   |    16   |    19   |    21   |    23   |    26    |    51    |    75    |    100   |    124   |    149   |    158   |
|      2      |        16        |    AVS   |    5    |    8    |    11   |    14   |    18   |    21   |    24   |    27   |    30   |    34    |    67    |    100   |    133   |    165   |    198   |    211   |
|      4      |        3.5       |    AVS   |    3    |    3    |    4    |    5    |    6    |    7    |    8    |    9    |    10   |    11    |    22    |    33    |    44    |    55    |    66    |    70    |
|      4      |         4        |    AVS   |    3    |    3    |    4    |    5    |    6    |    7    |    8    |    9    |    10   |    11    |    22    |    33    |    44    |    55    |    66    |    70    |
|      4      |         6        |    AVS   |    3    |    4    |    5    |    6    |    7    |    9    |    10   |    11   |    12   |    13    |    26    |    38    |    51    |    62    |    75    |    79    |
|      4      |         8        |    AVS   |    3    |    5    |    6    |    8    |    9    |    11   |    12   |    14   |    16   |    18    |    34    |    51    |    67    |    84    |    100   |    106   |
|      4      |        12        |    AVS   |    4    |    6    |    9    |    11   |    13   |    16   |    19   |    21   |    23   |    26    |    51    |    75    |    100   |    124   |    149   |    158   |
|      4      |        16        |    AVS   |    5    |    8    |    11   |    14   |    18   |    21   |    24   |    27   |    30   |    34    |    67    |    100   |    133   |    165   |    198   |    211   |
|      6      |        3.5       |    AVS   |    3    |    4    |    5    |    6    |    7    |    9    |    10   |    11   |    13   |    14    |    27    |    41    |    54    |    68    |    81    |    86    |
|      6      |         4        |    AVS   |    3    |    4    |    5    |    6    |    7    |    9    |    10   |    11   |    13   |    14    |    27    |    41    |    54    |    68    |    81    |    86    |
|      6      |         6        |    AVS   |    3    |    4    |    5    |    6    |    7    |    9    |    10   |    11   |    13   |    14    |    27    |    41    |    54    |    68    |    81    |    86    |
|      6      |         8        |    AVS   |    3    |    5    |    6    |    8    |    9    |    11   |    12   |    14   |    16   |    18    |    34    |    51    |    67    |    84    |    100   |    106   |
|      6      |        12        |    AVS   |    4    |    6    |    9    |    11   |    13   |    16   |    19   |    21   |    23   |    26    |    51    |    75    |    100   |    124   |    149   |    158   |
|      6      |        16        |    AVS   |    5    |    8    |    11   |    14   |    18   |    21   |    24   |    27   |    30   |    34    |    67    |    100   |    133   |    165   |    198   |    211   |
|      8      |        3.5       |    AVS   |    3    |    4    |    6    |    7    |    9    |    10   |    12   |    14   |    15   |    17    |    33    |    49    |    66    |    82    |    98    |    105   |
|      8      |         4        |    AVS   |    3    |    4    |    6    |    7    |    9    |    10   |    12   |    14   |    15   |    17    |    33    |    49    |    66    |    82    |    98    |    105   |
|      8      |         6        |    AVS   |    3    |    4    |    6    |    7    |    9    |    10   |    12   |    14   |    15   |    17    |    33    |    49    |    66    |    82    |    98    |    105   |
|      8      |         8        |    AVS   |    3    |    5    |    6    |    8    |    9    |    11   |    12   |    14   |    16   |    18    |    34    |    51    |    67    |    84    |    100   |    106   |
|      8      |        12        |    AVS   |    4    |    6    |    9    |    11   |    13   |    16   |    19   |    21   |    23   |    26    |    51    |    75    |    100   |    124   |    149   |    158   |
|      8      |        16        |    AVS   |    5    |    8    |    11   |    14   |    18   |    21   |    24   |    27   |    30   |    34    |    67    |    100   |    133   |    165   |    198   |    211   |


### <a name="horizon-sizing-inputs"></a>Horizon のサイズ設定の入力

計画したワークロードについて収集する必要があるものは次のとおりです。

* 同時実行されるデスクトップの数

* デスクトップごとに必要な仮想 CPU

* デスクトップごとに必要な仮想 RAM

* デスクトップごとに必要なストレージ

一般に、VDI のデプロイは CPU または RAM に制約があり、これにより、ホストのサイズが決まります。 パフォーマンス テストで検証された LoginVSI ナレッジ ワーカー タイプのワークロードについて、次の例を見てみましょう。

* 同時実行される 2,000 のデスクトップ デプロイ

* デスクトップごとに 2 つの仮想 CPU

* デスクトップごとに 4 GB の仮想 RAM

* デスクトップごとに 50 GB のストレージ

この例では、ホストの合計数が 18 になり、ホストごとの VM の密度は 111 になります。

>[!IMPORTANT]
>お客様のワークロードは、LoginVSI ナレッジ ワーカーのこの例とは異なります。 デプロイの計画の一環として、特定のサイズとパフォーマンスのニーズに応じて VMware EUC SE を操作します。 ホストのサイズを確認し、それに応じて調整する前に、実際の計画されたワークロードを使用して、独自のパフォーマンス テストを実行してください。

## <a name="horizon-on-azure-vmware-solution-licensing"></a>Azure VMware Solution 上の Horizon のライセンス 

Azure VMware Solution 上の Horizon の実行コストには、4 つのコンポーネントがあります。 

### <a name="azure-vmware-solution-capacity-cost"></a>Azure VMware Solution の容量コスト

価格の詳細については、[Azure VMware Solution の価格](https://azure.microsoft.com/pricing/details/azure-vmware/)に関するページを参照してください。

### <a name="horizon-licensing-cost"></a>Horizon ライセンス コスト

Azure VMware Solution では、次の 2 つのライセンスを使用できます。これは、同時ユーザー (CCU) または名前付きユーザー (NU) のどちらでもかまいません。

* Horizon サブスクリプション ライセンス

* Horizon ユニバーサル サブスクリプション ライセンス

近い将来、Azure VMware Solution にのみ Horizon をデプロイする場合は、Horizon サブスクリプション ライセンスを使用してください。その方が低コストです。

Azure VMware Solution とオンプレミスにデプロイする場合は、ディザスター リカバリーのユース ケースと同様に、Horizon ユニバーサル サブスクリプション ライセンスを選択してください。 これにはオンプレミス デプロイ用の vSphere ライセンスが含まれているため、コストが高くなります。

VMware EUC 営業チームと協力して、ニーズに応じた Horizon ライセンス コストを判断します。

### <a name="azure-instance-types"></a>Azure インスタンスの種類

Horizon インフラストラクチャに必要な Azure 仮想マシンのサイズを把握するには、[こちら](https://techzone.vmware.com/resource/horizon-on-azure-vmware-solution-configuration#horizon-installation-on-azure-vmware-solution)にある VMware のガイドラインを参照してください。

## <a name="references"></a>References
[Horizon Agent for Linux のシステム要件](https://docs.vmware.com/en/VMware-Horizon/2012/linux-desktops-setup/GUID-E268BDBF-1D89-492B-8563-88936FD6607A.html)


## <a name="next-steps"></a>次のステップ
Azure VMware Solution 上の VMware Horizon の詳細については、[VMware Horizon に関する FAQ](https://www.vmware.com/content/dam/digitalmarketing/vmware/en/pdf/products/horizon/vmw-horizon-on-microsoft-azure-vmware-solution-faq.pdf) を参照してください。
