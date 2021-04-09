---
title: Azure Site Recovery を使用して SQL Server のディザスター リカバリーを設定する
description: この記事では、SQL Server と Azure Site Recovery を使用して SQL Server のディザスター リカバリーを設定する方法について説明します。
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 08/02/2019
ms.author: sutalasi
ms.openlocfilehash: 1b02b089fea7e883bdc6c58c7a2845af12b50a37
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96011950"
---
# <a name="set-up-disaster-recovery-for-sql-server"></a>SQL Server のためにディザスター リカバリーを設定する

この記事では、アプリケーションの SQL Server バックエンドを保護する方法について説明します。 そのためには、SQL Server のビジネス継続性とディザスター リカバリー (BCDR) テクノロジと [Azure Site Recovery](site-recovery-overview.md) の組み合わせを使用します。

開始する前に、SQL Server のディザスター リカバリー機能を理解していることを確認してください。 次のような機能があります。

* フェールオーバー クラスタリング
* Always On 可用性グループ
* データベース ミラーリング
* ログ配布
* アクティブな地理的レプリケーション
* 自動フェールオーバー グループ

## <a name="combining-bcdr-technologies-with-site-recovery"></a>BCDR テクノロジと Site Recovery の組み合わせ

SQL Server インスタンスを復旧するための BCDR テクノロジの選択は、次の表で説明されている目標復旧時間 (RTO) と目標復旧時点 (RPO) のニーズに基づいている必要があります。 Site Recovery と選択したテクノロジのフェールオーバー操作を組み合わせて、アプリケーション全体の復旧を調整します。

デプロイの種類 | BCDR テクノロジ | SQL Server の予測される RTO | SQL Server の予測される RPO |
--- | --- | --- | ---
Azure IaaS (サービスとしてのインフラストラクチャ) 仮想マシン (VM) 上またはオンプレミスの SQL Server。| [Always On 可用性グループ](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server?view=sql-server-2017) | セカンダリ レプリカをプライマリにするためにかかる時間。 | セカンダリ レプリカへのレプリケーションは非同期であるため、一部のデータが失われます。
Azure IaaS VM 上またはオンプレミスの SQL Server。| [フェールオーバー クラスタリング (Always On FCI)](/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server?view=sql-server-2017) | ノード間でフェールオーバーするためにかかる時間。 | Always On FCI は共有ストレージを使用するため、ストレージ インスタンスの同じビューをフェールオーバー時に使用できます。
Azure IaaS VM 上またはオンプレミスの SQL Server。| [データベース ミラーリング (高パフォーマンス モード)](/sql/database-engine/database-mirroring/database-mirroring-sql-server?view=sql-server-2017) | サービスを強制するためにかかる時間。この場合、ミラー サーバーがウォーム スタンバイ サーバーとして使用されます。 | レプリケーションは非同期です。 ミラー データベースは、プリンシパル データベースよりやや遅れることがあります。 この遅れは通常、短時間です。 ただし、プリンシパルまたはミラー サーバーのシステムの負荷が高い場合は長くなることがあります。<br/><br/>ログ配布がデータベース ミラーリングの補足になる場合があります。 これは、非同期データベース ミラーリングの適切な代替手段になります。
Azure 上のサービスとしてのプラットフォーム (PaaS) としての SQL。<br/><br/>このデプロイの種類には、単一データベースとエラスティック プールが含まれます。 | アクティブな地理的レプリケーション | フェールオーバーがトリガーされてから 30 秒。<br/><br/>セカンダリ データベースのいずれかに対してフェールオーバーがアクティブ化されると、その他のすべてのセカンダリが新しいプライマリに自動的にリンクされます。 | 5 秒の RPO。<br/><br/>アクティブ geo レプリケーションは、SQL Server の Always On テクノロジを使用します。 これは、スナップショット分離を使用して、プライマリ データベース上のコミットされたトランザクションをセカンダリ データベースに非同期的にレプリケートします。<br/><br/>セカンダリ データには部分トランザクションが含まれないことが保証されます。
Azure 上のアクティブ geo レプリケーションが構成された PaaS としての SQL。<br/><br/>このデプロイの種類には、マネージド インスタンス、エラスティック プール、単一データベースが含まれます。 | 自動フェールオーバー グループ | 1 時間の RTO。 | 5 秒の RPO。<br/><br/>自動フェールオーバー グループは、アクティブ geo レプリケーションの上にグループ セマンティクスを提供します。 ただし、同じ非同期レプリケーション メカニズムが使用されます。
Azure IaaS VM 上またはオンプレミスの SQL Server。| Azure Site Recovery を使用したレプリケーション | RTO は通常 15 分未満です。 詳細については、[Site Recovery によって提供される RTO SLA](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/) に関するページを参照してください。 | アプリケーション整合性では 1 時間、クラッシュ整合性では 5 分です。 より低い RPO を求めている場合は、別の BCDR テクノロジを使用してください。

