---
title: SAP ワークロードの計画とデプロイに関するチェックリスト | Microsoft Docs
description: Azure への SAP ワークロードのデプロイを計画し、ワークロードをデプロイするときのチェックリスト
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/10/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 51bfd632e854132be27a7b971cf03e9fe5b00138
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102504305"
---
# <a name="sap-workloads-on-azure-planning-and-deployment-checklist"></a>Azure での SAP ワークロード: 計画とデプロイに関するチェックリスト

このチェックリストは、SAP NetWeaver、S/4HANA、および Hybris のアプリケーションを Azure のサービスとしてのインフラストラクチャに移行するお客様を対象に作られています。 プロジェクトの期間中、お客様と SAP パートナーは、このチェックリストを見直す必要があります。 重要なこととして、チェックの多くをプロジェクトの開始時と計画段階に行うことに注意してください。 デプロイが完了した後では、デプロイされている Azure インフラストラクチャまたは SAP ソフトウェア リリースに対する単純な変更が、複雑になる場合があります。

プロジェクトの主要なマイルストーンでチェックリストを確認します。 これにより、小さな問題が大きな問題になる前に、問題を検出できるようになります。 また、必要な変更を再エンジニアリングしてテストするのに十分な時間があります。 このチェックリストが完全なものだと考えないでください。 状況によっては、さらに多くのチェックを実行することが必要になる場合があります。

チェックリストに Azure と関係のないタスクは含まれません。 たとえば、SAP アプリケーションのインターフェイスは、Azure プラットフォームまたはホスティング プロバイダーへの移行の間に変化します。

このチェックリストは、既にデプロイされているシステムにも使用できます。 デプロイした後で、書き込みアクセラレータ、Availability Zones、新しい VM の種類のような新機能が、追加されている可能性があります。 そのため、チェックリストを定期的に確認すると、Azure プラットフォームの新機能を確実に把握するのに役立ちます。

## <a name="project-preparation-and-planning-phase"></a>プロジェクトの準備と計画フェーズ
このフェーズでは、Azure プラットフォームへの SAP ワークロードの移行を計画します。 このフェーズでは、少なくとも、次のドキュメントを作成し、次に示す移行の要素を定義して検討する必要があります。

