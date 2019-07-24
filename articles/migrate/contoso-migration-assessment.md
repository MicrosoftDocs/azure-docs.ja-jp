---
title: Contoso での Azure への移行についてオンプレミスのワークロードを評価する | Microsoft Docs
description: Contoso が Azure への移行に向けて Azure Migrate と Data Migration Assistant を使用してオンプレミスのマシンを評価する方法を説明します。
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: raynew
ms.openlocfilehash: 4739308d301291bf88e8ae547ba85f9648339c4e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58118461"
---
# <a name="contoso-migration-assess-on-premises-workloads-for-migration-to-azure"></a>Contoso の移行: Azure への移行の対象となるオンプレミスのワークロードを評価する

この記事では、Contoso が、Azure への移行対象として、オンプレミスの SmartHotel360 アプリを評価します。

この記事は、架空の会社 Contoso がオンプレミスのリソースを Microsoft Azure クラウドに移行する方法について説明するシリーズの一部です。 このシリーズでは、背景情報や詳細なデプロイ シナリオを示しながら、移行インフラストラクチャをセットアップする方法、移行のためのオンプレミス リソースの適合性を評価する方法、さまざまな種類の移行を実行する方法を説明します。 シナリオは複雑になってきています。 今後もこのシリーズの記事を追加していく予定です。

記事 | 詳細 | Status
--- | --- | ---
[記事 1:概要](contoso-migration-overview.md) | 記事シリーズ、Contoso の移行戦略およびシリーズで使用されているサンプル アプリの概要です。 | 使用可能
[記事 2: Azure インフラストラクチャをデプロイする](contoso-migration-infrastructure.md) | Contoso がオンプレミス インフラストラクチャと Azure インフラストラクチャを移行に向けて準備します。 このシリーズのすべての記事で同じインフラストラクチャを使用します。 | 使用可能
記事 3:Azure への移行の対象となるオンプレミスのリソースの評価 | Contoso が、VMware で実行されているオンプレミスの SmartHotel360 アプリを評価します。 Contoso では、アプリの VM は Azure Migrate サービスを使用して評価し、アプリの SQL Server データベースは Data Migration Assistant を使用して評価します。 | この記事の内容は次のとおりです。
[記事 4: Azure VM および SQL Database Managed Instance でのアプリのリホスト](contoso-migration-rehost-vm-sql-managed-instance.md) | Contoso が、オンプレミスの SmartHotel360 アプリの Azure へのリフトアンドシフト移行を実行します。 アプリのフロントエンドは、Azure Site Recovery サービスを使用して移行します。 Azure Database Migration Service を使用してアプリ データベースを Azure SQL Database Managed Instance に移行します。 | 使用可能
[記事 5:Azure VM でのアプリのリホスト](contoso-migration-rehost-vm.md) | Contoso が Site Recovery サービスを使用して SmartHotel360 アプリの VM を Azure VM に移行します。 | 使用可能
[記事 6:Azure VM および SQL Server AlwaysOn 可用性グループでのアプリのリホスト](contoso-migration-rehost-vm-sql-ag.md) | Contoso が SmartHotel360 アプリを移行します。Site Recovery を使用してアプリの VM を移行し、Database Migration Service を使用して、アプリ データベースを AlwaysOn 可用性グループによって保護されている SQL Server クラスターに移行します。 | 使用可能
[記事 7:Linux アプリの Azure VM への再ホスト](contoso-migration-rehost-linux-vm.md) | Contoso が Site Recovery サービスを使用して、Azure VM への Linux osTicket アプリのリフトアンドシフト移行を完了します。 | 使用可能
[記事 8:Azure VM および Azure Database for MySQL での Linux アプリのリホスト](contoso-migration-rehost-linux-vm-mysql.md) | Contoso が Site Recovery を使用して Linux osTicket アプリを Azure VM に移行します。 アプリ データベースを Azure Database for MySQL に移行するために MySQL Workbench を使用します。 | 使用可能
[記事 9:Azure Web App と Azure SQL Database でのアプリのリファクター](contoso-migration-refactor-web-app-sql.md) | Contoso が SmartHotel360 アプリを Azure Web アプリに移行します。また、Database Migration Assistant を使用して、アプリ データベースを Azure SQL Server インスタンスに移行します。 | 使用可能
[記事 10:Azure Web アプリと Azure Database for MySQL 内で Linux アプリをリファクタリングする](contoso-migration-refactor-linux-app-service-mysql.md) | Contoso が Azure Traffic Manager を使用し、その Linux osTicket アプリを、複数の Azure リージョンの Azure Web アプリに移行します。この Azure Web アプリは、継続的デリバリーを目的として GitHub と統合されます。 Contoso は、アプリ データベースを Azure Database for MySQL インスタンスに移行します。 | 使用可能
[記事 11:Azure DevOps サービスでの Team Foundation Server をリファクタリングします](contoso-migration-tfs-vsts.md) | Contoso がそのオンプレミスの Team Foundation Server の展開を Azure の Azure DevOps Services に移行します。 | 使用可能
[記事 12:Azure コンテナーと Azure SQL Database でのアプリの再構築](contoso-migration-rearchitect-container-sql.md) | Contoso が SmartHotel アプリを Azure に移行します。 その後、アプリの Web 階層を Azure Service Fabric 内で動作する Windows コンテナーとして再構築し、さらに、Azure SQL Database を使用してデータベースを再構築します。 | 使用可能
[記事 13:Azure でのアプリのリビルド](contoso-migration-rebuild.md) | Contoso が Azure のさまざまな機能とサービス (Azure App Service、Azure Kubernetes Service (AKS)、Azure Functions、Azure Cognitive Services、Azure Cosmos DB など) を使用して SmartHotel アプリをリビルドします。 | 使用可能
[記事 14:Azure への移行のスケーリング](contoso-migration-scale.md) | 移行の組み合わせを試した後、Contoso は Azure への完全移行に向けてスケーリングを準備します。 | 使用可能


