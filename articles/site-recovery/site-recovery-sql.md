---
title: SQL Server と Azure Site Recovery を使用して SQL Server のディザスター リカバリーを設定する | Microsoft Docs
description: この記事では、SQL Server と Azure Site Recovery を使用して、SQL Server のディザスター リカバリーを設定する方法について説明します。
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/30/2019
ms.author: sutalasi
ms.openlocfilehash: 1c44b10b54a5f58dff1aecf36c3633cc8ffbd8f0
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491772"
---
# <a name="set-up-disaster-recovery-for-sql-server"></a>SQL Server のためにディザスター リカバリーを設定する

この記事では、SQL Server のビジネス継続性とディザスター リカバリー (BCDR) テクノロジおよび [Azure Site Recovery](site-recovery-overview.md) の組み合わせを使用してアプリケーションの SQL Server バックエンドを保護する方法について説明します。

開始する前に、フェールオーバー クラスタリング、Always On 可用性グループ、データベース ミラーリング、ログ配布、アクティブ geo レプリケーション、自動フェールオーバー グループなど、SQL Server ディザスター リカバリー機能について理解しておいてください。

## <a name="dr-recommendation-for-integration-of-sql-server-bcdr-technologies-with-site-recovery"></a>SQL Server の BCDR テクノロジを Site Recovery と統合する際の DR の推奨事項

SQL Server を復旧するための BCDR テクノロジの選択は、次の表に示すように、RTO と RPO のニーズに基づいて行う必要があります。 選択を行ったら、Site Recovery をそのテクノロジのフェールオーバー操作と統合して、アプリケーション全体の復旧を調整できます。

**デプロイの種類** | **BCDR テクノロジ** | **SQL の予想される RTO** | **SQL の予想される RPO** |
--- | --- | --- | ---
Azure IaaS VM 上またはオンプレミスの SQL Server| **[Always On 可用性グループ](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server?view=sql-server-2017)** | セカンダリ レプリカをプライマリにするのにかかる時間と同じ | レプリケーションはセカンダリ レプリカに対して非同期であるため、一部のデータが失われます。
Azure IaaS VM 上またはオンプレミスの SQL Server| **[フェールオーバー クラスタリング (Always On FCI)](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server?view=sql-server-2017)** | ノード間のフェールオーバーにかかる時間と同じ | 共有ストレージが使用されるため、フェールオーバー時にストレージ インスタンスの同じビューを使用できます。
Azure IaaS VM 上またはオンプレミスの SQL Server| **[データベース ミラーリング (高パフォーマンス モード)](https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server?view=sql-server-2017)** | ミラー サーバーをウォーム スタンバイ サーバーとして使用する、サービスの強制にかかる時間と同じ | レプリケーションは非同期です。 ミラー データベースは、プリンシパル データベースよりやや遅れることがあります。 通常、この時間差はわずかです。ただし、プリンシパルまたはミラー サーバーのシステムの負荷が高い場合は、この時間差が大きくなる可能性があります。<br></br>ログ配布はデータベース ミラーリングを補うことができ、非同期データベース ミラーリングの有効な代替手段となります。
Azure 上の PaaS としての SQL<br></br>(エラスティック プール、SQL Database サーバー) | **アクティブ geo レプリケーション** | トリガーされてから 30 秒<br></br>セカンダリ データベースの 1 つに対してフェールオーバーがアクティブな場合、その他すべてのセカンダリ データベースは新しいプライマリ データベースに自動的にリンクします。 | RPO は 5 秒<br></br>アクティブ geo レプリケーションでは、SQL Server の Always On テクノロジを活用し、スナップショット分離を使用して、プライマリ データベース上のコミットされたトランザクションをセカンダリ データベースに非同期にレプリケートします。 <br></br>セカンダリ データには部分トランザクションが含まれないことが保証されます。
Azure 上のアクティブ geo レプリケーションで構成された PaaS としての SQL<br></br>(SQL Database Managed Instance、エラスティック プール、SQL Database サーバー) | **自動フェールオーバー グループ** | RTO は 1時間 | RPO は 5 秒<br></br>自動フェールオーバー グループにはアクティブ geo レプリケーションに加えてグループ セマンティックが用意されていますが、同じ非同期レプリケーション メカニズムを使用します。
Azure IaaS VM 上またはオンプレミスの SQL Server| **Azure Site Recovery によるレプリケーション** | 通常は 15 分未満。 Azure Site Recovery で提供される RTO SLA の詳細については、[こちら](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/)をご覧ください。 | アプリケーション一貫性では 1 時間、クラッシュ一貫性では 5 分です。 

