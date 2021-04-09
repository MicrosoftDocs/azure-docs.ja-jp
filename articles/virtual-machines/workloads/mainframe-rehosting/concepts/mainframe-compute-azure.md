---
title: メインフレーム コンピューティングを Azure Virtual Machines に移行する
description: Azure のコンピューティング リソースはメインフレームのキャパシティに十分に匹敵するので、IBM z14 アプリケーションを移行して最新化できます。
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: virtual-machines
ms.subservice: mainframe-rehosting
ms.openlocfilehash: 84682514d83de9d6a613581d68301efe33cd44ee
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2021
ms.locfileid: "104954396"
---
# <a name="move-mainframe-compute-to-azure"></a>メインフレーム コンピューティングを Azure に移行する

メインフレームは高い信頼性と可用性に定評があり、多くの企業のバックボーンとして信頼され続けています。 ほとんど無限のスケーラビリティとコンピューティング パワーを備えていると考えられることもよくあります。 しかし、利用可能な最大のメインフレームの能力を超えるほど成長する企業も出てきています。 それが当てはまるなら、Azure が俊敏性、リーチ、インフラストラクチャのコスト削減を提供します。

Microsoft Azure でメインフレームのワークロードを実行するには、メインフレームのコンピューティング能力と Azure の比較を知っておく必要があります。 IBM z14 メインフレーム (これを書いている時点で最新のモデル) を基にして、この記事では Azure で同等の結果を実現する方法を説明します。

まず最初に、環境を並べて見てみましょう。 次の図は、アプリケーションを実行するためのメインフレーム環境と Azure のホスティング環境を比較したものです。

![Azure のサービスとエミュレーション環境では、同等のサポートが提供され、移行が効率化される](media/mainframe-compute-azure.png)

メインフレームのパワーは、何千人ものユーザーの何百万もの更新を処理するオンライン トランザクション処理 (OLTP) システムによく使用されます。 このようなアプリケーションでは、多くの場合、トランザクション処理、画面処理、フォーム入力のためのソフトウェアが使用されます。 顧客情報管理システム (CICS)、情報管理システム (IMS)、またはトランザクション インターフェイス パッケージ (TIP) が使用されることもあります。

図で示されているように、Azure 上の TPM エミュレーターでは CICS と IMS のワークロードを処理できます。 Azure のバッチ システム エミュレーターでは、ジョブ制御言語 (JCL) の役割が実行されます。 メインフレームのデータは、Azure SQL Database などの Azure データベースに移行されます。 Azure のサービスまたは Azure Virtual Machines でホストされているその他のソフトウェアは、システム管理に使用できます。

## <a name="mainframe-compute-at-a-glance"></a>メインフレーム コンピューティングの概要

z14 メインフレームでは、プロセッサが最大 4 つの "*ドロワー*" に配置されています。 *ドロワー* は単に、プロセッサとチップセットのクラスターです。 各ドロアーは、6 個のアクティブな中央処理装置 (CP) チップを持つことができ、各 CP は 10 個のシステム コントローラー (SC) チップを備えています。 Intel x86 の用語では、ドロワーあたり 6 個のソケット、ソケットあたり 10 個のコア、そして 4 個のドロワーがあります。 このアーキテクチャでは、z14 の場合、最大でだいたい 24 個のソケットと 240 個のコアが提供されます。

z14 の高速 CP のクロック速度は 5.2 GHz です。 通常、z14 はすべての CP を備えて提供されます。 それらは必要に応じてアクティブ化されます。 顧客は一般に、実際の使用量に関係なく、1 か月あたり少なくとも 4 時間のコンピューティング時間を課金されます。

メインフレームのプロセッサは、次のいずれかの種類として構成できます。

- 汎用 (GP) プロセッサ
- システム z 統合情報プロセッサ (zIIP)
- Linux 統合ファシリティ (IFL) プロセッサ
- システム アシスト プロセッサ (SAP)
- 統合カップリング ファシリティ (ICF) プロセッサ

## <a name="scaling-mainframe-compute-up-and-out"></a>メインフレーム コンピューティングのスケールアップとスケールアウト

IBM メインフレームでは、最大 240 コアまでスケールアップできます数 (単一システムでの現在の z14 のサイズ)。 さらに、IBM メインフレームは、カップリング ファシリティ (CF) と呼ばれる機能によってスケールアウトできます。 CF では、複数のメインフレーム システムが同じデータに同時にアクセスできます。 CF を使用して、メインフレームの Parallel Sysplex テクノロジはメインフレームのプロセッサをクラスターにグループ化します。 このガイドが書かれた時点では、Parallel Sysplex 機能によって各 64 プロセッサの 32 グループがサポートされていました。 最大 2,048 個のプロセッサをこの方法でグループ化し、コンピューティング キャパシティをスケールアウトできます。

