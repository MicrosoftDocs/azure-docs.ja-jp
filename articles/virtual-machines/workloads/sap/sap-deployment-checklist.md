---
title: SAP ワークロードの計画とデプロイに関するチェックリスト | Microsoft Docs
description: Azure での SAP ワークロードの計画とデプロイに関するチェックリストです
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/01/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fef2d42282291bb0ea6afeea03e60234d3d47a4d
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2019
ms.locfileid: "58878725"
---
# <a name="sap-workload-on-azure-planning-and-deployment-checklist"></a>Azure での SAP ワークロードの計画とデプロイに関するチェックリスト 

このチェックリストは、SAP NetWeaver、S/4HANA、および Hybris のアプリケーションを Azure のサービスとしてのインフラストラクチャに移行するお客様を対象に作られています。  お客様および SAP パートナーは、プロジェクトの期間中にこのチェックリストを見直す必要があります。 プロジェクトの開始時と計画段階に、多くのチェックを行うことが重要です。 デプロイが完了した後では、デプロイされている Azure インフラストラクチャまたは SAP ソフトウェア リリースでの基本的な変更が、複雑になる場合があります。 プロジェクト全体の重要なマイルストーンで、このチェックリストを確認してください。  小さな問題を大きな問題になる前に検出できるので、必要な変更を再設計してテストするのに十分な時間があります。 チェックリストは完全なものではありません。 個々の状況により、さらに多くのチェックを実施することが必要になる場合があります。 

作成されたチェックリストには、Azure とは関係のないタスクは含まれません。  例:SAP アプリケーションのインターフェイスは、Azure パブリック クラウドまたはホスティング プロバイダーへの移行の間に変化します。    

このチェックリストは、既にデプロイされているシステムにも使用できます。 デプロイした後で、書き込みアクセラレータ、Availability Zones、新しい VM の種類などの新機能が、追加されている可能性があります。  そのため、チェックリストを定期的に確認すると、Azure プラットフォームの新機能を把握していることを確認するのに役立ちます。 

## <a name="project-preparation-and-planning-phase"></a>プロジェクトの準備と計画フェーズ
このフェーズでは、Azure パブリック クラウドへの SAP ワークロードの移行を計画します。 検討および定義するエンティティと項目の最小限のセットを次に示します。

