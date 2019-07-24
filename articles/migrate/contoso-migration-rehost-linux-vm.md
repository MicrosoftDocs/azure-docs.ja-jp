---
title: リホスト-移行およびオンプレミス Linux アプリの Azure VM へのリホスト | Microsoft Docs
description: Contoso が、Azure VM に移行することによってオンプレミス Linux アプリをどのようにリホストするかを説明します。
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: raynew
ms.openlocfilehash: 200f9c5df0d4165341e38ca9d4dd85ad75c8403c
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59010364"
---
# <a name="contoso-migration-rehost-an-on-premises-linux-app-to-azure-vms"></a>Contoso の移行: オンプレミス アプリを Azure VM にリホストする

この記事では、Contoso が、オンプレミスの Linux ベースのサービスデスク アプリ (**osTicket**) を、どのように Azure IaaS VM にリホストするかを示します。

このドキュメントは、架空の会社 Contoso がオンプレミス リソースを Microsoft Azure クラウドに移行する方法を説明するシリーズ記事の 1 つです。 このシリーズには背景情報とシナリオのセットが含まれており、移行インフラストラクチャのセットアップ方法と、さまざまな種類の移行を実施する方法を具体的に説明しています。 シナリオは複雑になってきています。 今後、徐々に記事を追加する予定です。

**記事** | **詳細** | **状態**
--- | --- | ---
[記事 1:概要](contoso-migration-overview.md) | 記事シリーズ、Contoso の移行戦略およびシリーズで使用されているサンプル アプリの概要です。 | 使用可能
[記事 2: Azure インフラストラクチャをデプロイする](contoso-migration-infrastructure.md) | Contoso がオンプレミス インフラストラクチャと Azure インフラストラクチャを移行に向けて準備します。 このシリーズの移行に関するすべての記事で同じインフラストラクチャを使用します。 | 使用可能
[記事 3: Azure への移行についてオンプレミスのリソースを評価する](contoso-migration-assessment.md)  | Contoso が、VMware で実行されているオンプレミスの SmartHotel360 アプリを評価します。 Contoso では、アプリの VM は Azure Migrate サービスを使用して評価し、アプリの SQL Server データベースは Data Migration Assistant を使用して評価します。 | 使用可能
[記事 4: Azure VM および SQL Database Managed Instance でのアプリのリホスト](contoso-migration-rehost-vm-sql-managed-instance.md) | Contoso が、オンプレミスの SmartHotel360 アプリの Azure へのリフトアンドシフト移行を実行します。 Contoso は、[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) を使用してアプリのフロントエンド VM を移行します。 アプリ データベースの Azure SQL Database Managed Instance への移行には、[Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview) を使用します。 | 使用可能   
[記事 5:Azure VM でのアプリのリホスト](contoso-migration-rehost-vm.md) | Contoso が Site Recovery サービスを使用して SmartHotel360 アプリの VM を Azure VM に移行します。 | 使用可能
[記事 6:Azure VM 上および SQL Server AlwaysOn 可用性グループ内でアプリをリホストする](contoso-migration-rehost-vm-sql-ag.md) | Contoso が SmartHotel360 アプリを移行します。 Contoso は、Site Recovery を使用してアプリの VM を移行します。 Contoso は、Database Migration Service を使用して、AlwaysOn 可用性グループで保護されている SQL Server クラスターにアプリのデータベースを移行します。 | 使用可能 
記事 7: Linux アプリを Azure VM 上でリホストする | Contoso が Azure Site Recovery を使用して Azure VM への Linux osTicket アプリのリフトアンドシフト移行を行います | この記事の内容は次のとおりです。
[記事 8:Linux アプリを Azure VM 上と Azure MySQL 上でリホストする](contoso-migration-rehost-linux-vm-mysql.md) | Contoso が Azure Site Recovery を使用して Linux osTicket アプリを Azure VM に移行する方法、および MySQL Workbench を使用してアプリのデータベースを Azure MySQL Server インスタンスに移行します。 | 使用可能
[記事 9: Azure Web Apps と Azure SQL Database でアプリをリファクタリングする](contoso-migration-refactor-web-app-sql.md) | Contoso が SmartHotel360 アプリを Azure Web アプリに移行します。また、Database Migration Assistant を使用して、アプリ データベースを Azure SQL Server インスタンスに移行します。 | 使用可能
[記事 10: Azure Web Apps と Azure MySQL で Linux アプリをリファクタリングする](contoso-migration-refactor-linux-app-service-mysql.md) | Contoso が Azure Traffic Manager を使用し、その Linux osTicket アプリを、複数の Azure リージョンの Azure Web アプリに移行します。この Azure Web アプリは、継続的デリバリーを目的として GitHub と統合されます。 Contoso は、アプリ データベースを Azure Database for MySQL インスタンスに移行します。 | 使用可能 
[記事 11: Azure DevOps Services で TFS をリファクタリングする](contoso-migration-tfs-vsts.md) | Contoso がそのオンプレミスの Team Foundation Server の展開を Azure の Azure DevOps Services に移行します。 | 使用可能
[記事 12: Azure Containers と Azure SQL Database でアプリを再構築する](contoso-migration-rearchitect-container-sql.md) | Contoso が SmartHotel アプリを Azure に移行します。 その後、アプリの Web 階層を Azure Service Fabric 内で動作する Windows コンテナーとして再構築し、さらに、Azure SQL Database を使用してデータベースを再構築します。 | 使用可能
[記事 13:Azure でのアプリのリビルド](contoso-migration-rebuild.md) | Contoso が Azure のさまざまな機能とサービス (Azure App Service、Azure Kubernetes Service (AKS)、Azure Functions、Azure Cognitive Services、Azure Cosmos DB など) を使用して SmartHotel アプリをリビルドします。 | 使用可能
[記事 14:Azure への移行のスケーリング](contoso-migration-scale.md) | 移行の組み合わせを試した後、Contoso は Azure への完全移行に向けてスケーリングを準備します。 | 使用可能



