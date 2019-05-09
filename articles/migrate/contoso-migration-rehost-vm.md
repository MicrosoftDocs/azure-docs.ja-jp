---
title: Azure Site Recovery を使用した Azure VM への移行によって Contoso アプリを再ホストする | Microsoft Docs
description: Azure Site Recovery サービスを使用したオンプレミス マシンの Azure へのリフトアンドシフト移行によって、オンプレミス アプリを再ホストする方法を説明します。
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/11/2018
ms.author: raynew
ms.openlocfilehash: 4a6ed900753747c1d5bf394aced54da11177320f
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58118393"
---
# <a name="contoso-migration-rehost-an-on-premises-app-to-azure-vms"></a>Contoso の移行: オンプレミス アプリを Azure VM にリホストする


この記事では、Contoso がアプリ VM を Azure VM に移行することによって、オンプレミスの SmartHotel360 アプリを Azure で再ホストする方法について説明します。


このドキュメントは、架空の会社 Contoso がオンプレミス リソースを Microsoft Azure クラウドに移行する方法を説明するシリーズ記事の 1 つです。 このシリーズには背景情報やシナリオが含まれ、移行インフラストラクチャのセットアップ、移行のためのオンプレミス リソースへのアクセス、およびさまざまな種類の移行の実行が説明されています。 シナリオは複雑になってきています。 今後、徐々に記事を追加する予定です。

**記事** | **詳細** | **状態**
--- | --- | ---
[記事 1:概要](contoso-migration-overview.md) | 記事シリーズ、Contoso の移行戦略およびシリーズで使用されているサンプル アプリの概要です。 | 使用可能
[記事 2: Azure インフラストラクチャをデプロイする](contoso-migration-infrastructure.md) | Contoso がオンプレミス インフラストラクチャと Azure インフラストラクチャを移行に向けて準備します。 このシリーズの移行に関するすべての記事で同じインフラストラクチャを使用します。 | 使用可能
[記事 3: Azure への移行についてオンプレミスのリソースを評価する](contoso-migration-assessment.md)  | Contoso が、VMware で実行されているオンプレミスの SmartHotel360 アプリを評価します。 Contoso では、アプリの VM は Azure Migrate サービスを使用して評価し、アプリの SQL Server データベースは Data Migration Assistant を使用して評価します。 | 使用可能
[記事 4: Azure VM および SQL Database Managed Instance でのアプリのリホスト](contoso-migration-rehost-vm-sql-managed-instance.md) | Contoso が、オンプレミスの SmartHotel360 アプリの Azure へのリフトアンドシフト移行を実行します。 Contoso は、[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) を使用してアプリのフロントエンド VM を移行します。 アプリ データベースの Azure SQL Database Managed Instance への移行には、[Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview) を使用します。 | 使用可能
記事 5:Azure VM でのアプリのリホスト | Contoso が Site Recovery サービスを使用して SmartHotel360 アプリの VM を Azure VM に移行します。 | この記事の内容は次のとおりです。
[記事 6:Azure VM および SQL Server AlwaysOn 可用性グループでのアプリのリホスト](contoso-migration-rehost-vm-sql-ag.md) | Contoso が SmartHotel360 アプリを移行します。 Contoso は、Site Recovery を使用してアプリの VM を移行します。 Contoso は、Database Migration Service を使用して、AlwaysOn 可用性グループで保護されている SQL Server クラスターにアプリのデータベースを移行します。 | 使用可能
[記事 7:Azure VM での Linux アプリのリホスト](contoso-migration-rehost-linux-vm.md) | Contoso が Azure Site Recovery を使用して Azure VM への Linux osTicket アプリのリフトアンドシフト移行を行います | 使用可能
[記事 8:Linux アプリを Azure VM 上と Azure MySQL 上でリホストする](contoso-migration-rehost-linux-vm-mysql.md) | Contoso が Azure Site Recovery を使用して Linux osTicket アプリを Azure VM に移行する方法、および MySQL Workbench を使用してアプリのデータベースを Azure MySQL Server インスタンスに移行します。 | 使用可能
[記事 9: Azure Web Apps と Azure SQL Database でアプリをリファクタリングする](contoso-migration-refactor-web-app-sql.md) | Contoso が SmartHotel360 アプリを Azure Web アプリに移行します。また、Database Migration Assistant を使用して、アプリ データベースを Azure SQL Server インスタンスに移行します。 | 使用可能
[記事 10: Azure Web Apps と Azure MySQL で Linux アプリをリファクタリングする](contoso-migration-refactor-linux-app-service-mysql.md) | Contoso が Azure Traffic Manager を使用し、その Linux osTicket アプリを、複数の Azure リージョンの Azure Web アプリに移行します。この Azure Web アプリは、継続的デリバリーを目的として GitHub と統合されます。 Contoso は、アプリ データベースを Azure Database for MySQL インスタンスに移行します。 | 使用可能
[記事 11: Azure DevOps Services で TFS をリファクタリングする](contoso-migration-tfs-vsts.md) | Contoso がそのオンプレミスの Team Foundation Server の展開を Azure の Azure DevOps Services に移行します。 | 使用可能
[記事 12: Azure Containers と Azure SQL Database でアプリを再構築する](contoso-migration-rearchitect-container-sql.md) | Contoso が SmartHotel アプリを Azure に移行します。 その後、アプリの Web 階層を Azure Service Fabric 内で動作する Windows コンテナーとして再構築し、さらに、Azure SQL Database を使用してデータベースを再構築します。 | 使用可能
[記事 13:Azure でのアプリのリビルド](contoso-migration-rebuild.md) | Contoso が Azure のさまざまな機能とサービス (Azure App Service、Azure Kubernetes Service (AKS)、Azure Functions、Azure Cognitive Services、Azure Cosmos DB など) を使用して SmartHotel アプリをリビルドします。 | 使用可能
[記事 14:Azure への移行のスケーリング](contoso-migration-scale.md) | 移行の組み合わせを試した後、Contoso は Azure への完全移行に向けてスケーリングを準備します。 | 使用可能