> [!NOTE]
> Site Recovery を使用して SQL ワークロードを保護する場合のいくつかの重要な考慮事項。
> * Site Recovery はアプリケーションに依存しません。 Site Recovery は、サポートされているオペレーティング システム上にデプロイされたすべてのバージョンの SQL Server を保護するために役立ちます。 詳細については、レプリケートされるコンピューターの[復旧のサポート マトリックス](vmware-physical-azure-support-matrix.md#replicated-machines)を参照してください。
> * Azure、Hyper-V、VMware、または物理インフラストラクチャにあるすべてのデプロイに対して Site Recovery を使用することを選択できます。 Site Recovery を使用して [SQL Server クラスターを保護する方法](#how-to-help-protect-a-sql-server-cluster)については、この記事の最後にあるガイダンスに従ってください。
> * コンピューター上で観察されたデータ変化率が [Site Recovery の制限](vmware-physical-azure-support-matrix.md#churn-limits)内にあることを確認してください。 この変化率は、1 秒あたりの書き込みバイト数で測定されます。 Windows を実行しているコンピューターの場合、この変化率は、タスク マネージャーで **[パフォーマンス]** タブを選択することによって表示できます。 各ディスクの書き込み速度を観察します。
> * Site Recovery は、記憶域スペース ダイレクトでのフェールオーバー クラスター インスタンスのレプリケーションをサポートしています。 詳細については、[記憶域スペース ダイレクト レプリケーションを有効にする方法](azure-to-azure-how-to-enable-replication-s2d-vms.md)に関するページを参照してください。
> 
> SQL ワークロードを Azure に移行する場合は、[SQL Server のパフォーマンス ガイドラインを Azure Virtual Machines](../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md) に適用することをお勧めします。

## <a name="disaster-recovery-of-an-application"></a>アプリケーションのディザスター リカバリー

Site Recovery は、復旧計画を使用して、アプリケーション全体のテスト フェールオーバーとフェールオーバーを調整します。

復旧計画がニーズに応じて完全にカスタマイズされるようにするためのいくつかの前提条件があります。 通常は、すべての SQL Server デプロイに Active Directory デプロイが必要です。 また、アプリケーション層への接続も必要です。

### <a name="step-1-set-up-active-directory"></a>手順 1:Active Directory のセットアップ

SQL Server が正常に実行されるように、セカンダリ復旧サイトに Active Directory をセットアップします。

* **小規模企業**: オンプレミス サイトのための少数のアプリケーションと 1 つのドメイン コントローラーがあります。 サイト全体をフェールオーバーする場合は、Site Recovery レプリケーションを使用します。 このサービスは、ドメイン コントローラーをセカンダリ データセンターまたは Azure にレプリケートします。
* **中規模企業から大企業**: 追加のドメイン コントローラーのセットアップが必要になることがあります。
  - 多数のアプリケーションと Active Directory フォレストがあり、アプリケーションまたはワークロードごとにフェールオーバーする場合は、セカンダリ データセンターまたは Azure に別のドメイン コントローラーをセットアップします。
  -  リモート サイトに復旧するために Always On 可用性グループを使用している場合は、セカンダリ サイトまたは Azure に別のドメイン コントローラーをセットアップします。 このドメイン コントローラーは、復旧された SQL Server インスタンスのために使用されます。

この記事の手順では、セカンダリの場所でドメイン コントローラーを使用できることを前提にしています。 詳細については、[Site Recovery を使用して Active Directory を保護する](site-recovery-active-directory.md)ための手順を参照してください。

### <a name="step-2-ensure-connectivity-with-other-tiers"></a>手順 2:他の層との接続を確保する

ターゲット Azure リージョンでデータベース層が実行されるようになったら、アプリケーションおよび Web 層との接続が存在することを確認してください。 テスト フェールオーバーで接続を検証するために、事前に必要な手順を実行します。

接続を考慮してアプリケーションを設計する方法を理解するには、次の例を参照してください。

* [クラウド ディザスター リカバリー に対応するアプリケーションの設計](../azure-sql/database/designing-cloud-solutions-for-disaster-recovery.md)
* [エラスティック プールを使用したディザスター リカバリー戦略](../azure-sql/database/disaster-recovery-strategies-for-applications-with-elastic-pool.md)

### <a name="step-3-interoperate-with-always-on-active-geo-replication-and-auto-failover-groups"></a>手順 3:Always On、アクティブ geo レプリケーション、および自動フェールオーバー グループと相互運用する

BCDR テクノロジの Always On、アクティブ geo レプリケーション、および自動フェールオーバー グループには、ターゲット Azure リージョンで実行されている SQL Server のセカンダリ レプリカがあります。 アプリケーション フェールオーバーのための最初の手順は、このレプリカをプライマリとして指定することです。 この手順では、セカンダリに既にドメイン コントローラーが存在することを前提にしています。 自動フェールオーバーの実行を選択したときは、この手順が必要ない場合があります。 Web およびアプリケーション層は、データベース フェールオーバーが完了した後でのみフェールオーバーします。

> [!NOTE]
> Site Recovery を使用して SQL コンピューターを保護している場合は、これらのコンピューターの復旧グループを作成し、そのフェールオーバーを復旧計画に追加するだけで済みます。

アプリケーションおよび Web 層の仮想マシンを含む[復旧計画を作成](site-recovery-create-recovery-plans.md)します。 次の手順は、データベース層のフェールオーバーを追加する方法を示しています。

1. SQL 可用性グループをフェールオーバーするスクリプトを [Resource Manager 仮想マシン](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAG.ps1)と[従来の仮想マシン](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAGClassic.ps1)の両方にインポートします。 これらのスクリプトを Azure Automation アカウントにインポートします。

    [!["Azure へのデプロイ" のロゴの画像](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. 復旧計画の最初のグループの事前アクションとして ASR-SQL-FailoverAG スクリプトを追加します。

1. このスクリプトで使用可能な手順に従ってオートメーション変数を作成します。 この変数は、可用性グループの名前を指定します。

### <a name="step-4-conduct-a-test-failover"></a>手順 4:テスト フェールオーバーを実行する

SQL Always On などの一部の BCDR テクノロジでは、テスト フェールオーバーをネイティブにサポートしていません。 *このようなテクノロジを使用している場合にのみ*、次のアプローチをお勧めします。

1. Azure で可用性グループ レプリカをホストする VM に [Azure Backup](../backup/backup-azure-vms-first-look-arm.md) をセットアップします。

1. 復旧計画のテスト フェールオーバーをトリガーする前に、前の手順で取得されたバックアップから VM を復旧します。

    ![Azure Backup から構成を復元するためのウィンドウを示すスクリーンショット](./media/site-recovery-sql/restore-from-backup.png)

1. バックアップから復元された VM で[クォーラムを強制](/sql/sql-server/failover-clusters/windows/force-a-wsfc-cluster-to-start-without-a-quorum#PowerShellProcedure)します。

1. リスナーの IP アドレスをテスト フェールオーバー ネットワークで使用できるアドレスに更新します。

    ![[ルール] ウィンドウと IP アドレス プロパティのダイアログのスクリーンショット](./media/site-recovery-sql/update-listener-ip.png)

1. リスナーをオンラインにします。

    ![サーバー名と状態を示す Content_AG というラベルが付いたウィンドウのスクリーンショット](./media/site-recovery-sql/bring-listener-online.png)

1. フェールオーバー ネットワークのロード バランサーに、1 つの IP アドレス (各可用性グループ リスナーに対応するフロントエンド IP アドレス プールの IP アドレス) と、バックエンド プールの SQL Server VM があることを確認します。

     !["SQL-AlwaysOn-LB - フロントエンド IP プール" というタイトルのウィンドウのスクリーンショット](./media/site-recovery-sql/create-load-balancer1.png)

    !["SQL-AlwaysOn-LB - バックエンド IP プール" というタイトルのウィンドウのスクリーンショット](./media/site-recovery-sql/create-load-balancer2.png)

1. 後の復旧グループで、この復旧計画のためのアプリケーション層のフェールオーバー、次に Web 層のフェールオーバーを追加します。

1. 復旧計画のテスト フェールオーバーを実行して、アプリケーションのエンド ツー エンドのフェールオーバーをテストします。

## <a name="steps-to-do-a-failover"></a>フェールオーバーを実行する手順

手順 3. でスクリプトを追加し、それを手順 4. で検証したら、手順 3. で作成された復旧計画のフェールオーバーを実行できます。

アプリケーションおよび Web 層のフェールオーバーの手順は、テスト フェールオーバーとフェールオーバーの両方の復旧計画で同じである必要があります。

## <a name="how-to-help-protect-a-sql-server-cluster"></a>SQL Server クラスターを保護する方法

SQL Server Standard エディションまたは SQL Server 2008 R2 を実行しているクラスターの場合は、Site Recovery レプリケーションを使用して SQL Server を保護することをお勧めします。

### <a name="azure-to-azure-and-on-premises-to-azure"></a>Azure から Azure およびオンプレミスから Azure

Site Recovery では、Azure リージョンにレプリケートするときに、ゲスト クラスターはサポートされていません。 また、SQL Server Standard エディションでは、低コストのディザスター リカバリー ソリューションも提供されません。 このシナリオでは、プライマリの場所で SQL Server クラスターをスタンドアロン SQL Server インスタンスに対して保護し、それをセカンダリで復旧することをお勧めします。

1. プライマリ Azure リージョンまたはオンプレミス サイトで、追加のスタンドアロン SQL Server インスタンスを構成します。

1. このインスタンスを、保護するデータベースのミラーとして機能するように構成します。 高安全性モードでミラーリングを構成します。

1. [Azure](azure-to-azure-tutorial-enable-replication.md)、[Hyper-V](./hyper-v-azure-tutorial.md)、または [VMware VM と物理サーバー](./vmware-azure-tutorial.md)のためのプライマリ サイト上で Site Recovery を構成します。

1. Site Recovery レプリケーションを使用して、新しい SQL Server インスタンスをセカンダリ サイトにレプリケートします。 これは高安全性のミラー コピーであるため、プライマリ クラスターと同期されますが、Site Recovery レプリケーションを使用してレプリケートされます。

   ![プライマリ サイト、Site Recovery、および Azure の関係とそれらの間のフローを示す Standard クラスターの画像](./media/site-recovery-sql/standalone-cluster-local.png)

### <a name="failback-considerations"></a>フェールバックに関する考慮事項

SQL Server Standard クラスターの場合、計画されていないフェールオーバーの後のフェールバックには SQL Server のバックアップと復元が必要です。 この操作は、ミラーの再確立によって、ミラー インスタンスから元のクラスターに対して実行されます。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

### <a name="how-does-sql-server-get-licensed-when-used-with-site-recovery"></a>Site Recovery と共に使用される場合、SQL Server のライセンスはどのように取得されますか。

SQL Server 向けの Site Recovery レプリケーションは、ソフトウェア アシュアランスのディザスター リカバリー特典の対象になります。 この対象範囲は、Site Recovery のすべてのシナリオ (オンプレミスから Azure へのディザスター リカバリーおよびリージョン間の Azure IaaS ディザスター リカバリー) に適用されます。 詳細については、「[Azure Site Recovery の価格](https://azure.microsoft.com/pricing/details/site-recovery/)」を参照してください。

### <a name="will-site-recovery-support-my-sql-server-version"></a>現在使用している SQL Server バージョンは Site Recovery でサポートされますか。

Site Recovery はアプリケーションに依存しません。 Site Recovery は、サポートされているオペレーティング システム上にデプロイされたすべてのバージョンの SQL Server を保護するために役立ちます。 詳細については、レプリケートされるコンピューターの[復旧のサポート マトリックス](vmware-physical-azure-support-matrix.md#replicated-machines)を参照してください。

## <a name="next-steps"></a>次のステップ

* [Site Recovery のアーキテクチャ](./azure-to-azure-architecture.md)を学習してください。
* Azure の SQL Server については、セカンダリ Azure リージョンでの復旧のための[高可用性ソリューション](../azure-sql/virtual-machines/windows/business-continuity-high-availability-disaster-recovery-hadr-overview.md#azure-only-high-availability-solutions)を学習してください。
* SQL Database については、セカンダリ Azure リージョンでの復旧のための[ビジネス継続性](../azure-sql/database/business-continuity-high-availability-disaster-recover-hadr-overview.md)および[高可用性](../azure-sql/database/high-availability-sla.md)オプションを学習してください。
* オンプレミスの SQL Server コンピューターについては、Azure Virtual Machines での復旧のための[高可用性オプション](../azure-sql/virtual-machines/windows/business-continuity-high-availability-disaster-recovery-hadr-overview.md#hybrid-it-disaster-recovery-solutions)を学習してください。