1. 概要設計ドキュメント – このドキュメントに必要な内容は次のとおりです。
    1. SAP コンポーネントとアプリケーションの現在のインベントリ、および Azure でのターゲット アプリケーション インベントリ
    2. さまざまな関係者の責任と分担を定義する責任分担表 (RACI) を作成して、処理します。 計画と最初のデプロイを通して、大ざっぱなレベルで開始し、徐々に詳細なレベルに進みます
    2. 概要ソリューション アーキテクチャ
    3. デプロイする Azure リージョンについての決定。 Azure リージョンの一覧については、「[Azure リージョン](https://azure.microsoft.com/global-infrastructure/regions/)」をご覧ください。 各 Azure リージョンで利用できるサービスについては、「[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/)」をご覧ください
    4. オンプレミスから Azure に接続するためのネットワーク アーキテクチャ。 [Azure の仮想データ センターのブループリント](https://docs.microsoft.com/azure/architecture/vdc/)に慣れ始めます
    5. ビジネスに大きな影響のあるデータを Azure で実行するためのセキュリティ原則。 資料を読む場合は、「[Azure のセキュリティのドキュメント](https://docs.microsoft.com/azure/security/)」から始めます
2.  技術設計ドキュメント – 次のものが含まれます。
    1.  ソリューション ブロック図 
    2.  Azure でのコンピューティング、ストレージ、ネットワーク コンポーネントのサイズの決定。 Azure VM の SAP のサイズ設定については、SAP サポート ノート [#1928533](https://launchpad.support.sap.com/#/notes/1928533) をご覧ください 
    3.  ビジネス継続性とディザスター リカバリーのアーキテクチャ
    4.  OS、DB、カーネル、SAP サポート パック バージョンの詳細。 SAP NetWeaver または S/4HANA でサポートされているすべての OS リリースが Azure VM でもサポートされているとは限りません。 DBMS のリリースについても同じです。 整合していることを確認し、必要であれば SAP リリース、DBMS リリース、または OS リリースをアップグレードして SAP と Azure すのサポート対象期間内にするには、以下のソースをチェックする必要があります。 SAP と Microsoft による完全なサポートを受けるには、SAP と Azure のサポート対象リリースの組み合わせになっている必要があります。 必要に応じて、一部のソフトウェア コンポーネントのアップグレードを計画する必要があります。 サポートされている SAP、OS、DBMS ソフトウェアの詳細については、次の場所に記載されています:
        1.  SAP サポート ノート [#1928533](https://launchpad.support.sap.com/#/notes/1928533)。 このノートでは、Azure VM でサポートされる最小の OS リリースが定義されています。 また、ほとんどの非 HANA データベースに必要な最小のデータベース リリースも定義されています。 また、ノートでは、SAP でサポートされるさまざまな Azure VM の 種類に対する SAP のサイズ設定も示されています。
        2.  SAP サポート ノート [#2039619](https://launchpad.support.sap.com/#/notes/2039619)。 ノートでは、Azure での Oracle サポートのマトリックスが定義されています。 Oracle では、Azure for SAP ワークロードでのゲスト OS として、Windows と Oracle Linux のみがサポートされていることに注意してください。 このサポートに関する声明は、SAP インスタンスを実行する SAP アプリケーション層にも適用されます。 ただし、Oracle では、Pacemaker による Oracle Linux での SAP セントラル サービスの高可用性はサポートされていません。 Oracle Linux 上での ASCS の高可用性が必要な場合は、SIOS Protection Suite for Linux を利用する必要があります。 詳細な SAP 認定データについては、SAP サポート ノート [#1662610 - Support details for SIOS Protection Suite for Linux (SIOS Protection Suite for Linux のサポートの詳細)](https://launchpad.support.sap.com/#/notes/1662610) をご覧ください。 Windows では、SAP がサポートする SAP セントラル サービスの Windows フェールオーバー クラスター ソリューションが、DBMS レイヤーとして Oracle と共にサポートされています。 
        3.  SAP サポート ノート [#2235581](https://launchpad.support.sap.com/#/notes/2235581) では、さまざまな OS リリースでの SAP HANA のサポート マトリックスを入手できます
        4.  SAP HANA でサポートされる Azure VM と [HANA Large Instances](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) の一覧は、[こちら](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)をご覧ください
        5.  [SAP 製品の可用性マトリックス](https://support.sap.com/en/)
        6.  他の SAP 固有製品に関する他の SAP ノート  
    5.  SAP の運用システムには厳密な 3 層設計をお勧めします。 同じ VM でASCS と APP サーバーを組み合わせることはお勧めしません。  SAP セントラル サービスに対するマルチ SID クラスター構成の使用は、Azure でゲスト OS として Windows を使用する場合にサポートされます。 一方、Azure 上の Linux オペレーティング システムでは、SAP セントラル サービスのマルチ SID クラスター構成はサポートされません。 Windows ゲスト OS の場合のドキュメントは、以下をご覧ください。
        1.  [Azure で Windows Server フェールオーバー クラスタリングと共有ディスクを使用する SAP ASCS/SCS インスタンスのマルチ SID 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ascs-ha-multi-sid-wsfc-shared-disk)
        2.  [Azure での Windows Server フェールオーバー クラスタリングとファイル共有による SAP ASCS/SCS インスタンスのマルチ SID 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ascs-ha-multi-sid-wsfc-file-share)
    6.  高可用性とディザスター リカバリーのアーキテクチャ
        1.  RTO と RPO に基づいて、必要な高可用性とディザスター リカバリー アーキテクチャの概要を定義します
        2.  同じゾーン内での高可用性の場合は、Azure で目的の DBMS が提供する必要のあるものを確認します。 ほとんどの DBMS では同期ホット スタンバイの同期メソッドが提供されており、運用システムに推奨されます。 「[SAP ワークロードのための Azure Virtual Machines DBMS デプロイの考慮事項](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)」と関連ドキュメントから始まるさまざまなデータベースについての SAP 関連ドキュメントもチェックしてください。
            1.  たとえば[こちら](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server?view=sql-server-2017)で SQL Server について説明されているように、DBMS レイヤーの共有ディスク構成で Windows フェールオーバー クラスター サービスを使用することは、サポートされて**いません**。 代わりに次のようなソリューションがあります。
                1.  [SQL Server AlwaysOn](https://docs.microsoft.com/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups) 
                2.  [Oracle データの保護](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/configure-oracle-dataguard)
                3.  [HANA システム レプリケーション](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html)
        3.  異なる Azure リージョン間のディザスター リカバリーの場合は、さまざまな DBMS ベンダーによって提供される可能性を確認します。 そのほとんどでは、非同期レプリケーションまたはログ配布がサポートされています
        4.  SAP アプリケーション層では、ビジネス回帰テスト システムを実行するかどうかを定義します。理想的には、同じ Azure リージョンまたはお使いの DR リージョン内の、運用デプロイのレプリカです。 後者の場合、そのビジネス回帰システムを運用環境の DR ターゲットとすることができます
        5.  DR サイトに非運用システムを置かない場合は、Azure DR リージョンに SAP アプリケーション層をレプリケートする実行可能な方法として、Azure Site Recovery を検討します。 「[多層 SAP NetWeaver アプリ デプロイのディザスター リカバリーを設定する](https://docs.microsoft.com/azure/site-recovery/site-recovery-sap)」もご覧ください 
        6.  [Azure Availability Zones](https://docs.microsoft.com/azure/availability-zones/az-overview) を利用する結合された HA/DR 構成を使用する場合は、可用性ゾーンを利用できる Azure リージョンと、2 つの可用性ゾーン間のネットワーク待機時間の増加によってもたらされる可能性のある制限についてよく理解します  
3.  顧客/パートナーは、すべての SAP インターフェイス (SAP と SAP 以外) のインベントリを作成する必要があります。 
4.  Foundation Services の設計 - この設計には次のような項目が含まれます
    1.  Active Directory と DNS の設計
    2.  Azure 内のネットワーク トポロジと、さまざまな SAP システムの割り当て
    3.  Azure 内のインフラストラクチャと SAP アプリケーションを管理するさまざまなチームの[ロール ベース アクセス](https://docs.microsoft.com/azure/role-based-access-control/overview)の構造
    3.  リソース グループのトポロジ 
    4.  [タグ付けの戦略](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags#tags-and-billing)
    5.  VM と他のインフラストラクチャ コンポーネントおよび論理名の名前付け規則
5.  Microsoft Premier サポート契約 – MS テクニカル アカウント マネージャー (TAM) を識別します。 SAP によるサポート要件については、SAP サポート ノート [#2015553](https://launchpad.support.sap.com/#/notes/2015553) をご覧ください 
6.  さまざまなサブスクリプションに対する Azure サブスクリプションの数とコア クォータを定義します。 必要に応じて、[Azure サブスクリプションのクォータを増やすサポート要求を開きます](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) 
7.  Azure への SAP データの移行に関するデータの削減とデータ移行計画。 SAP NetWeaver システムについては、制限付きの膨大なデータ数のボリュームを保持する方法についてのガイドラインが SAP にあります。 SAP では、SAP ERP システムでのデータ管理に関する[こちらの広範なガイド](https://help.sap.com/http.svc/rc/2eb2fba8f8b1421c9a37a8d7233da545/7.0/en-US/Data_Management_Guide_Version_70E.PDF)が公開されています。 ただし、一部のコンテンツは、NetWeaver と S/4HANA システムに一般に適用されます。
8.  自動デプロイ方法を定義して決定します。 Azure へのインフラストラクチャ デプロイの背後にある自動化の目的は、決定論的な方法でデプロイし、決定論的な結果を得ることです。 多くのお客様は、PowerShell または CLI ベースのスクリプトを使用します。 しかし、SAP 用に Azure インフラストラクチャをデプロイしたり、SAP ソフトウェアをインストールしたりすることに使用できるさまざまなオープン ソース テクノロジがあります。 例は GitHub で見つかります。
    1.  [Azure Cloud での SAP の自動デプロイ](https://github.com/Azure/sap-hana)
    2.  [SAP HANA のインストール](https://github.com/AzureCAT-GSI/SAP-HANA-ARM)
9.  お客様、システム インテグレーター、Microsoft、およびその他の関連するパーティの間での、定期的な設計およびデプロイ レビューの頻度を定義します

 
## <a name="pilot-phase-strongly-recommended"></a>パイロット フェーズ (強く推奨)
 
パイロットは、事前に、またはプロジェクトの計画および準備と並行して、実行できます。 また、計画および準備フェーズにおいて作成されたアプローチと設計をテストするために、このフェーズを使用することもできます。 パイロット フェーズは、実際の概念実証まで拡大できます。 パイロット デプロイの間に、完全な HA/DR ソリューションおよびセキュリティ設計を設定して検証することが推奨されます。 お客様によっては、スケーラビリティ テストもこのフェーズで実施できます。 他のお客様は、SAP サンドボックス システムのデプロイをパイロット フェーズとして使用します。 そのため、パイロットを実行するために Azure に移行するシステムが識別されているものと想定します。

1. Azure へのデータ転送を最適化します。 お客様のケースに大きく依存しますが、Express 回線に十分な帯域幅がある場合、[Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) によるオンプレミスへの転送が最も高速でした。 他のお客様では、インターネットを使用すると高速であるのがわかりました
2. データベース データのエクスポートとインポートが含まれる SAP 異種プラットフォームの移行の場合は、エクスポートおよびインポート フェーズをテストして最適化します。 移行先プラットフォームとして SQL Server が含まれる大規模な移行に対する推奨事項については、[こちら](https://techcommunity.microsoft.com/t5/Running-SAP-Applications-on-the/SAP-OS-DB-Migration-to-SQL-Server-8211-FAQ-v6-2-April-2017/ba-p/368070)をご覧ください。 たとえば「[Database Migration Option (DMO) of SUM 2.0 SP03](https://launchpad.support.sap.com/#/notes/2631152)」 (SUM 2.0 SP03 のデータベース移行オプション (DMO)) で説明されているように、移行と SAP リリースのアップグレードを組み合わせて、ソースとターゲットの DBMS プラットフォームで特定の組み合わせを実現するときに、組み合わされたリリース アップグレードまたは [SAP DMO](https://blogs.sap.com/2013/11/29/database-migration-option-dmo-of-sum-introduction/) プロセスが必要ない場合は、Migration Monitor/SWPM のアプローチを採用できます。 
   1.  ソースへのエクスポート、Azure へのエクスポート ファイルのアップロード、インポートのパフォーマンス。  エクスポートとインポートの間のオーバーラップを最大化します
   2.  インフラストラクチャのサイズ設定に反映するには、ターゲットと移行先プラットフォームの間でデータベースのボリュームを評価します    
   3.  タイミングを検証して最適化します 
3. 技術的な検証 
   1. VM の種類
      1.  SAP サポート ノート、SAP HANA ハードウェア ディレクトリ、および再び SAP PAM のリソースを検証し、Azure でサポートされる VM、それらの VM の種類に対してサポートされる OS のリリース、サポートされる SAP および DBMS のリリースに、変更がないことを確認します
      2.  もう一度、Azure にデプロイするアプリケーションおよびインフラストラクチャのサイズを検証します。 既存のアプリケーションの移行では、多くの場合、使用しているインフラストラクチャと [SAP ベンチマーク Web ページ](https://www.sap.com/dmc/exp/2018-benchmark-directory/#/sd)から必要な SAPS を取得し、それを SAP サポート ノート [#1928533](https://launchpad.support.sap.com/#/notes/1928533) に記載されている SAPS 番号と比較することができます。 また、[こちらの記事](https://techcommunity.microsoft.com/t5/Running-SAP-Applications-on-the/SAPS-ratings-on-Azure-VMs-8211-where-to-look-and-where-you-can/ba-p/368208)にも留意してください
      3.  計画フェーズで選択したさまざまな種類の VM の最大ストレージ スループットとネットワーク スループットに関して、Azure VM のサイズ設定を評価およびテストします。 データは以下で見つかります。
          1.  「[Azure の Windows 仮想マシンのサイズ](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json)」。 サイズ設定では**キャッシュされていないディスクの最大スループット**を検討することが重要です
          2.  「[Azure の Linux 仮想マシンのサイズ](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json)」。サイズ設定では**キャッシュされていないディスクの最大スループット**を検討することが重要です
   2. Storage
      1.  SAP アプリケーション レイヤーを表す VM や、さほどパフォーマンスが要求されない DBMS デプロイには、最小要件として [Azure Standard SSD ストレージ](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-ssd)を使用します
      2.  一般に、[Azure Standard HDD ディスク](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-hdd)は使用しないことをお勧めします
      2.  少しでもパフォーマンスが重視される DBMS VM には、[Azure Premium Storage](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#premium-ssd) を使用します
      2.  [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/) を使用します
      3.  M シリーズの DBMS ログ ドライブには、Azure 書き込みアクセラレータを使用します。 [書き込みアクセラレータ](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator)に関する記事に記載されている書き込みアクセラレータの制限と使用方法に注意してください
      4.  DBMS のさまざまな種類については、[一般的な SAP 関連の DBMS ドキュメント](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)と、一般ドキュメントで参照されている DBMS ごとの個別ドキュメントを確認してください
      5.  SAP HANA についての詳細は、「[Azure における SAP HANA インフラストラクチャの構成と運用](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)」をご覧ください
      6.  デバイス ID を使用して Azure データ ディスクを Azure Linux VM にマウントしないでください。 代わりに、汎用一意識別子 (UUID) を使用します。 たとえば、グラフィカル ツールを使用して Azure データ ディスクをマウントする場合には注意してください。 /etc/fstab 内のエントリを再確認して、UUID を使用して、ディスクがマウントされていることを確認します
          1.  詳細については、 [こちら](https://docs.microsoft.com/azure/virtual-machines/linux/attach-disk-portal#connect-to-the-linux-vm-to-mount-the-new-disk)
   3. ネットワーク
      1.  VNet のインフラストラクチャ、および Azure 仮想ネットワーク内またはネットワーク間での SAP アプリケーションの分散をテストして評価します
          1.  以下に基づいて、ハブとスポークの仮想ネットワーク アーキテクチャ、または単一の Azure 仮想ネットワーク内でのマイクロセグメンテーションのアプローチを評価します
              1.  [ピアリングされた Azure VNet](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) 間のデータ交換のためのコスト。 コストについては、「[仮想ネットワークの価格](https://azure.microsoft.com/pricing/details/virtual-network/)」を確認してください
              2.  仮想ネットワークのサブネットでホストされているアプリケーションまたは VM がセキュリティ リスクになった場合の、NSG の変更による仮想ネットワーク内のサブネットの分離と比較したときの、Azure 仮想ネットワーク間のピアリングの高速切断の利点
              3.  オンプレミス、外部世界、および Azure に構築した仮想データセンターの間のネットワーク トラフィックの集中ログ記録と監査
          2.  SAP アプリケーション層と SAP DBMS 層の間のデータ パスを評価およびテストします。 
              1.  SAP アプリケーションと、SAP NetWeaver、Hybris、または S/4HANA ベースの SAP システムの DBMS 層の間の通信パスに [Azure ネットワーク仮想アプライアンス](https://azure.microsoft.com/solutions/network-appliances/)を配置することは、サポートされていません
              2.  ピアリングされていない異なる Azure 仮想ネットワークに、SAP アプリケーション層と SAP DBMS を配置することは、サポートされていません
              3.  [Azure の ASG と NSG ルール](https://docs.microsoft.com/azure/virtual-network/security-overview)は、SAP アプリケーション層と SAP DBMS 層の間のルートを定義するためにサポートされます
          3.  SAP アプリケーション層および SAP DBMS 層で使用される VM 上で、[Azure 高速ネットワーク](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/)が有効になっていることを確認します。 Azure で高速ネットワークをサポートするには、異なる OS レベルが必要であることに留意してください。
              1.  Windows Server 2012 R2 またはそれ以降のリリース
              2.  SUSE Linux 12 SP3 またはそれ以降のリリース
              3.  RHEL 7.4 またはそれ以降のリリース
              4.  Oracle Linux 7.5。 RHCKL カーネルを使用するには、リリースが 3.10.0-862.13.1.el7 である必要があります。 Oracle UEK カーネル リリース 5 を使用する必要があります
          4.   SAP サポート ノート [#500235](https://launchpad.support.sap.com/#/notes/500235) および SAP サポート ノート [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E) に従って、SAP アプリケーション層 VM と DBMS VM の間のネットワーク待機時間をテストして評価します。 SAP サポート ノート [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E) のネットワーク待機時間のガイダンスに照らして結果を評価します。 ネットワーク待機時間は、中程度から良好な範囲でなければなりません。 [こちら](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance)に記載されているように、VM と HANA Large Instance ユニットの間のトラフィックには例外が適用されます
          5.   Direct Server Return を使用するように ILB のデプロイが設定されていることを確認します。 DBMS 層での高可用性構成用に Azure ILB が使用されている場合、この設定により待機時間が短縮されます
          6.   Azure Load Balancer を Linux ゲスト オペレーティング システムと共に使用している場合、Linux ネットワーク パラメーター **net.ipv4.tcp_timestamps** が **0** に設定されていることを確認します。 以前のバージョンの SAP Note [#2382421](https://launchpad.support.sap.com/#/notes/2382421) に記載されていた推奨情報とは矛盾します。 Azure Load Balancer と連動させるためには、このパラメーターを 0 に設定する必要があり、その点を反映するために、この SAP Note もいずれ更新されます。
   4. 高可用性とディザスター リカバリーのデプロイ 
      1. 特定の Azure 可用性ゾーンを定義しないで SAP アプリケーション層をデプロイする場合は、単一の SAP システムの SAP ダイアログ インスタンスまたはミドルウェア インスタンスを実行するすべての VM が、[可用性セット](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability)にデプロイされることを確認します。 
         1.   SAP セントラル サービスと DBMS に対して高可用性が必要ない場合は、これらの VM を SAP アプリケーション層と同じ可用性セットにデプロイできます
      2. パッシブ レプリカによる高可用性用に SAP セントラル サービスと DBMS 層を保護する場合は、SAP セントラル サービス用の 2 つのノードを 1 つの独立した可用性セットに配置し、2 つの DBMS ノードを別の可用性セットに配置します
      3. Azure Availability Zones にデプロイする場合は、可用性セットを利用できません。 ただし、アクティブとパッシブのセントラル サービス ノードを 2 つの異なる Availability Zones にデプロイする必要があります。このようにすると、ゾーン間の待機時間が最小になります。
         1.   可用性ゾーンをまたがる DBMS および SAP セントラル サービス層に対して Windows または Pacemaker のフェールオーバー クラスターを確立する場合は、[Azure Standard Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones) を使用する必要があることに注意してください。 [Basic Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview#skus) は、ゾーン間のデプロイには使用できません 
   5. タイムアウトの設定
      1. 異なる SAP インスタンスの SAP NetWeaver 開発者トレースをチェックして、エンキュー サーバーと SAP ワーク プロセスの間の接続が切断されていないことを確認します。 このような接続の切断は、次の 2 つのレジストリ パラメーターを設定することによって回避できます。
         1.   HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\KeepAliveTime = 120000 - [こちらの記事](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-2000-server/cc957549(v=technet.10))もご覧ください
         2.   HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\KeepAliveInterval = 120000 - [こちらの記事](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-2000-server/cc957548(v=technet.10))もご覧ください 
      2. オンプレミスにデプロイされた SAP GUI インターフェイスと、Azure にデプロイされた SAP アプリケーション層の間での GUI のタイムアウトを回避するため、default.pfl またはインスタンス プロファイルで次のパラメーターが設定されていることを確認します。
         1.   rdisp/keepalive_timeout = 3600
         2.   rdisp/keepalive = 20
      3. Windows フェールオーバー クラスター構成を使用する場合は、応答のないノードに対応する時間が Azure に対して正しく設定されていることを確認します。 Microsoft の記事「[Tuning Failover Cluster Network Thresholds (フェールオーバー クラスター ネットワークしきい値の調整)](https://blogs.msdn.microsoft.com/clustering/2012/11/21/tuning-failover-cluster-network-thresholds/)」では、パラメーターの一覧と、それらがフェールオーバーの感度に及ぼす影響が示されています。 一覧のパラメーターのうち、次の 2 つのパラメーターには次の値を設定する必要があります。
         1.   SameSubNetDelay = 2
         2.   SameSubNetThreshold = 15
4. 高可用性とディザスター リカバリーの手順をテストします
   1. VM (Windows ゲスト OS) をシャットダウンするか、またはオペレーティング システムをパニック モードにする (Linux ゲスト OS) ことによって、フェールオーバーの状況をシミュレートし、フェールオーバーの構成が設計どおりに動作するかどうかを確認します。 
   2. フェールオーバーの実行にかかる時間を測定します。 時間が長すぎる場合は、次のことを検討します。
      1.   SUSE Linux では、Azure フェンス エージェントの代わりに SBD デバイスを使用して、フェールオーバーを高速化します
      2.   SAP HANA では、データの再読み込みに時間がかかりすぎる場合は、プロビジョニングするストレージ帯域幅を増やすことを検討します
   3. バックアップ/復元のシーケンスとタイミングをテストし、必要であれば調整します。 バックアップ時間が十分であることを確認するだけでなく、 復元もテストし、復元アクティビティのタイミングを取得します。 RTO がデータベースまたは VM 復元プロセスに依存している場合、復元時間が RTO SLA 内であることを確認します
   4. リージョンをまたぐ DR 機能とアーキテクチャをテストします
5. セキュリティのチェック
   1.  実装した Azure のロールベースのアクセス制御 (RBAC) アーキテクチャの有効性をテストします。 目標は、異なるチームのアクセスとアクセス許可を分離して制限することです。 例として、SAP Basis チームのメンバーは、VM をデプロイし、Azure ストレージから特定の Azure 仮想ネットワークにディスクを割り当てることができなければなりません。 ただし、SAP Basis チームは、独自の仮想ネットワークを作成したり、既存の仮想ネットワークの設定を変更したりすることができてはなりません。 一方、ネットワーク チームのメンバーは、SAP アプリケーションと DBMS の VM が実行されている仮想ネットワークに、VM をデプロイできてはなりません。 また、ネットワーク チームのメンバーは、VM の属性の変更または VM やディスクの削除もできてはなりません。  
   2.  [NSG と ASC](https://docs.microsoft.com/azure/virtual-network/security-overview) のルールが意図したとおりに機能し、保護されたリソースをシールドしていることを確認します
   3.  暗号化する必要のあるすべてのリソースが暗号化されていることを確認します。 証明書をバックアップし、保存し、それらの証明書にアクセスして暗号化されたエンティティを復元するプロセスを定義して、実行します。 
   4.  OS のサポートの観点から可能な場合は、OS ディスクに対して [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-faq) を使用します
   5.  使用されている暗号化のレイヤーが多すぎないことを確認します。 Azure Disk Encryption を使用し、DBMS Transparent Data Encryption のメソッドの 1 つを使用することには、ある程度意味があります
6. パフォーマンス テスト
   1.  SAP トレースと測定に基づく SAP では、該当する場合、上位 10 のオンライン レポートと現在の実装を比較します 
   2.  SAP トレースと測定に基づく SAP では、該当する場合、上位 10 のバッチ ジョブと現在の実装を比較します 
   3.  SAP トレースと測定に基づく SAP では、インターフェイス経由での SAP システムへのデータ転送を比較します。 オンプレミスから Azure などのように、異なる場所の間で現在転送が行われていることがわかっているインターフェイスに注目します 


## <a name="non-production-phase"></a>非運用フェーズ 
このフェーズでは、パイロットまたは PoC が成功した後、非運用 SAP システムの Azure へのデプロイを開始するものと想定されます。 概念実証からのすべての知識と経験を、そのようなデプロイに応用する必要があります。 PoC で示されているすべての条件と手順が、この種のデプロイにも適用されます。 このフェーズでは、通常、開発システム、単体テスト システム、およびビジネス回帰テスト システムを Azure にデプロイします。 将来の運用システムがそうであるように、1 つの SAP アプリケーション ラインの少なくとも 1 つの非運用システムに、完全な高可用性を構成することをお勧めします。 そのフェーズ中に考慮する必要がある追加の手順は次のとおりです。  

1.  古いプラットフォームから Azure にシステムを移行する前に、CPU 使用率、ストレージのスループット、IOPS データなどのリソース消費データを収集します。 DBMS レイヤーのユニットからは特に収集しますが、アプリケーション レイヤーのユニットからも行います。 また、ネットワークとストレージの待機時間を測定します。
2.  システムの可用性の使用時間パターンを記録します。 目的は、非運用システムを 7 x 24 使用できるようにする必要があるか、それとも週や月の特定の期間にシャットダウンできる非運用システムがあるかを、明らかにすることです
3.  Azure 内の VM 用に独自の OS イメージを作成する必要があるか、または Azure イメージ ギャラリーのイメージを使用するかどうかを、テストして定義します。 Azure ギャラリーのイメージを使用する場合は、OS ベンダーとのサポート契約を反映する正しいイメージを選択する必要があります。 一部の OS ベンダーは、Azure ギャラリーでライセンス持ち込みイメージを提供しています。 他の OS イメージについては、Azure によって見積もられる価格にサポートが含まれています。 独自の OS イメージを作成する場合は、次の記事でドキュメントを確認できます。
    1.  [このドキュメント](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource)を基にして、Azure にデプロイされる Windows VM の汎用化されたイメージを構築できます
    2.  [このドキュメント](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image)を基にして、Azure にデプロイされる Linux VM の汎用化されたイメージを構築できます
3.  Azure VM ギャラリーの SUSE および Red Hat Linux イメージを使用する場合、Azure VM ギャラリーで、Linux ベンダーによって提供されている SAP 用のイメージを使用する必要があります
4.  Microsoft サポート契約に関する SAP のサポート要件を満たすことを確認します。 詳しくは、SAP サポート ノート [#2015553](https://launchpad.support.sap.com/#/notes/2015553) をご覧ください。 HANA Large Instances については、ドキュメント「[オンボードの要件](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-onboarding-requirements)」をご覧ください
4.  ダウンタイムを選択して時間内に VM を再起動できるよう、適切なユーザーが[計画メンテナンスの通知](https://azure.microsoft.com/blog/a-new-planned-maintenance-experience-for-your-virtual-machines/)を受け取ることを確認します
5.  [Channel9](https://channel9.msdn.com/) などのチャネルで Microsoft 提供の Azure ドキュメントを常にチェックし、デプロイに適用する可能性のある新しい機能を確認します
6.  新しい VM SKU に関するサポート ノート [#1928533](https://launchpad.support.sap.com/#/notes/1928533) のようなものや、新しくサポートされるようになった OS や DBMS のリリースに関するものなど、Azure に関係のある SAP ノートをチェックします。 新しい VM の種類と古い VM の種類で価格を比較し、価格性能比が最適な VM をデプロイできるようにします
7.  SAP サポート ノート、SAP HANA ハードウェア ディレクトリ、および再び SAP PAM のリソースを検証し、Azure でサポートされる VM、それらの VM でサポートされる OS のリリース、サポートされる SAP および DBMS のリリースに、変更がないことを確認します
8.  [こちら](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)で Azure の新しい HANA 認定 SKU をチェックし、計画していたものと価格を比較して、最終的によりよい価格性能比のユニットを入手するように変更します 
9.  新しい VM の種類を利用するようにデプロイ スクリプトを調整し、使用したい Azure の新機能を組み込みます
10. インフラストラクチャをデプロイした後、SAP サポート ノート [#500235](https://launchpad.support.sap.com/#/notes/500235) および SAP サポート ノート [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E) に従って、SAP アプリケーション層 VM と DBMS VM の間のネットワーク待機時間をテストして評価します。 SAP サポート ノート [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E) のネットワーク待機時間のガイダンスに照らして結果を評価します。 ネットワーク待機時間は、中程度から良好な範囲でなければなりません。 [こちら](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance)に記載されているように、VM と HANA Large Instance ユニットの間のトラフィックには例外が適用されます。 「[SAP ワークロードのための Azure Virtual Machines DBMS デプロイの考慮事項](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general#azure-network-considerations)」および「[Azure における SAP HANA インフラストラクチャの構成と運用](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)」で説明されている制限が自分のデプロイに該当しないことを確認します
11. ワークロードを適用する前に、概念実証フェーズに記載されている他のすべてのチェックを実行します
12. ワークロードを適用したら、Azure でのこれらのシステムのリソース消費量を記録し、古いプラットフォームで取得した記録と比較します。 大きな違いがある場合は、将来のデプロイで VM のサイズを調整します。 ダウンサイズすると、VM のストレージとネットワークの帯域幅も減少することに留意してください。
    1.  「[Azure の Windows 仮想マシンのサイズ](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json)」。 
    2.  [Azure の Linux 仮想マシンのサイズ](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json) 
13. システムのコピー機能やプロセスについて確認します。 目的は、プロジェクト チームが新しいシステムをすばやく入手できるよう、自分で開発システムやテスト システムを間単にコピーできるようにすることです。 そのようなタスクを実行するツールとして、[SAP LaMa](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+Landscape+Management+%28SAP+LaMa%29+at+a+Glance) を検討してください。
14. チームの Azure のロールベースのアクセス、アクセス許可、およびプロセスを最適化して調整します。それにより、一方では、義務が確実に分離されるようにします。 他方では、すべてのチームが Azure インフラストラクチャでタスクを実行できるようにします。
15. 高可用性とディザスター リカバリーの手順を演習、テスト、文書化し、そのようなタスクをスタッフが実行できるようにします。 欠点を明らかにし、デプロイに統合する新しい Azure の機能を調整します

 
## <a name="production-preparation-phase"></a>運用準備フェーズ 
このフェーズでは、非運用デプロイでの経験と知識を収集し、将来の運用デプロイに適用します。 前のフェーズに加えて、現在のホスト場所と Azure 間のデータ転送作業も準備する必要があります。 

1.  Azure に移行する前に、運用システムの必要な SAP リリース アップグレードで作業します
2.  運用システムの移行後に実施する必要のある機能テストと業務テストについて、ビジネス オーナーと同意します
    1.  これらのテストがすべて、現在のホストの場所にあるソース システムで実行されることを確認します。 システムが Azure に初めてに移行されたときにはテストが実施されないようにします
2.  Azure への運用移行プロセスをテストします。 すべての運用システムを同じタイム フレームで Azure に移行しない場合は、同じホスト場所にする必要がある運用システムのグループを作成します。 データの移行を演習してテストします。 一般的な方法は次のとおりです。
    1.  バックアップ/復元のような DBMS の方法と、SQL Server AlwaysOn、HANA システム レプリケーション、またはログ配布を組み合わせて、Azure にデータベースの内容をシードして同期します
    2.  小さなデータベースに対してはバックアップ/復元を使用します
    3.  SAP SWPM ツールに実装されている SAP Migration モニターを使用して、異種の移行を実行します
    4.  SAP リリースのアップグレードと組み合わせる必要がある場合は、[SAP DMO](https://blogs.sap.com/2013/11/29/database-migration-option-dmo-of-sum-introduction/) プロセスを使用します。 ソース DBMS とターゲット DBMS のすべての組み合わせがサポートされているわけではないことに留意してください。 詳細については、DMO の各種リリースに対する特定の SAP サポート ノートをご覧ください。 たとえば、[SUM 2.0 SP04 のデータベース移行オプション (DMO)](https://launchpad.support.sap.com/#/notes/2644872)などです
    5.  バックアップまたは SAP エクスポート ファイルを移動する必要がある場合は、インターネット経由と ExpressRoute 経由のどちらの方がデータ転送スループットが高いかをテストします。 インターネット経由でデータを移動する場合は、将来の運用システムで適用する必要がある NSG/ASG セキュリティ ルールの一部を変更することが必要になる場合があります。
3.  古いプラットフォームから Azure にシステムを移行する前に、CPU 使用率、ストレージのスループット、IOPS データなどのリソース消費データを収集します。 DBMS レイヤーのユニットからは特に収集しますが、アプリケーション レイヤーのユニットからも行います。 また、ネットワークとストレージの待機時間を測定します。
4.  SAP サポート ノート、SAP HANA ハードウェア ディレクトリ、および再び SAP PAM のリソースを検証し、Azure でサポートされる VM、それらの VM でサポートされる OS のリリース、サポートされる SAP および DBMS のリリースに、変更がないことを確認します 
4.  VM の種類と Azure の機能について決定した最新の変更に合わせてデプロイ スクリプトを調整します
5.  インフラストラクチャとアプリケーションをデプロイした後、一連のチェックを実施して検証します。
    1.  正しい VM の種類が正しい属性とストレージ サイズでデプロイされた
    2.  VM が正しい目的の OS リリースとパッチになっていて、統一されていることを確認する
    3.  VM のセキュリティ強化が必要な状態になっていて、統一されていることを確認する
    4.  正しいアプリケーション リリースとパッチがインストールおよびデプロイされていることを確認する
    5.  計画どおりに、VM が Azure 可用性セットにデプロイされた
    6.  待機時間の影響を受けやすいディスクに対し、または [99.9% の単一 VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) が必要な場合に、Azure Premium Storage が使用された
    7.  Azure 書き込みアクセラレータが正しくデプロイされたことを確認する
        1.  VM、ストレージ スペース、またはストライプ セット内で、Azure 書き込みアクセラレータのサポートが必要なディスクが正しく構築されたことを確認する
            1.  「[Linux でのソフトウェア RAID の構成](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid)」を確認する
            2.  「[Azure で Linux VM の LVM を構成する](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm)」を確認する
    8.  [Azure マネージド ディスク](https://azure.microsoft.com/services/managed-disks/)だけが使用された
    9.  正しい可用性セットおよび Availability Zones に VM がデプロイされた
    10. SAP アプリケーション層および SAP DBMS 層で使用される VM 上で、[Azure 高速ネットワーク](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/)が有効になっていることを確認する
    11. SAP アプリケーションと、SAP NetWeaver、Hybris、または S/4HANA ベースの SAP システムの DBMS 層の間の通信パスに [Azure ネットワーク仮想アプライアンス](https://azure.microsoft.com/solutions/network-appliances/)が配置されていない
    12. ASG と NSG のルールで、必要であり計画されていた通信が許可され、必要な場合は通信がブロックされる
    13. 前に説明したようにタイムアウトの設定が正しく設定されている
    14. SAP サポート ノート [#500235](https://launchpad.support.sap.com/#/notes/500235) および SAP サポート ノート [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E) に従って、SAP アプリケーション層 VM と DBMS VM の間のネットワーク待機時間をテストして評価します。 SAP サポート ノート [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E) のネットワーク待機時間のガイダンスに照らして結果を評価します。 ネットワーク待機時間は、中程度から良好な範囲でなければなりません。 [こちら](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance)に記載されているように、VM と HANA Large Instance ユニットの間のトラフィックには例外が適用されます
    15. 必要な場所に、必要な暗号化方法で、暗号化がデプロイされていることを確認する
    16. インターフェイスおよび他のアプリケーションが、新しくデプロイされたインフラストラクチャに接続できることを確認する
6.  Azure の計画メンテナンスに対応するためのプレイブックを作成します。 計画メンテナンスのときにシステムと VM を再起動する順序を定義します
    

## <a name="go-live-phase"></a>運用開始フェーズ
運用開始フェーズでは、前のフェーズで作成したプレイブックに従う必要があります。 テストおよびトレーニングした手順を実行します。 構成とプロセスの直前の変更は受け付けません。 さらに、次の措置を適用します。

1. Azure portal の監視および他の監視ツールが動作していることを確認します。  推奨されるツールは、Perfmon (Windows) または SAR (Linux) です。 
    1.  CPU カウンター 
        1.  平均 CPU 時間 – 合計 (すべての CPU)
        2.  平均 CPU 時間 – 各個別プロセッサ (m128 VM では 128 プロセッサ)
        3.  CPU 時間カーネル – 各個別プロセッサ
        4.  CPU 時間ユーザー – 各個別プロセッサ
    5.  メモリ 
        1.  空きメモリ
        2.  メモリ ページ イン/秒
        3.  メモリ ページ アウト/秒
    4.  ディスク 
        1.  ディスク読み取り KB/秒 – 個別ディスクごと 
        2.  ディスク読み取り数/秒 – 個別ディスクごと
        3.  ディスク読み取りミリ秒/回 – 個別ディスクごと
        4.  ディスク書き込み KB/秒 – 個別ディスクごと 
        5.  ディスク書き込み数/秒 – 個別ディスクごと
        6.  ディスク書き込みミリ秒/回 – 個別ディスクごと
    5.  ネットワーク 
        1.  ネットワーク パケット イン/秒
        2.  ネットワーク パケット アウト/秒
        3.  ネットワーク KB イン/秒
        4.  ネットワーク KB アウト/秒 
2.  データの移行後に、ビジネス オーナーと合意したすべての検証テストを実行します。 元のソース システムの結果がある検証テストの結果のみを受け付けます
3.  インターフェイスが機能しているかどうか、および他のアプリケーションが新しくデプロイされた運用システムと通信できるかどうかを確認します
4.  SAP トランザクション STMS を使用して、トランスポートと修正システムを確認します
5.  システムが運用環境にリリースされたら、データベースのバックアップを行います
6.  システムが運用環境にリリースされたら、SAP アプリケーション層 VM の VM バックアップを実行します
7.  現在の運用開始フェーズには含まれないが、この運用開始フェーズで Azure に移行した SAP システムと通信する SAP システムの場合は、SM51 でホスト名バッファーをリセットする必要があります。 このステップでは、Azure に移行したアプリケーション インスタンスの名前に関連付けられているキャッシュされた古い IP アドレスが削除されます  


## <a name="post-production"></a>運用後
このフェーズでは、システムの監視、運用、管理に関するすべてのことを行います。 SAP の観点からは、古いホストの場所で実行する必要があった一般的なタスクが適用されます。 実行する Azure 固有のタスクは次のとおりです。

1. 料金の高いシステムについて Azure の請求を分析します
2. VM 側およびストレージ側で、価格/パフォーマンス効率を最適化します
3. システムがシャットダウンできる時間を最適化します  


## <a name="next-steps"></a>次の手順
以下のドキュメントをご覧ください。

- [SAP NetWeaver のための Azure Virtual Machines の計画と実装](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
- [SAP NetWeaver のための Azure Virtual Machines のデプロイ](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
- [SAP ワークロードのための Azure Virtual Machines DBMS デプロイの考慮事項](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)