## <a name="overview"></a>概要

Azure への移行を検討する際、Contoso 社は技術的および財務的な評価を実行して、オンプレミスのワークロードがクラウドへの移行に適しているかどうかを確認する必要があります。 Contoso 社のチームは特に、移行対象のマシンおよびデータベースの互換性を評価する必要があります。 Contoso のリソースを Azure 内で実行するための容量とコストを見積もる必要があります。

作業を開始し、関連するテクノロジの理解を深めるために、Contoso は次の表にまとめたオンプレミス アプリのうち 2 つを評価します。 同社は、移行対象のアプリをリホストしてリファクターする移行シナリオを評価します。 リホストとリファクタリングの詳細については、[Contoso での移行の概要](contoso-migration-overview.md)に関する記事を参照してください。

アプリの名前 | プラットフォーム | アプリ層 | 詳細
--- | --- | --- | ---
SmartHotel360<br/><br/> (Contoso の旅行要件を管理します) | SQL Server データベースが搭載された Windows 上で実行 | 2 層アプリ。 1 つの VM (**WEBVM**) 上でフロントエンド ASP.NET Web サイトが実行され、別の VM (**SQLVM**) 上で SQL Server が実行されます。 | VM は VMware であり、vCenter サーバーによって管理される ESXi ホスト上で実行されます。<br/><br/> サンプル アプリは [GitHub](https://github.com/Microsoft/SmartHotel360) からダウンロードできます。
osTicket<br/><br/> (Contoso のサービス デスク アプリ) | MySQL PHP (LAMP) を含む Linux/Apache 上で実行 | 2 層アプリ。 1 つの VM (**OSTICKETWEB**) 上でフロントエンド PHP Web サイトが実行され、別の VM (**OSTICKETMYSQL**) 上で MySQL データベースが実行されます。 | このアプリは、社内の従業員と外部の顧客の問題を追跡するために、顧客サービス アプリによって使用されます。<br/><br/> サンプルは [GitHub](https://github.com/osTicket/osTicket) からダウンロードできます。

## <a name="current-architecture"></a>現在のアーキテクチャ

この図は、現在の Contoso オンプレミス アーキテクチャを示しています。

![現在の Contoso アーキテクチャ](./media/contoso-migration-assessment/contoso-architecture.png)  

- Contoso にはメイン データセンターが 1 つあります。 そのデータセンターは、米国東部のニューヨーク市に位置しています。
- Contoso は、その他にも米国内全体で 3 つの地方支店があります。
- メイン データセンターは、光ファイバーによるメトロ イーサネット接続 (500 MBps) を通じて、インターネットに接続されています。
- 各支店は、ビジネス クラスの接続を使用して、インターネットにローカルで接続されています。メイン データセンターには、IPsec VPN トンネルで接続されています。 このセットアップにより、Contoso のネットワーク全体を永続的に接続でき、インターネット接続が最適化されます。
- メイン データセンターは、VMware によって完全に仮想化されています。 Contoso には、vCenter Server 6.5 で管理されている ESXi 6.5 仮想化ホストが 2 つあります。
- Contoso は ID 管理に Active Directory を使用しています。 Contoso は内部ネットワーク上で DNS サーバーを使用しています。
- データセンター内のドメイン コントローラーは、VMware VM 上で実行されています。 支店にあるドメイン コントローラーは、物理サーバー上で実行されています。

## <a name="business-drivers"></a>ビジネス ドライバー

Contoso の IT リーダーシップ チームは、自社のビジネス パートナーと密接に連絡を取り合い、ビジネス部門がこの移行で何を達成しようとしているのかを理解しました。

- **ビジネスの成長への対応**: Contoso は成長しています。 その結果、会社のオンプレミスのシステムとインフラストラクチャにかかる圧力が増加しています。
- **効率化**: Contoso は不要な手順を取り除き、開発者とユーザーのプロセスを効率化する必要があります。 顧客の要求にすばやく対応できるように、ビジネス部門は IT 部門に対して、時間やコストを無駄にせず、迅速に作業を行うことを求めています。
- **迅速性の向上**: Contoso IT は、ビジネス部門の要求に対して、対応力を向上させる必要があります。 会社がグローバル経済で成功を収めるために、市場で起きる変化に遅れることなく迅速に対応する必要があります。 Contoso の IT 部門がビジネスの妨げになったり、ビジネスの妨げになったりするようなことがあってはなりません。
- **スケール**:会社のビジネスが順調に成長している中で、Contoso IT 部門は、同じペースで拡張できるシステムを提供する必要があります。

## <a name="assessment-goals"></a>評価の目標

Contoso クラウド チームは、移行評価の目標を決定しました。

- 移行しても、Azure のアプリは、Contoso のオンプレミス VMWare 環境で現在提供されているのと同じパフォーマンスを発揮できる必要があります。 クラウドに移行したからといって、アプリのパフォーマンスの重要性が低下するわけではありません。
- Contoso は自社のアプリケーションおよびデータベースと Azure の要件との互換性を理解する必要があります。 また、Azure でのホスティング オプションを理解する必要もあります。
- アプリをクラウドに移動した後、Contoso のデータベース管理を最小化する必要があります。  
- Contoso は移行オプションだけでなく、インフラストラクチャをクラウドに移動した後のインフラストラクチャに関連するコストも理解する必要があります。

## <a name="assessment-tools"></a>評価用ツール

Contoso は移行の評価に Microsoft のツールを使用します。 ツールは Contoso の目標に合ったものであり、同社が必要とするすべての情報を提供します。

テクノロジ | 説明 | コスト
--- | --- | ---
[Data Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview?view=ssdt-18vs2017) | Contoso は Data Migration Assistant を使用して、Azure でのデータベースの機能に影響を与える可能性のある互換性の問題を評価し、検出します。 Data Migration Assistant は、SQL のソースとターゲット間の機能パリティを評価します。 そのうえで、パフォーマンスと信頼性の向上箇所を推奨します。 | Data Migration Assistant は無料でダウンロードできるツールです。
[Azure Migrate](https://docs.microsoft.com/azure/migrate/migrate-overview) | Contoso は、Azure Migrate サービスを使用して VMware VM を評価します。 Azure Migrate は、マシンの移行適合性を評価します。 そのうえで、Azure で実行するための、サイズとコストの見積もりを提供します。  | 2018 年 5 月の時点で Azure Migrate は無料サービスです。
[サービス マップ](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map) | Azure Migrate では、会社が移行しようとしているマシン間の依存関係が Service Map を使用して示されます。 | Service Map は、Azure Monitor ログの一部です。 現時点では、Contoso は Service Map を 180 日間無料で使用できます。

このシナリオでは、Contoso は Data Migration Assistant をダウンロードして実行し、旅行アプリ用のオンプレミス SQL Server データベースを評価します。 Contoso は Azure への移行前に、Azure Migrate と依存関係マッピングを使用してアプリ VM を評価します。

## <a name="assessment-architecture"></a>評価のアーキテクチャ

![移行評価のアーキテクチャ](./media/contoso-migration-assessment/migration-assessment-architecture.png)

- Contoso は一般的なエンタープライズ組織を表す架空の名前です。
- Contoso にはオンプレミスのデータセンター (**contoso-datacenter**) と、オンプレミスのドメイン コントローラー (**CONTOSODC1**、**CONTOSODC2**) があります。
- VMware VM は、バージョン 6.5 を実行している VMware ESXi ホスト (**contosohost1**、**contosohost2**) 上にあります。
- VMware 環境は、vCenter Server 6.5 (VM 上で実行されている **vcenter.contoso.com**) によって管理されています。
- SmartHotel360 旅行アプリには次の特徴があります。
    - アプリは 2 つの VMware VM (**WEBVM** と **SQLVM**) に階層化されています。
    - VM は、VMware ESXi ホスト **contosohost1.contoso.com** 上にあります。
    - VM は Windows Server 2008 R2 Datacenter SP1 を実行しています。
- VMware 環境は、VM で実行中の vCenter Server (**vcenter.contoso.com**) によって管理されます。
- osTicket サービス デスク アプリ:
    - アプリは 2 つの VM (**OSTICKETWEB** と **OSTICKETMYSQL**) に階層化されています。
    - VM では Ubuntu Linux Server 16.04-LTS を実行しています。
    - **OSTICKETWEB** では Apache 2 と PHP 7.0 が実行されています。
    - **OSTICKETMYSQL** では MySQL 5.7.22 が実行されています。

## <a name="prerequisites"></a>前提条件

Contoso と他のユーザーは、次に示す評価の前提条件を満たす必要があります。

- Azure サブスクリプションまたは Azure サブスクリプションに含まれるリソース グループに対する、所有者または共同作成者のアクセス許可。
- バージョン 6.5、6.0、または 5.5 を実行しているオンプレミスの vCenter Server インスタンス。
- vCenter Server の読み取り専用アカウント、またはそれを作成するためのアクセス許可。
- .ova テンプレートを使用して vCenter Server インスタンス上に VM を作成するためのアクセス許可。
- バージョン 5.5 以降を実行している少なくとも 1 つの ESXi ホスト。
- 少なくとも 2 つのオンプレミス VMware VM (1 つは SQL Server データベースを実行)。
- Azure Migrate エージェントを各 VM にインストールするためのアクセス許可。
- VM には、インターネットへの直接接続が必要です。  
    - [必要な URL](https://docs.microsoft.com/azure/migrate/concepts-collector) へのインターネット アクセスを制限できます。  
    - VM がインターネットに接続できない場合、VM に Azure [Log Analytics Gateway](../azure-monitor/platform/gateway.md) をインストールし、エージェント トラフィックにそのゲートウェイを通過させる必要があります。
- SQL Server インスタンスを実行している VM の FQDN (データベース評価用)。
- SQL Server VM 上で実行されている Windows ファイアウォールでは、TCP ポート 1433 (既定値) で外部接続が許可されている必要があります。 このセットアップにより、Data Migration Assistant の接続が可能になります。

## <a name="assessment-overview"></a>評価の概要

Contoso が評価を行う方法は次のとおりです。

> [!div class="checklist"]
> * **手順 1:Data Migration Assistant をダウンロードしてインストールする**:Contoso はオンプレミスの SQL Server データベースを評価するために Data Migration Assistant を準備します。
> * **手順 2:Data Migration Assistant を使用してデータベースを評価する**:Contoso はデータベース評価を実行して分析します。
> * **手順 3:Azure Migrate を使用した VM 評価の準備をする**:Contoso はオンプレミスのアカウントをセットアップし、VMware 設定を微調整します。
> * **手順 4:Azure Migrate を使用してオンプレミスの VM を検出する**:Contoso は Azure Migrate コレクター VM を作成します。 次に、コレクターを実行して、評価する対象の VM を検出します。
> * **手順 5:Azure Migrate による依存関係の分析の準備をする**:VM 間の依存関係マッピングを確認できるように、Contoso は Azure Migrate エージェントを VM にインストールします。
> * **手順 6:Azure Migrate を使用して VM を評価する**:Contoso は依存関係を確認して VM をグループ化し、評価を実行します。 評価ができたら、Contoso は移行に向けて評価を分析します。

## <a name="step-1-download-and-install-data-migration-assistant"></a>手順 1:Data Migration Assistant をダウンロードしてインストールする

1. Contoso は [Microsoft ダウンロード センター](https://www.microsoft.com/download/details.aspx?id=53595)から Data Migration Assistant をダウンロードします。
    - Data Migration Assistant は、SQL Server インスタンスに接続可能な任意のマシンにインストールできます。 Contoso では、これを SQL Server マシンで実行する必要はありません。
    - Data Migration Assistant は SQL Server ホスト マシンで実行しないようにする必要があります。
2. Contoso はダウンロードしたセットアップ ファイル (DownloadMigrationAssistant.msi) を実行して、インストールを開始します。
3. **[完了]** ページで、ウィザードを終了する前に **[Microsoft Data Migration Assistant の起動]** を選択します。

## <a name="step-2-run-and-analyze-the-database-assessment-for-smarthotel360"></a>手順 2:SmartHotel360 のデータベース評価を実行して分析する

次に、Contoso は評価を実行して、SmartHotel360 アプリ用のオンプレミス SQL Server データベースを分析できます。

1. Data Migration Assistant で、Contoso は **[新規]** > **[評価]** を選択し、評価にプロジェクト名を付けます。
2. **[ソース サーバーの種類]** で **[Azure Virtual Machines 上の SQL Server]** を選択します。

    ![Data Migration Assistant - ソースの選択](./media/contoso-migration-assessment/dma-assessment-1.png)

   > [!NOTE]
   >    現時点では、Data Migration Assistant は Azure SQL Database Managed Instance への移行の評価をサポートしていません。 この問題を回避するために、Contoso では Azure VM 上の SQL Server を想定されるターゲットとして評価に使用します。

3. **[ターゲット バージョンを選択する]** で、SQL Server 2017 をターゲット バージョンとして選択します。 これは SQL Database Managed Instance によって使用されるバージョンであるため、Contoso はこのバージョンを選択する必要があります。
4. 互換性と新機能に関する情報の検出に役立つレポートを選択します。
   - **[互換性の問題]** では、移行を中断させる可能性がある変更や、移行前に微調整が必要な変更がわかります。 このレポートにより、Contoso は現在使用している機能の中で非推奨になった機能を常に把握することができます。 問題は、互換性レベルごとに整理されます。
   - **[新機能のお勧め]** では、移行後にデータベースで使用できる、ターゲット SQL Server プラットフォームの新機能についてわかります。 新機能のお勧めは、**[パフォーマンス]**、**[セキュリティ]**、**[ストレージ]** の見出し別に整理されます。

     ![Data Migration Assistant - 互換性の問題と新機能](./media/contoso-migration-assessment/dma-assessment-2.png)

2. **[サーバーへの接続]** で、データベースを実行している VM の名前と、それにアクセスするための資格情報を入力します。 VM が SQL Server に確実に接続できるように、**[サーバー証明書を信頼する]** をオンにします。 次に、**[接続]** を選択します。

    ![Data Migration Assistant - サーバーへの接続](./media/contoso-migration-assessment/dma-assessment-3.png)

3. **[ソースの追加]** で、Contoso は評価するデータベースを選択し、**[次へ]** を選択して評価を開始します。
4. 評価が作成されます。

    ![Data Migration Assistant - 評価の作成](./media/contoso-migration-assessment/dma-assessment-4.png)

5. **[結果の確認]** で、評価の結果を確認します。

### <a name="analyze-the-database-assessment"></a>データベース評価を分析する

結果は、利用可能になるとすぐに表示されます。 問題を修正した場合は、**[評価の再開]** を選択して評価を再実行する必要があります。

1. **[互換性の問題]** レポートで、互換性のレベルごとに問題の有無をチェックします。 互換性レベルでは SQL Server バージョンが次のようにマップされます。

   - 100:SQL Server 2008/Azure SQL Database
   - 110:SQL Server 2012/Azure SQL Database
   - 120:SQL Server 2014/Azure SQL Database
   - 130:SQL Server 2016/Azure SQL Database
   - 140:SQL Server 2017/Azure SQL Database

     ![Data Migration Assistant - 互換性の問題レポート](./media/contoso-migration-assessment/dma-assessment-5.png)

2. **[機能に関する推奨事項]** レポートで、Contoso は評価を通して推奨されるパフォーマンス、セキュリティ、ストレージの機能を移行後に確認します。 インメモリ OLTP と列ストア インデックス、Stretch Database、Always Encrypted、動的データ マスク、Transparent Data Encryption など、さまざまな機能が推奨されます。

    ![Data Migration Assistant - 機能に関する推奨事項レポート](./media/contoso-migration-assessment/dma-assessment-6.png)

    > [!NOTE]
    > Contoso がすべての SQL Server データベースで [Transparent Data Encryption を有効にする](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017)ことが推奨されます。 これは、データベースがオンプレミスでホストされている場合よりも、クラウドにある場合に、よりいっそう重要です。 Transparent Data Encryption は、移行後にのみ有効にする必要があります。 Transparent Data Encryption が既に有効になっている場合は、証明書または非対称キーを対象サーバーのマスター データベースに移動する必要があります。 [Transparent Data Encryption によって保護されたデータベースを別の SQL Server に移動する方法については、こちらを参照してください](https://docs.microsoft.com/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server?view=sql-server-2017)。

2. Contoso は評価を JSON または CSV 形式でエクスポートできます。

> [!NOTE]
> 大規模な評価を行う場合:
> - 複数の評価を同時に実行し、**[すべての評価]** ページで評価の状態を表示します。
> - 評価を [SQL Server データベース](https://docs.microsoft.com/sql/dma/dma-consolidatereports?view=ssdt-18vs2017)に統合します。
> - 評価を [Power BI レポート](https://docs.microsoft.com/sql/dma/dma-powerbiassesreport?view=ssdt-18vs2017)に統合します。

## <a name="step-3-prepare-for-vm-assessment-by-using-azure-migrate"></a>手順 3:Azure Migrate を使用した VM 評価の準備をする

Contoso は、Azure Migrate で評価対象の VM を自動的に検出するために使用できる VMware アカウントを作成する必要があります。次に、VM を作成するための権限を確認します。さらに、開く必要があるポートを書き留め、統計情報設定レベルを設定します。

### <a name="set-up-a-vmware-account"></a>VMware アカウントを設定する

VM の検出には、次のプロパティを持つ、vCenter Server の読み取り専用アカウントが必要です。

- **ユーザーの種類**:少なくとも読み取り専用ユーザーが必要です。
- **アクセス許可**:データセンター オブジェクトの場合は、**子オブジェクトに伝達** チェック ボックスをオンにします。 **[ロール]** では、**[読み取り専用]** を選択します。
- **[詳細]**:ユーザーはデータセンター レベルで割り当てられ、データセンター内のすべてのオブジェクトにアクセスできます。
- アクセスを制限するには、**子オブジェクトへの伝達**特権を持つ**アクセスなし**ロールを子オブジェクト (vSphere ホスト、データストア、VM、ネットワーク) に割り当てます。

### <a name="verify-permissions-to-create-a-vm"></a>VM を作成するためのアクセス許可を確認する

Contoso は、.ova 形式でファイルをインポートして、VM を作成するためのアクセス許可があることを確認します。 [特権を持つロールを作成して割り当てる方法については、こちらを参照してください](https://kb.vmware.com/s/article/1023189?other.KM_Utility.getArticleLanguage=1&r=2&other.KM_Utility.getArticleData=1&other.KM_Utility.getArticle=1&ui-comm-runtime-components-aura-components-siteforce-qb.Quarterback.validateRoute=1&other.KM_Utility.getGUser=1)。

### <a name="verify-ports"></a>ポートを確認する

Contoso の評価では、依存関係のマッピングを使用します。 依存関係マッピングを使用するには、評価する対象の VM にエージェントをインストールする必要があります。 エージェントは、各 VM の TCP ポート 443 から Azure に接続できる必要があります。 [接続の要件については、こちらを参照してください](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid)。

### <a name="set-statistics-settings"></a>統計情報設定を行う

Contoso は、デプロイを開始する前に vCenter Server の統計情報設定をレベル 3 に設定する必要があります。

> [!NOTE]
> - レベルを設定した後、少なくとも 1 日待ってから評価を実行する必要があります。 そうしないと、評価が想定どおりに機能しない可能性があります。
> - 3 より高いレベルにした場合、評価は機能します。ただし、以下について留意してください。
>    - ディスクとネットワークのパフォーマンス データが収集されません。
>    - ストレージに関して、オンプレミス ディスクとサイズが同じ Azure の Standard ディスクが Azure Migrate によって推奨されます。
>    - ネットワークに関して、各オンプレミス ネットワーク アダプターに Azure のネットワーク アダプターが推奨されます。
>    - 計算に関して、Azure Migrate によって VM コアとメモリ サイズが調査され、同じ構成の Azure VM が推奨されます。 条件を満たす Azure VM サイズが複数ある場合、最も低コストのディスクをお勧めします。
> - レベル 3 を使用したサイズ設定の詳細については、「[サイズ変更](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation#sizing)」を参照してください。

レベルを設定するには:

1. vSphere Web Client で、Contoso は vCenter Server インスタンスを開きます。
2. **[管理]** > **[設定]** > **[全般]** > **[編集]** を選択します。
3. **[統計]** で、統計情報レベル設定を **[レベル 3]** に設定します。

    ![vCenter Server の統計情報レベル](./media/contoso-migration-assessment/vcenter-statistics-level.png)

## <a name="step-4-discover-vms"></a>手順 4:Discover VMs

Contoso が VM を探索するには、Azure Migrate プロジェクトを作成します。 Contoso はコレクター VM をダウンロードし、セットアップします。 次に、コレクターを実行してオンプレミスの VM を検出します。

### <a name="create-a-project"></a>プロジェクトの作成

1. [Azure Portal](https://portal.azure.com) で、**Azure Migrate** を検索します。 次に、プロジェクトを作成します。
2. プロジェクト名 (**ContosoMigration**) と Azure サブスクリプションを指定します。 新しい Azure リソース グループ (**ContosoFailoverRG**) を作成します。
    > [!NOTE]
    > - Azure Migrate プロジェクトを作成できるのは、米国中西部または米国東部リージョンに限られます。
    > - 移行は、任意のターゲットの場所で計画できます。
    > - プロジェクトの場所は、オンプレミスの VM から収集されたメタデータを格納するためにのみ使用されます。

    ![Azure Migrate - 移行プロジェクトの作成](./media/contoso-migration-assessment/project-1.png)

### <a name="download-the-collector-appliance"></a>コレクター アプライアンスをダウンロードする

Azure Migrate は、*コレクター アプライアンス*と呼ばれるオンプレミスの VM を作成します。 この VM はオンプレミスの VMware VM を検出し、検出した VM についてのメタデータを Azure Migrate サービスに送信します。 Contoso がコレクター アプライアンスをセットアップするには、OVA テンプレートをダウンロードしてオンプレミスの vCenter Server インスタンスにインポートし、VM を作成します。

1. Azure Migrate プロジェクトで、**[開始]** > **[検出と評価]** > **[マシンの検出]** を選択します。 OVA テンプレート ファイルをダウンロードします。
2. プロジェクト ID とキーをコピーします。 プロジェクトと ID は、コレクターを構成するために必要です。

    ![Azure Migrate - OVA ファイルのダウンロード](./media/contoso-migration-assessment/download-ova.png)

### <a name="verify-the-collector-appliance"></a>コレクター アプライアンスを確認する

Contoso は、VM をデプロイする前に OVA ファイルが安全であることを確認します。

1. ファイルをダウンロードしたマシンで、管理者用のコマンド ウィンドウを開きます。
2. 次のコマンドを実行して、OVA ファイルのハッシュを生成します。

    ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```

    **例**

    ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. 生成されたハッシュは、[こちら](https://docs.microsoft.com/azure/migrate/tutorial-assessment-vmware#continuous-discovery)に示されているハッシュ値と一致する必要があります。

### <a name="create-the-collector-appliance"></a>コレクター アプライアンスを作成する

次に、Contoso はダウンロードしたファイルを vCenter Server インスタンスにインポートして、コレクター アプライアンス VM をプロビジョニングできます。

1. vSphere Client コンソールで、**[ファイル]** > **[OVF テンプレートのデプロイ]** を選択します。

    ![vSphere Web Client - OVF テンプレートのデプロイ](./media/contoso-migration-assessment/vcenter-wizard.png)

2. OVF テンプレートのデプロイ ウィザードで **[ソース]** を選択し、OVA ファイルの場所を指定します。
3. **[名前] と [場所]** では、コレクター VM の表示名を指定します。 次に、VM をホストするインベントリの場所を選択します。 コレクター アプライアンスを実行するホストまたはクラスターも指定します。
4. **[ストレージ]** でストレージの場所を指定します。 **[ディスク フォーマット]** でストレージをプロビジョニングする方法を選択します。
5. **[ネットワーク マッピング]** で、コレクター VM を接続するためのネットワークを指定します。 このネットワークには、Azure にメタデータを送信するためのインターネット接続が必要です。
6. 設定を確認し、**[デプロイ後に電源オン]** > **[完了]** を選択します。 アプライアンスが作成されると、正常に完了したことを確認するメッセージが表示されます。

### <a name="run-the-collector-to-discover-vms"></a>コレクターを実行して VM を検出する

次に、Contoso はコレクターを実行して VM を検出します。 現時点でオペレーティング システムの言語およびコレクター インターフェイスの言語としてコレクターでサポートされるのは、**英語 (米国)** のみであることに注意してください。

1. vSphere Client コンソールで、**[コンソールを開く]** を選択します。 コレクター VM の設定で、言語、タイム ゾーン、パスワードを指定します。
2. デスクトップにある **[コレクターの実行]** ショートカットを選択します。

    ![vSphere Client コンソール - コレクターのショートカット](./media/contoso-migration-assessment/collector-shortcut.png)

3. Azure Migrate Collector で、**[前提条件のセットアップ]** を選択します。 ライセンス条項に同意し、サード パーティの情報を確認します。
4. コレクターによって、VM からインターネットにアクセスできること、時間が同期されていること、コレクター サービスが実行されていることがチェックされます。 (コレクター サービスは、既定で VM にインストールされます。)Contoso は VMware PowerCLI もインストールします。

    > [!NOTE]
    > VM からインターネットにプロキシを使用しないで直接アクセスできると仮定しています。

    ![Azure Migrate Collector - 前提条件の確認](./media/contoso-migration-assessment/collector-verify-prereqs.png)

5. **[vCenter Server の詳細を指定する]** で、vCenter Server インスタンスの名前 (FQDN) または IP アドレスと、検出に使用する読み取り専用の資格情報を指定します。
6. VM 検出のスコープを選択します。 コレクターは、指定されたスコープ内の VM のみを検出できます。 スコープは、特定のフォルダー、データセンター、またはクラスターに設定できます。 スコープに含まれる VM の数が 1,500 を超えないようにします。

    ![vCenter Server の詳細を指定する](./media/contoso-migration-assessment/collector-connect-vcenter.png)

7. **[移行プロジェクトを指定します]** で、ポータルからコピーした Azure Migrate プロジェクトの ID とキーを指定します。 プロジェクトの ID とキーを取得するには、プロジェクトの **[概要]** ページ > **[マシンの検出]** に移動します。  

    ![移行プロジェクトの指定](./media/contoso-migration-assessment/collector-connect-azure.png)

8. **[コレクションの進行状況を表示します]** で、Contoso は検出を監視し、VM から収集されたメタデータがスコープ内にあることを確認できます。 コレクターがおおよその検出時間を表示します。

    ![コレクションの進行状況の表示](./media/contoso-migration-assessment/collector-collection-process.png)

### <a name="verify-vms-in-the-portal"></a>ポータル内での VM の特定

収集の完了後、Contoso は VM がポータルに表示されていることを確認します。

1. Azure Migrate プロジェクトで、**[管理]** > **[マシン]** を選択します。 検出したい VM が表示されていることを確認します。

    ![Azure Migrate - 検出されたマシン](./media/contoso-migration-assessment/discovery-complete.png)

2. 現時点では、マシンに Azure Migrate エージェントはインストールされていません。 Contoso は、依存関係を表示するためにエージェントをインストールする必要があります。

    ![Azure Migrate - エージェントのインストールが必要](./media/contoso-migration-assessment/machines-no-agent.png)

## <a name="step-5-prepare-for-dependency-analysis"></a>手順 5:依存関係の分析の準備をする

評価する VM 間の依存関係を表示するために、Contoso はエージェントをダウンロードしてアプリ VM にインストールします。 Windows と Linux の両方について、アプリのすべての VM にエージェントをインストールします。

### <a name="take-a-snapshot"></a>スナップショットを取得する

VM を変更する前に VM のコピーを保管するために、エージェントをインストールする前にスナップショットを取得します。

![マシンのスナップショット](./media/contoso-migration-assessment/snapshot-vm.png)

### <a name="download-and-install-the-vm-agents"></a>VM エージェントをダウンロードしてインストールする

1. **[マシン]** でマシンを選択します。 **[依存関係]** 列の **[要インストール]** を選択します。
2. **[マシンの検出]** ウィンドウで以下の操作を行います。
    - Windows VM ごとに Microsoft Monitoring Agent (MMA) と Dependency Agent をダウンロードします。
    - Linux VM ごとに MMA と Dependency Agent をダウンロードします。
3. ワークスペース ID とキーをコピーします。 ワークスペース ID とキーは、MMA をインストールするときに必要です。

    ![エージェントのダウンロード](./media/contoso-migration-assessment/download-agents.png)

### <a name="install-the-agents-on-windows-vms"></a>Windows VM にエージェントをインストールする

Contoso は VM ごとにインストールを実行します。

#### <a name="install-the-mma-on-windows-vms"></a>Windows VM に MMA をインストールする

1. ダウンロードしたエージェントをダブルクリックします。
2. **[インストール先フォルダー]** では既定のインストール先フォルダーをそのまま使用し、**[次へ]** を選択します。
3. **[エージェントのセットアップ オプション]** で、**[Azure Log Analytics にエージェントを接続する]** > **[次へ]** を選択します。

    ![Microsoft Monitoring Agent のセットアップ - エージェントのセットアップ オプション](./media/contoso-migration-assessment/mma-install.png)

4. **[Azure Log Analytics]** で、ポータルからコピーしたワークスペース ID とキーを貼り付けます。

    ![Microsoft Monitoring Agent のセットアップ - Azure Log Analytics](./media/contoso-migration-assessment/mma-install2.png)

5. **[インストールの準備完了]** で、MMA をインストールします。

#### <a name="install-the-dependency-agent-on-windows-vms"></a>Windows に依存関係エージェントをインストールする

1. ダウンロードした Dependency Agent をダブルクリックします。
2. ライセンス条項に同意し、インストールが完了するまで待ちます。

    ![Dependency Agent のセットアップ - インストール](./media/contoso-migration-assessment/dependency-agent.png)

### <a name="install-the-agents-on-linux-vms"></a>Linux VM にエージェントをインストールする

Contoso は VM ごとにインストールを実行します。

#### <a name="install-the-mma-on-linux-vms"></a>Linux VM に MMA をインストールする

1. 次のコマンドを使用して、VM ごとに Python の ctypes ライブラリをインストールします。

    `sudo apt-get install python-ctypeslib`
2. MMA エージェントをインストールするコマンドは、root として実行する必要があります。 ルートになるには、次のコマンドを実行し、root のパスワードを入力します。

    `sudo -i`
3. MMA をインストールします。
   - コマンドでワークスペース ID とキーを入力します。
   - コマンドは 64 ビット用です。
   - ワークスペース ID とプライマリ キーは、Azure portal の Log Analytics ワークスペースにあります。 **[設定]** を選択し、次に **[接続されたソース]** タブを選択します。
   - Log Analytics エージェントをダウンロードしてチェックサムを検証し、エージェントをインストールして利用できる状態にするためには、以下のコマンドを実行します。

     ```
     wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w 6b7fcaff-7efb-4356-ae06-516cacf5e25d -s k7gAMAw5Bk8pFVUTZKmk2lG4eUciswzWfYLDTxGcD8pcyc4oT8c6ZRgsMy3MmsQSHuSOcmBUsCjoRiG2x9A8Mg==
     ```

#### <a name="install-the-dependency-agent-on-linux-vms"></a>Linux VM に Dependency Agent をインストールする

MMA をインストールした後、Contoso は Linux VM に Dependency Agent をインストールします。

1. Dependency Agent を Linux コンピューターにインストールするには、InstallDependencyAgent-Linux64.bin (自己解凍バイナリを含むシェル スクリプト) を使用します。 sh を使用してそのファイルを実行するか、実行アクセス許可をファイル自体に追加します。

2. root として Linux の Dependency Agent をインストールします。

    ```
    wget --content-disposition https://aka.ms/dependencyagentlinux -O InstallDependencyAgent-Linux64.bin && sudo sh InstallDependencyAgent-Linux64.bin -s
    ```

## <a name="step-6-run-and-analyze-the-vm-assessment"></a>手順 6:VM 評価を実行して分析する

これで、Contoso はマシンの依存関係を確認し、グループを作成することができます。 その後、グループの評価を実行します。

### <a name="verify-dependencies-and-create-a-group"></a>依存関係を確認し、グループを作成する

1. 分析対象のマシンを確認するために、Contoso は **[依存関係の表示]** を選択します。

    ![Azure Migrate - マシンの依存関係の表示](./media/contoso-migration-assessment/view-machine-dependencies.png)

2. SQLVM 依存関係マップでは、SQLVM について次の詳細が表示されます。

    - 指定した期間 (既定では 1 時間) に SQLVM でアクティブなネットワーク接続が実行されたプロセス グループまたはプロセス。
    - 依存関係にあるすべてのマシン間の受信 (クライアント) および送信 (サーバー) TCP 接続。
    - Azure Migrate エージェントがインストールされた依存関係にあるマシンが、別々のボックスとして表示されます。
    - エージェントがインストールされていないマシンには、ポートと IP アドレスの情報が表示されます。

3. エージェントがインストールされているマシン (WEBVM) について、マシンのボックスを選択すると詳細情報が表示されます。 その情報には、FQDN、オペレーティング システム、MAC アドレスが含まれます。

    ![Azure Migrate - グループの依存関係の表示](./media/contoso-migration-assessment/sqlvm-dependencies.png)

4. グループに追加する VM (SQLVM と WEBVM) を選択します。 Ctrl キーを押しながらクリックして、複数の VM を選択します。
5. **[グループの作成]** を選択し、名前 (**smarthotelapp**) を入力します。

    > [!NOTE]
    > さらに細かい依存関係を表示するために、時間の範囲を拡張できます。 特定の期間、つまり開始日と終了日を選択できます。

### <a name="run-an-assessment"></a>評価を実行する

1. **[グループ]** でグループ (**smarthotelapp**) を開き、**[評価の作成]** を選択します。

    ![Azure Migrate - 評価の作成](./media/contoso-migration-assessment/run-vm-assessment.png)

2. 評価を表示するには、**[管理]** > **[評価]** を選択します。

Contoso では既定の評価の設定を使用しましたが、[設定はカスタマイズ](how-to-modify-assessment.md)できます。

### <a name="analyze-the-vm-assessment"></a>VM 評価を分析する

Azure Migrate 評価には、オンプレミスと Azure の互換性、推奨される適切な Azure VM サイズ設定、推定月間 Azure コストに関する情報が含まれています。

![Azure Migrate - 評価レポート](./media/contoso-migration-assessment/assessment-overview.png)

#### <a name="review-confidence-rating"></a>信頼度レーティングを確認する

![Azure Migrate - 評価の表示](./media/contoso-migration-assessment/assessment-display.png)

評価には、1 つ星から 5 つ星 (1 つ星が最低で 5 つ星が最高) までの信頼度レーティングが付いています。
- 信頼度レーティングは、評価の計算に必要なデータ ポイントの可用性に基づいて、評価に割り当てられます。
- レーティングは、Azure Migrate による推奨サイズの信頼性を見積もるために役立ちます。
- 信頼度レーティングは、*パフォーマンス ベースのサイズ設定*を行う場合に役に立ちます。 Azure Migrate には、使用率ベースのサイズ設定を行うために十分なデータ ポイントがない場合があります。 *[オンプレミス]* のサイズ設定の場合、Azure Migrate には VM のサイズ変更に必要なすべてのデータ ポイントがあるため、信頼度レーティングは常に 5 つ星になります。
- 次のように、使用可能なデータ ポイントの割合に応じて、評価の信頼度レーティングが決まります。

   データ ポイントの可用性 | 信頼度レーティング
   --- | ---
   0% - 20% | 1 つ星
   21% - 40% | 2 つ星
   41% - 60% | 3 つ星
   61% - 80% | 4 つ星
   81% - 100% | 5 つ星

#### <a name="verify-azure-readiness"></a>Azure 対応性の状態を確認する

![Azure Migrate - 評価の準備状態](./media/contoso-migration-assessment/azure-readiness.png)  

評価レポートには、情報が表でまとめられて表示されます。 パフォーマンス ベースのサイズ設定を表示するには、Azure Migrate に次の情報が必要です。 この情報を収集できない場合、サイズ設定評価は正確でない可能性があります。

- CPU とメモリの使用率データ。
- VM にアタッチされた各ディスクの読み取り/書き込み IOPS とスループット。
- VM にアタッチされた各ネットワーク アダプターのネットワーク受信/送信情報。

Setting | 指示内容 | 詳細
--- | --- | ---
**Azure VM 対応性** | VM が移行できる状態であるかどうかを示します。 | 次の状態があります。</br><br/>- Azure に対応<br/><br/>- 条件付きで対応 <br/><br/>- Azure に未対応<br/><br/>- 対応不明<br/><br/> VM の準備が整っていない場合、Azure Migrate によって修正手順が表示されます。
**Azure VM サイズ** | 準備が完了している VM について、Azure Migrate によって Azure VM サイズが推奨されます。 | サイズ設定の推奨事項は、評価のプロパティによって異なります。<br/><br/>- パフォーマンス ベースのサイズ設定を使用した場合、サイズ設定では VM のパフォーマンス履歴が考慮されます。<br/><br/>- *[オンプレミス]* を使用した場合、サイズ設定はオンプレミスの VM サイズに基づき、使用率データは使われません。
**推奨されるツール** | Azure マシンではエージェントが実行されているため、マシン内で実行されているプロセスが Azure Migrate によって確認されます。 そのマシンがデータベース マシンであるかどうかが識別されます。
**VM 情報** | レポートには、オペレーティング システム、ブートの種類、ディスクとストレージの情報など、オンプレミス VM の設定が表示されます。

#### <a name="review-monthly-cost-estimates"></a>推定月間コストを確認する

このビューには、Azure で VM を実行する際の計算コストとストレージ コストの合計が表示されます。 各マシンの詳細も表示されます。

![Azure Migrate - Azure のコスト](./media/contoso-migration-assessment/azure-costs.png)

- コストの見積もりは、マシンの推奨サイズを使って計算されます。
- コンピューティングとストレージの見積もり月額コストが、グループ内の全 VM について集計されます。

## <a name="clean-up-after-assessment"></a>評価後のクリーンアップ

- 評価の終了後、Contoso は今後の評価に使用するために Azure Migrate のアプライアンスを保持します。
- VMware VM をオフにします。 追加の VM を評価するときに再度使用します。
- **Contoso の移行**プロジェクトを Azure に保管します。 現在、プロジェクトは米国東部 Azure リージョンにある **ContosoFailoverRG** リソース グループにデプロイされています。
-  コレクター VM には 180 日間の評価版ライセンスがあります。 この制限の有効期限が切れた場合は、コレクターをダウンロードして再度セットアップする必要があります。

## <a name="conclusion"></a>まとめ

このシナリオでは、Contoso が Data Migration Assessment ツールを使用して SmartHotel360 アプリ データベースを評価しました。 また、Azure Migrate サービスを使用してオンプレミスの VM を評価しました。 次いで、オンプレミス リソースを Azure に移行する準備を確実に整えるため、評価を確認しました。

## <a name="next-steps"></a>次の手順

このシリーズの次の記事では、Contoso がリフトアンドシフト移行によって SmartHotel360 アプリを Azure で再ホストします。 Contoso は、Azure Site Recovery を使用してアプリのフロントエンド WEBVM を移行します。 Database Migration Service を使用してアプリ データベースを Azure SQL Database Managed Instance に移行します。 このデプロイで[今すぐ開始](contoso-migration-rehost-vm-sql-managed-instance.md)。