この記事では、Contoso は 2 階層の Windows を移行します。 VMware VM 上で実行されている NET SmartHotel360 アプリを Azure に移行します。 このアプリを使用したい場合は、オープン ソースとして提供されていますので、[GitHub](https://github.com/Microsoft/SmartHotel360) からダウンロードできます。



## <a name="business-drivers"></a>ビジネス ドライバー

IT リーダーシップ チームは、ビジネス パートナーと密接に連絡を取り合い、彼らがこの移行で何を達成しようとしているのかを理解しました。

- **ビジネスの成長への対応**: Contoso は成長を続けています。そのため、オンプレミスのシステムとインフラストラクチャに負荷がかかっています。
- **リスクの限定**:SmartHotel360 アプリは、Contoso のビジネスにとって非常に重要です。 同社は、リスクを負うことなく Azure にこのアプリを移行したいと考えています。
- **拡張**:Contoso は、アプリを変更することは望んでいません。ただその安定を確保することが望みです。


## <a name="migration-goals"></a>移行の目標

Contoso クラウド チームは、この移行の目標を設定しました。 これらの目標を使用して、最良の移行方法を決定します。

- 移行しても、Azure 内のアプリは、現在の VMware と同じパフォーマンスを発揮できる必要があります。 このアプリは、オンプレミスの場合と同様に、クラウド内にあっても非常に重要であり続けます。
- Contoso は、このアプリへの投資は望んでいません。 これはビジネスにとって重要なものですが、現状のまま確実にクラウドに移すことだけを Contoso は望んでいます。
- Contoso は、このアプリの操作モデルを変更したくありません。 クラウドでは、今行っているのと同じ方法で操作したいのです。
- Contoso は、アプリの機能を変更したくありません。 変えるのはアプリの場所のみです。


## <a name="solution-design"></a>ソリューション設計

Contoso は目標と要件を決定した後、デプロイ ソリューションを設計およびレビューし、移行に使用する Azure サービスを含め、移行プロセスを決めます。

### <a name="current-app"></a>現在のアプリ

- アプリは 2 つの VM (**WEBVM** と **SQLVM**) に階層化されています。
- VM は、VMware ESXi ホスト **contosohost1.contoso.com** (バージョン 6.5) 上に配置されています。
- VMware 環境は、VM 上で実行中の vCenter Server 6.5 (**vcenter.contoso.com**) によって管理されています。
- Contoso にはオンプレミスのデータセンター (contoso-datacenter) があり、そこにオンプレミスのドメイン コントローラー (**contosodc1**) が含まれています。

### <a name="proposed-architecture"></a>提案されたアーキテクチャ

- このアプリは運用のワークロードであるため、Azure 内のアプリの VM は、運用リソース グループ ContosoRG 内に存在することになります。
- アプリの VM はプライマリ Azure リージョン (米国東部 2) に移行され、運用ネットワーク (VNET-PROD-EUS2) 内に配置されます。
- Web のフロントエンド VM は、運用ネットワーク内のフロントエンド サブネット (PROD-FE-EUS2) に置かれます。
- データベース VM は、運用ネットワーク内のデータベース サブネット (PROD-DB-EUS2) 内に存在することになります。
- Contoso データセンター内のオンプレミス VM は、移行が行われた後に使用停止にされます。

![シナリオのアーキテクチャ](./media/contoso-migration-rehost-vm/architecture.png)

### <a name="database-considerations"></a>データベースの考慮事項

Contoso はソリューション設計プロセスの一環として、Azure SQL Database と SQL Server の機能を比較しました。 次の事柄を検討した結果、同社は、Azure IaaS VM 上で稼働する SQL Server を採用することに決定しました。

- Contoso がオペレーティング システムまたはデータベース サーバーをカスタマイズしなければならなくなった場合や、同じ VM にサードパーティのアプリを併置して実行することが必要になった場合、SQL Server を実行する Azure VM を使用することが最適な解決策であると考えられる。
- SQL Database マネージド インスタンスでは、Contoso が将来、ソフトウェア アシュアランスに基づき、SQL Server 用の Azure ハイブリッド使用特典を利用して、既存のライセンスを割引料金のライセンスに交換することができる。 これによって、Managed Instance を最大 30% 節約することができます。



### <a name="solution-review"></a>ソリューションのレビュー

Contoso は、長所と短所の一覧をまとめて、提案されたデザインを評価します。

**考慮事項** | **詳細**
--- | ---
**長所** | アプリの VM はどちらも変更を加えることなく Azure に移されるため、移行が簡単で済みます。<br/><br/> Contoso は両方のアプリの VM にリフトアンドシフトを使用するため、アプリのデータベース用に特別な構成や移行ツールは不要です。<br/><br/> Contoso は、Azure ハイブリッド特典を使用して、ソフトウェア アシュアランスへの投資を活かすことができます。<br/><br/> Contoso は、Azure で引き続き アプリの VM を完全に制御できます。
**短所** | WEBVM と SQLVM では Windows Server 2008 R2 が実行されています。 このオペレーティング システムは、特定のロールを対象に Azure でサポートされます (2018 年 7 月)。 [詳細情報](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)。<br/><br/> アプリの Web 層とデータ層は、依然として単一フェールオーバー ポイントになります。</br><br/> SQLVM が動作する SQL Server 2008 R2はメインストリーム サポートの対象外です。 ただし、Azure VM ではサポートされています (2018 年 7 月)。 [詳細情報](https://support.microsoft.com/en-us/help/956893)。<br/><br/> Contoso は今後も、Azure App Service や Azure SQL Database といったマネージド サービスにアプリを移行するのではなく、Azure VM としてアプリをサポートしていく必要があります。



### <a name="migration-process"></a>移行プロセス

Contoso は、アプリのフロントエンド VM とデータベース VM を、Site Recovery を使用して Azure VM に移行します。

- 最初の手順として、Site Recovery 用の Azure コンポーネントを準備してセットアップし、オンプレミスの VMware インフラストラクチャを準備します。
- [Azure インフラストラクチャ](contoso-migration-infrastructure.md)は既に稼働しているため、Contoso に必要なのは、Site Recovery 用の Azure コンポーネントを追加することだけです。
- すべての準備ができたら、VM のレプリケートを開始できます。
- レプリケーションを有効にしたら、Azure へのフェールオーバーによって VM を移行します。

![移行プロセス](./media/contoso-migration-rehost-vm/migraton-process.png)



### <a name="azure-services"></a>Azure サービス

**サービス** | **説明** | **コスト**
--- | --- | ---
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | このサービスは、Azure VM、オンプレミス VM、物理サーバーの移行とディザスター リカバリーを調整および管理します。  | Azure へのレプリケーションの間に、Azure Storage の料金が発生します。 フェールオーバーが発生すると、Azure VM が作成されて料金がかかります。 料金と価格について[詳しくはこちら](https://azure.microsoft.com/pricing/details/site-recovery/)をご覧ください。


## <a name="prerequisites"></a>前提条件

このシナリオを実行するために Contoso に必要なものを以下に示します。

**要件** | **詳細**
--- | ---
**Azure サブスクリプション** | このシリーズの先行する記事の中で、Contoso はサブスクリプションを作成しました。 Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/pricing/free-trial/)を作成してください。<br/><br/> 無料アカウントを作成する場合、サブスクリプションの管理者としてすべてのアクションを実行できます。<br/><br/> 既存のサブスクリプションを使用しており、管理者でない場合は、管理者に依頼して所有者アクセス許可または共同作成者アクセス許可を割り当ててもらう必要があります。<br/><br/> さらに詳細なアクセス許可が必要な場合は、[こちらの記事](../site-recovery/site-recovery-role-based-linked-access-control.md)をご覧ください。
**Azure インフラストラクチャ** | [Contoso で Azure インフラストラクチャを設定する方法](contoso-migration-infrastructure.md)を確認してください。<br/><br/> Site Recovery 用の[ネットワーク](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network)と[ストレージ](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage)の具体的な要件の詳細を確認してください。
**オンプレミスのサーバー** | オンプレミスの vCenter Server は、バージョン 5.5、6.0、または 6.5 を実行している必要があります。<br/><br/> ESXi ホストは、バージョン 5.5、6.0 または 6.5 を実行している必要があります。<br/><br/> ESXi ホスト上で 1 つ以上の VMware VM が実行されている必要があります。
**オンプレミスの VM** | VM は [Azure の要件](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements)を満たす必要があります。


## <a name="scenario-steps"></a>シナリオのステップ

Contoso の管理者が移行を実行する方法を次に示します。

> [!div class="checklist"]
> * **手順 1:Site Recovery のために Azure を準備する**: レプリケートされたデータを保持するための Azure ストレージ アカウントと、Recovery Services コンテナーを作成します。
> * **手順 2:Site Recovery 用にオンプレミスの VMware を準備する**: フェールオーバー後に Azure VM に接続するために、VM 検出およびエージェント インストール用のアカウントを準備します。
> * **手順 3:VM をレプリケートする**: レプリケーションを設定し、Azure Storage への VM のレプリケーションを開始します。
> * **手順 4:Site Recovery を使用して VM を移行する**:テスト フェールオーバーを実行してすべてが機能していることを確認した後、完全フェールオーバーを実行して VM を Azure に移行します。




## <a name="step-1-prepare-azure-for-the-site-recovery-service"></a>手順 1:Site Recovery サービス用に Azure を準備する

Contoso が VM を Azure に移行するには、以下の Azure コンポーネントが必要です。

- フェールオーバー中に作成される Azure VM が配置される VNet。
- レプリケートされたデータを保持する Azure ストレージ アカウント。
- Azure 内の Recovery Services コンテナー。

これらを次のように設定します。

1. ネットワークを設定する。Contoso は、[Azure インフラストラクチャのデプロイ](contoso-migration-infrastructure.md)を行ったときに、Site Recovery に使用できるネットワークを既にセットアップしています。

    - SmartHotel360 アプリは運用アプリであり、VM はプライマリ リージョンである米国東部 2 の Azure 運用ネットワーク (VNET-PROD-EUS2) に移行されます。
    - 両方の VM は、運用リソースのために使用される ContosoRG リソース グループに配置されます。
    - アプリのフロントエンド VM (WEBVM) は、運用ネットワーク内のフロントエンド サブネット (PROD-FE-EUS2) に移行されます。
    - アプリのデータベース VM (SQLVM) は、運用ネットワーク内のデータベース サブネット (PROD-DB-EUS2) に移行されます。

2. ストレージ アカウントをセットアップする。Contoso は、プライマリ リージョンに Azure ストレージ アカウント (contosovmsacc20180528) を作成します。
   - ストレージ アカウントは、Recovery Services コンテナーと同じリージョンに存在する必要があります。
   - 標準ストレージと LRS レプリケーション付きの汎用アカウントを使用します。

     ![Site Recovery ストレージ](./media/contoso-migration-rehost-vm/asr-storage.png)

3. コンテナーを作成する。ネットワークとストレージ アカウントが用意できたので、Recovery Services コンテナー (ContosoMigrationVault) を作成し、それを米国東部 2 リージョンの ContosoFailoverRG リソース グループ内に配置します。

    ![Recovery Services コンテナー](./media/contoso-migration-rehost-vm/asr-vault.png)

**さらにサポートが必要な場合**

Site Recovery のために Azure を設定することについての[説明を参照します](https://docs.microsoft.com/azure/site-recovery/tutorial-prepare-azure)。


## <a name="step-2-prepare-on-premises-vmware-for-site-recovery"></a>手順 2: Site Recovery のためにオンプレミスの VMware を準備する

Contoso は、オンプレミスで以下を準備します。

- VM の検出を自動化するための vCenter サーバーまたは vSphere ESXi ホストのアカウント。
- モビリティ サービスの VMware VM への自動インストールを可能にするアカウント。
- フェールオーバー後にレプリケートされた Azure VM に Contoso が接続できるようにするためのオンプレミスの VM の設定。


### <a name="prepare-an-account-for-automatic-discovery"></a>自動検出用のアカウントを準備する

Site Recovery では、次のことを実行するために、VMware サーバーへのアクセスが必要です。

- VM を自動的に検出します。
- VM のレプリケーション、フェールオーバー、およびフェールバックの調整。
- 少なくとも読み取り専用のアカウントが必要です。 このアカウントは、ディスクの作成と削除、VM の電源オンなどの操作を実行できる必要があります。

Contoso の管理者は、アカウントを以下のように設定します。

1. vCenter レベルでロールを作成します。
2. そのロールに必要なアクセス許可を割り当てます。



### <a name="prepare-an-account-for-mobility-service-installation"></a>モビリティ サービスのインストール用のアカウントを準備する

各 VM にモビリティ サービスをインストールする必要があります。

- Site Recovery は、VM のレプリケーションが有効な場合に、モバイル サービスの自動プッシュ インストールを実行できます。
- Site Recovery がプッシュ インストールを実行するために VM にアクセスできるアカウントが必要です。 このアカウントは、レプリケーションを設定するときに指定します。
- このアカウントは、VM にインストールする権限があれば、ドメイン アカウントでもローカル アカウントでもかまいません。


### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>フェールオーバー後に Azure VM に接続するための準備をする

Contoso は、フェールオーバー後に Azure VM に接続することを望んでいます。 そのため、Contoso の管理者は、移行前に以下の操作を行います。

1. インターネットでアクセスする場合:

   - フェールオーバーの前に、オンプレミスの VM で RDP を有効にします。
   - **パブリック** プロファイルに TCP 規則と UDP 規則が追加されていることを確認します。
   - **[Windows ファイアウォール]** > **[許可されたアプリ]** で、すべてのプロファイルで RDP が許可されていることを確認します。

2. サイト間 VPN でアクセスする場合:

   - オンプレミスのマシンで RDP を有効にします。
   - **[Windows ファイアウォール]** -> **[Allowed apps and features]\(許可されたアプリと機能\)** で、**ドメイン ネットワークとプライベート ネットワーク**での RDP を許可します。
   - オンプレミスの VM 上のオペレーティング システムの SAN ポリシーを **[OnlineAll]** に設定します。

さらに、フェールオーバーを実行するときに、以下を確認する必要があります。

- フェールオーバーをトリガーするときに、VM 上に保留中の Windows 更新プログラムがないようにします。 ある場合は、更新が完了するまで、VM にログインすることはできません。
- フェールオーバー後、**ブート診断**を調べて、VM のスクリーンショットを確認できます。 これが機能しない場合は、VM が実行中であることを確認し、こちらの[トラブルシューティングのヒント](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)を参照してください。


**さらにサポートが必要な場合**

- 自動検出のためにロールを作成して割り当てることについての[説明を参照します](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-automatic-discovery)。
- モビリティ サービスのプッシュ インストールのためにアカウントを作成することについての[説明を参照します](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-mobility-service-installation)。


## <a name="step-3-replicate-the-on-premises-vms"></a>手順 3: オンプレミスの VM をレプリケートする

Contoso の管理者は、Azure への移行を実行する前に、レプリケーションを設定して有効にする必要があります。

### <a name="set-a-replication-goal"></a>レプリケーションの目標を設定する

1. コンテナー内のコンテナー名 (ContosoVMVault) で、レプリケーションの目標を設定します (**[作業の開始]** > **[Site Recovery]** > **[インフラストラクチャの準備]**)。
2. 各マシンがオンプレミスに配置されていること、VMware で実行されていること、および Azure にレプリケートすることを指定します。

    ![レプリケーションの目標](./media/contoso-migration-rehost-vm/replication-goal.png)

### <a name="confirm-deployment-planning"></a>展開の計画を確認する

続行するには、**[Yes, I have done it]\(はい、完了しました\)** を選択して、デプロイ計画が完了したことを確認します。 このシナリオでは、Contoso が移行するのは 2 つの VM だけなので、デプロイ計画は不要です。


### <a name="set-up-the-source-environment"></a>ソース環境をセットアップする

Contoso の管理者は、ソース環境を構成する必要があります。 これを行うには、OVF テンプレートをダウンロードし、それを使用して、Site Recovery 構成サーバーを高可用性オンプレミス VMware VM としてデプロイします。 構成サーバーを起動して実行したら、それをコンテナーに登録します。

構成サーバーでは、いくつかのコンポーネントが実行されます。

- オンプレミスと Azure の間の通信を調整し、データのレプリケーションを管理する構成サーバー コンポーネント。
- プロセス サーバー: レプリケーション ゲートウェイとして機能します。 レプリケーション データを受信し、そのデータをキャッシュ、圧縮、暗号化によって最適化して、Azure Storage に送信します。
- また、プロセス サーバーは、レプリケートする VM へのモビリティ サービスのインストールや、オンプレミスの VMware VM の自動検出も行います。



Contoso の管理者は、これらのステップを以下のように実行します。

1. コンテナーで、**[インフラストラクチャの準備]** > **[ソース]** > **[構成サーバー]** から OVF テンプレートをダウンロードします。
    
    ![OVF をダウンロードする](./media/contoso-migration-rehost-vm/add-cs.png)

2. VMware にテンプレートをインポートし、VM を作成してデプロイします。

    ![OVF テンプレート](./media/contoso-migration-rehost-vm/vcenter-wizard.png)

3. 初めて VM をオンにすると、Windows Server 2016 インストール環境が起動します。 使用許諾契約書に同意し、管理者パスワードを入力します。
4. インストールの完了後に、管理者として VM にサインインします。 初回のサインインでは、Azure Site Recovery 構成ツールが既定で実行されます。
5. このツールで、構成サーバーをコンテナーに登録するための名前を指定します。
6. このツールは、VM が Azure に接続できることを確認します。 接続が確立されたら、Azure サブスクリプションにサインインします。 資格情報は、構成サーバーを登録するコンテナーにアクセスできる必要があります。

    ![構成サーバーの登録](./media/contoso-migration-rehost-vm/config-server-register2.png)

7. ツールがいくつかの構成タスクを実行した後、再起動されます。
8. マシンに再度サインインすると、構成サーバーの管理ウィザードが自動的に起動されます。
9. ウィザードで、レプリケーション トラフィックを受信する NIC を選択します。 この設定は、構成後に変更することはできません。
10. サブスクリプション、リソース グループ、および構成サーバーを登録するコンテナーを選択します。
        ![コンテナー](./media/contoso-migration-rehost-vm/cswiz1.png)

10. MySQL Server と VMWare PowerCLI をダウンロードしてインストールします。
11. 確認後、vCenter サーバーまたは vSphere ホストの FQDN または IP アドレスを指定します。 ポートは既定のままにし、Azure 内のサーバーに対して、わかりやすい名前を付けます。
12. 自動検出のために作成したアカウントと、モビリティ サービスを自動的にインストールするために使用する資格情報を指定します。 Windows マシンの場合、このアカウントには、VM に対するローカル管理者特権が必要です。

    ![vCenter](./media/contoso-migration-rehost-vm/cswiz2.png)

7. 登録が完了したら、Azure portal で、構成サーバーと VMware サーバーがコンテナーの **[ソース]** ページの一覧に表示されていることを再確認します。 検出には 15 分以上かかる場合があります。
8. Site Recovery が、指定された設定を使用して VMware サーバーに接続し、VM を検出します。

### <a name="set-up-the-target"></a>ターゲットをセットアップする

次に、Contoso の管理者は、ターゲットのレプリケーション設定を指定します。

1. **[インフラストラクチャの準備]**(インフラストラクチャの準備) > **[ターゲット]** で、ターゲットの設定を選択します。
2. Site Recovery は、指定されたターゲットの場所に Azure ストレージ アカウントと ネットワークが存在することを確認します。

### <a name="create-a-replication-policy"></a>レプリケーション ポリシーを作成する

次に、Contoso の管理者は、レプリケーション ポリシーを作成できます。

1. **[インフラストラクチャの準備]** > **[レプリケーション設定]** > **[レプリケーション ポリシー]** >  **[作成と関連付け]** で、ポリシー **ContosoMigrationPolicy** を作成します。
2. 以下の既定の設定を使用します。
    - **RPO しきい値**: 既定値は 60 分です。 この値で、復旧ポイントの作成頻度を指定します。 継続的なレプリケーションがこの制限を超えると、アラートが生成されます。
    - **[復旧ポイントのリテンション期間]**。 既定値は 24 時間です。 この値は、各復旧ポイントのリテンション期間の長さを指定します。 レプリケートされた VM は、期間内の任意の時点に復旧できます。
    - **[アプリ整合性スナップショットの頻度]**。 既定値は 1 時間です。 この値は、アプリケーション整合性スナップショットが作成される頻度を指定します。

        ![レプリケーション ポリシーの作成](./media/contoso-migration-rehost-vm/replication-policy.png)

5. このポリシーは自動的に構成サーバーに関連付けられます。

    ![レプリケーション ポリシーを関連付ける](./media/contoso-migration-rehost-vm/replication-policy2.png)

### <a name="enable-replication-for-webvm"></a>WEBVM のレプリケーションを有効にする

すべての準備が整ったら、Contoso の管理者は、VM のレプリケーションを有効にできます。 WebVM から始めます。

1. **[アプリケーションのレプリケート]** > **[ソース]** > **[+ レプリケート]** で、ソースの設定を選択します。
2. VM を有効にすることを指定し、vCenter Server と構成サーバーを選択します。

    ![レプリケーションを有効にする](./media/contoso-migration-rehost-vm/enable-replication1.png)

3. リソース グループ、Azure ネットワーク、およびストレージ アカウントを含むターゲットの設定を選択します。

    ![レプリケーションを有効にする](./media/contoso-migration-rehost-vm/enable-replication2.png)

4. **WebVM** のレプリケーションを選択し、レプリケーション ポリシーを確認し、レプリケーションを有効にします。

   - VNet とサブネットを選択する必要があるため、この段階では WEBVM のみを選択しています。アプリの VM は異なるサブネットに配置されます。
   - レプリケーションを有効にすると、Site Recovery によってモビリティ サービスが VM 上に自動的にインストールされます。

     ![レプリケーションを有効にする](./media/contoso-migration-rehost-vm/enable-replication3.png)

5. レプリケーションの進行状況は、**[ジョブ]** で追跡します。 **保護の最終処理**ジョブが実行されると、マシンはフェールオーバーできる状態になります。
6. Azure portal の **[要点]** で、Azure にレプリケートする VM の構造を確認できます。


### <a name="enable-replication-for-sqlvm"></a>SQLVM のレプリケーションを有効にする

次に、Contoso の管理者は、上記と同じプロセスを使用して、SQLVM マシンのレプリケートを開始できます。

1. ソースの設定を選択します。

    ![レプリケーションを有効にする](./media/contoso-migration-rehost-vm/enable-replication1.png)

2. ターゲットの設定を指定します。

     ![レプリケーションを有効にする](./media/contoso-migration-rehost-vm/enable-replication2-sqlvm.png)

3. レプリケーションの SQLVM を選択します。

    ![レプリケーションを有効にする](./media/contoso-migration-rehost-vm/enable-replication3-sqlvm.png)

4. WEBVM で使用したのと同じレプリケーション ポリシーを適用し、レプリケーションを有効にします。

    ![インフラストラクチャ ビュー](./media/contoso-migration-rehost-vm/essentials.png)

**さらにサポートが必要な場合**

- これらすべてのステップの詳細な手順については、[オンプレミス VMware VM のディザスター リカバリーの設定に関する記事](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial)を参照してください。
- [ソース環境の設定](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source)、[構成サーバーのデプロイ](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server)、および[レプリケーション設定の構成](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication)に役立つ詳細な手順が記載されています。
- [レプリケーションの有効化](https://docs.microsoft.com/azure/site-recovery/vmware-azure-enable-replication)の詳細を確認します。


## <a name="step-4-migrate-the-vms"></a>手順 4:VM を移行する

Contoso の管理者は、簡単なテスト フェールオーバーを実行した後、VM を移行するための完全フェールオーバーを実行します。

### <a name="run-a-test-failover"></a>テスト フェールオーバーの実行

テスト フェールオーバーを実行して、すべてが想定どおりに動作していることを確認できます。

1. テスト フェールオーバーを実行し、選択可能な最新の時点 (**最後に処理された時点**) を復帰させます。
2. **[Shut down machine before beginning failover]\(フェールオーバー前にマシンをシャットダウンする\)** を選択します。これにより、Site Recovery は、フェールオーバーをトリガーする前にソース VM のシャットダウンを試みます。 仮にシャットダウンが失敗したとしても、フェールオーバーは続行されます。
3. テスト フェールオーバーでは、以下が実行されます。

    - 移行を実行するために必要なすべての条件が満たされていることを確認する前提条件チェックが実行されます。
    - Azure VM を作成できるように、フェールオーバーによってデータが処理されます。 最新の復旧ポイントを選択した場合は、データから復旧ポイントが作成されます。
    - 前の手順で処理されたデータを使用して、Azure VM が作成されます。

3. フェールオーバーが完了すると、Azure Portal にはレプリカの Azure VM が表示されます。 VM が適切なサイズであること、適切なネットワークに接続されていること、および実行されていることを確認します。
4. テスト フェールオーバーを確認したら、このフェールオーバーをクリーンアップし、確認された事柄をすべて記録して保存します。

### <a name="create-and-customize-a-recovery-plan"></a>復旧計画を作成してカスタマイズする

 テスト フェールオーバーが正常に機能することを確認したら、Contoso の管理者は移行のための復旧計画を作成します。

- 復旧計画では、フェールオーバーの実行順序と、Azure VM を Azure 内でオンライにする方法を指定します。
- アプリは 2 階層であるため、データ VM (SQLVM) がフロントエンド VM (WEBVM) の前に起動されるように復旧計画をカスタマイズします。

1. **[復旧計画 (サイトの回復)]** > **[+ 復旧計画]** で計画を作成し、その計画に VM を追加します。

    ![復旧計画](./media/contoso-migration-rehost-vm/recovery-plan.png)

2. 計画を作成したら、それをカスタマイズします (**[復旧計画]** > **[SmartHotelMigrationPlan]** > **[カスタマイズ]**)。
2.  **[グループ 1:起動]** から WEBVM を削除します。 これにより、最初の起動操作が SQLVM のみに影響することが保証されます。
3.  **[+ グループ]** > **[保護された項目の追加]** で、[グループ 2:起動] に WEBVM を追加します。 VM は、2 つの異なるグループに属す必要があります。


### <a name="migrate-the-vms"></a>VM を移行する


次に、Contoso の管理者は、移行を完了する完全フェールオーバーを実行します。

1. 復旧計画を選択し、**[フェールオーバー]** を選択します。
2. 最新の復旧ポイントにフェールオーバーすることを選択し、フェールオーバーをトリガーする前に Site Recovery がオンプレミスの VM のシャットダウンを試みる必要があることを指定します。 フェールオーバーの進行状況は **[ジョブ]** ページで確認できます。

    ![フェールオーバー](./media/contoso-migration-rehost-vm/failover1.png)


3. フェールオーバーが完了したら、Azure VM が想定どおりに Azure Portal に表示されることを確認します。

    ![フェールオーバー](./media/contoso-migration-rehost-vm/failover2.png)

3. 確認後、各 VM の移行を完了します。 これにより、VM のレプリケーションが停止し、Site Recovery の課金が停止します。

    ![フェールオーバー](./media/contoso-migration-rehost-vm/failover3.png)

**さらにサポートが必要な場合**

- テスト フェールオーバーの実行に関する[説明を参照します](https://docs.microsoft.com/azure/site-recovery/tutorial-dr-drill-azure)。
- 復旧計画の作成方法に関する[説明を参照します](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans)。
- Azure へのフェールオーバーに関する[説明を参照します](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover)。

## <a name="clean-up-after-migration"></a>移行後にクリーンアップする

移行が完了し、SmartHotel360 のアプリ層が Azure VM 上で実行されています。

次に、Contoso は、以下のクリーンアップ手順を完了する必要があります。

- WEBVM マシンを vCenter インベントリから削除します。
- SQLVM マシンを vCenter インベントリから削除します。
- WEBVM と SQLVM をローカル バックアップ ジョブから削除します。
- VM の新しい場所と IP アドレスを示すように内部ドキュメントを更新します。
- VM と対話しているリソースを確認し、すべての関連する設定またはドキュメントを新しい構成を反映するように更新します。

## <a name="review-the-deployment"></a>デプロイを再調査する

これでアプリが実行されるようになったので、Contoso は、アプリを完全に操作可能にし、セキュリティで保護する必要があります。

### <a name="security"></a>セキュリティ

Contoso のセキュリティ チームは、Azure VM を再調査して、セキュリティの問題を特定します。

- アクセスを制御するために、VM のネットワーク セキュリティ グループ (NSG) を見直します。 NSG は、許可されたトラフィックのみがアプリに到達できるようにするために使用されます。
- また、このチームは、ディスク上のデータ保護のために、Azure Disk Encryption と KeyVault の使用も検討します。

VM に関するセキュリティの実務の[詳細については、こちら](https://docs.microsoft.com/azure/security/azure-security-best-practices-vms)を参照してください。

## <a name="bcdr"></a>BCDR

事業継続とディザスター リカバリー (BCDR) のために、Contoso は次のアクションを実施します。

- データの安全性を確保する: Contoso は、Azure Backup サービスを使用して VM 上のデータをバックアップします。 [詳細情報](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。
- アプリの稼働状態を維持する: Contoso は、Site Recovery を使用して、Azure 内のアプリの VM をセカンダリ リージョンにレプリケートします。 [詳細情報](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-quickstart)。



### <a name="licensing-and-cost-optimization"></a>ライセンスとコストの最適化

1. Contoso は、VM の既存のライセンスを所有しており、Azure ハイブリッド特典を活用します。 Contoso は、この特典を活用するために、既存の Azure VM を変換します。
2. Contoso は、Microsoft の子会社である Cloudyn からライセンスが供与される Azure Cost Management を有効にします。 これは、Azure やその他のクラウド リソースを利用したり、管理したりできるようにするマルチクラウド コスト管理ソリューションです。 Azure Cost Management の詳細については、[こちら](https://docs.microsoft.com/azure/cost-management/overview)を参照してください。

## <a name="conclusion"></a>まとめ

この記事では、Contoso が Site Recovery サービスを使用してアプリ VM を Azure VM に移行することで、SmartHotel360 アプリを Azure 内で再ホストしました。


## <a name="next-steps"></a>次の手順

このシリーズの[次の記事](contoso-migration-rehost-vm-sql-ag.md)では、Contoso が SmartHotel360 アプリ フロントエンド VM を Azure VM で再ホストし、データベースを Azure の SQL Server AlwaysOn 可用性グループに移行する方法を説明します。

