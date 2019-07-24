---
title: Contoso オンプレミス アプリを Azure VM および Azure SQL Database Managed Instance に移行してリホストする | Microsoft Docs
description: Contoso がオンプレミス アプリを Azure VM で Azure SQL Database Managed Instance を使用してリホストする方法を説明します。
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/11/2018
ms.author: raynew
ms.openlocfilehash: 832614c46f0269460245d081f20897b591e31fce
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58101569"
---
# <a name="contoso-migration-rehost-an-on-premises-app-on-an-azure-vm-and-sql-database-managed-instance"></a>Contoso の移行: オンプレミス アプリを Azure VM および SQL Database Managed Instance にリホストする

この記事では、Contoso が Azure Site Recovery サービスを使用して、SmartHotel360 アプリ フロントエンド VM を Azure VM に移行します。 また、アプリ データベースを Azure SQL Database Managed Instance に移行します。

> [!NOTE]
> 現時点で Azure SQL Database Managed Instance はプレビュー段階です。

この記事は、架空の会社 Contoso がオンプレミスのリソースを Microsoft Azure クラウドに移行する方法について説明するシリーズ記事の 1 つです。 このシリーズには、背景情報と一連のシナリオが含まれており、移行インフラストラクチャのセットアップ方法や、さまざまな種類の移行方法を理解できるようになっています。 シナリオは複雑になってきています。 今後もこのシリーズの記事を追加していく予定です。


**記事** | **詳細** | **状態**
--- | --- | ---
[記事 1:概要](contoso-migration-overview.md) | Contoso の移行戦略、記事シリーズ、シリーズで使用するサンプル アプリの概要。 | 使用可能
[記事 2: Azure インフラストラクチャをデプロイする](contoso-migration-infrastructure.md) | Contoso がオンプレミス インフラストラクチャと Azure インフラストラクチャを移行に向けて準備します。 このシリーズの移行に関するすべての記事で同じインフラストラクチャを使用します。 | 使用可能
[記事 3: Azure への移行についてオンプレミスのリソースを評価する](contoso-migration-assessment.md) | VMware で実行されているオンプレミスの 2 層 SmartHotel アプリを Contoso が評価します。 Contoso は、アプリの VM を評価するために [Azure Migrate](migrate-overview.md) サービスを使用します。 アプリの SQL Server データベースの評価には、[Data Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) を使用します。 | 使用可能
記事 4: Azure VM および SQL Database Managed Instance でのアプリのリホスト | Contoso が、オンプレミスの SmartHotel アプリの Azure へのリフトアンドシフト移行を実行します。 Contoso は、[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) を使用してアプリのフロントエンド VM を移行します。 アプリ データベースの Azure SQL Database Managed Instance への移行には、[Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview) を使用します。 | この記事の内容は次のとおりです。
[記事 5: Azure VM でアプリをリホストする](contoso-migration-rehost-vm.md) | Contoso が Site Recovery サービスを使用して SmartHotel アプリの VM を Azure VM に移行します。 | 使用可能
[記事 6:Azure VM および SQL Server AlwaysOn 可用性グループでアプリをリホストする](contoso-migration-rehost-vm-sql-ag.md) | Contoso が SmartHotel アプリを移行します。 Contoso は、Site Recovery を使用してアプリの VM を移行します。 Contoso は、Database Migration Service を使用して、AlwaysOn 可用性グループで保護されている SQL Server クラスターにアプリのデータベースを移行します。 | 使用可能
[記事 7:Linux アプリを Azure VM 上でリホストする](contoso-migration-rehost-linux-vm.md) | Contoso が Site Recovery を使用して、Azure VM への Linux osTicket アプリのリフトアンドシフト移行を完了します。 | 使用可能
[記事 8:Azure VM および Azure Database for MySQL での Linux アプリのリホスト](contoso-migration-rehost-linux-vm-mysql.md) | Contoso が Site Recovery を使用して Linux osTicket アプリを Azure VM に移行します。 アプリ データベースを Azure Database for MySQL に移行するために MySQL Workbench を使用します。 | 使用可能
[記事 9:Azure Web App と Azure SQL Database 内でアプリをリファクタリングする](contoso-migration-refactor-web-app-sql.md) | Contoso が SmartHotel アプリを Azure Web アプリに移行し、アプリ データベースを Azure SQL Server インスタンスに移行します。 | 使用可能
[記事 10:Azure Web アプリと Azure Database for MySQL での Linux アプリのリファクター](contoso-migration-refactor-linux-app-service-mysql.md) | Contoso が Linux osTicket アプリを複数サイト上の Azure Web アプリに移行します。 Web アプリは継続的デリバリー用に GitHub と統合されます。 Contoso は、アプリ データベースを Azure Database for MySQL インスタンスに移行します。 | 使用可能
[記事 11:Azure DevOps サービスで Team Foundation Server をリファクタリングする](contoso-migration-tfs-vsts.md) | Contoso がそのオンプレミスの Team Foundation Server の展開を移行することにより Azure の Azure DevOps Services に移行します。 | 使用可能
[記事 12:Azure コンテナーと Azure SQL Database 内でアプリを再構築する](contoso-migration-rearchitect-container-sql.md) | Contoso が SmartHotel アプリを Azure に移行し、移行したアプリを再構築します。 Contoso は、アプリの Web 階層を Windows コンテナーとして再構築し、Azure SQL Database を使用してアプリ データベースを再構築します。 | 使用可能
[記事 13:Azure でアプリを再構築する](contoso-migration-rebuild.md) | Contoso が Azure のさまざまな機能とサービス (Azure App Service、Azure Kubernetes Service、Azure Functions、Azure Cognitive Services、Azure Cosmos DB など) を使用して SmartHotel アプリをリビルドします。 | 使用可能
[記事 14:Azure への移行のスケーリング](contoso-migration-scale.md) | 移行の組み合わせを試した後、Contoso は Azure への完全移行に向けてスケーリングを準備します。 | 使用可能