この記事で Contoso は、Linux Apache MySQL PHP (LAMP) で実行されている 2 階層の **osTicket** アプリを Azure に移行します。 アプリの VM は、Azure Site Recovery サービスを使用して移行されます。 このオープンソース アプリを使用したい場合は、[GitHub](https://github.com/osTicket/osTicket) からダウンロードできます。

## <a name="business-drivers"></a>ビジネス ドライバー

IT リーダーシップ チームは、ビジネス パートナーと密接に連絡を取り合い、彼らがこの移行で何を達成しようとしているのかを理解しました。

- **ビジネスの成長への対応**: Contoso は成長を続けています。その結果、オンプレミスのシステムとインフラストラクチャに負荷がかかっています。
- **リスクの限定**: サービスデスク アプリは、Contoso のビジネスにとって非常に重要です。 Contoso は、リスクを負うことなく Azure に移行したいと考えています。
- **拡張**: Contoso は、今すぐアプリに変更を加えることは望んでいません。 ただアプリの安定性を確保することが望みです。


## <a name="migration-goals"></a>移行の目標

Contoso クラウド チームは、この移行の目標を明確にして、最適な移行方法を決定しました。

- 移行後の Azure 内のアプリは、現在のオンプレミス VMWare 環境と同じパフォーマンスを発揮できる必要があります。  このアプリは、オンプレミスの場合と同様に、クラウド内にあっても非常に重要であり続けます。 
- Contoso は、このアプリへの投資は望んでいません。  これはビジネスにとって重要なものですが、現状のまま確実にクラウドに移すことだけを Contoso は望んでいます。
- Contoso は、このアプリの操作モデルを変更したくありません。 今と同じ方法のままクラウドでアプリを操作したいのです。
- Contoso は、アプリの機能を変更したくありません。 変えるのはアプリの場所のみです。
- Windows アプリの移行を何件か完了したら、Contoso は Azure で Linux ベースのインフラストラクチャを使用する方法を学びたいと考えています。


## <a name="solution-design"></a>ソリューション設計

Contoso は目標と要件を決定した後、デプロイ ソリューションを設計およびレビューし、移行に使用する Azure サービスを含め、移行プロセスを決めます。

### <a name="current-app"></a>現在のアプリ

- OSTicket アプリは 2 つの VM (**OSTICKETWEB** および **OSTICKETMYSQL**) に階層化されています。
- VM は、VMware ESXi ホスト **contosohost1.contoso.com** (バージョン 6.5) 上に配置されます。
- VMware 環境は、VM 上で実行中の vCenter Server 6.5 (**vcenter.contoso.com**) によって管理されています。
- Contoso にオンプレミスのデータセンター (**contoso-datacenter**) があり、そこにオンプレミスのドメイン コントローラー (**contosodc1**) が含まれています。

### <a name="proposed-architecture"></a>提案されたアーキテクチャ

- このアプリは運用のワークロードであるため、Azure 内の VM は、運用リソース グループ **ContosoRG** 内に存在することになります。
- VM はプライマリ リージョン (米国東部 2) に移行され、運用ネットワーク (VNET-PROD-EUS2) 内に配置されます。
    - Web VM は、フロントエンド サブネット (PROD-FE-EUS2) 内に存在することになります。
    - データベース VM は、データベース サブネット (PROD-DB-EUS2) 内に存在することになります。
- Contoso データセンター内のオンプレミス VM は、移行が行われた後に使用停止にされます。

![シナリオのアーキテクチャ](./media/contoso-migration-rehost-linux-vm/architecture.png) 

### <a name="solution-review"></a>ソリューションのレビュー

Contoso は、長所と短所の一覧をまとめて、提案されたデザインを評価します。

**考慮事項** | **詳細**
--- | ---
**長所** | アプリの VM はどちらも変更を加えることなく Azure に移されるため、移行が簡単で済みます。<br/><br/> Contoso は両方のアプリの VM にリフトアンドシフトを使用するため、アプリのデータベース用に特別な構成や移行ツールは不要です。<br/><br/> Contoso は、Azure で引き続き アプリの VM を完全に制御できます。 </br>/br> アプリの VM では、動作保証済みの Linux ディストリビューション Ubuntu 16.04-TLS が実行されています。 [詳細情報](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)。
**短所** | アプリの Web 層とデータ層は、依然として単一フェールオーバー ポイントになります。 <br/><br/> Contoso は今後も、Azure App Service や Azure Database for MySQL といったマネージド サービスにアプリを移行するのではなく、Azure VM としてアプリをサポートしていく必要があります。<br/><br/> リフトアンドシフトの VM 移行によって単純化すれば、[Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/overview) の特長 (ビルトインの高可用性、予測可能なパフォーマンス、単純なスケーリング、自動バックアップ、ビルトインのセキュリティ) がフル活用できないことを Contoso は認識しています。

### <a name="migration-process"></a>移行プロセス

Contoso は次のように移行されます。

1. 最初の手順として、Contoso は、Site Recovery をデプロイするために必要な Azure とオンプレミスのインフラストラクチャを設定します。
2. Azure とオンプレミスのコンポーネントを準備したら、VM のレプリケーションを設定し、有効にします。
3. レプリケーションが動作中になったら、VM を Azure にフェールオーバーすることによって VM を移行します。

![移行プロセス](./media/contoso-migration-rehost-linux-vm/migration-process.png)

### <a name="azure-services"></a>Azure サービス

**サービス** | **説明** | **コスト**
--- | --- | ---
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | このサービスは、Azure VM、オンプレミス VM、物理サーバーの移行とディザスター リカバリーを調整および管理します。  | Azure へのレプリケーションの間に、Azure Storage の料金が発生します。  フェールオーバーが発生すると、Azure VM が作成されて料金がかかります。 料金と価格について[詳しくはこちら](https://azure.microsoft.com/pricing/details/site-recovery/)をご覧ください。

 
## <a name="prerequisites"></a>前提条件

このシナリオで Contoso に必要なことを以下に示します。

**要件** | **詳細**
--- | ---
**Azure サブスクリプション** | このシリーズの先行する記事の中で、Contoso はサブスクリプションを作成しました。 Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/pricing/free-trial/)を作成してください。<br/><br/> 無料アカウントを作成する場合、サブスクリプションの管理者としてすべてのアクションを実行できます。<br/><br/> 既存のサブスクリプションを使用しており、管理者でない場合は、管理者に依頼して所有者アクセス許可または共同作成者アクセス許可を割り当ててもらう必要があります。<br/><br/> さらに詳細なアクセス許可が必要な場合は、[こちらの記事](../site-recovery/site-recovery-role-based-linked-access-control.md)をご覧ください。 
**Azure インフラストラクチャ** | Contoso は、[移行のための Azure インフラストラクチャ](contoso-migration-infrastructure.md)についての記事で説明されているように、Azure インフラストラクチャを設定します。<br/><br/> Site Recovery 用の[ネットワーク](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network)と[ストレージ](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage)の具体的な要件の詳細を確認してください。
**オンプレミスのサーバー** | オンプレミス vCenter Server は、バージョン 5.5、6.0、または 6.5 を実行している必要があります<br/><br/> バージョン 5.5、6.0、または 6.5 を実行している ESXi ホスト<br/><br/> ESXi ホスト上で実行している 1 つ以上の VMware VM。
**オンプレミスの VM** | Site Recovery を使用した移行がサポートされる [Linux マシンを確認](https://docs.microsoft.com//azure/site-recovery/vmware-physical-azure-support-matrix#replicated-machines)します。<br/><br/> サポートされる [Linux のファイル システムとストレージ システム](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#linux-file-systemsguest-storage)を確認します。<br/><br/> VM は [Azure の要件](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements)を満たす必要があります。


## <a name="scenario-steps"></a>シナリオのステップ

Contoso は、次のようにして移行を完了します。

> [!div class="checklist"]
> * **手順 1: Site Recovery のために Azure を準備する**: レプリケートされたデータを保持するための Azure ストレージ アカウントと、Recovery Services のコンテナーを作成します。
> * **手順 2: Site Recovery 用にオンプレミスの VMware を準備する**: Contoso は、VM の検出とエージェント インストールのために使用されるアカウントを準備し、フェールオーバー後に Azure VM に接続する準備を行います。
> * **手順 3: VM をレプリケートする**: Contoso は、移行元と移行先の環境を設定し、レプリケーション ポリシーを作成して、Azure のストレージへの VM のレプリケーションを開始します。
> * **手順 4: Site Recovery を使用して VM を移行する**: Contoso は、テスト フェールオーバーを実行してすべてが機能していることを確認した後、完全フェールオーバーを実行して VM を Azure に移行します。


## <a name="step-1-prepare-azure-for-the-site-recovery-service"></a>手順 1: Site Recovery サービス用に Azure を準備する

Site Recovery のために、Contoso には数個の Azure コンポーネントが必要です。

- レプリケートされたデータを保持する Azure の新しいストレージ アカウント。 
- Azure 内の Recovery Services コンテナー。
- フェールオーバーされるリソースが配置された VNet。 Contoso は、[Azure インフラストラクチャのデプロイメント](contoso-migration-infrastructure.md)の間に VNet を既に作成したため、必要なのはストレージ アカウントとコンテナーを作成することだけです。

1. Contoso の管理者は、米国東部 2 リージョンの中に Azure のストレージ アカウント (contosovmsacc20180528) を作成します。

    - ストレージ アカウントは、Recovery Services コンテナーと同じリージョンに存在する必要があります。
    - Standard ストレージと LRS レプリケーション付きの汎用アカウントを使用します。

      ![Site Recovery ストレージ](./media/contoso-migration-rehost-linux-vm/asr-storage.png)

2. ネットワークとストレージ アカウントを準備したら、コンテナー (ContosoMigrationVault) を作成し、それをプライマリの米国東部 2 リージョン内の **ContosoFailoverRG** リソース グループ内に配置します。

    ![Recovery Services コンテナー](./media/contoso-migration-rehost-linux-vm/asr-vault.png)


**さらにサポートが必要な場合**

Site Recovery のために Azure を設定することについての[説明を参照します](https://docs.microsoft.com/azure/site-recovery/tutorial-prepare-azure)。


## <a name="step-2-prepare-on-premises-vmware-for-site-recovery"></a>手順 2: Site Recovery のためにオンプレミスの VMware を準備する

Contoso の管理者は、次のようにオンプレミスの VMware インフラストラクチャを準備します。

- VM の検出を自動化するために、vCenter サーバー上または vSphere ESXi ホスト上にアカウントを作成します。
- レプリケートする VMware VM 上にモビリティ サービスを自動インストールできるアカウントを作成します。
- オンプレミスの VM を準備して、それらの VM が、移行後に Azure VM が作成されたらそこへ接続できるようにします。


### <a name="prepare-an-account-for-automatic-discovery"></a>自動検出用のアカウントを準備する

Site Recovery では、次のことを実行するために、VMware サーバーへのアクセスが必要です。

- VM を自動的に検出します。 少なくとも読み取り専用のアカウントが必要です。
- レプリケーション、フェールオーバー、およびフェールバックを調整します。 ディスクを作成および削除する、VM の電源をオンにするなどの操作を実行できるアカウントが必要です。

Contoso の管理者は、アカウントを以下のように設定します。

1. vCenter レベルでロールを作成します。
2. そのロールに必要なアクセス許可を割り当てます。



### <a name="prepare-an-account-for-mobility-service-installation"></a>モビリティ サービスのインストール用のアカウントを準備する

移行元の Linux VM にモビリティ サービスをインストールする必要があります。

- VM のレプリケーションが有効になっていると、Site Recovery はこのコンポーネントの自動プッシュ インストールを行うことができます。
- 自動プッシュ インストールの場合は、Site Recovery が VM へのアクセスに使用するアカウントを準備する必要があります。
- アカウントの詳細が、レプリケーションのセットアップ中の入力です。 
- このアカウントは、VM にインストールするアクセス許可を持つドメインまたはローカルのアカウントです。


### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>フェールオーバー後に Azure VM に接続するための準備をする

Contoso は、Azure へのフェールオーバー後に、Azure 内のレプリケートされた VM に接続できることを望んでいます。 これを行うには、Contoso の管理者がいくつかのことを実行する必要があります。

- Azure VM にインターネット経由でアクセスするには、移行前に、オンプレミス Linux VM 上で SSH を有効にします。  Ubuntu の場合、これは次のコマンドを使用して完了させることができます: **Sudo apt-get ssh install -y**。
- 移行を実行 (フェールオーバー) したら、**ブート診断**を調べて、VM のスクリーンショットを表示できます。
- これが機能しない場合は、その VM が実行中であることを確認し、これらの[トラブルシューティングのヒント](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)を見直してください。


**さらにサポートが必要な場合**

- 自動検出のためにロールを作成して割り当てることについての[説明を参照します](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-automatic-discovery)。
- モビリティ サービスのプッシュ インストールのためにアカウントを作成することについての[説明を参照します](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-mobility-service-installation)。


## <a name="step-3-replicate-the-on-premises-vms"></a>手順 3: オンプレミスの VM をレプリケートする

Web VM を Azure に移行する前に、Contoso の管理者はレプリケーションを設定し、有効にします。

### <a name="set-a-protection-goal"></a>保護の目標を設定する

1. コンテナー内のコンテナー名 (ContosoVMVault) の下で、レプリケーションの目標を設定します (**[作業の開始]** > **[Site Recovery]** > **[Prepare infrastructure]**(インフラストラクチャの準備))。
2. マシンがオンプレミスに配置されていること、それらのマシンは VMware VM であること、およびレプリケート先は Azure にすることを指定します。
    ![レプリケーションの目標](./media/contoso-migration-rehost-linux-vm/replication-goal.png)

### <a name="confirm-deployment-planning"></a>展開の計画を確認する

続行するには、**[Yes, I have done it]**(はい、完了しました) を選択して、デプロイ計画が完了したことを確認します。 このシナリオで Contoso が移行するのは 1 つの VM だけなので、デプロイ計画は不要です。

### <a name="set-up-the-source-environment"></a>ソース環境をセットアップする

次に、Contoso の管理者は、ソース環境を構成する必要があります。 これを行うには、OVF テンプレートをダウンロードし、それを使用して、Site Recovery 構成サーバーを高可用性オンプレミス VMware VM としてデプロイします。 構成サーバーを起動して実行したら、それをコンテナーに登録します。

構成サーバーでは、いくつかのコンポーネントが実行されます。

- オンプレミスと Azure の間の通信を調整し、データのレプリケーションを管理する構成サーバー コンポーネント。
- プロセス サーバー: レプリケーション ゲートウェイとして機能します。 レプリケーション データを受信し、そのデータをキャッシュ、圧縮、暗号化によって最適化して、Azure Storage に送信します。
- また、プロセス サーバーは、レプリケートする VM へのモビリティ サービスのインストールや、オンプレミスの VMware VM の自動検出も行います。

Contoso の管理者は、これらのステップを以下のように実行します。

1. OVF テンプレートを **[Prepare Infrastructure]**(インフラストラクチャの準備) > **[ソース]** > **[構成サーバー]** からダウンロードします。
    
    ![OVF をダウンロードする](./media/contoso-migration-rehost-linux-vm/add-cs.png)

2. VMware にテンプレートをインポートして VM を作成し、その VM をデプロイします。

    ![OVF テンプレート](./media/contoso-migration-rehost-linux-vm/vcenter-wizard.png)

3. 初めて VM をオンにすると、Windows Server 2016 インストール環境が起動します。 使用許諾契約書に同意し、管理者パスワードを入力します。
4. インストールの完了後に、管理者として VM にサインインします。 初めてサインインしたときは、Azure Site Recovery 構成ツールが既定で実行されます。
5. このツールでは、構成サーバーをコンテナーに登録するために使用する名前を指定します。
6. このツールは、VM が Azure に接続できることを確認します。 接続が確立されたら、Azure サブスクリプションにサインインします。 この資格情報は、構成サーバーを登録するコンテナーにアクセスできる必要があります。

    ![構成サーバーの登録](./media/contoso-migration-rehost-linux-vm/config-server-register2.png)

7. ツールがいくつかの構成タスクを実行した後、再起動されます。
8. マシンに再度サインインすると、構成サーバーの管理ウィザードが自動的に起動されます。
9. ウィザードで、レプリケーション トラフィックを受信する NIC を選択します。 この設定は、構成後に変更することはできません。
10. サブスクリプション、リソース グループ、および構成サーバーを登録するコンテナーを選択します。

    ![コンテナー](./media/contoso-migration-rehost-linux-vm/cswiz1.png) 

11. MySQL Server と VMWare PowerCLI をダウンロードしてインストールします。 
12. 検証が終わったら、vCenter サーバーまたは vSphere ホストの FQDN または IP アドレスを指定します。 ポートは既定のままにして、vCenter サーバーには、わかりやすい名前を指定します。
13. 自動検出のために作成したアカウントと、モビリティ サービスを自動的にインストールするために使用する必要がある資格情報を指定します。

    ![vCenter](./media/contoso-migration-rehost-linux-vm/cswiz2.png)

14. 登録が完了したら、Azure portal で、構成サーバーと VMware サーバーがコンテナーの **[ソース]** ページの一覧に表示されていることを確認します。 検出には 15 分以上かかる場合があります。 
15. Site Recovery はその後、VMware サーバーに接続し、VM を検出します。

### <a name="set-up-the-target"></a>ターゲットをセットアップする

次に、Contoso の管理者は、ターゲットのレプリケーション設定を構成します。

1. **[インフラストラクチャの準備]**(インフラストラクチャの準備) > **[ターゲット]** で、ターゲットの設定を選択します。
2. Site Recovery は、指定されたターゲットに Azure のストレージ アカウントとネットワークが存在することを確認します。

### <a name="create-a-replication-policy"></a>レプリケーション ポリシーを作成する

ソースとターゲットが設定されたら、レプリケーション ポリシーを作成する準備が整います。

1. **[インフラストラクチャの準備]** > **[レプリケーション設定]** > **[レプリケーション ポリシー]** >  **[作成と関連付け]** で、ポリシー **ContosoMigrationPolicy** を作成します。
2. 以下の既定の設定を使用します。
    - **RPO しきい値**: 既定値は 60 分です。 この値で、復旧ポイントの作成頻度を指定します。 継続的なレプリケーションがこの制限を超えると、アラートが生成されます。
    - **[復旧ポイントのリテンション期間]**。 既定値は 24 時間です。 この値は、各復旧ポイントのリテンション期間の長さを指定します。 レプリケートされた VM は、期間内の任意の時点に復旧できます。
    - **[アプリ整合性スナップショットの頻度]**。 既定値は 1 時間です。 この値は、アプリケーション整合性スナップショットが作成される頻度を指定します。
 
        ![レプリケーション ポリシーの作成](./media/contoso-migration-rehost-linux-vm/replication-policy.png)

5. このポリシーは自動的に構成サーバーに関連付けられます。 

    ![レプリケーション ポリシーを関連付ける](./media/contoso-migration-rehost-linux-vm/replication-policy2.png)

**さらにサポートが必要な場合**

- これらすべてのステップの詳細な手順については、[オンプレミス VMware VM のディザスター リカバリーの設定に関する記事](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial)を参照してください。
- [ソース環境の設定](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source)、[構成サーバーのデプロイ](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server)、および[レプリケーション設定の構成](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication)に役立つ詳細な手順が記載されています。
- Linux 用の Azure ゲスト エージェントの詳細については、[こちら](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux)を参照してください。

**さらにサポートが必要な場合**

- これらすべてのステップの詳細な手順については、[オンプレミス VMware VM のディザスター リカバリーの設定に関する記事](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial)を参照してください。
- [ソース環境の設定](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source)、[構成サーバーのデプロイ](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server)、および[レプリケーション設定の構成](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication)に役立つ詳細な手順が記載されています。
- Linux 用の Azure ゲスト エージェントの詳細については、[こちら](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux)を参照してください。



### <a name="enable-replication-for-osticketweb"></a>OSTICKETWEB のレプリケーションを有効にする

これで Contoso の管理者が、**OSTICKETWEB** VM のレプリケートを開始できるようになりました。

1. **[アプリケーションのレプリケート]** > **[ソース]** > **[+Replicate]\(+ レプリケート\)** で、ソースの設定を選択します。
2. vCenter サーバーと構成サーバーを含めて、仮想マシンを有効にすることを選択し、ソース設定を選択します。

    ![レプリケーションを有効にする](./media/contoso-migration-rehost-linux-vm/enable-replication-source.png)

3. ターゲットの設定を指定します。これには、フェールオーバー後に Azure VM が配置されるリソース グループと VNet、レプリケートされたデータが保存されるストレージ アカウントを指定することも含まれます。

     ![レプリケーションを有効にする](./media/contoso-migration-rehost-linux-vm/enable-replication2.png)

3. レプリケーションの対象として **OSTICKETWEB** VM を選択します。 

   - この段階では、**OSTICKETWEB** のみを選択します。これは、VNet とサブネットの両方を選択する必要があり、VM は同じサブネットの中にないためです。
   - VM に対してレプリケーションが有効にされると、Site Recovery がモビリティ サービスを自動的にインストールします。

     ![レプリケーションを有効にする](./media/contoso-migration-rehost-linux-vm/enable-replication3.png)

4. VM のプロパティで、モビリティ サービスをマシンに自動的にインストールするためにプロセス サーバーが使用するアカウントを選択します。

     ![モビリティ サービス](./media/contoso-migration-rehost-linux-vm/linux-mobility.png)

5. **[レプリケーションの設定]** > **[レプリケーション設定の構成]** で、正しいレプリケーション ポリシーが適用されていることを確認し、**[Enable Replication]**(レプリケーションを有効にする) を選択します。
6. レプリケーションの進行状況は、**[ジョブ]** で追跡します。 **保護の最終処理**ジョブが実行されると、マシンはフェールオーバーできる状態になります。



### <a name="enable-replication-for-osticketmysql"></a>OSTICKETMYSQL のレプリケーションを有効にする

Contoso の管理者は、**OSTICKETMYSQL** のレプリケートを開始できるようになりました。

1. **[アプリケーションのレプリケート]** > **[ソース]** > **[+Replicate]\(+ レプリケート\)** で、ソースのとターゲットの設定を選択します。
2. レプリケーションのために **OSTICKETMYSQL** VM を選択し、モビリティ サービスのインストールに使用するアカウントを選択します。

    ![レプリケーションを有効にする](./media/contoso-migration-rehost-linux-vm/mysql-enable.png)

3. OSTICKETWEB で使用したのと同じレプリケーション ポリシーを適用し、レプリケーションを有効にします。  

**さらにサポートが必要な場合**

これらのすべてのステップの詳細な手順は、「[レプリケーションを有効にする](https://docs.microsoft.com/azure/site-recovery/vmware-azure-enable-replication)」で確認できます。


## <a name="step-4-migrate-the-vms"></a>手順 4: VM を移行する 

Contoso の管理者は、簡単なテスト フェールオーバーを実行してから VM を移行します。

### <a name="run-a-test-failover"></a>テスト フェールオーバーの実行

テスト フェールオーバーを実行すると、移行の前に、すべてが正常に機能していることを確認する助けになります。 

1. テスト フェールオーバーを実行し、選択可能な最新の時点 (**最後に処理された時点**) を復帰させます。
2. **[Shut down machine before beginning failover]\(フェールオーバー前にマシンをシャットダウンする\)** を選択します。これにより、Site Recovery は、フェールオーバーをトリガーする前にソース VM のシャットダウンを試みます。 仮にシャットダウンが失敗したとしても、フェールオーバーは続行されます。 
3. テスト フェールオーバーでは、以下が実行されます。 
    - 移行を実行するために必要なすべての条件が満たされていることを確認する前提条件チェックが実行されます。
    - Azure VM を作成できるように、フェールオーバーによってデータが処理されます。 最新の復旧ポイントが選択された場合は、データから復旧ポイントが作成されます。
    - 前の手順で処理されたデータを使用して、Azure VM が作成されます。
3. フェールオーバーが完了すると、Azure Portal にはレプリカの Azure VM が表示されます。 VM が適切なサイズであること、適切なネットワークに接続されていること、実行中であることを確認します。 
4. 確認したら、フェールオーバーをクリーンアップして、確認された事柄をすべて記録して保存します。

### <a name="create-and-customize-a-recovery-plan"></a>復旧計画を作成してカスタマイズする

 テスト フェールオーバーが正常に機能することを確認したら、Contoso の管理者は移行のための復旧計画を作成します。 

- 復旧計画では、どのような順序でフェールオーバーを実行し、どのように Azure VM を Azure 内に加えるかを指定します。
- 2 階層のアプリを移行しようとしているため、データ VM (SQLVM) がフロントエンド (WEBVM) の前に起動されるように復旧計画をカスタマイズします。


1. **[復旧計画 (サイトの回復)]** > **[+ 復旧計画]** で計画を作成し、その計画に VM を追加します。

    ![復旧計画](./media/contoso-migration-rehost-linux-vm/recovery-plan.png)

2. 計画を作成したら、カスタマイズのためにその計画を選択します (**[復旧計画]** > **[OsTicketMigrationPlan]** > **[カスタマイズ]**)。
3.  彼らは、**OSTICKETWEB** を **[グループ 1: 開始]** から削除します。  これにより、最初の開始操作が **OSTICKETMYSQL** のみに影響を与えるようにします。

    ![復旧グループ](./media/contoso-migration-rehost-linux-vm/recovery-group1.png)

4.  **[+ グループ]** > **[保護された項目の追加]** で、**OSTICKETWEB** を **[グループ 2: 開始]** に追加します。  これらが 2 つの異なるグループ内にあることが必要です。

    ![復旧グループ](./media/contoso-migration-rehost-linux-vm/recovery-group2.png)


### <a name="migrate-the-vms"></a>VM を移行する


Contoso の管理者は、復旧計画に基づいてフェールオーバーを実行し、VM を移行する準備が整いました。

1. 計画を選択してから **[フェールオーバー]** を選択します。
2.  最新の復旧ポイントにフェールオーバーすることを選択し、Site Recovery では、フェールオーバーをトリガーする前にオンプレミス VM のシャットダウンを試みる必要があることを指定します。 フェールオーバーの進行状況は **[ジョブ]** ページで確認できます。

    ![フェールオーバー](./media/contoso-migration-rehost-vm/failover1.png)

3. フェールオーバー中に、vCenter Server は、ESXi ホストで実行されている 2 つの VM を停止するコマンドを発行します。

    ![フェールオーバー](./media/contoso-migration-rehost-linux-vm/vcenter-failover.png)

3. フェールオーバーが完了したら、Azure VM が想定どおりに Azure Portal に表示されることを確認します。

    ![フェールオーバー](./media/contoso-migration-rehost-linux-vm/failover2.png)  

3. Azure で VM を確認した後は、移行を完了し、VM ごとの移行プロセスを終了させます。 これで、VM のレプリケーションが停止し、その VM に対する Site Recovery の課金が停止します。

    ![フェールオーバー](./media/contoso-migration-rehost-linux-vm/failover3.png)


### <a name="connect-the-vm-to-the-database"></a>VM をデータベースに接続する

移行プロセスの最終ステップとして、Contoso の管理者は、**OSTICKETMYSQL** VM で実行されているアプリ データベースを指すようにアプリケーションの接続文字列を更新します。 

1. Putty または別の SSH クライアントを使用して、**OSTICKETWEB** VM への SSH 接続を作成します。 この VM はプライベートなので、プライベート IP アドレスを使用して接続します。

    ![データベースへの接続](./media/contoso-migration-rehost-linux-vm/db-connect.png)  

    ![データベースへの接続](./media/contoso-migration-rehost-linux-vm/db-connect2.png)  

2. **OSTICKETWEB** VM が **OSTICKETMYSQL** VM と通信できることを確認する必要があります。 構成は、この時点で、オンプレミスの IP アドレス 172.16.0.43 を使用してハードコードされています。

    **更新より前**
    
    ![IP の更新](./media/contoso-migration-rehost-linux-vm/update-ip1.png)  

    **更新より後**
    
    ![IP の更新](./media/contoso-migration-rehost-linux-vm/update-ip2.png) 
    
3. **systemctl restart apache2** と指定してサービスを再起動します。

    ![Restart](./media/contoso-migration-rehost-linux-vm/restart.png) 

4. 最後に、いずれかの Contoso ドメイン コント ローラー上で、**OSTICKETWEB** と **OSTICKETMYSQL** の DNS レコードを更新します。

    ![DNS の更新](./media/contoso-migration-rehost-linux-vm-mysql/update-dns.png) 

    ![DNS の更新](./media/contoso-migration-rehost-linux-vm-mysql/update-dns.png) 

**さらにサポートが必要な場合**

- テスト フェールオーバーの実行に関する[説明を参照します](https://docs.microsoft.com/azure/site-recovery/tutorial-dr-drill-azure)。 
- 復旧計画の作成方法に関する[説明を参照します](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans)。
- Azure へのフェールオーバーに関する[説明を参照します](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover)。

## <a name="clean-up-after-migration"></a>移行後にクリーンアップする

移行が完了した時点では、Azure VM で osTicket のアプリ層が実行されています。

ここで、以下のようにクリーンアップを実行する必要があります。 

- vCenter のインベントリからオンプレミスの VM を削除します。
- ローカルのバックアップ ジョブからからオンプレミスの VM を削除します。
- 社内のドキュメントを更新して、OSTICKETWEB および OSTICKETMYSQL の新しい場所と IP アドレスを示します。
- VM と対話しているリソースを確認し、すべての関連する設定またはドキュメントを新しい構成を反映するように更新します。
- Contoso では、依存関係のマッピングを指定して Azure Migrate サービスを使用し、移行のための VM を評価しました。 管理者は、この目的のためにインストールした Microsoft Monitoring Agent と Dependency Agent を、VM から削除する必要があります。

## <a name="review-the-deployment"></a>デプロイを再調査する

アプリが実行されるようになり、Contoso は新しいインフラストラクチャを完全に操作可能にして、セキュリティで保護する必要があります。

### <a name="security"></a>セキュリティ

Contoso のセキュリティ チームは、OSTICKETWEB と OSTICKETMYSQL の VM を再調査して、セキュリティの問題を特定します。

- アクセスを制御するために、VM のネットワーク セキュリティ グループ (NSG) を見直します。 NSG は、アプリケーションに対して許可されるトラフィックのみが通過できるようにするために使用されます。
- ディスクの暗号化と Azure KeyVault を使用した、VM ディスク上のデータのセキュリティ保護もこのチームが検討します。

VM に関するセキュリティの実務の[詳細については、こちら](https://docs.microsoft.com/azure/security/azure-security-best-practices-vms)を参照してください。

### <a name="bcdr"></a>BCDR

事業継続とディザスター リカバリーのために、Contoso は次のアクションを実施します。

- **データの安全性を確保する**: Contoso は、Azure Backup サービスを使用して VM 上のデータをバックアップします。 [詳細情報](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。
- **アプリの稼働状態を維持する**: Contoso は、Site Recovery を使用して、Azure 内のアプリの VM をセカンダリ リージョンにレプリケートします。 [詳細情報](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-quickstart)。

### <a name="licensing-and-cost-optimization"></a>ライセンスとコストの最適化

- Contoso は、リソースのデプロイ後、[Azure インフラストラクチャのデプロイ](contoso-migration-infrastructure.md#set-up-tagging)中に定義されたとおりに Azure タグを割り当てます。
- Contoso に、Ubuntu サーバーとのライセンスの問題はありません。
- Contoso は、Microsoft の子会社である Cloudyn からライセンスが供与される Azure Cost Management を有効にします。 これは、Azure やその他のクラウド リソースの利用や管理に役立つ、マルチクラウド対応のコスト管理ソリューションです。  Azure Cost Management の詳細については、[こちら](https://docs.microsoft.com/azure/cost-management/overview)を参照してください。 


## <a name="next-steps"></a>次の手順

この記事では、Contoso が、2 つの Linux VM 上で階層化されているオンプレミス サービスデスク アプリを、Azure Site Recovery を使用して Azure IaaS VM に移行した方法を示しました。

このシリーズの次の記事では、Contoso が、同じサービスデスク アプリをどのように Azure に移行するかを示します。 Contoso は今回、Site Recovery を使用してアプリのフロントエンド VM を移行します。そして、バックアップと復元に加えて、MySQL ワークベンチ ツールを使用してアプリ データベースを Azure Database for MySQL に移行します。 では、[始めてください](contoso-migration-rehost-linux-vm-mysql.md)。
