---
title: Azure 上の IBM DB2 pureScale
description: この記事では、Azure 上で IBM DB2 pureScale 環境を実行するためのアーキテクチャを紹介します。
author: njray
manager: edprice
editor: edprice
ms.service: virtual-machines
ms.subservice: mainframe-rehosting
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/09/2018
ms.author: edprice
ms.openlocfilehash: 4ab0dfabc5fc4773413e7009bb8a16e6b77d5c09
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2021
ms.locfileid: "104950197"
---
# <a name="ibm-db2-purescale-on-azure"></a>Azure 上の IBM DB2 pureScale

IBM DB2 pureScale 環境は、Linux オペレーティング システム上で高可用性とスケーラビリティを備えた Azure 向けデータベース クラスターを提供します。 この記事では、Azure 上で DB2 pureScale を実行するためのアーキテクチャを紹介します。

## <a name="overview"></a>概要

企業では長い間、オンライン トランザクション処理 (OLTP) のニーズに応えるために、従来のリレーショナル データベース管理システム (RDBMS) プラットフォームを使用してきました。 今日、容量を拡大し、コストを削減し、一定した運用コスト構造を維持するために、多くの企業がメインフレーム ベースのデータベース環境を Azure に移行しています。 多くの場合、移行はレガシープラットフォームを最新化する最初のステップとなります。 

最近、ある企業のお客様は、z/OS 上で動作している IBM DB2 環境を Azure 上の IBM DB2 pureScale に再ホストしました。 DB2 pureScale データベース クラスター ソリューションは、Linux オペレーティング システムで高可用性とスケーラビリティを提供します。 お客様は、DB2 pureScale をインストールする前に、Azure の大規模なスケールアップ システムの単一の仮想マシン (VM) で、スタンドアロンのスケールアップ インスタンスとして DB2 を正常に実行しました。 

元の環境と同一ではありませんが、Linux 上の IBM DB2 pureScale は、メインフレーム上の Parallel Sysplex 構成で実行される z/OS 向けの IBM DB2 とほぼ同等の高可用性とスケーラビリティの機能を提供します。 このシナリオでは、クラスターは iSCSI 経由で共有ストレージ クラスターに接続されます。 特にクラウド ストレージに対して最適化された無料のスケーラブルなオープン ソース分散ファイル システムとして、GlusterFS ファイル システムを使用しました。 ただし、IBM ではこのソリューションはサポートされなくなりました。 IBM からのサポートを維持するには、サポートされている iSCSI 互換ファイル システムを使用する必要があります。 Microsoft では、記憶域スペース ダイレクト (S2D) をオプションとして提供しています。