この記事で使用されているサンプルの SmartHotel360 アプリは、[GitHub](https://github.com/Microsoft/SmartHotel360) からダウンロードできます。



## <a name="business-drivers"></a>ビジネス ドライバー

Contoso の IT リーダーシップ チームは、自社のビジネス パートナーと密接に連絡を取り合い、ビジネス部門がこの移行で何を達成しようとしているのかを理解しました。

- **ビジネスの成長への対応**: Contoso は成長しています。 その結果、会社のオンプレミスのシステムとインフラストラクチャにかかる圧力が増加しています。
- **効率化**: Contoso は不要な手順を取り除き、開発者とユーザーのプロセスを効率化する必要があります。 顧客の要求にすばやく対応できるように、ビジネス部門は IT 部門に対して、時間やコストを無駄にせず、迅速に作業を行うことを求めています。
- **迅速性の向上**: Contoso IT は、ビジネス部門の要求に対して、対応力を向上させる必要があります。 会社がグローバル経済で成功を収めるために、市場で起きる変化に遅れることなく迅速に対応する必要があります。 Contoso の IT 部門がビジネスの妨げになったり、ビジネスの妨げになったりするようなことがあってはなりません。
- **スケール**:会社のビジネスが順調に成長している中で、Contoso IT 部門は、同じペースで拡張できるシステムを提供する必要があります。

## <a name="migration-goals"></a>移行の目標

Contoso クラウド チームは、この移行の目標を確認しました。 会社では、移行の目標を使用して、最適な移行方法を決定します。

- 移行しても、Azure のアプリは、Contoso のオンプレミス VMWare 環境で現在提供されているのと同じパフォーマンスを発揮できる必要があります。 クラウドに移行したからといって、アプリのパフォーマンスの重要性が低下するわけではありません。
- Contoso はアプリへの投資を望んでいません。 このアプリはビジネスに欠くことのできない重要なものですが、Contoso は現在の形式のままクラウドに移行したいと考えています。
- アプリ移行後のデータベース管理のタスクを最小限に抑える必要があります。
- Contoso は、このアプリに Azure SQL Database を使用したくないと考えています。 Contoso は別のデータベースを探しています。


## <a name="solution-design"></a>ソリューション設計

Contoso は目標と要件を決定した後、デプロイ ソリューションを設計およびレビューし、移行に使用する Azure サービスを含め、移行プロセスを決めます。

### <a name="current-architecture"></a>現在のアーキテクチャ 

- Contoso にはメイン データセンターが 1 つあります (**contoso-datacenter**)。 そのデータセンターは、米国東部のニューヨーク市に位置しています。
- Contoso は、その他にも米国内全体で 3 つの地方支店があります。
- メイン データセンターは、光ファイバーによるメトロ イーサネット接続 (500 MBps) を通じて、インターネットに接続されています。
- 各支店は、ビジネス クラスの接続を使用して、インターネットにローカルで接続されています。メイン データセンターには、IPsec VPN トンネルで接続されています。 このセットアップにより、Contoso のネットワーク全体を永続的に接続でき、インターネット接続が最適化されます。
- メイン データセンターは、VMware によって完全に仮想化されています。 Contoso には、vCenter Server 6.5 で管理されている ESXi 6.5 仮想化ホストが 2 つあります。
- Contoso は ID 管理に Active Directory を使用しています。 Contoso は内部ネットワーク上で DNS サーバーを使用しています。
- Contoso には、オンプレミスのドメイン コントローラーがあります (**contosodc1**)。
- ドメイン コントローラーは、VMware VM 上で実行されています。 支店にあるドメイン コントローラーは、物理サーバー上で実行されています。
- SmartHotel360 アプリは、2 つの VM (**WEBVM** と **SQLVM**) に階層化されており、VMware ESXi バージョン 6.5 ホスト (**contosohost1.contoso.com**) 上に配置されています。 
- VMware 環境は、VM 上で実行中の vCenter Server 6.5 (**vcenter.contoso.com**) によって管理されています。

![現在の Contoso アーキテクチャ](./media/contoso-migration-rehost-vm-sql-managed-instance/contoso-architecture.png)  

### <a name="proposed-architecture"></a>提案されたアーキテクチャ

このシナリオでは、Contoso は次のように 2 層のオンプレミス旅行アプリを移行したいと考えています。

- Azure SQL Database Managed Instance にアプリのデータベース (**SmartHotelDB**) を移行します。
- フロントエンド **WebVM** を Azure VM に移行します。
- Contoso データセンター内のオンプレミス VM は、移行の終了後に使用停止にされます。

![シナリオのアーキテクチャ](media/contoso-migration-rehost-vm-sql-managed-instance/architecture.png) 

### <a name="database-considerations"></a>データベースの考慮事項

Contoso はソリューション設計プロセスの一環として、Azure SQL Database と SQL Server Managed Instance の機能を比較しました。 Managed Instance にすることを決定するにあたり、次の考慮事項が役に立ちました。

- Managed Instance では、最新のオンプレミス SQL Server バージョンとのほぼ 100% の互換性を提供することが目的とされています。 Microsoft は、SQL Server をオンプレミスまたは IaaS VM で実行していて、最小限の設計変更で完全に管理されたサービスにアプリを移行することを望んでいるお客様に、Managed Instance を推奨しています。
- Contoso は、多数のアプリをオンプレミスから IaaS に移行することを計画しています。 それらの多くは、ISV から提供されたものです。 Contoso は、サポートされていない可能性がある SQL Database を使用するのではなく、Managed Instance を使用すると、これらのアプリのデータベース互換性を確保するのに役立つことを理解しています。
- Contoso は、完全に自動化されたデータ移行サービス (DMS) を使用して、Managed Instance へのリフトアンドシフト移行を簡単に実行できます。 このサービスを導入すると、Contoso は将来のデータベース移行にそれを再利用できます。
- SQL Managed Instance では、SmartHotel360 アプリの重要な問題である SQL Server エージェントがサポートされています。 Contoso ではこの互換性が必要です。互換性がないと、アプリで必要なメンテナンス プランを再設計する必要があります。
- ソフトウェア アシュアランスに基づき、Contoso は、SQL Database Managed Instance で SQL Server 用の Azure ハイブリッド特典を利用して、既存のライセンスを割引料金のライセンスに交換することができます。 これによって、Contoso は Managed Instance を最大 30% 節約することができます。
- Managed Instance は仮想ネットワークに完全に含まれるため、Contoso のデータに対して高レベルの分離性とセキュリティが提供されます。 Contoso は、パブリック インターネットから分離された環境を維持しながら、パブリック クラウドのメリットを得ることができます。
- Managed Instance では、常時暗号化、動的データ マスキング、行レベルのセキュリティ、脅威検出など、多くのセキュリティ機能がサポートされています。


### <a name="solution-review"></a>ソリューションのレビュー

Contoso は、長所と短所の一覧をまとめて、提案されたデザインを評価します。

**考慮事項** | **詳細**
--- | ---
**長所** |  WEBVM は変更することなく Azure に移されるため、移行が簡単で済みます。<br/><br/> SQL Managed Instance では、Contoso の技術面の要件と目標がサポートされています。<br/><br/> Managed Instance では、SQL Server 2008 R2 から移行するときに、現在の展開との 100% の互換性が提供されます。<br/><br/>  ソフトウェア アシュアランスへの投資を利用し、SQL Server と Windows Server に対する Azure ハイブリッド特典を使用できます。<br/><br/> 将来の移行では、Database Migration Service を再利用できます。<br/><br/> SQL Managed Instance には、Contoso が構成する必要のないフォールト トレランスが組み込まれています。 そのため、データ層がフェールオーバーの単一ポイントではなくなります。
**短所** | WEBVM では、Windows Server 2008 R2 が実行されています。  このオペレーティング システムは Azure でサポートされていますが、サポートされるプラットフォームではなくなりました。 [詳細情報](https://support.microsoft.com/en-us/help/956893)。<br/><br/> WEBVM だけがサービスを提供しているので、Web 層はまだ単一のフェールオーバー ポイントです。<br/><br/> Contoso は、Azure App Service といったマネージド サービスにアプリを移行するのではなく、VM としてアプリ Web 層を引き続きサポートする必要があります。<br/><br/> データ層については、Contoso がオペレーティング システムやデータベース サーバーをカスタマイズしたい場合、または SQL Server と共にサードパーティ製アプリを実行したい場合は、Managed Instance は最適なソリューションではない可能性があります。 IaaS VM で SQL Server を実行すると、このような柔軟性が提供されます。 

### <a name="migration-process"></a>移行プロセス

Contoso は、次の手順を実行して、SmartHotel360 アプリの Web 層とデータ層を Azure に移行します。

1. Contoso は既に Azure インフラストラクチャを整備済みであるため、このシナリオで使用する少数の特定 Azure コンポーネントを追加するだけでかまいません。
2. データ層を移行するときは、Data Migration Service を使用します。 Data Migration Service は、Contoso データセンターと Azure との間のサイト間 VPN 接続を経由して、オンプレミス SQL Server VM に接続します。 次に、Data Migration Service はデータベースを移行します。
3. Web 層を移行するときは、Site Recovery によるリフトアンドシフト移行を使用します。 そのプロセスでは、オンプレミス VMware 環境の準備を整え、レプリケーションを設定して有効にしたうえで、VM を Azure にフェールオーバーすることによって移行する必要があります。

     ![移行のアーキテクチャ](media/contoso-migration-rehost-vm-sql-managed-instance/migration-architecture.png) 

### <a name="azure-services"></a>Azure サービス

Service | 説明 | コスト
--- | --- | ---
[Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview) | Database Migration Service を使用すると、複数のデータベース ソースから Azure データ プラットフォームに、ダウンタイムを最小限に抑えながらシームレスに移行できます。 | [サポートされているリージョン](https://docs.microsoft.com/azure/dms/dms-overview#regional-availability)に関する情報と、[Database Migration Service の価格](https://azure.microsoft.com/pricing/details/database-migration/)に関する情報をご覧ください。
[Azure SQL Database Managed Instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) | Managed Instance は、Azure クラウド内の完全に管理された SQL Server インスタンスを表すマネージド データベース サービスです。 最新バージョンの SQL Server データベース エンジンと同じコードを使用し、最新の機能、パフォーマンスの向上、およびセキュリティ更新プログラムが適用されています。 | Azure で実行されている SQL Database Managed Instance を使用すると、容量に基づく料金がかかります。 [Managed Instance の価格の詳細については、こちらを参照してください](https://azure.microsoft.com/pricing/details/sql-database/managed/)。 
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | Site Recovery サービスは、Azure VM、オンプレミス VM、物理サーバーの移行とディザスター リカバリーを調整および管理します。  | Azure へのレプリケーションの間に、Azure Storage の料金が発生します。  フェールオーバーが発生すると、Azure VM が作成されて料金がかかります。 [Site Recovery の料金と価格の詳細については、こちらを参照してください](https://azure.microsoft.com/pricing/details/site-recovery/)。

 

## <a name="prerequisites"></a>前提条件

Contoso と他のユーザーは、次に示すこのシナリオの前提条件を満たす必要があります。

必要条件 | 詳細
--- | ---
**Managed Instance プレビューへの登録** | SQL Database Managed Instance の限定パブリック プレビューに登録する必要があります。 [サインアップ](https://portal.azure.com#create/Microsoft.SQLManagedInstance)するには、Azure サブスクリプションが必要です。 サインアップの完了に数日かかることがあるので、このシナリオのデプロイを始める前にサインアップしてください。
**Azure サブスクリプション** | このシリーズの最初の記事で評価を行ったときに、サブスクリプションは既に作成しているはずです。 Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/pricing/free-trial/)を作成してください。<br/><br/> 無料アカウントを作成する場合、サブスクリプションの管理者としてすべてのアクションを実行できます。<br/><br/> 既存のサブスクリプションを使用していて、サブスクリプションの管理者でない場合は、管理者に依頼して所有者または共同作成者のアクセス許可を割り当ててもらう必要があります。<br/><br/> より詳細なアクセス許可が必要な場合は、「[ロールベースのアクセス制御を使用して Site Recovery のアクセスを管理する](../site-recovery/site-recovery-role-based-linked-access-control.md)」を参照してください。 
**Site Recovery (オンプレミス)** | オンプレミスの vCenter Server インスタンスでは、バージョン 5.5、6.0、または 6.5 を実行している必要があります。<br/><br/> バージョン 5.5、6.0、または 6.5 を実行している ESXi ホスト<br/><br/> ESXi ホスト上で実行している 1 つ以上の VMware VM。<br/><br/> VM は [Azure の要件](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements)を満たす必要があります。<br/><br/> サポートされている[ネットワーク](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network)および[ストレージ](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage)の構成。
**Database Migration Service** | Database Migration Service には、[互換性のあるオンプレミスの VPN デバイス](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices)が必要です。<br/><br/> オンプレミスの VPN デバイスを構成できる必要があります。 外部に接続するパブリック IPv4 アドレスが必要です。 このアドレスを NAT デバイスの内側に割り当てることはできません。<br/><br/> オンプレミスの SQL Server データベースにアクセスできることを確認します。<br/><br/> Windows ファイアウォールは、ソース データベース エンジンにアクセスできる必要があります。 [データベース エンジン アクセス用の Windows Firewall を構成する方法については、こちらを参照してください](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)。<br/><br/> データベース マシンの前面にファイアウォールがある場合は、SMB ポート 445 経由でのデータベースおよびファイルへのアクセスを許可する規則を追加します。<br/><br/> ソース SQL Server インスタンスおよびターゲット Managed Instance への接続に使用する資格情報は、sysadmin サーバー ロールのメンバーである必要があります。<br/><br/> Database Migration Service がソース データベースをバックアップするために使用できるネットワーク共有が、オンプレミスのデータベースに存在する必要があります。<br/><br/> ソース SQL Server インスタンスを実行しているサービス アカウントに、ネットワーク共有に対する書き込みアクセス許可があることを確認します。<br/><br/> ネットワーク共有に対するフル コントロールのアクセス許可を持つ、Windows ユーザーとパスワードをメモしておきます。 Database Migration Service は、これらのユーザーの資格情報を偽装して、Azure Storage コンテナーにバックアップ ファイルをアップロードします。<br/><br/> SQL Server Express のインストール プロセスでは、TCP/IP プロトコルが既定で**無効化**されます。 これが有効になっていることを確認します。

## <a name="scenario-steps"></a>シナリオのステップ

Contoso が予定しているデプロイ設定方法は次のとおりです。

> [!div class="checklist"]
> * **手順 1: SQL Database Managed Instance を設定する**: Contoso はオンプレミス SQL Server データベースの移行先となる Managed Instance を事前に作成しておく必要があります。
> * **手順 2: Database Migration Service を準備する**: データベース移行プロバイダーを登録し、インスタンスを作成した後に、Database Migration Service プロジェクトを作成する必要があります。 また、Database Migration Service 用に Shared Access Signature (SAS) の Uniform Resource Identifier (URI) も設定する必要があります。 SAS URI によって Contoso のストレージ アカウント内のリソースへの委任アクセスが可能になり、Contoso はストレージ オブジェクトへの制限付きアクセス許可を付与できます。 SQL Server バックアップ ファイルのアップロード先であるストレージ アカウント コンテナーに Database Migration Service がアクセスできるように、SAS URI を設定します。
> * **手順 3: Site Recovery のために Azure を準備する**: Contoso は、Site Recovery 用にレプリケートされたデータを保持するストレージ アカウントを作成する必要があります。 Azure Recovery Services コンテナーも作成する必要があります。
> * **手順 4: Site Recovery 用にオンプレミスの VMware を準備する**: Contoso は、フェールオーバー後に Azure VM に接続するために、VM 検出およびエージェント インストール用のアカウントを準備します。
> * **手順 5: VM をレプリケートする**: レプリケーションを設定するために、Contoso は Site Recovery のソース環境とターゲット環境を構成し、レプリケーション ポリシーを設定して、Azure Storage への VM のレプリケーションを開始します。
> * **手順 6: Database Migration Service を使用してデータベースを移行する**: Contoso は、データベースを移行します。
> * **手順 7: Site Recovery を使用して VM を移行する**: Contoso は、テスト フェールオーバーを実行して、すべて問題なく動作していることを確認します。 その後、完全なフェールオーバーを実行して VM を Azure に移行します。

## <a name="step-1-prepare-a-sql-database-managed-instance"></a>手順 1: SQL Database Managed Instance を準備する

Azure SQL Database Managed Instance を設定するため、Contoso には次の要件を満たしているサブネットが必要です。

- サブネットは専用でなければなりません。 また、サブネットは空で他のクラウド サービスを含んでいない必要があります。 サブネットはゲートウェイ サブネットであってはなりません。
- Managed Instance を作成した後は、サブネットにリソースを追加することはできません。
- サブネットにネットワーク セキュリティ グループを関連付けることはできません。
- サブネットにはユーザー定義のルーティング (UDR) ルート テーブルが必要です。 割り当てられている唯一のルートが 0.0.0.0/0 の次ホップ インターネットである必要があります。 
- 省略可能なカスタム DNS: カスタム DNS が Azure 仮想ネットワークで指定されている場合、Azure の再帰的なリゾルバーの IP アドレス (168.63.129.16 など) をリストに追加する必要があります。 [Managed Instance のカスタム DNS の構成方法については、こちらを参照してください](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-custom-dns)。
- サブネットにサービス エンドポイント (ストレージまたは SQL) を関連付けることはできません。 仮想ネットワークではサービス エンドポイントを無効にする必要があります。
- サブネットには 16 個以上の IP アドレスが必要です。 [Managed Instance サブネットのサイズを指定する方法については、こちらを参照してください](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-vnet-configuration)。
- Contoso のハイブリッド環境では、カスタム DNS 設定が必要です。 Contoso は、自社の 1 つ以上の Azure DNS サーバーを使用するように DNS 設定を構成します。 [DNS のカスタマイズの詳細については、こちらを参照してください](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-custom-dns)。

### <a name="set-up-a-virtual-network-for-the-managed-instance"></a>Managed Instance 用の仮想ネットワークを設定する

Contoso の管理者は仮想ネットワークを次のように設定します。 

1. 米国東部 2 プライマリ リージョンに新しい仮想ネットワーク (**VNET-SQLMI-EU2**) を作成します。 その仮想ネットワークを **ContosoNetworkingRG** リソース グループに追加します。
2. 10.235.0.0/24 のアドレス空間を割り当てます。 その範囲がエンタープライズ内の他のどのネットワークとも重複しないことを確認します。
3. ネットワークに以下の 2 つのサブネットを追加します。
    - **SQLMI-DS-EUS2** (10.235.0.0.25)
    - **SQLMI-SAW-EUS2** (10.235.0.128/29) このサブネットは、Managed Instance にディレクトリを接続するために使用されます。

      ![Managed Instance - 仮想ネットワークの作成](media/contoso-migration-rehost-vm-sql-managed-instance/mi-vnet.png)

4. 仮想ネットワークとサブネットがデプロイされた後、ネットワークを以下のようにピアリングします。

    - **VNET-SQLMI-EUS2** と **VNET-HUB-EUS2** (米国東部 2 のハブ仮想ネットワーク) とピアリングします。
    - **VNET-SQLMI-EUS2** と **VNET-PROD-EUS2** (実稼働ネットワーク) をピアリングします。

      ![ネットワーク ピアリング](media/contoso-migration-rehost-vm-sql-managed-instance/mi-peering.png)

5. Contoso は、カスタム DNS 設定を行います。 DNS は、最初は Contoso の Azure ドメイン コント ローラーを指しています。 Azure DNS はセカンダリです。 Contoso Azure ドメイン コントローラーは、以下のように配置されます。

    - 配置先は **PROD-DC-EUS2** サブネットで、これは米国東部 2 の実稼働ネットワーク (**VNET-PROD-EUS2**) 内にあります。
    - **CONTOSODC3** アドレス: 10.245.42.4
    - **CONTOSODC4** アドレス: 10.245.42.5
    - Azure DNS リゾルバー: 168.63.129.16

      ![ネットワークの DNS サーバー](media/contoso-migration-rehost-vm-sql-managed-instance/mi-dns.png)

*さらにサポートが必要な場合*

- [SQL Database Managed Instance の概要については、こちらを参照してください](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance)。
- [SQL Database Managed Instance 用の仮想ネットワークを作成する方法については、こちらを参照してください](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-vnet-configuration)。
- [ピアリングを設定する方法については、こちらを参照してください](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering)。
- [Azure Active Directory DNS 設定を更新する方法については、こちらを参照してください](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started-dns)。

### <a name="set-up-routing"></a>ルーティングを設定する

Managed Instance は、プライベート仮想ネットワーク内に配置されます。 仮想ネットワークが Azure Management Service と通信するためのルート テーブルが必要です。 仮想ネットワークが、仮想ネットワークを管理するサービスと通信できない場合、仮想ネットワークへのアクセスができなくなります。

Contoso は以下の点を考慮します。

- ルート テーブルには、Managed Instance から送信されたパケットを仮想ネットワーク内でルーティングする方法を指定した一連のルール (ルート) が含まれています。
- ルート テーブルは、Managed Instance がデプロイされているサブネットに関連付けられています。 サブネットから離れる各パケットは、関連付けられたルート テーブルに基づいて処理されます。
- 1 つのサブネットは、1 つのルート テーブルにのみ関連付けることができます。
- Microsoft Azure では、ルート テーブルの作成に追加料金はかかりません。

  Contoso の管理者は次のようにしてルーティングを設定します。

1. **ContosoNetworkingRG** リソース グループに UDR (ルート) テーブルを作成します。

    ![ルート テーブル](media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table.png)

2. Managed Instance の要件に準拠するため、ルート テーブル (**MIRouteTable**) を展開した後、アドレス プレフィックスが 0.0.0.0/0 のルートを追加します。 **[次ホップの種類]** オプションは **[インターネット]** に設定します。

    ![ルート テーブルのプレフィックス](media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table-prefix.png)
    
3. (**VNET-SQLMI-EUS2** ネットワーク内の) **SQLMI-DB-EUS2** サブネットにルート テーブルを関連付けます。 

    ![ルート テーブルのサブネット](media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table-subnet.png)
    
*さらにサポートが必要な場合*

[Managed Instance 用のルートを設定する方法については、こちらを参照してください](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-create-tutorial-portal)。

### <a name="create-a-managed-instance"></a>マネージド インスタンスを作成する

以上で Contoso の管理者は、SQL Database Managed Instance をプロビジョニングできるようになりました。

1. Managed Instance はビジネス アプリにサービスを提供するため、米国東部 2 プライマリ リージョンに Managed Instance をデプロイします。 その Managed Instance を **ContosoRG** リソース グループに追加します。
2. 価格レベルを選択し、インスタンスのコンピューティングとストレージのサイズを設定します。 [Managed Instance の価格の詳細については、こちらを参照してください](https://azure.microsoft.com/pricing/details/sql-database/managed/)。

    ![マネージド インスタンス](media/contoso-migration-rehost-vm-sql-managed-instance/mi-create.png)

3. Managed Instance をデプロイすると、**ContosoRG** リソース グループ内に 2 つの新しいリソースが表示されます。

    - 仮想クラスター (複数の Managed Instance が存在する場合)
    - SQL Server Database Managed Instance 

      ![マネージド インスタンス](media/contoso-migration-rehost-vm-sql-managed-instance/mi-resources.png)

*さらにサポートが必要な場合*

[Managed Instance をプロビジョニングする方法については、こちらを参照してください](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-create-tutorial-portal)。

## <a name="step-2-prepare-the-database-migration-service"></a>手順 2: Database Migration Service を準備する

Database Migration Service を準備するために、Contoso の管理者は、いくつかの作業を行う必要があります。

- Azure 内で Database Migration Service プロバイダーを登録します。
- Database Migration Service に Azure Storage へのアクセス権を付与し、データベースの移行に使用するバックアップ ファイルをアップロードできるようにします。 Azure Storage へのアクセス権を付与するために、Azure Blob Storage コンテナーを作成します。 Blob Storage コンテナー用の SAS URI を生成します。 
- Database Migration Service プロジェクトを作成します。

その後、次の手順を行います。

1. サブスクリプションにデータベース移行プロバイダーを登録します。
    ![Database Migration Service - 登録](media/contoso-migration-rehost-vm-sql-managed-instance/dms-subscription.png)

2. Blob Storage コンテナーを作成します。 SAS URI を生成し、Database Migration Service がその URI にアクセスできるようにします。

    ![Database Migration Service - SAS URI の生成](media/contoso-migration-rehost-vm-sql-managed-instance/dms-sas.png)

3. Database Migration Service インスタンスを作成します。 

    ![Database Migration Service - インスタンスの作成](media/contoso-migration-rehost-vm-sql-managed-instance/dms-instance.png)

4. Database Migration Service インスタンスを **VNET-PROD-DC-EUS2** 仮想ネットワークの **PROD-DC-EUS2** サブネットに配置します。
    - Database Migration Service をそこに配置する理由は、このサービスが、VPN ゲートウェイ経由でオンプレミスの SQL Server VM にアクセスできる仮想ネットワーク内にあることが必要なためです。
    - **VNET-PROD-EUS2** は **VNET-HUB-EUS2** とピアリングされ、リモート ゲートウェイの使用を許可されます。 **[リモート ゲートウェイを使用する]** オプションにより、Database Migration Service は必要に応じて通信ができるようになります。

        ![Database Migration Service - ネットワークの構成](media/contoso-migration-rehost-vm-sql-managed-instance/dms-network.png)

*さらにサポートが必要な場合*

- [Database Migration Service を設定する方法については、こちらを参照してください](https://docs.microsoft.com/azure/dms/quickstart-create-data-migration-service-portal)。
- [SAS を作成して使用する方法については、こちらを参照してください](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2)。


## <a name="step-3-prepare-azure-for-the-site-recovery-service"></a>手順 3: Site Recovery サービス用に Azure を準備する

Contoso が Web 層の VM (**WEBMV**) を移行するために Site Recovery を設定するには、複数の Azure 要素が必要です。

- フェールオーバーしたリソースを配置する仮想ネットワーク。
- レプリケートしたデータを保持するためのストレージ アカウント。 
- Azure 内の Recovery Services コンテナー。

Contoso の管理者は、Site Recovery を次のように設定します。

1. VM は SmartHotel360 アプリに対する Web フロントエンドであるため、Contoso は VM を既存の実稼働ネットワーク (**VNET-PROD-EUS2**) とサブネット (**PROD-FE-EUS2**) にフェールオーバーします。 ネットワークとサブネットは米国東部 2 プライマリ リージョンに配置されます。 ネットワークは、[Azure インフラストラクチャをデプロイ](contoso-migration-infrastructure.md)したときに設定しています。
2. ストレージ アカウント (**contosovmsacc20180528**) を作成します。 汎用アカウントを使用します。 標準的なストレージとローカル冗長ストレージのレプリケーションを選択します。

    ![Site Recovery - ストレージ アカウントの作成](media/contoso-migration-rehost-vm-sql-managed-instance/asr-storage.png)

3. ネットワークとストレージ アカウントを作成したので、コンテナー (**ContosoMigrationVault**) を作成します。 そのコンテナーを **ContosoFailoverRG** リソース グループ (米国東部 2 プライマリ リージョン内にある) に配置します。

    ![Recovery Services - コンテナーの作成](media/contoso-migration-rehost-vm-sql-managed-instance/asr-vault.png)

*さらにサポートが必要な場合*

[Site Recovery 用に Azure を設定する方法については、こちらを参照してください](https://docs.microsoft.com/azure/site-recovery/tutorial-prepare-azure)。


## <a name="step-4-prepare-on-premises-vmware-for-site-recovery"></a>手順 4: Site Recovery のためにオンプレミスの VMware を準備する

Site Recovery 用に VMware を準備するために、Contoso の管理者は以下の作業を行う必要があります。

- vCenter Server インスタンスまたは vSphere ESXi ホスト上にアカウントを準備します。 アカウントは VM の検出を自動化します。
- レプリケート対象の VMware VM にモビリティ サービスを自動インストールできるアカウントを準備します。
- フェールオーバー後にオンプレミス VM を作成したときに Azure VM に接続できるように、オンプレミス VM を準備します。


### <a name="prepare-an-account-for-automatic-discovery"></a>自動検出用のアカウントを準備する

Site Recovery では、次のことを実行するために、VMware サーバーへのアクセスが必要です。

- VM を自動的に検出します。 読み取り専用以上のアカウントが必要です。
- レプリケーション、フェールオーバー、およびフェールバックを調整します。 Contoso には、ディスクを作成および削除する、VM をオンにするなどの操作を実行できるアカウントが必要です。

Contoso の管理者は、以下の作業を行ってアカウントを設定します。

1. vCenter レベルでロールを作成します。
2. 必要なアクセス許可をそのロールに割り当てます。

*さらにサポートが必要な場合*

[自動検出用のロールを作成して割り当てる方法については、こちらを参照してください](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-automatic-discovery)。

### <a name="prepare-an-account-for-mobility-service-installation"></a>モビリティ サービスのインストール用のアカウントを準備する

レプリケート対象の VM にはモビリティ サービスをインストールする必要があります。 Contoso は、モビリティ サービスに関して以下の点を考慮します。

- VM のレプリケーションを有効にすると、Site Recovery はこのコンポーネントの自動プッシュ インストールを行うことができます。
- 自動プッシュ インストールを行うためには、Site Recovery から VM へのアクセスに使用するアカウントを準備する必要があります。
- Azure コンソールでレプリケーションを構成するときに、このアカウントを指定します。
- Contoso には、VM にインストールするアクセス許可を持つドメイン アカウントまたはローカル アカウントが必要です。

*さらにサポートが必要な場合*

[モビリティ サービスのプッシュ インストール用にアカウントを作成する方法については、こちらを参照してください](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-mobility-service-installation)。

### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>フェールオーバー後に Azure VM に接続するための準備をする

Contoso は、Azure へのフェールオーバー後に、Azure 内のレプリケートされた VM に接続できることを望んでいます。 Azure 内のレプリケートされた VM に接続するには、移行前にオンプレミスの VM 上でいくつかの作業を行う必要があります。 

1. インターネット経由でアクセスする場合、フェールオーバーの前に、オンプレミスの VM 上の RDP を有効にします。 TCP と UDP の規則が **[パブリック]** プロファイルに追加されていることを確認し、**[Windows ファイアウォール]** > **[許可されたアプリ]** で、すべてのプロファイルで RDP が許可されていることを確認します。
2. サイト間 VPN 経由でアクセスする場合は、オンプレミスのコンピューター上で RDP を有効にします。 **[Windows ファイアウォール]** > **[許可されたアプリおよび機能]** で、**ドメイン ネットワークとプライベート ネットワーク**での RDP を許可します。
3. オンプレミス VM 上のオペレーティング システムの SAN ポリシーを **OnlineAll** に設定します。

Contoso の管理者は、フェールオーバーを実行するときに次の項目も確認する必要があります。

- フェールオーバーをトリガーするときに、VM 上に保留中の Windows 更新プログラムがないようにします。 Windows 更新プログラムが保留中の場合は、更新プログラムが終了するまで、ユーザーは仮想マシンにサインインできません。
- フェールオーバー後、管理者は **[ブート診断]** をオンにして、VM のスクリーンショットを確認する必要があります。 ブート診断を表示できない場合は、VM が実行されていることを確認し、[トラブルシューティングのヒント](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)を確認する必要があります。

## <a name="step-5-replicate-the-on-premises-vms-to-azure"></a>手順 5: オンプレミスの VM を Azure にレプリケートする

Azure への移行を実行する前に、Contoso の管理者は、オンプレミスの VM に対するレプリケーションを設定して有効にする必要があります。

### <a name="set-a-replication-goal"></a>レプリケーションの目標を設定する

1. コンテナーのコンテナー名 (**ContosoVMVault**) の下で、レプリケーションの目標を設定します (**[作業の開始]** > **[Site Recovery]** > **[インフラストラクチャの準備]**)。
2. マシンがオンプレミスにあること、VMware VM であること、および Azure にレプリケートすることを指定します。

    ![インフラストラクチャの準備 - 保護の目標](./media/contoso-migration-rehost-vm-sql-managed-instance/replication-goal.png)

### <a name="confirm-deployment-planning"></a>展開の計画を確認する

続行するため、Contoso の管理者は展開計画が完了したことを確認します。 **[はい、完了しました]** を選択します。 この展開では、Contoso が移行する VM は 1 つのみなので、展開計画は不要です。

### <a name="set-up-the-source-environment"></a>ソース環境をセットアップする

次に、Contoso の管理者はソース環境を構成します。 ソース環境を設定するには、OVF テンプレートをダウンロードし、そのテンプレートを使用して、構成サーバーとそれに関連するコンポーネントを高可用性オンプレミス VMware VM として展開します。 サーバーのコンポーネントは次のとおりです。

- 構成サーバーは、オンプレミスのインフラストラクチャと Azure との間の通信を調整します。 また、データのレプリケーションを管理します。
- プロセス サーバー: レプリケーション ゲートウェイとして機能します。 プロセス サーバーは次の処理を行います。
    - レプリケーション データを受信する。
    - レプリケーション データをキャッシュ、圧縮、暗号化によって最適化する。
    - レプリケーション データを Azure Storage に送信する。
- また、プロセス サーバーでは、レプリケート対象の VM にモビリティ サービスがインストールされます。 プロセス サーバーはオンプレミス VMware VM の自動検出を実行します。
- 構成サーバー VM を作成して起動した後、そのサーバーをコンテナーに登録します。

ソース環境を設定するため、Contoso の管理者は次のことを行います。

1. OVF テンプレートを Azure portal からダウンロードします (**[インフラストラクチャの準備]** > **[ソース]** > **[構成サーバー]**)。
    
    ![構成サーバーの追加](./media/contoso-migration-rehost-vm-sql-managed-instance/add-cs.png)

2. VMware にテンプレートをインポートし、VM を作成してデプロイします。

    ![OVF テンプレートのデプロイ](./media/contoso-migration-rehost-vm-sql-managed-instance/vcenter-wizard.png)

3.  初めて VM をオンにすると、VM は Windows Server 2016 インストール エクスペリエンスで起動されます。 使用許諾契約書に同意し、管理者パスワードを入力します。
4. インストールが完了したら、管理者として VM にサインインします。 初めてサインインしたときは、Azure Site Recovery 構成ツールが自動的に実行されます。
5. Site Recovery 構成ツールで、構成サーバーをコンテナーに登録するために使用する名前を入力します。
6. ツールによって VM から Azure への接続が確認されます。 接続が確立された後、**[サインイン]** を選択して、Azure サブスクリプションにサインインします。 この資格情報によって、構成サーバーを登録するコンテナーにアクセスできる必要があります。 

    ![構成サーバーを登録する](./media/contoso-migration-rehost-vm-sql-managed-instance/config-server-register2.png)

7. ツールがいくつかの構成タスクを実行した後、再起動されます。 再度マシンにサインインします。 構成サーバーの管理ウィザードが自動的に起動されます。
8. ウィザードで、レプリケーション トラフィックを受信する NIC を選択します。 この設定は、構成後に変更することはできません。
9. サブスクリプション、リソース グループ、および構成サーバーを登録する Recovery Services コンテナーを選択します。

    ![Recovery Services コンテナーの選択](./media/contoso-migration-rehost-vm-sql-managed-instance/cswiz1.png)

10. MySQL Server と VMWare PowerCLI をダウンロードしてインストールします。 次に、サーバーの設定を検証します。
11. 検証が終わったら、vCenter Server または vSphere ホストの FQDN または IP アドレスを入力します。 ポートは既定のままにし、Azure 内の vCenter Server インスタンスの表示名を入力します。
12. レプリケーションに使用できる VMware VM を Site Recovery が自動的に検出できるように、以前作成したアカウントを指定します。 
13. レプリケーションが有効なときにモビリティ サービスを自動的にインストールするための資格情報を入力します。 Windows マシンの場合、このアカウントには、VM に対するローカル管理者のアクセス許可が必要です。 

    ![vCenter Server の構成](./media/contoso-migration-rehost-vm-sql-managed-instance/cswiz2.png)

7. 登録が完了したら、Azure portal で、構成サーバーと VMware サーバーがコンテナーの **[ソース]** ページの一覧に表示されていることを確認します。 検出には 15 分以上かかる場合があります。 
8. Site Recovery は指定された設定を使用して VMware サーバーに接続し、VM を検出します。

### <a name="set-up-the-target"></a>ターゲットをセットアップする

次に、Contoso の管理者は、ターゲットのレプリケーション環境を構成します。

1. **[インフラストラクチャの準備]**(インフラストラクチャの準備) > **[ターゲット]** で、ターゲットの設定を選択します。
2. Site Recovery は、指定されたターゲットにストレージ アカウントとネットワークが存在することを確認します。

### <a name="create-a-replication-policy"></a>レプリケーション ポリシーを作成する

ソースとターゲットの設定が完了したら、Contoso の管理者はレプリケーション ポリシーを作成し、構成サーバーにポリシーを関連付けます。

1. **[インフラストラクチャの準備]** > **[レプリケーションの設定]** > **[レプリケーション ポリシー]** >  **[作成と関連付け]** で、**ContosoMigrationPolicy** ポリシーを作成します。
2. 以下の既定の設定を使用します。
   - **RPO しきい値**: 既定値は 60 分です。 この値で、復旧ポイントの作成頻度を指定します。 継続的なレプリケーションがこの制限を超えると、アラートが生成されます。
   - **[復旧ポイントのリテンション期間]**: 既定値は 24 時間です。 この値は、各復旧ポイントのリテンション期間の長さを指定します。 レプリケートされた VM は、期間内の任意の時点に復旧できます。
   - **[アプリ整合性スナップショットの頻度]**: 既定値は 1 時間です。 この値は、アプリケーション整合性スナップショットが作成される頻度を指定します。
 
     ![レプリケーション ポリシー - 作成](./media/contoso-migration-rehost-vm-sql-managed-instance/replication-policy.png)

3. このポリシーは自動的に構成サーバーに関連付けられます。 

    ![レプリケーション ポリシー - 関連付け](./media/contoso-migration-rehost-vm-sql-managed-instance/replication-policy2.png)

*さらにサポートが必要な場合*

- これらのステップの詳細な手順については、「[Azure にオンプレミス VMware VM のディザスター リカバリーを設定する](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial)」を参照してください。
- [ソース環境の設定](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source)、[構成サーバーのデプロイ](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server)、および[レプリケーション設定の構成](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication)に役立つ詳細な手順が記載されています。

### <a name="enable-replication"></a>レプリケーションを有効にする

Contoso の管理者は、WebVM のレプリケートを開始できるようになりました。

1. **[アプリケーションのレプリケート]** > **[ソース]** > **[レプリケート]** で、ソースの設定を選択します。
2. 仮想マシンを有効にすることを指定して、vCenter Server インスタンスを選択し、構成サーバーを設定します。

    ![レプリケーションを有効にする - ソース](./media/contoso-migration-rehost-vm-sql-managed-instance/enable-replication1.png)
 
3. ターゲットの設定を指定します。これには、フェールオーバー後に Azure VM が配置されるリソース グループおよびネットワークを指定することも含まれます。 レプリケートされたデータの保存先となるストレージ アカウントを指定します。

    ![レプリケーションを有効にする - ターゲット](./media/contoso-migration-rehost-vm-sql-managed-instance/enable-replication2.png)

4. レプリケーションに対して **WebVM** を選択します。 レプリケーションを有効にすると、Site Recovery によって各 VM にモビリティ サービスがインストールされます。 

    ![レプリケーションを有効にする - VM の選択](./media/contoso-migration-rehost-vm-sql-managed-instance/enable-replication3.png)

5. 目的のレプリケーション ポリシーが選択されていることを確認し、**WEBVM** のレプリケーションを有効にします。 レプリケーションの進行状況は、**[ジョブ]** で追跡します。 **保護の最終処理**ジョブが実行されると、マシンはフェールオーバーできる状態になります。
6. Azure portal の **[要点]** で、Azure にレプリケートしている VM の状態を確認できます。

    ![インフラストラクチャ ビュー](./media/contoso-migration-rehost-vm-sql-managed-instance/essentials.png)

*さらにサポートが必要な場合*

これらのステップの詳細な手順は、「[レプリケーションを有効にする](https://docs.microsoft.com/azure/site-recovery/vmware-azure-enable-replication)」で確認できます。

## <a name="step-6-migrate-the-database"></a>手順 6: データベースを移行する 

Contoso の管理者は、Database Migration Service プロジェクトを作成し、その後にデータベースを移行する必要があります。

### <a name="create-a-database-migration-service-project"></a>Database Migration Service プロジェクトを作成する

1. Database Migration Service プロジェクトを作成します。 ソース サーバーのタイプとして **[SQL Server]**、ターゲットとして **[Azure SQL Database Managed Instance]** を選択します。

     ![Database Migration Service - 新しい移行プロジェクト](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-project.png)

2. 移行ウィザードが開きます。

### <a name="migrate-the-database"></a>データベースを移行する 

1. 移行ウィザードで、オンプレミス データベースが配置されるソース VM を指定します。 データベースにアクセスするための資格情報を入力します。

    ![Database Migration Service - ソースの詳細](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-wizard-source.png)

2. 移行するデータベース (**SmartHotel.Registration**) を選択します。

    ![Database Migration Service - ソース データベースの選択](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-wizard-sourcedb.png)

3. ターゲットとして、Azure の Managed Instance の名前とアクセス資格情報を入力します。

    ![Database Migration Service - ターゲットの詳細](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-target-details.png)

4. **[新しいアクティビティ]** > **[移行の実行]** で、移行を実行する際の設定を指定します。
    - ソースおよびターゲットの資格情報。
    - 移行する対象のデータベース。
    - オンプレミス VM 上で作成したネットワーク共有。 Database Migration Service は、この共有にソースのバックアップを取得します。 
        - ソースの SQL Server インスタンスを実行するサービス アカウントには、この共有に対する書き込み権限が必要です。
        - 共有の FQDN パスを使用する必要があります。
    - ストレージ アカウント コンテナーへのアクセス権を Database Migration Service に付与する SAS URI。このコンテナーに、サービスによって移行用バックアップ ファイルがアップロードされます。

        ![Database Migration Service - 移行の設定の構成](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-migration-settings.png)

5. 移行の設定を保存し、移行を実行します。
6. **[Overview]\(概要\)** で、移行のステータスを監視します。

    ![Database Migration Service - 監視](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-monitor1.png)

7. 移行が完了したら、Managed Instance 上にターゲット データベースが存在することを確認します。

    ![Database Migration Service - データベース移行の確認](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-monitor2.png)

## <a name="step-7-migrate-the-vm"></a>手順 7: VM の移行

Contoso の管理者は、簡単なテスト フェールオーバーを実行してから VM を移行します。

### <a name="run-a-test-failover"></a>テスト フェールオーバーの実行

WEBVM を移行する前にテスト フェールオーバーを実行すると、すべてが正常に機能しているかどうかを確認できます。 管理者は、次の手順を完了します。

1. テスト フェールオーバーを実行し、選択可能な最新の時点 (**最後に処理された時点**) を復帰させます。
2. **[フェールオーバーを開始する前にマシンをシャットダウンします]** を選択します。 このオプションを選択すると、Site Recovery は、フェールオーバーをトリガーする前にソース VM をシャットダウンしようとします。 仮にシャットダウンが失敗したとしても、フェールオーバーは続行されます。 
3. テスト フェールオーバーでは、以下が実行されます。 
    1. 移行の実行に必要なすべての条件が満たされていることを確認する前提条件チェックが実行されます。
    2. Azure VM を作成できるように、フェールオーバーによってデータが処理されます。 最新の復旧ポイントが選択された場合は、データから復旧ポイントが作成されます。
    3.  前のステップで処理されたデータを使用して、Azure VM が作成されます。
3. フェールオーバーが完了すると、レプリカの Azure VM が Azure portal に表示されます。 すべてが正常に機能していることを確認します。つまり、VM が適切なサイズであること、VM が適切なネットワークに接続されていること、および VM が実行されていることを確認します。 
4. テスト フェールオーバーを確認したら、このフェールオーバーをクリーンアップし、確認された事柄をすべて記録します。 

### <a name="migrate-the-vm"></a>VM の移行

1. テスト フェールオーバーが正常に機能することを確認したら、移行のための復旧計画を作成し、WEBVM を計画に追加します。

     ![復旧計画の作成 - 項目の選択](./media/contoso-migration-rehost-vm-sql-managed-instance/recovery-plan.png)

2. 計画に基づいてフェールオーバーを実行し、最新の復旧ポイントを選択します。 Site Recovery がフェールオーバーをトリガーする前にオンプレミス VM をシャットダウンするように指定します。

    ![フェールオーバー](./media/contoso-migration-rehost-vm-sql-managed-instance/failover1.png)

3. フェールオーバーが完了したら、Azure VM が想定どおりに Azure Portal に表示されることを確認します。

   ![復旧計画の詳細](./media/contoso-migration-rehost-vm-sql-managed-instance/failover2.png)

4. 検査の後、移行を完了して移行プロセスを終了し、VM のレプリケーションを停止して、VM の Site Recovery の課金を停止します。

    ![フェールオーバー - 移行の完了](./media/contoso-migration-rehost-vm-sql-managed-instance/failover3.png)

### <a name="update-the-connection-string"></a>接続文字列を更新する

移行プロセスの最終ステップとして、アプリケーションの接続文字列を更新して、Contoso の Managed Instance で実行されている移行されたデータベースを指すようにします。

1. Azure portal で **[設定]** > **[接続文字列]** を選択して、接続文字列を探します。

    ![Connection strings](./media/contoso-migration-rehost-vm-sql-managed-instance/failover4.png)  

2. SQL Database Managed Instance のユーザー名とパスワードを使用して文字列を更新します。
3. 文字列を構成した後は、アプリケーションの web.config ファイルに含まれる現在の接続文字列を置き換えます。
4. ファイルを更新して保存した後、コマンド プロンプト ウィンドウで `IISRESET /RESTART` を実行して、WEBVM 上で IIS を再起動します。
5. IIS が再起動した後は、SQL Database Managed Instance 上で実行されているデータベースがアプリで使用されます。
6. この時点で、オンプレミスの SQLVM マシンをシャットダウンできます。 移行が完了しました。

*さらにサポートが必要な場合*

- [テスト フェールオーバーを実行する方法については、こちらを参照してください](https://docs.microsoft.com/azure/site-recovery/tutorial-dr-drill-azure)。 
- [復旧計画を作成する方法については、こちらを参照してください](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans)。
- [Azure にフェールオーバーする方法については、こちらを参照してください](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover)。

## <a name="clean-up-after-migration"></a>移行後にクリーンアップする

移行が完了しました。SmartHotel360 アプリは Azure VM 上で実行されており、SmartHotel360 データベースは Azure SQL Database Managed Instance 上で使用可能です。  

ここで、以下のクリーンアップ タスクを行う必要があります。  

- WEBVM マシンを vCenter Server インベントリから削除します。
- SQLVM マシンを vCenter Server インベントリから削除します。
- WEBVM と SQLVM をローカル バックアップ ジョブから削除します。
- WEBVM の新しい場所と IP アドレスを示すように内部ドキュメントを更新します。
- SQLVM を内部ドキュメントから削除します。 代わりに、SQLVM が削除され、VM インベントリに残っていないことを示すようにドキュメントを変更できます。
- 使用停止になった VM とやりとりするリソースがあれば確認します。 すべての関連する設定またはドキュメントを更新して新しい構成を反映します。

## <a name="review-the-deployment"></a>デプロイを再調査する

リソースを Azure に移行したら、新しいインフラストラクチャを完全に操作可能にして、セキュリティで保護する必要があります。

### <a name="security"></a>セキュリティ

Contoso セキュリティ チームは、Azure VM と SQL Database Managed Instance を調査し、その実装に関して、セキュリティ上の問題がないかを確認します。

- チームは、VM のアクセスを制御するために使用されるネットワーク セキュリティ グループを確認します。 ネットワーク セキュリティ グループにより、アプリに対して許可されたトラフィックのみが通過できるようにすることができます。
- Contoso のセキュリティ チームは、ディスク上のデータ保護のために、Azure Disk Encryption と Azure Key Vault の使用も検討します。
- チームは、Managed Instance 上での脅威の検出を有効にします。 脅威が検出された場合は、セキュリティ チームとサービス デスク システムにアラートを送信して、チケットを作成します。 [Managed Instance を対象にした脅威の検出の詳細については、こちらを参照してください](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-threat-detection)。

     ![Managed Instance のセキュリティ - 脅威の検出](./media/contoso-migration-rehost-vm-sql-managed-instance/mi-security.png)  

VM のセキュリティに関する作業の詳細については、「[Azure における IaaS ワークロードのセキュリティに関するベスト プラクティス](https://docs.microsoft.com/azure/security/azure-security-best-practices-vms)」を参照してください。

### <a name="bcdr"></a>BCDR

事業継続とディザスター リカバリー (BCDR) のために、Contoso は次のアクションを実施します。

- データの安全性を確保する: Contoso は、Azure Backup サービスを使用して VM 上のデータをバックアップします。 [詳細情報](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。
- アプリの稼働状態を維持する: Contoso は、Site Recovery を使用して、Azure 内のアプリの VM をセカンダリ リージョンにレプリケートします。 [詳細情報](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-quickstart)。
- Contoso は、[データベースのバックアップ](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups)など、SQL Managed Instance の管理についてさらに学習します。


### <a name="licensing-and-cost-optimization"></a>ライセンスとコストの最適化

- Contoso は、WEBVM の既存のライセンスを所有しています。 Azure ハイブリッド特典の価格を利用するために、Contoso は既存の Azure VM を変換します。
- Microsoft の子会社である Cloudyn からライセンス供与される Azure Cost Management を有効にします。 Cost Management は、Azure や他のクラウド リソースを使用および管理する際に役立つマルチクラウド コスト管理ソリューションです。 [Azure Cost Management の詳細については、こちらを参照してください](https://docs.microsoft.com/azure/cost-management/overview)。 


## <a name="conclusion"></a>まとめ

この記事では、Contoso が Site Recovery サービスを使用して、アプリ フロントエンド VM を Azure に移行し、Azure で SmartHotel360 アプリを再ホストしました。 オンプレミス データベースの Azure SQL Database Managed Instance への移行には、Azure Database Migration Service を使用しました。

## <a name="next-steps"></a>次の手順

このシリーズの次の記事では、Contoso が Azure Site Recovery サービスを使用して、[SmartHotel360 アプリを Azure VM に再ホスト](contoso-migration-rehost-vm.md)します。