> [!NOTE]
> Azure Site Recovery で SQL ワークロードを保護する際に、重要な考慮事項がいくつかあります。
> * Azure Site Recovery はアプリケーションに依存しないため、サポートされているオペレーティング システムに展開されたどのバージョンの SQL Server も Azure Site Recovery で保護できます。 [詳細情報](vmware-physical-azure-support-matrix.md#replicated-machines)。
> * Azure、Hyper-V、VMware、または物理インフラストラクチャでのデプロイに Site Recovery を使用できます。 Azure Site Recovery で SQL Server クラスターを保護する方法については、このドキュメントの最後にある[ガイダンス](site-recovery-sql.md#how-to-protect-a-sql-server-cluster-standard-editionsql-server-2008-r2)に従ってください。
> * マシンで確認されたデータ変化率 (1 秒あたりの書き込みバイト数) が [Site Recovery の制限](vmware-physical-azure-support-matrix.md#churn-limits)の範囲内であることを確認します。 Windows マシンの場合、タスク マネージャーの [パフォーマンス] タブでこれを確認できます。 各ディスクの書き込み速度を監視します。
> * Azure Site Recovery では、記憶域スペース ダイレクトでのフェールオーバー クラスター インスタンスのレプリケーションがサポートされています。 [詳細情報](azure-to-azure-how-to-enable-replication-s2d-vms.md)。
 

## <a name="disaster-recovery-of-application"></a>アプリケーションのディザスター リカバリー

**Azure Site Recovery では、復旧計画を利用して、アプリケーション全体のテスト フェールオーバーとフェールオーバーを調整します。** 

復旧計画をニーズに合わせて完全にカスタマイズできるようにするための前提条件がいくつかあります。 通常、SQL Server デプロイには Active Directory が必要です。 また、アプリケーション層への接続も必要です。

### <a name="step-1-set-up-active-directory"></a>手順 1:Active Directory のセットアップ

SQL Server を正常に実行するために、セカンダリ復旧サイトに Active Directory をセットアップします。

* **小規模企業** - アプリケーションの数が少なく、オンプレミスのサイト用のドメイン コントローラーが 1 つしかない場合にサイト全体をフェールオーバーするには、Site Recovery レプリケーションを使用して、セカンダリ データセンターまたは Azure にドメイン コント ローラーをレプリケートすることをお勧めします。
* **中規模および大企業** - アプリケーションの数が多く、Active Directory フォレストがある場合に、アプリケーションまたはワークロード単位でフェールオーバーするには、追加のドメイン コントローラーをセカンダリ データセンターまたは Azure にセットアップすることをお勧めします。 Always On 可用性グループを使用してリモート サイトに復旧する場合は、もう 1 つ追加のドメイン コントローラーをセカンダリ サイトまたは Azure にセットアップして、復旧した SQL Server インスタンスに対して使用することをお勧めします。

この記事に記載された手順は、2 番目の拠点でドメイン コントローラーが使用できることを前提としています。 [こちら](site-recovery-active-directory.md) を参照してください。

### <a name="step-2-ensure-connectivity-with-other-application-tiers-and-web-tier"></a>手順 2:他のアプリケーション層および Web 層との接続を確保する

ターゲット Azure リージョンでデータベース層が稼働状態になったら、アプリケーション層および Web 層と接続されていることを確認します。 テスト フェールオーバーで接続を検証するために、事前に必要な手順を実行します。

次の例を参照し、接続を考慮してアプリケーションを設計する方法を理解してください。
* [クラウド ディザスター リカバリー に対応するアプリケーションの設計](../sql-database/sql-database-designing-cloud-solutions-for-disaster-recovery.md)
* [エラスティック プールを使用したディザスター リカバリー戦略](../sql-database/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)

### <a name="step-3-integrate-with-always-on-active-geo-replication-or-auto-failover-groups-for-application-failover"></a>手順 3:アプリケーションのフェールオーバーのために、Always On、アクティブ geo レプリケーション、または自動フェールオーバー グループと統合する

BCDR テクノロジの Always-On、アクティブ geo レプリケーション、自動フェールオーバー グループでは、ターゲット Azure リージョンで SQL Server のセカンダリ レプリカが実行されています。 そのため、アプリケーションのフェールオーバーでは、まず、このレプリカをプライマリにします (セカンダリにドメイン コントローラーが既にあることを前提としています)。 自動フェールオーバーを実行することを選択した場合、この手順は必要でない場合もあります。 Web 層またはアプリケーション層のフェールオーバーは、データベースのフェールオーバーが完了してから実行します。

> [!NOTE] 
> SQL マシンを Azure Site Recovery で保護している場合は、これらのマシンの復旧グループを作成し、フェールオーバーを復旧計画に追加するだけで済みます。

アプリケーション層と Web 層の仮想マシンを含めた[復旧計画を作成](site-recovery-create-recovery-plans.md)します。 次の手順に従って、データベース層のフェールオーバーを追加します。

1. Azure Automation アカウントにスクリプトをインポートします。 これは SQL 可用性グループをフェールオーバーするスクリプトを [Resource Manager 仮想マシン](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAG.ps1)と[クラシック仮想マシン](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAGClassic.ps1)に含んでいます。

    [![Azure へのデプロイ](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)


1. 復旧計画の最初のグループの事前アクションとして、ASR SQL-FailoverAG を追加します。

1. 可用性グループの名前を指定するオートメーション変数を作成するには、スクリプトで使用できる指示に従います。

### <a name="step-4-conduct-a-test-failover"></a>手順 4:テスト フェールオーバーを実行する

SQL Always On などの一部の BCDR テクノロジでは、テスト フェールオーバーはネイティブでサポートされていません。 そのため、**このようなテクノロジと統合する場合にのみ**、次の方法をお勧めします。

1. Azure で可用性グループ レプリカをホストする仮想マシンに [Azure Backup](../backup/backup-azure-arm-vms.md) をセットアップします。

1. 復旧計画のテスト フェールオーバーをトリガーする前に、前の手順で作成したバックアップから仮想マシンを復元します。

    ![Azure Backup からの復元](./media/site-recovery-sql/restore-from-backup.png)

1. バックアップから復元された仮想マシンで[クォーラムを強制](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/force-a-wsfc-cluster-to-start-without-a-quorum#PowerShellProcedure)します。

1. リスナーの IP アドレスをテスト フェールオーバーのネットワークで利用できる IP アドレスに更新します。

    ![リスナー IP を更新する](./media/site-recovery-sql/update-listener-ip.png)

1. リスナーをオンラインにします。

    ![リスナーをオンラインにする](./media/site-recovery-sql/bring-listener-online.png)

1. 1 つの IP が各可用性グループ リスナーに対応するフロントエンド IP プールの下に作成された状態、そしてバックエンド プールに SQL 仮想マシンが追加された状態で、ロード バランサーを作成します。

     ![ロード バランサーを作成する - フロントエンド IP プール](./media/site-recovery-sql/create-load-balancer1.png)

    ![ロード バランサーを作成する - バックエンド プール](./media/site-recovery-sql/create-load-balancer2.png)

1. 後続の復旧グループで、この復旧計画にアプリケーション層のフェールオーバーを追加し、その後、Web 層のフェールオーバーを追加します。 
1. 復旧計画のテスト フェールオーバーを実行して、アプリケーションのエンド ツー エンドのフェールオーバーをテストします。

## <a name="steps-to-do-a-failover"></a>フェールオーバーを実行する手順

手順 3. で復旧計画にスクリプトを追加し、手順 4. で特殊な方法を使ってテスト フェールオーバーを実行して復旧計画を検証したら、手順 3. で作成した復旧計画のフェールオーバーを実行できます。

アプリケーション層と Web 層のフェールオーバーの手順は、テスト フェールオーバーとフェールオーバーの両方の復旧計画で同じである必要があります。

## <a name="how-to-protect-a-sql-server-cluster-standard-editionsql-server-2008-r2"></a>SQL Server クラスターを保護する方法 (Standard エディション/SQL Server 2008 R2)

SQL Server Standard エディション または SQL Server 2008 R2 を実行するクラスターでは、Site Recovery レプリケーションを使用して SQL Server を保護することをお勧めします。

### <a name="azure-to-azure-and-on-premises-to-azure"></a>Azure から Azure およびオンプレミスから Azure

Site Recovery では、Azure リージョンにレプリケートするときに、ゲスト クラスターはサポートされていません。 SQL Server も Standard エディション用に低コストのディザスター リカバリー ソリューションを提供しません。 このシナリオでは、プライマリ ロケーションでスタンドアロン SQL Server に対して SQL Server クラスターを保護し、セカンダリで復旧することをお勧めします。

1. プライマリ Azure リージョンまたはオンプレミス サイトで、追加のスタンドアロン SQL Server インスタンスを構成します。
1. このインスタンスを、保護が必要なデータベースのミラーとして機能するように構成します。 高い安全性モードでミラーリングを構成します。
1. プライマリ サイト ([Azure](azure-to-azure-tutorial-enable-replication.md)、[Hyper-V](site-recovery-hyper-v-site-to-azure.md)、または [VMware VM/物理サーバー](site-recovery-vmware-to-azure-classic.md)) で Site Recovery を構成します。
1. Site Recovery レプリケーションを使用して、新しい SQL Server インスタンスをセカンダリ サイトにレプリケートします。 これは高い安全性のミラー コピーであるため、プライマリ クラスターと同期されますが、Site Recovery レプリケーションを使用してレプリケートされます。


![Standard クラスター](./media/site-recovery-sql/standalone-cluster-local.png)

### <a name="failback-considerations"></a>フェールバックに関する考慮事項

SQL Server Standard のクラスターの場合、計画されていないフェールオーバー後のフェールバックでは、ミラーを再確立して、SQL Server のバックアップと復元 (ミラー インスタンスから元のクラスターへ) が必要になります。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

### <a name="how-does-sql-get-licensed-when-protected-with-azure-site-recovery"></a>Azure Site Recovery で保護されている場合、SQL のライセンスはどのように取得されますか?
SQL Server 向けの Azure Site Recovery レプリケーションは、Azure Site Recovery のすべてのシナリオ (オンプレミスから Azure へのディザスター リカバリー、またはリージョンをまたがる Azure IaaS のディザスター リカバリー) で、ソフトウェア アシュアランス (ディザスター リカバリー特典) の対象になります。 [詳細については、こちらを参照してください。](https://azure.microsoft.com/pricing/details/site-recovery/)

### <a name="will-azure-site-recovery-support-my-sql-version"></a>現在使用している SQL バージョンは Azure Site Recovery でサポートされますか?
Azure Site Recovery はアプリケーションに依存しません。 そのため、サポートされているオペレーティング システムに展開されたどのバージョンの SQL Server も Azure Site Recovery で保護できます。 [詳細情報](vmware-physical-azure-support-matrix.md#replicated-machines)

## <a name="next-steps"></a>次の手順
* Site Recovery のアーキテクチャについて[こちら](site-recovery-components.md)をご覧ください。
* Azure の SQL Server については、セカンダリ Azure リージョンでの復旧のための[高可用性ソリューション](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md#azure-only-high-availability-solutions)の詳細をご覧ください。
* Azure の SQL Database については、セカンダリ Azure リージョンでの復旧のための[ビジネス継続性](../sql-database/sql-database-business-continuity.md)および[高可用性](../sql-database/sql-database-high-availability.md)オプションの詳細をご覧ください。
* オンプレミスの SQL Server マシンについては、Azure Virtual Machines での復旧のための高可用性オプションの[詳細](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md#hybrid-it-disaster-recovery-solutions)をご覧ください。