CF では、コンピューティング クラスターは直接アクセスでデータを共有できます。 それは、ロック情報、キャッシュ情報、および共有データ リソースのリストに使用されます。 1 つ以上の CF を使用する Parallel Sysplex は、"すべてが共有された" スケールアウト コンピューティング クラスターと考えることができます。 これらの機能について詳しくは、IBM の Web サイトの「[Parallel Sysplex on IBM Z (IBM Z での Parallel Sysplex)](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources)」をご覧ください。

アプリケーションでは、これらの機能を使用して、スケールアウト パフォーマンスと高可用性の両方を提供できます。 CICS が CF で Parallel Sysplex を使用する方法については、「[IBM CICS and the Coupling Facility: Beyond the Basics](https://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf)」レッドブックをダウンロードしてください。

## <a name="azure-compute-at-a-glance"></a>Azure コンピューティングの概要

Intel ベースのサーバーはメインフレームほどパワーがないと誤解されている人がいます。 しかし、新しいコア密度の Intel ベースのシステムのコンピューティング キャパシティはメインフレームに匹敵します。 このセクションでは、Azure のコンピューティングおよびストレージ向けのサービスとしてのインフラストラクチャ (IaaS) オプションについて説明します。 Azure ではサービスとしてのプラットフォーム (PaaS) のオプションも提供されていますが、この記事では、同等のメインフレーム キャパシティを提供する IaaS の選択肢に注目します。

Azure Virtual Machines では、さまざまなサイズと種類のコンピューティング パワーが提供されています。 Azure では、仮想 CPU (vCPU) がメインフレームのコアにほぼ相当します。

現在、Azure Virtual Machines で提供されているサイズの範囲は 1 - 128 vCPU までです。 仮想マシン (VM) の種類は、特定のワークロードに最適化されています。 たとえば、次の一覧は、VM の種類 (この記事の時点) とその推奨される用途を示したものです。

| サイズ     | 種類と説明                                                                 |
|----------|--------------------------------------------------------------------------------------|
| D シリーズ | 64 個の vCPU と最大 3.5 GHz のクロック速度で、一般的な用途                           |
| E シリーズ | 最大 64 個の vCPU によるメモリ最適化                                                 |
| F シリーズ | 最大 64 個の vCPUと 3.7 GHz のクロック速度によるコンピューティング最適化                       |
| H シリーズ | ハイ パフォーマンス コンピューティング (HPC) アプリケーション用に最適化                          |
| L シリーズ | NoSQL などのデータベースに支えられた高スループット アプリケーション用のストレージ最適化 |
| M シリーズ | 最大 128 個の vCPU を備えた、最大のコンピューティングおよびメモリ最適化 VM                        |

利用可能な VM について詳しくは、「[Virtual Machines シリーズ](https://azure.microsoft.com/pricing/details/virtual-machines/series/)」をご覧ください。

z14 メインフレームは、最大で 240 コアを使用できます。 しかし、z14 メインフレームで 1 つのアプリケーションまたはワークロードにすべてのコアが使用されることはほとんどありません。 実際には、メインフレームではワークロードが論理パーティション (LPAR) に分離され、LPAR にはレーティング (MIPS (百万命令/秒) または MSU (百万サービス単位)) があります。 Azure でメインフレームのワークロードを実行するために必要な匹敵する VM サイズを決めるときは、MIPS (または MSU) のレーティングを考慮します。

一般的な見積もりは次のとおりです。

-   vCPU あたり 150 MIPS

-   プロセッサあたり 1,000 MIPS

LPAR での特定のワークロードに対する適切な VM サイズを決めるには、最初にワークロード用に VM を最適化します。 次に、必要な vCPU の数を決定します。 控えめな推定値では、vCPU あたり 150 MIPS です。 この推定値を基にすると、たとえば、16 vCPU の F シリーズ VM では、2,400 MIPS の LPAR を使用する IBM Db2 のワークロードを簡単にサポートできます。

## <a name="azure-compute-scale-up"></a>Azure のコンピューティングのスケールアップ

M シリーズの VM は、128 vCPU までスケールアップできます (この記事の執筆時点)。 vCPU あたり 150 MIPS の控えめな推定値を使用しすると、M シリーズの VM は約 19,000 MIPS に相当します。 メインフレームの MIPS を推定する一般的な規則では、プロセッサあたり 1,000 MIPS です。 z14 メインフレームは、最大で 24 個のプロセッサを装備でき、単一のメインフレーム システムに対して約 24,000 MIPS を提供できます。

最大の単一 z14 メインフレームの方が、Azure で使用できる最大の VM より約 5,000 MIPS 多くなります。 ただし、ワークロードのデプロイ方法を比較することが重要です。 メインフレーム システムにアプリケーションとリレーショナル データベースの両方がある場合、通常は、同じ物理メインフレーム上の、それぞれに専用の LPAR にデプロイされます。 同じソリューションが、Azure では、多くの場合、アプリケーション用に 1 つの VM と、データベース用に独立した適切なサイズの VM を使用してデプロイされます。

たとえば、M64 vCPU システムでアプリケーションをサポートし、データベースには M96 vCPU を使用する場合は、次の図に示すように、約 150 個の vCPU つまり約 24,000 MIPS が必要です。

![24,000 MIPS のワークロードのデプロイの比較](media/mainframe-compute-mips.png)

方法としては、LPAR を個別の VM に移行します。 その後、Azure は、1 つのメインフレーム システムにデプロイされるほとんどのアプリケーションに必要なサイズに、簡単にスケールアップします。

## <a name="azure-compute-scale-out"></a>Azure のコンピューティングのスケールアウト

Azure ベースのソリューションの利点の 1 つは、スケールアウトする機能です。スケーリングにより、ほぼ無制限のコンピューティング キャパシティをアプリケーションに利用できます。 Azure では、コンピューティング パワーのスケールアウトに複数の方法がサポートされています。

- **クラスター全体への負荷分散。** このシナリオでは、アプリケーションは[ロード バランサー](../../../../load-balancer/load-balancer-overview.md)またはリソース マネージャーを使用して、クラスター内の複数の VM 間にワークロードを分散させることができます。 より多くのコンピューティング キャパシティが必要な場合は、VM がクラスターに追加されます。

- **仮想マシン スケール セット。** このバースト シナリオでは、アプリケーションは、VM の使用量に基づいて追加される[コンピューティング リソース](../../../../virtual-machine-scale-sets/overview.md)にスケーリングできます。 需要が低下すると、スケール セット内の VM の数も減り、コンピューティング パワーの効率的な使用が保証されます。

- **PaaS スケーリング。** Azure PaaS のオファリングでは、コンピューティング リソースがスケーリングされます。 たとえば、[Azure Service Fabric](../../../../service-fabric/service-fabric-overview.md) では、要求の量の増加に合わせてコンピューティング リソースが割り当てられます。

- **Kubernetes クラスター。** Azure 上のアプリケーションでは、指定されたリソースに対するコンピューティング サービス用に [Kubernetes クラスター](../../../../aks/concepts-clusters-workloads.md)を使用できます。 Azure Kubernetes Service (AKS) は、Kubernetes ノード、プール、および Azure 上のクラスターを調整するマネージド サービスです。

コンピューティング リソースをスケールアウトする適切な方法を選択するには、Azure とメインフレームの違いを理解することが重要です。 要点は、コンピューティング リソースによってデータが共有される方法、または共有されるかどうか、です。 Azure では、通常 (既定では)、データは複数の VM によって共有されません。 スケールアウト コンピューティング クラスター内の複数の VM によってデータを共有する必要がある場合は、共有されるデータが、この機能をサポートするリソース内に存在する必要があります。 次のセクションで説明するように、Azure では、データの共有にはストレージが関係します。

## <a name="azure-compute-optimization"></a>Azure のコンピューティングの最適化

Azure アーキテクチャでは処理の各レベルを最適化できます。 各環境に最適な種類の VM と機能を使用します。 次の図では、Db2 を使用する CICS アプリケーションをサポートするために、Azure で可能性のある 1 つの VM デプロイ パターンを示します。 プライマリ サイトには、運用、運用前、およびテスト用の VM が高可用性でデプロイされます。 セカンダリ サイトは、バックアップおよびディザスター リカバリー用です。

各レベルで適切なディザスター リカバリー サービスを提供することもできます。 たとえば、運用およびデータベース用の VM ではホットまたはウォーム リカバリーが必要であり、開発およびテスト用の VM ではコールド リカバリーをサポートするようなことがあります。

![ディザスター リカバリーをサポートする高可用性のデプロイ](media/mainframe-compute-dr.png)

## <a name="next-steps"></a>次のステップ

- [メインフレーム移行](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [Azure 仮想マシンでのメインフレーム リホスト](../overview.md)
- [メインフレーム ストレージを Azure に移動する](mainframe-storage-Azure.md)

### <a name="ibm-resources"></a>IBM リソース

- [IBM Z 上の Parallel Sysplex](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources)
- [IBM CICS and the Coupling Facility: Beyond the Basics](https://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf)
- [Db2 pureScale 機能のインストールに必要なユーザーを作成する](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)
- [Db2icrt - インスタンス コマンドを作成する](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)
- [Db2 pureScale クラスター化データベース ソリューション](https://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)
- [IBM Data Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

### <a name="azure-government"></a>Azure Government

- [メインフレーム アプリケーション用の Microsoft Azure Government クラウド](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/)
- [Microsoft と FedRAMP](https://www.microsoft.com/TrustCenter/Compliance/FedRAMP)

### <a name="more-migration-resources"></a>その他の移行リソース

- [Azure 仮想データ センターのリフト アンド シフト ガイド](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
- [GlusterFS iSCSI](https://glusterdocs.readthedocs.io/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/)