この記事では、この Azure への移行に使用されるアーキテクチャについて説明します。 顧客は、構成をテストするために Red Hat Linux 7.4 を使用していました。 このバージョンは、Azure Marketplace から入手できます。 Linux ディストリビューションを選択する前に、現在サポートされているバージョンを必ず確認してください。 詳細については、[IBM DB2 pureScale](https://www.ibm.com/support/knowledgecenter/SSEPGG) と [GlusterFS](https://docs.gluster.org/en/latest/) に関するドキュメントを参照してください。

この記事は、DB2 実装計画の出発点です。 業務要件は違っても、同じ基本パターンが適用されます。 また、このアーキテクチャ パターンは、Azure 上のオンライン分析処理 (OLAP) アプリケーションにも使用できます。

この記事では、z/OS 対応の IBM DB2 データベースから Linux 上で実行される IBM DB2 pureScale へ移行する際の相違点や想定される移行タスクについては説明しません。 また、DB2 z/OS から DB2 pureScale へ移行する際のサイズ設定の見積もりやワークロード分析についても説明しません。 

お使いの環境に最適な DB2 pureScale アーキテクチャを決定できるように、十分にサイズ設定を見積もり、仮説を立てることをお勧めします。 ソース システム上で、データ共有アーキテクチャ、Coupling Facility 構成、分散データ機能 (DDF) 利用状況統計が備わった DB2 z/OS Parallel Sysplex について必ず検討してください。

> [!NOTE]
> この記事では、DB2 移行の 1 つの方法を取り上げていますが、方法はほかにもあります。 たとえば、DB2 pureScale は仮想化されたオンプレミス環境で実行することもできます。 IBM ではさまざまな構成で、Microsoft Hyper-V 上の DB2 をサポートしています。 詳細については、IBM Knowledge Center の[DB2 pureScale 仮想化アーキテクチャ](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/r0061462.html)に関するページを参照してください。

## <a name="architecture"></a>Architecture

Azure 上での高可用性とスケーラビリティをサポートするために、スケールアウトの共有データ アーキテクチャを DB2 pureScale に使用できます。 顧客の移行では、次のアーキテクチャの例を使用しました。

![ストレージとネットワークを示した Azure 仮想マシン上の DB2 pureScale](media/pureScaleArchitecture.png "ストレージとネットワークを示した Azure 仮想マシン上の DB2 pureScale")


この図は、DB2 pureScale クラスターに必要な論理層を示しています。 これらには、クライアント用、管理、キャッシング用、データベース エンジン用、および共有ストレージ用の仮想マシンが含まれます。 

この図には、データベース エンジン ノードに加えて、クラスター キャッシング機能 (CF) に使用される 2 つのノードも含まれています。 データベース エンジン自体には、最低 2 つのノードが使用されます。 pureScale クラスターに属している DB2 サーバーはメンバーと呼ばれています。 

クラスターは、スケールアウト ストレージと高可用性を提供するために、iSCSI 経由で 3 ノード共有ストレージ クラスターに接続されています。 DB2 pureScale は、Linux を実行している Azure 仮想マシンにインストールされます。

この方法は、組織の規模とスケールに応じて変更できるテンプレートです。 これは以下の項目に基づいています。

-   2 つ以上のデータベース メンバーが 2 つ以上の CF ノードと組み合わされます。 このノードは、共有メモリのグローバル バッファー プール (GBP) とグローバル ロック マネージャー (GLM) サービスを管理して、アクティブなメンバーからの共有アクセスとロックの競合を制御します。 1 つの CF ノードはプライマリとして、もう 1 つはセカンダリのフェールオーバー CF ノードとして機能します。 その環境内で単一障害点を回避するために、DB2 pureScale クラスターには 4 つ以上のノードが必要です。

-   高パフォーマンスの共有ストレージ (図の P30 サイズに表示)。 各ノードはこのストレージを使用します。

-   データ メンバーと共有ストレージの高パフォーマンス ネットワーク。

### <a name="compute-considerations"></a>コンピューティングに関する考慮事項

このアーキテクチャは、Azure 仮想マシン上でアプリケーション、ストレージ、およびデータ層を実行します。 [展開セットアップ スクリプト](https://aka.ms/db2onazure)では、以下を作成します。

-   DB2 pureScale クラスター。 Azure 上で必要なコンピューティング リソースの種類は、設定に依存します。 一般的には、次の 2 つの方法を使用できます。

    -   マルチノードの、ハイパフォーマンス コンピューティング (HPC) 形式のネットワークを使用する。この場合、小規模から中規模サイズのインスタンスから共有ストレージにアクセスします。 この HPC タイプの構成では、Azure のメモリ最適化 E シリーズまたはストレージ最適化 L シリーズの[仮想マシン](../../../sizes.md)によって、必要なコンピューティング性能が提供されます。

    -   データ エンジンに使用する大規模な仮想マシン インスタンスの数を減らす。 大規模なインスタンスの場合、大量のメモリ内ワークロードには最大のメモリ最適化 [M シリーズ](https://azure.microsoft.com/pricing/details/virtual-machines/series/)の仮想マシンが理想的です。 DB2 の実行に使用される論理パーティション (LPAR) のサイズによっては、専用のインスタンスが必要になる場合があります。

-   DB2 CF では、E シリーズや L シリーズなど、メモリ最適化の仮想マシンを使用します。

-   Linux を実行している Standard\_DS4\_v2 仮想マシンを使用する共有ストレージ クラスター。

-   管理ジャンプボックスは、Linux を実行している Standard\_DS2\_v2 仮想マシンです。  別の方法として Azure Bastion もあります。これは、仮想ネットワーク内のすべての VM にセキュリティで保護された RDP/SSH エクスペリエンスを提供するサービスです。

-   クライアントは、Windows を実行している Standard\_DS3\_v2 仮想マシンです (テストに使用されます)。

-   *オプション*。 ミラーリング監視サーバー。 これは、以前の特定のバージョンの DB2 pureScale でのみ必要です。 この例では、Linux を実行している Standard\_DS3\_v2 仮想マシンを使用します (DB2 pureScale に使用されます)。

> [!NOTE]
> DB2 pureScale クラスターには、DB2 インスタンスが 2 つ以上必要です。 また、キャッシュ インスタンスとロック マネージャー インスタンスも必要になります。

### <a name="storage-considerations"></a>ストレージに関する考慮事項

Oracle RAC と同様に、DB2 pureScale は、高パフォーマンス ブロック I/O のスケールアウト データベースです。 ニーズに適した最大の [Azure Premium SSD](../../../disks-types.md) オプションを使用することをお勧めします。 開発環境とテスト環境には、より小規模なストレージ オプションが適しているかもしれませんが、運用環境では多くの場合、ストレージ容量がさらに必要になります。 例のアーキテクチャでは、サイズと価格に対する IOPS の比率から、[P30](https://azure.microsoft.com/pricing/details/managed-disks/) を使用しています。 最適なパフォーマンスのためには、サイズに関係なく Premium Storage を使用します。

DB2 pureScale では、あらゆるものを共有するアーキテクチャを使用します。ここでは、全クラスター ノードからすべてのデータにアクセスできます。 オンデマンドまたは専用インスタンスのどちらであっても、複数のインスタンス間で Premium Storage を共有する必要があります。

大規模な DB2 pureScale クラスターでは、100,000 の IOPS と共に、200 テラバイト (TB) 以上のプレミアム共有ストレージが必要になる場合があります。 DB2 pureScale では、Azure 上で使用できる iSCSI ブロック インターフェイスがサポートされています。 iSCSI インターフェイスには、S2D または別のツールを使用して実装できる共有ストレージ クラスターが必要になります。 このタイプのソリューションでは、Azure 上に仮想ストレージ エリア ネットワーク (vSAN) デバイスを作成します。 DB2 pureScale では vSAN を使って、仮想マシン間でのデータ共有に使用されるクラスター化されたファイル システムをインストールします。

### <a name="networking-considerations"></a>ネットワークに関する考慮事項

IBM は、DB2 pureScale クラスター内のすべてのメンバーに対して InfiniBand ネットワークを推奨しています。 DB2 pureScale では、利用可能な場合は CF にリモート ダイレクト メモリ アクセス (RDMA) も使用されます。

セットアップ中に、Azure [リソース グループ](../../../../azure-resource-manager/management/overview.md)を作成してすべての仮想マシンを含めます。 一般には、リソースの有効期間や管理者に基づいて、リソースをグループ化します。 このアーキテクチャ内の仮想マシンには、[高速ネットワーク](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/)が必要です。 これは、1 つの仮想マシンに対してシングルルート I/O 仮想化 (SR-IOV) を介して、一貫性のある超低ネットワーク待ち時間を提供する Azure 機能です。

どの Azure 仮想マシンも、メイン、Gluster FS フロントエンド (gfsfe)、Gluster FS バックエンド (bfsbe)、DB2 pureScale (db2be)、DB2 pureScale フロントエンド (db2fe) というサブネットを含む仮想ネットワークにデプロイされます。 また、インストール スクリプトは、メイン サブネットの仮想マシン上にプライマリ [NIC](../../../windows/multiple-nics.md) を作成します。

仮想ネットワーク内のネットワーク トラフィックを制限したり、サブネットを分離したりするには、[ネットワーク セキュリティ グループ](../../../../virtual-network/virtual-network-vnet-plan-design-arm.md)を使用します。

Azure では、DB2 pureScale は、ストレージのネットワーク接続として TCP/IP を使用する必要があります。

## <a name="next-steps"></a>次のステップ

-   [Azure 上にこのアーキテクチャを展開する](deploy-ibm-db2-purescale-azure.md)