1. 概要設計ドキュメント。 このドキュメントには以下を含める必要があります。
    - SAP コンポーネントとアプリケーションの現在のインベントリ、および Azure 用のターゲット アプリケーション インベントリ。
    - 関係者の責任と分担を定義する責任分担表 (RACI)。 計画と最初のデプロイを通して、大まかに始め、徐々に詳細なレベルに進みます。
    - 概要ソリューション アーキテクチャ。
    - どの Azure リージョンをデプロイ先とするかについての決定。 [Azure リージョンの一覧](https://azure.microsoft.com/global-infrastructure/regions/)を参照してください。 各リージョンで利用可能なサービスの詳細については、[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/)に関する記事を参照してください。
    - オンプレミスから Azure に接続するためのネットワーク アーキテクチャ。 [Azure の仮想データ センターのブループリント](/azure/architecture/vdc/)について理解を深めることを始めてください。
    - 影響の大きいビジネス データを Azure で運用するためのセキュリティの原則。 データ セキュリティの詳細については、まず [Azure のセキュリティのドキュメント](../../../security/index.yml)を参照してください。
2.  技術設計ドキュメント。 このドキュメントには以下を含める必要があります。
    - ソリューションのブロック図。
    - Azure でのコンピューティング、ストレージ、ネットワーク コンポーネントのサイズの決定。 Azure VM の SAP のサイズ設定については、[SAP 
    -  note #1928533] (https://launchpad.support.sap.com/#/notes/1928533) ) を参照してください。
    - ビジネス継続性とディザスター リカバリーのアーキテクチャ。
    - OS、DB、カーネル、SAP サポート パック バージョンについての詳細情報。 SAP NetWeaver または S/4HANA でサポートされているすべての OS リリースが必ずしも Azure VM でサポートされているとは限りません。 DBMS のリリースについても同じです。 SAP と Azure のサポートを確実なものにするために、次のソースを確認し、必要に応じて SAP リリース、DBMS リリース、OS リリースをアップグレードしてください。 SAP と Microsoft から完全なサポートを受けるには、SAP と Azure でサポートされているリリースの組み合わせを使用する必要があります。 必要に応じて、ソフトウェア コンポーネントのアップグレードを計画する必要があります。 サポートされている SAP、OS、DBMS ソフトウェアの詳細については、次の場所に記載されています。
        - [SAP サポート ノート #1928533](https://launchpad.support.sap.com/#/notes/1928533)。 このノートでは、Azure VM でサポートされる最小の OS リリースが定義されています。 また、ほとんどの非 HANA データベースに必要な最小のデータベース リリースも定義されています。 最後に、SAP でサポートされている Azure VM の種類について、SAP のサイズ設定が示されています。
        - [SAP サポート ノート #2015553](https://launchpad.support.sap.com/#/notes/2015553)。 このノートでは、Azure Storage に関するサポート ポリシーと Microsoft との必要なサポート関係が定義されています。
        - [SAP サポート ノート #2039619](https://launchpad.support.sap.com/#/notes/2039619)。 このノートでは、Azure の Oracle サポートのマトリックスが定義されています。 Oracle では、Azure for SAP ワークロードでのゲスト オペレーティング システムとして、Windows と Oracle Linux のみがサポートされています。 このサポートに関する声明は、SAP インスタンスを実行する SAP アプリケーション レイヤーにも適用されます。 ただし、Oracle では、Pacemaker による Oracle Linux での SAP セントラル サービスの高可用性はサポートされていません。 Oracle Linux 上での ASCS の高可用性が必要な場合は、SIOS Protection Suite for Linux を使用する必要があります。 詳細な SAP 認定データについては、「SAP サポート ノート [#1662610 - SIOS Protection Suite for Linux のサポートの詳細](https://launchpad.support.sap.com/#/notes/1662610)」を参照してください。 Windows では、SAP がサポートする SAP セントラル サービスの Windows Server フェールオーバー クラスタリング ソリューションが、DBMS レイヤーとして Oracle と共にサポートされています。
        - [SAP サポート ノート #2235581](https://launchpad.support.sap.com/#/notes/2235581)。 このノートでは、さまざまな OS リリースでの SAP HANA のサポート マトリックスが示されています。
        - SAP HANA でサポートされる Azure VM と [HANA Large Instances](./hana-overview-architecture.md) の一覧は、[SAP の Web サイト](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)に掲載されています。
        - [SAP 製品の可用性マトリックス](https://support.sap.com/en/)。
        - [SAP サポート ノート #2555629 - SAP HANA 2.0 Dynamic Tiering - ハイパーバイザーとクラウドのサポート](https://launchpad.support.sap.com/#/notes/2555629)
        - [SAP サポート ノート #1662610 - SIOS Protection Suite for Linux のサポートの詳細](https://launchpad.support.sap.com/#/notes/1662610)
        - 他の SAP 固有製品に関する SAP ノート。     
    - SAP セントラル サービスに対するマルチ SID クラスター構成の使用は、Azure での Windows、SLES、RHEL の各ゲスト オペレーティング システム上でサポートされます。 その影響範囲によって、このようなマルチ SID クラスターに配置する ASCS/SCS が増加する可能性があることに注意してください。 各ゲスト OS のシナリオに関するドキュメントについては、次の記事を参照してください。
        - [Azure で Windows Server フェールオーバー クラスタリングと共有ディスクを使用する SAP ASCS/SCS インスタンスのマルチ SID 高可用性](./sap-ascs-ha-multi-sid-wsfc-shared-disk.md)
        - [Azure での Windows Server フェールオーバー クラスタリングとファイル共有による SAP ASCS/SCS インスタンスのマルチ SID 高可用性](./sap-ascs-ha-multi-sid-wsfc-file-share.md)
        - [SUSE Linux Enterprise Server for SAP Applications マルチ SID 上の Azure VM での SAP NetWeaver の高可用性ガイド](./high-availability-guide-suse-multi-sid.md)
        - [Red Hat Enterprise Linux for SAP Applications マルチ SID 上の Azure VM での SAP NetWeaver の高可用性ガイド](./high-availability-guide-rhel-multi-sid.md)
    - 高可用性とディザスター リカバリーのアーキテクチャ。
        - RTO と RPO に基づいて、必要な高可用性とディザスター リカバリー アーキテクチャの概要を定義します。
        - 1 つのゾーン内での高可用性の場合は、Azure で目的の DBMS が提供する必要のあるものを確認します。 ほとんどの DBMS パッケージでは同期ホット スタンバイの同期メソッドが提供されており、運用システム用に推奨されます。 「[SAP ワークロードのための Azure Virtual Machines DBMS デプロイの考慮事項](./dbms_guide_general.md)」と関連ドキュメントから始めて、さまざまなデータベースについての SAP 関連ドキュメントもチェックしてください。
           たとえば、[こちら](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)で SQL Server について説明されているように、DBMS レイヤーの共有ディスク構成で Windows Server フェールオーバー クラスタリングを使用することは、サポートされていません。 代わりに次のようなソリューションを使用してください。
           - [SQL Server AlwaysOn](/previous-versions/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups)
           - [Oracle データの保護](../oracle/configure-oracle-dataguard.md)
           - [HANA システム レプリケーション](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html)
        - Azure リージョン間にまたがるディザスター リカバリー用には、さまざまな DBMS ベンダーから提供されているソリューションを検討してください。 そのほとんどでは、非同期レプリケーションまたはログ配布がサポートされています。
        - SAP アプリケーション レイヤーでは、ビジネス回帰テスト システムを実行するかどうかを決定します。理想的には、同じ Azure リージョンまたはお使いの DR リージョン内の、運用デプロイのレプリカです。 2 番目のケースでは、そのビジネス回帰システムを運用環境のデプロイの DR ターゲットとすることができます。
        - DR サイトに非運用システムを置かない場合は、Azure DR リージョンに SAP アプリケーション レイヤーをレプリケートする方法として、Azure Site Recovery を検討します。 詳細については、「[多層 SAP NetWeaver アプリ デプロイのディザスター リカバリーを設定する](../../../site-recovery/site-recovery-sap.md)」を参照してください。
        - [Azure Availability Zones](../../../availability-zones/az-overview.md) を使って、組み合わされた HADR 構成を使用する場合は、Availability Zones が使用可能な Azure リージョンについて理解しておいてください。 また、2 つの Availability Zones 間のネットワーク待機時間の増加によって生じる可能性のある制約についても考慮します。  
3.  すべての SAP インターフェイス (SAP と SAP 以外) のインベントリ。
4.  基礎サービスの設計。 この設計には、次の項目が含まれている必要があります。
    - Active Directory と DNS の設計。
    - Azure 内のネットワーク トポロジと、さまざまな SAP システムの割り当て。
    - Azure 内のインフラストラクチャと SAP アプリケーションを管理するチームの [Azure ロールベースのアクセス制御 (Azure RBAC)](../../../role-based-access-control/overview.md) の構造。
    - リソース グループのトポロジ。
    - [タグ付けの戦略](../../../azure-resource-manager/management/tag-resources.md#tags-and-billing)。
    - VM と他のインフラストラクチャ コンポーネントおよび論理名の名前付け規則。
5.  Microsoft Professional または Premier サポート契約。 Microsoft の Premier サポート契約をお持ちの場合は、Microsoft テクニカル アカウント マネージャー (TAM) を確認してください。 SAP のサポート要件については、[SAP サポート ノート #2015553](https://launchpad.support.sap.com/#/notes/2015553) を参照してください。
6.  Azure サブスクリプションの数と、サブスクリプションのコア クォータ。 必要に応じて、[Azure サブスクリプションのクォータを増やすサポート リクエストを開きます](../../../azure-portal/supportability/resource-manager-core-quotas-request.md)。
7.  Azure への SAP データの移行に関するデータの削減とデータ移行計画。 SAP NetWeaver システムについては、大容量のデータ量を制限する方法についてのガイドラインが SAP にあります。 SAP ERP システムでのデータ管理に関する[こちらの SAP ガイド](https://wiki.scn.sap.com/wiki/download/attachments/247399467/DVM_%20Guide_7.2.pdf?version=1&modificationDate=1549365516000&api=v2)を参照してください。 一部のコンテンツは、一般に NetWeaver と S/4HANA システムにも適用されます。
8.  自動化されたデプロイのアプローチ。 Azure へのインフラストラクチャ デプロイの自動化の目的は、決定論的な方法でデプロイし、決定論的な結果を得ることです。 多くのお客様は、PowerShell または CLI ベースのスクリプトを使用します。 しかしながら、SAP 用に Azure インフラストラクチャをデプロイしたり、SAP ソフトウェアをインストールしたりすることに使用できるさまざまなオープン ソース テクノロジがあります。 GitHub で例を見つけることができます。
    - [Azure Cloud での SAP の自動デプロイ](https://github.com/Azure/sap-hana)
    - [SAP HANA のインストール](https://github.com/AzureCAT-GSI/SAP-HANA-ARM)
9.  お客様、システム インテグレーター、Microsoft、およびその他の関係者の間での、定期的な設計およびデプロイ レビューの頻度を定義します。


## <a name="pilot-phase-strongly-recommended"></a>パイロット フェーズ (強く推奨)
 
プロジェクトの計画と準備の前または実行中に、パイロットを実行できます。 また、計画および準備フェーズで作成したアプローチと設計をテストするために、パイロット フェーズを使用することもできます。 さらにパイロット フェーズを拡張して、実際の概念実証にすることができます。

パイロット デプロイの間に、完全な HADR ソリューションおよびセキュリティ設計を設定して検証することをお勧めします。 このフェーズでスケーラビリティ テストを実施するお客様もいます。 他のお客様は、SAP サンドボックス システムのデプロイをパイロット フェーズとして使用します。 パイロットのために Azure に移行するシステムを既に識別済みであると想定します。

1. Azure へのデータ転送を最適化します。 最適な選択は、具体的なシナリオによって大きく異なります。 ExpressRoute 回線に十分な帯域幅がある場合、[Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) を介したオンプレミスからの転送が最速になります。 他のシナリオでは、インターネット経由で転送する方が高速です。
2. データのエクスポートとインポートを伴う SAP 異種プラットフォームの移行の場合は、エクスポートおよびインポート フェーズをテストして最適化します。 SQL Server が移行先プラットフォームである大規模な移行の場合は、[推奨事項](https://techcommunity.microsoft.com/t5/Running-SAP-Applications-on-the/SAP-OS-DB-Migration-to-SQL-Server-8211-FAQ-v6-2-April-2017/ba-p/368070)を参照してください。 組み合わされたリリース アップグレードが必要ない場合は、Migration Monitor/SWPM を使用できます。 移行と SAP リリース アップグレードを組み合わせる場合は、[SAP DMO](https://blogs.sap.com/2013/11/29/database-migration-option-dmo-of-sum-introduction/) プロセスを使用できます。 これを行うには、ソースとターゲットの DBMS プラットフォームの組み合わせについて、特定の要件を満たす必要があります。 このプロセスは、[SUM 2.0 SP03 のデータベース移行オプション (DMO)](https://launchpad.support.sap.com/#/notes/2631152) に記載されています。
   1.  ソースへのエクスポート、Azure へのエクスポート ファイルのアップロード、インポートのパフォーマンス。 エクスポートとインポートの間のオーバーラップを最大化します。
   2.  インフラストラクチャのサイズを設定するために、対象のプラットフォームと移行先プラット フォームのデータベースの容量を評価します。
   3.  タイミングを検証して最適化します。
1. 技術的な検証。
   1. VM の種類。
        - SAP サポート ノート、SAP HANA ハードウェア ディレクトリ、SAP PAM にあるリソースをもう一度確認します。 Azure でサポートされている VM、それらの VM の種類でサポートされている OS リリース、サポートされている SAP および DBMS のリリースに変更がないことを確認します。
        - もう一度、Azure にデプロイするアプリケーションおよびインフラストラクチャのサイズを検証します。 既存のアプリケーションを移行するのであれば、多くの場合、使用しているインフラストラクチャと [SAP ベンチマーク Web ページ](https://www.sap.com/dmc/exp/2018-benchmark-directory/#/sd)から必要な SAPS を取得し、それを [SAP サポート ノート #1928533](https://launchpad.support.sap.com/#/notes/1928533) に記載されている SAPS 番号と比較することができます。 また、[SAP 評価に関するこの記事](https://techcommunity.microsoft.com/t5/Running-SAP-Applications-on-the/SAPS-ratings-on-Azure-VMs-8211-where-to-look-and-where-you-can/ba-p/368208)に注意してください。
        - 計画フェーズで選択した種類の VM の最大ストレージ スループットとネットワーク スループットに関して、Azure VM のサイズ設定を評価およびテストします。 手順については、こちらを参照してください:
           -  「[Azure の Windows 仮想マシンのサイズ](../../sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)」。 サイズ設定では *キャッシュされていないディスクの最大スループット* を検討することが重要です。
           -  [Azure の Linux 仮想マシンのサイズ](../../sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。 サイズ設定では *キャッシュされていないディスクの最大スループット* を検討することが重要です。
   2. ストレージ。
        - ドキュメント「[SAP ワークロードの Azure Storage の種類](./planning-guide-storage.md)」を確認してください。
        - SAP アプリケーション レイヤーを表す VM や、さほどパフォーマンスが要求されない DBMS デプロイには、最小要件として [Azure Standard SSD ストレージ](../../disks-types.md#standard-ssd)を使用します。
        - 一般に、[Azure Standard HDD ディスク](../../disks-types.md#standard-hdd)を使用することはお勧めしません。
        - リモートでのパフォーマンスが重視される DBMS VM には、[Azure Premium Storage](../../disks-types.md#premium-ssd) を使用します。
        - [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/) を使用します。
        - M シリーズの DBMS ログ ドライブには、Azure 書き込みアクセラレータを使用します。 [書き込みアクセラレータ](../../how-to-enable-write-accelerator.md)に関する記事に記載されている書き込みアクセラレータの制限と使用方法に注意してください。
        - DBMS のさまざまな種類については、[一般的な SAP 関連の DBMS ドキュメント](./dbms_guide_general.md)と、一般ドキュメントで参照されている DBMS ごとの個別ドキュメントを確認してください。
        - SAP HANA の詳細については、「[Azure における SAP HANA インフラストラクチャの構成と運用](./hana-vm-operations.md)」を参照してください。
        - デバイス ID を使用して Azure データ ディスクを Azure Linux VM にマウントしないでください。 代わりに、汎用一意識別子 (UUID) を使用します。 たとえば、グラフィカル ツールを使用して Azure データ ディスクをマウントする場合には注意してください。 /etc/fstab 内のエントリを再確認して、ディスクをマウントするのに UUID が使用されていることを確認します。 詳細については、[こちらの記事](../../linux/attach-disk-portal.md#connect-to-the-linux-vm-to-mount-the-new-disk)を参照してください。
   3. ネットワーク。
        - 仮想ネットワークのインフラストラクチャと、Azure 仮想ネットワーク内またはネットワーク間での SAP アプリケーションの分散をテストして評価します。
        -  ハブとスポークの仮想ネットワーク アーキテクチャのアプローチ、または単一の Azure 仮想ネットワーク内でのマイクロセグメンテーションのアプローチを評価します。 この評価の基準は次のとおりです。
               1. [ピアリングされた Azure 仮想ネットワーク](../../../virtual-network/virtual-network-peering-overview.md)間のデータ交換のためのコスト。 コストについては、「[Virtual Network の価格](https://azure.microsoft.com/pricing/details/virtual-network/)」を参照してください。
               2. 仮想ネットワーク内のサブネットを分離するためにネットワーク セキュリティ グループを変更するのではなく、Azure 仮想ネットワーク間のピアリングを高速切断する利点。 この評価は、仮想ネットワークのサブネットでホストされているアプリケーションや VM がセキュリティ上のリスクになった場合に備えるためのものです。
                3. オンプレミス、外部世界、および Azure に構築した仮想データセンターの間のネットワーク トラフィックの集中ログ記録と監査。
        - SAP アプリケーション レイヤーと SAP DBMS レイヤーの間のデータ パスを評価およびテストします。
            -  SAP アプリケーションと、SAP NetWeaver、Hybris、または S/4HANA ベースの SAP システムの DBMS レイヤーの間の通信パスに [Azure ネットワーク仮想アプライアンス](https://azure.microsoft.com/solutions/network-appliances/)を配置することは、サポートされていません。
            -  ピアリングされていない別々の Azure 仮想ネットワークに SAP アプリケーション レイヤーと SAP DBMS を配置することは、サポートされていません。
            -  [アプリケーション セキュリティ グループとネットワーク セキュリティ グループの規則](../../../virtual-network/network-security-groups-overview.md)を使用して、SAP アプリケーション レイヤーと SAP DBMS レイヤーとの間のルートを定義できます。
        - SAP アプリケーション レイヤーおよび SAP DBMS レイヤーで使用される VM 上で、[Azure 高速ネットワーク](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/)が有効になっていることを確認します。 Azure で高速ネットワークをサポートするには、異なる OS レベルが必要であることに留意してください。
            - Windows Server 2012 R2 以降。
            - SUSE Linux 12 SP3 以降。
            - RHEL 7.4 以降。
            - Oracle Linux 7.5。 RHCKL カーネルを使用している場合は、リリース 3.10.0-862.13.1.el7 が必要です。 Oracle UEK カーネルを使用している場合は、リリース 5 が必要です。
        - SAP サポート ノート [#500235](https://launchpad.support.sap.com/#/notes/500235) および [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E) に従って、SAP アプリケーション レイヤー VM と DBMS VM との間のネットワーク待機時間をテストして評価します。 [SAP サポート ノート #1100926](https://launchpad.support.sap.com/#/notes/1100926/E) のネットワーク待機時間のガイダンスに照らして結果を評価します。 ネットワーク待機時間は、中程度または良好な範囲でなければなりません。 [この記事](./hana-network-architecture.md#networking-architecture-for-hana-large-instance)に記載されているように、VM と HANA Large Instance ユニットとの間のトラフィックには例外が適用されます。
        - Direct Server Return を使用するように ILB のデプロイが設定されていることを確認します。 DBMS レイヤーでの高可用性構成用に Azure ILB が使用されている場合、この設定により待機時間が短縮されます。
        - Azure Load Balancer を Linux ゲスト オペレーティング システムと共に使用している場合、Linux ネットワーク パラメーター **net.ipv4.tcp_timestamps** が **0** に設定されていることを確認します。 この推奨事項は、[SAP ノート #2382421](https://launchpad.support.sap.com/#/notes/2382421) の以前のバージョンの推奨事項と競合しています。 その SAP ノートは、Azure ロード バランサーと連携するように、このパラメーターを **0** に設定する必要があるという記述に更新されました。
        - 最適なネットワーク待ち時間を実現するために [Azure 近接通信配置グループ](../../co-location.md)の使用を検討します。 詳細については、「[SAP アプリケーションで最適なネットワーク待ち時間を実現する Azure 近接通信配置グループ](sap-proximity-placement-scenarios.md)」を参照してください。
   4. 高可用性とディザスター リカバリーのデプロイ。
        - 特定の Azure 可用性ゾーンを定義しないで SAP アプリケーション レイヤーをデプロイする場合は、単一の SAP システムの SAP ダイアログ インスタンスまたはミドルウェア インスタンスを実行するすべての VM が、[可用性セット](../../availability-set-overview.md)にデプロイされることを確認します。
        - SAP セントラル サービスと DBMS に対して高可用性が必要ない場合は、これらの VM を SAP アプリケーション レイヤーと同じ可用性セットにデプロイできます。
        - パッシブ レプリケーションを使用して高可用性用に SAP セントラル サービスと DBMS レイヤーを保護する場合は、SAP セントラル サービス用の 2 つのノードを 1 つの独立した可用性セットに配置し、2 つの DBMS ノードを別の可用性セットに配置します。
        - Azure Availability Zones にデプロイする場合は、可用性セットを使用できません。 しかし、アクティブとパッシブのセントラル サービス ノードを 2 つの異なる Availability Zones にデプロイする必要があります。 待機時間が最も短い Availability Zones を使用します。
          Azure Availability Zones をまたがる DBMS および SAP セントラル サービス レイヤーに対して Windows または Pacemaker のフェールオーバー クラスターを確立する場合は、[Azure Standard Load Balancer](../../../load-balancer/load-balancer-standard-availability-zones.md) を使用する必要があることに注意してください。 [Basic Load Balancer](../../../load-balancer/load-balancer-overview.md) をゾーン間のデプロイに使用することはできません。
   5. タイムアウトの設定。
        - SAP インスタンスの SAP NetWeaver 開発者トレースをチェックして、エンキュー サーバーと SAP ワーク プロセスの間の接続が切断されていないことを確認します。 このような接続の切断は、次の 2 つのレジストリ パラメーターを設定することによって回避できます。
            - HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\KeepAliveTime = 120000。 詳細については、「[KeepAliveTime](/previous-versions/windows/it-pro/windows-2000-server/cc957549(v=technet.10))」を参照してください。
            - HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\KeepAliveInterval = 120000。 詳細については、「[KeepAliveInterval](/previous-versions/windows/it-pro/windows-2000-server/cc957548(v=technet.10))」を参照してください。
        - オンプレミスの SAP GUI インターフェイスと、Azure にデプロイされた SAP アプリケーション レイヤーの間での GUI のタイムアウトを回避するため、default.pfl またはインスタンス プロファイルで次のパラメーターが設定されていることを確認します。
            - rdisp/keepalive_timeout = 3600
            - rdisp/keepalive = 20
        - SAP エンキュー プロセスと SAP ワーク プロセスの間に確立した接続が中断されるのを防ぐために、**enque/encni/set_so_keepalive** パラメーターを **true** に設定する必要があります。 [SAP ノート #2743751](https://launchpad.support.sap.com/#/notes/2743751) も参照してください。  
        - Windows フェールオーバー クラスター構成を使用する場合は、応答のないノードに対応する時間が Azure に対して正しく設定されていることを確認します。 「[フェールオーバー クラスター ネットワークしきい値の調整](https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834)」の記事では、パラメーターの一覧と、それらがフェールオーバーの感度に及ぼす影響が示されています。 クラスター ノードが同じサブネット内にあるとすると、次のパラメーターを変更する必要があります。
            - SameSubNetDelay = 2000
            - SameSubNetThreshold = 15
            - RoutingHistorylength = 30
    6. OS の設定または修正プログラム
        - SAP で HANA を実行するには、次のノートとドキュメントをお読みください。
            -   [SAP サポート ノート #2814271 - SAP HANA バックアップがチェックサム エラーにより Azure で失敗する](https://launchpad.support.sap.com/#/notes/2814271)
            -   [SAP サポート ノート #2753418 - タイマーのフォールバックが原因でパフォーマンスが低下する可能性がある](https://launchpad.support.sap.com/#/notes/2753418)
            -   [SAP サポート ノート #2791572 - Azure で Hyper-V に対する VDSO サポートがないため、パフォーマンスが低下する](https://launchpad.support.sap.com/#/notes/2791572)
            -   [SAP サポート ノート #2382421 - HANA および OS レベルでのネットワーク構成の最適化](https://launchpad.support.sap.com/#/notes/2382421)
            -   [SAP サポート ノート #2694118 - Azure での Red Hat Enterprise Linux HA アドオン](https://launchpad.support.sap.com/#/notes/2694118)
            -   [SAP サポート ノート #1984787 - SUSE LINUX Enterprise Server 12: インストールに関する注意事項](https://launchpad.support.sap.com/#/notes/1984787)
            -   [SAP support note #2002167 - Red Hat Enterprise Linux 7.x: インストールとアップグレード](https://launchpad.support.sap.com/#/notes/0002002167)
            -   [SAP support note #2292690 - SAP HANA DB:Recommended OS settings for RHEL 7 (SAP HANA DB: RHEL 7 に推奨される OS 設定)](https://launchpad.support.sap.com/#/notes/0002292690)
            -   [SAP サポート ノート #2772999 - Red Hat Enterprise Linux 8.x: インストールと構成](https://launchpad.support.sap.com/#/notes/2772999)
            -   [SAP サポート ノート #2777782 - SAP HANA DB: RHEL 8 に推奨される OS 設定](https://launchpad.support.sap.com/#/notes/2777782)
            -   [SAP サポート ノート #2578899 - SUSE Linux Enterprise Server 15: インストールに関する注意事項](https://launchpad.support.sap.com/#/notes/2578899)
            -   [SAP サポート ノート #2455582 - Linux:GCC 6.x でコンパイルされた SAP アプリケーションの実行](https://launchpad.support.sap.com/#/notes/0002455582)
            -    [SAP サポート ノート #2729475 - SAP HANA が認定されている Azure VM での "ハイパーバイザーはサポートされていません" というエラーによる HWCCT の障害](https://launchpad.support.sap.com/#/notes/2729475)
1. 高可用性とディザスター リカバリーの手順をテストします。
   1. VM をシャットダウンするか (Windows ゲスト オペレーティング システム)、オペレーティング システムをパニック モードにすることで (Linux ゲスト オペレーティング システム)、フェールオーバーの状況をシミュレートします。 この手順は、フェールオーバー構成が設計どおりに動作するかどうかを判断するのに役立ちます。
   1. フェールオーバーの実行にかかる時間を測定します。 時間が長すぎる場合は、次のことを検討します。
        - SUSE Linux では、Azure フェンス エージェントの代わりに SBD デバイスを使用して、フェールオーバーを高速化します。
        - SAP HANA では、データの再読み込みに時間がかかりすぎる場合は、プロビジョニングするストレージ帯域幅を増やすことを検討します。
   3. バックアップ/復元のシーケンスとタイミングをテストし、必要に応じて修正します。 バックアップ時間が十分であることを確認してください。 また、復元と時間復元のアクティビティをテストする必要があります。 RTO がデータベースまたは VM 復元プロセスに依存している場合、復元時間が RTO SLA 内であることを確認します。
   4. リージョンをまたぐ DR 機能とアーキテクチャをテストします。
1. セキュリティのチェック。
   1. Azure のロールベースのアクセス制御 (Azure RBAC) アーキテクチャの有効性をテストします。 目標は、異なるチームのアクセスとアクセス許可を分離して制限することです。 例として、SAP Basis チームのメンバーは、VM をデプロイし、Azure Storage から特定の Azure 仮想ネットワークにディスクを割り当てることができなければなりません。 しかし、SAP Basis チームは、独自の仮想ネットワークを作成したり、既存の仮想ネットワークの設定を変更したりすることができてはなりません。 ネットワーク チームのメンバーは、SAP アプリケーションと DBMS の VM が実行されている仮想ネットワークに、VM をデプロイできてはなりません。 また、このチームのメンバーは、VM の属性の変更または VM やディスクの削除もできてはなりません。  
   1.  [ネットワーク セキュリティグループと ASC](../../../virtual-network/network-security-groups-overview.md) の規則が想定どおりに動作することを確認し、保護されたリソースをシールドします。
   1.  暗号化する必要のあるすべてのリソースが暗号化されていることを確認します。 証明書をバックアップして保存し、それらの証明書にアクセスして、暗号化されたエンティティを復元するプロセスを定義し、実装します。
   1.  OS のサポートの観点から可能な場合は、OS ディスクに対して [Azure Disk Encryption](../../../security/fundamentals/azure-disk-encryption-vms-vmss.md) を使用します。
   1.  使用している暗号化のレイヤーが多すぎないことを確認してください。 場合によっては、同じサーバー上の異なるディスクまたはコンポーネントを保護するために、Azure Disk Encryption と、DBMS Transparent Data Encryption のメソッドの 1 つを一緒に使用することに意味があります。  たとえば、SAP DBMS サーバーでは、オペレーティング システムのブート ディスク (OS で ADE がサポートされている場合)、および DBMS のデータ永続化ファイルによって使用されていないデータ ディスクで Azure Disk Encryption (ADE) を有効にできます。  たとえば、DBMS TDE 暗号化キーを保持しているディスクで ADE を使用します。
1. パフォーマンス テスト。 SAP で、SAP のトレースと測定値に基づいて次の比較を行います。
   - 該当する場合は、上位 10 個のオンライン レポートを現在の実装と比較します。
   - 該当する場合は、上位 10 個のバッチ ジョブを現在の実装と比較します。
   - インターフェイスを使用した SAP システムへのデータ転送を比較します。 オンプレミスから Azure へのように、異なる場所の間で転送が行われていることがわかっているインターフェイスに注目します。


## <a name="non-production-phase"></a>非運用フェーズ 
このフェーズでは、パイロットまたは概念実証 (POC) が成功した後、非運用 SAP システムの Azure へのデプロイを開始するものと想定されます。 POC 中に学んだこと、経験したことをすべてこのデプロイに組み込みます。 POC に関するすべての条件と手順は、このデプロイにも適用されます。

このフェーズ中に、通常、開発システム、単体テスト システム、およびビジネス回帰テスト システムを Azure にデプロイします。 1 つの SAP アプリケーション ラインの少なくとも 1 つの非運用システムに、将来の運用システムに構成する完全な高可用性を構成することをお勧めします。 このフェーズで完了する必要がある追加の手順を次に示します。  

1.  古いプラットフォームから Azure にシステムを移行する前に、CPU 使用率、ストレージのスループット、IOPS データなどのリソース消費データを収集します。 このデータは特に DBMS レイヤーのユニットから収集しますが、アプリケーション レイヤーのユニットからも収集します。 また、ネットワークとストレージの待機時間を測定します。
2.  お使いのシステムの可用性の使用時間パターンを記録します。 目的は、非運用システムを毎日 1 日中使用できるようにする必要があるか、それとも週や月の特定の期間にシャットダウンできる非運用システムがあるかを、明らかにすることです。
3.  Azure 内の VM 用にご自身の独自の OS イメージを作成する必要があるかどうか、または Shared Image Gallery のイメージを使用するかどうかを、テストして決定します。 Shared Image Gallery のイメージを使用する場合は、OS ベンダーとのサポート契約を反映した正しいイメージを使用する必要があります。 一部の OS ベンダーでは、Shared Image Gallery を使用して、独自のライセンス イメージを持ち込むことができます。 他の OS イメージについては、Azure によって見積もられる価格にサポートが含まれています。 独自の OS イメージを作成する場合は、次の記事でドキュメントを確認できます。
    -   [Azure にデプロイされた Windows VM の一般化されたイメージの構築](../../windows/capture-image-resource.md)
    -   [Azure にデプロイされた Linux VM の一般化されたイメージの構築](../../linux/capture-image.md)
3.  Shared Image Gallery の SUSE および Red Hat Linux イメージを使用する場合は、Shared Image Gallery 内の、Linux ベンダーによって提供されている SAP 用のイメージを使用する必要があります。
4.  Microsoft サポート契約に関する SAP のサポート要件を満たすことを確認します。 [SAP サポート ノート #2015553](https://launchpad.support.sap.com/#/notes/2015553) を参照してください。 HANA Large Instances については、「[オンボードの要件](./hana-onboarding-requirements.md)」を参照してください。
4.  最適なダウンタイムを選択できるよう、適切なユーザーが[計画メンテナンスの通知](https://azure.microsoft.com/blog/a-new-planned-maintenance-experience-for-your-virtual-machines/)を受け取ることを確認します。
5.  [Channel 9](https://channel9.msdn.com/) などのチャネルで Azure に関するプレゼンテーションを頻繁にチェックし、デプロイに適用できる可能性のある新しい機能を確認します。
6.  [サポート ノート #1928533](https://launchpad.support.sap.com/#/notes/1928533) のような Azure に関連した SAP ノートで、新しい VM SKU や新しくサポートされた OS および DBMS リリースがないかチェックします。 新しい VM の種類と以前の VM の種類の価格を比較し、価格性能比が最適な VM をデプロイできるようにします。
7.  SAP サポート ノート、SAP HANA ハードウェア ディレクトリ、SAP PAM を再確認します。 Azure 用にサポートされている VM、それらの VM でサポートされている OS リリース、サポートされている SAP および DBMS のリリースに変更がないことを確認します。
8.  Azure での新しい HANA 認定 SKU については、[SAP の Web サイト](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)を確認してください。 新しい SKU の価格と使用を予定していたものの価格を比較します。 最終的に、価格性能比が最適なものを使用するために、必要な変更を行います。
9.  新しい VM の種類を使用するようにデプロイ スクリプトを調整し、使用したい Azure の新機能を組み込みます。
10. インフラストラクチャをデプロイした後、SAP サポート ノート [#500235](https://launchpad.support.sap.com/#/notes/500235) および [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E) に従って、SAP アプリケーション レイヤー VM と DBMS VM の間のネットワーク待機時間をテストして評価します。 [SAP サポート ノート #1100926](https://launchpad.support.sap.com/#/notes/1100926/E) のネットワーク待機時間のガイダンスに照らして結果を評価します。 ネットワーク待機時間は、中程度または良好な範囲でなければなりません。 [この記事](./hana-network-architecture.md#networking-architecture-for-hana-large-instance)に記載されているように、VM と HANA Large Instance ユニットとの間のトラフィックには例外が適用されます。 「[SAP ワークロードのための Azure Virtual Machines DBMS デプロイの考慮事項](./dbms_guide_general.md#azure-network-considerations)」および「[Azure における SAP HANA インフラストラクチャの構成と運用](./hana-vm-operations.md)」で説明されている制限が自分のデプロイに該当しないことを確認します。
11. 「[SAP アプリケーションで最適なネットワーク待ち時間を実現する Azure 近接通信配置グループ](sap-proximity-placement-scenarios.md)」という記事に説明されているように、正しい [Azure 近接通信配置グループ](../../co-location.md)に VM がデプロイされることを確認します。
11. ワークロードを適用する前に、概念実証フェーズに記載されている他のすべてのチェックを実行します。
12. ワークロードを適用しながら、Azure でのシステムのリソース使用量を記録します。 この使用量と以前のプラットフォームのレコードを比較します。 大きな違いがある場合は、将来のデプロイで VM のサイズを調整します。 ダウンサイズすると、VM のストレージとネットワークの帯域幅も減少することに留意してください。
    - [Azure の Windows 仮想マシンのサイズ](../../sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
    - [Azure の Linux 仮想マシンのサイズ](../../sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 
13. システムのコピー機能やプロセスについて実験します。 目的は、プロジェクト チームが新しいシステムをすばやく入手できるよう、自分で開発システムやテスト システムを簡単にコピーできるようにすることです。 
14. チームの Azure のロールベースのアクセス、アクセス許可、およびプロセスを最適化して調整します。それにより、義務が確実に分離されるようにします。 同時に、すべてのチームが Azure のインフラストラクチャでタスクを実行できることを確認します。
15. 高可用性とディザスター リカバリーの手順を演習、テスト、文書化し、これらのタスクをスタッフが実行できるようにします。 欠点を明らかにし、デプロイに統合する新しい Azure の機能を調整します。


## <a name="production-preparation-phase"></a>運用準備フェーズ 
このフェーズでは、非運用環境のデプロイ中に経験および学習したことを収集し、将来の運用環境のデプロイに適用します。 また、現在のホスト場所と Azure 間のデータ転送作業も準備する必要があります。

1.  Azure に移行する前に、運用システムの必要な SAP リリース アップグレードを完了します。
1.  運用システムの移行後に実施する必要のある機能テストと業務テストについて、ビジネス所有者と同意します。
1.  これらのテストが、現在のホストの場所にあるソース システムで完了したことを確認します。 システムを Azure に移行した後で初めてテストを実行することがないようにしてください。
1.  運用システムを Azure に移行するプロセスをテストします。 すべての運用システムを同じタイム フレームで Azure に移行するのでない場合は、同じホスト場所にある必要がある運用システムのグループを作成します。 データ移行をテストします。 一般的な方法を以下に示します。
    - バックアップ/復元のような DBMS の方法と、SQL Server Always On、HANA システム レプリケーション、またはログ配布を組み合わせて、Azure にデータベースの内容をシードして同期します。
    - 小さなデータベースに対してはバックアップ/復元を使用します。
    - SAP SWPM に統合されている SAP Migration Monitor を使用して、異種の移行を実行します。
    - 移行を SAP リリースのアップグレードと組み合わせる必要がある場合は、[SAP DMO](https://blogs.sap.com/2013/11/29/database-migration-option-dmo-of-sum-introduction/) プロセスを使用します。 ソース DBMS とターゲット DBMS のすべての組み合わせがサポートされているわけではないことに留意してください。 詳細については、DMO の各種リリースに関する特定の SAP サポート ノートを参照してください。 たとえば、[SUM 2.0 SP04 のデータベース移行オプション (DMO)](https://launchpad.support.sap.com/#/notes/2644872) などです。
    - バックアップまたは SAP エクスポート ファイルを移動する必要がある場合は、インターネット経由と ExpressRoute 経由のどちらの方がデータ転送スループットが高いかをテストします。 インターネット経由でデータを移動する場合は、将来の運用システムで適用する必要があるネットワーク セキュリティ グループまたはアプリケーション セキュリティ グループの規則の一部を変更することが必要になる場合があります。
1.  システムを以前のプラットフォームから Azure に移行する前に、リソース消費データを収集します。 有用なデータには、CPU 使用率、ストレージ スループット、IOPS データなどがあります。 このデータは特に DBMS レイヤーのユニットから収集しますが、アプリケーション レイヤーのユニットからも収集します。 また、ネットワークとストレージの待機時間を測定します。
1.  SAP サポート ノートと必要な OS 設定、SAP HANA ハードウェア ディレクトリ、および SAP PAM を再確認します。 Azure 用にサポートされている VM、それらの VM でサポートされている OS リリース、サポートされている SAP および DBMS のリリースに変更がないことを確認します。
1.  VM の種類と Azure の機能について行った最新の決定事項を反映するようにデプロイ スクリプトを更新します。
1.  インフラストラクチャとアプリケーションをデプロイした後、次のことを検証します。
    - 正しい VM の種類が正しい属性とストレージ サイズでデプロイされたこと。
    - VM が目的どおりの正しい OS リリースとパッチになっていて、統一されていること。
    - VM が必要に応じて一貫した方法で強化されていること。
    - 正しいアプリケーション リリースとパッチがインストールされ、デプロイされたこと。
    - 計画どおりに、VM が Azure 可用性セットにデプロイされたこと。
    - 待機時間の影響を受けやすいディスクに対し、または [99.9% の単一 VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) が要求される場合、Azure Premium Storage が使用されていること。
    - Azure 書き込みアクセラレータが正しくデプロイされていること。
        - VM 内で、書き込みアクセラレータが必要な複数のディスクにまたがって、ストレージ スペースまたはストライプ セットが正しく構築されたことを確認します。
        - [Linux 上のソフトウェア RAID の構成](/previous-versions/azure/virtual-machines/linux/configure-raid)を確認します。
        - [Azure での Linux VM 上の LVM の構成](/previous-versions/azure/virtual-machines/linux/configure-lvm)を確認します。
    - [Azure マネージド ディスク](https://azure.microsoft.com/services/managed-disks/)だけが使用されていること。
    - 正しい可用性セットおよび Availability Zones に VM がデプロイされたこと。
    - SAP アプリケーション レイヤーおよび SAP DBMS レイヤーで使用される VM 上で、[Azure 高速ネットワーク](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/)が有効になっていること。
    - SAP アプリケーションと、SAP NetWeaver、Hybris、または S/4HANA ベースの SAP システムの DBMS レイヤーとの間の通信パスに [Azure ネットワーク仮想アプライアンス](https://azure.microsoft.com/solutions/network-appliances/)がないこと。
    - アプリケーション セキュリティ グループとネットワーク セキュリティ グループの規則で、必要であり計画されていた通信が許可され、必要な場合は通信がブロックされること。
    - 前に説明したようにタイムアウトの設定が正しく指定されていること。
    - 「[SAP アプリケーションで最適なネットワーク待ち時間を実現する Azure 近接通信配置グループ](sap-proximity-placement-scenarios.md)」という記事に説明されているように、正しい [Azure 近接通信配置グループ](../../co-location.md)に VM がデプロイされていること。
    - SAP アプリケーション レイヤーの VM と DBMS の VM との間のネットワーク待機時間が、SAP サポート ノート [#500235](https://launchpad.support.sap.com/#/notes/500235) および [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E) の説明に従ってテストおよび検証されていること。 [SAP サポート ノート #1100926](https://launchpad.support.sap.com/#/notes/1100926/E) のネットワーク待機時間のガイダンスに照らして結果を評価します。 ネットワーク待機時間は、中程度または良好な範囲でなければなりません。 [この記事](./hana-network-architecture.md#networking-architecture-for-hana-large-instance)に記載されているように、VM と HANA Large Instance ユニットとの間のトラフィックには例外が適用されます。
    - 必要に応じて適切な暗号化方法で暗号化が実装されたこと。
    - インターフェイスおよび他のアプリケーションが、新しくデプロイされたインフラストラクチャに接続できること。
1.  Azure の計画メンテナンスに対応するためのプレイブックを作成します。 計画メンテナンスのためにシステムと VM を再起動する順序を決定します。
    

## <a name="go-live-phase"></a>運用開始フェーズ
運用開始フェーズでは、前のフェーズで作成したプレイブックに確実に従ってください。 テストおよび演習した手順を行います。 構成とプロセスの直前の変更は受け付けません。 また、以下の手順を完了します。

1. Azure portal の監視および他の監視ツールが動作していることを確認します。 Windows の場合は Windows パフォーマンス モニター (perfmon)、Linux の場合は SAR をお勧めします。
    - CPU カウンター。
        - 平均 CPU 時間、合計 (すべての CPU)
        - 平均 CPU 時間、各個別プロセッサ (M128 VM では 128 プロセッサ)
        - CPU カーネル時間、各個別プロセッサ
        - CPU ユーザー時間、各個別プロセッサ
    - メモリ。
        - 空きメモリ
        - メモリ ページ イン/秒
        - メモリ ページ アウト/秒
    - [ディスク] を選択する必要があります。
        - ディスク読み取り (KBps)、個別ディスクごと
        - ディスク読み取り数/秒、個別ディスクごと
        - ディスク読み取り (マイクロ秒/回)、個別ディスクごと
        - ディスク書き込み (KBps)、個別ディスクごと
        - ディスク書き込み数/秒、個別ディスクごと
        - ディスク書き込み (マイクロ秒/回)、個別ディスクごと
    - ネットワーク。
        - ネットワーク パケット イン/秒
        - ネットワーク パケット アウト/秒
        - ネットワーク KB イン/秒
        - ネットワーク KB アウト/秒
1.  データ移行後に、ビジネス所有者と合意したすべての検証テストを実行します。 元のソース システムの結果がある検証テストの結果のみを受け付けます。
1.  インターフェイスが機能しているかどうか、および他のアプリケーションが新しくデプロイされた運用システムと通信できるかどうかを確認します。
1.  SAP トランザクション STMS を使用して、トランスポートと修正システムを確認します。
1.  システムが運用環境にリリースされた後、データベースのバックアップを実行します。
1.  システムが運用環境にリリースされた後、SAP アプリケーション レイヤー VM の VM バックアップを実行します。
1.  現在の運用開始フェーズには含まれないが、この運用開始フェーズで Azure に移行した SAP システムと通信する SAP システムの場合は、SM51 でホスト名バッファーをリセットする必要があります。 そうすることにより、Azure に移行したアプリケーション インスタンスの名前に関連付けられているキャッシュされた古い IP アドレスが削除されます。  


## <a name="post-production"></a>運用後
このフェーズでは、システムの監視、運用、管理に関することを行います。 SAP の観点からは、以前のホストの場所で完了する必要があった一般的なタスクが適用されます。 これらの Azure 固有のタスクも完了します。

1. 料金の高いシステムについて Azure の請求書を確認します。
2. VM 側およびストレージ側で、価格/パフォーマンス効率を最適化します。
3. システムをシャットダウンできる時間を最適化します。  


## <a name="next-steps"></a>次のステップ
次の記事を参照してください。

- [SAP NetWeaver のための Azure Virtual Machines の計画と実装](./planning-guide.md)
- [SAP NetWeaver のための Azure Virtual Machines のデプロイ](./deployment-guide.md)
- [SAP ワークロードのための Azure Virtual Machines DBMS デプロイの考慮事項](./dbms_guide_general.md)