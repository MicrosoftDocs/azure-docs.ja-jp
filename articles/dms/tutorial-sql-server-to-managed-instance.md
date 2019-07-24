---
title: チュートリアル:DMS を使用して Azure SQL Database マネージド インスタンスに移行する | Microsoft Docs
description: Azure Database Migration Service を使用して、オンプレミスの SQL Server から Azure SQL Database マネージド インスタンスに移行する方法を学習します。
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: cf285c18d2204da625c970a367177f86474149ab
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "58880986"
---
# <a name="tutorial-migrate-sql-server-to-an-azure-sql-database-managed-instance-offline-using-dms"></a>チュートリアル:DMS を使用してオフラインで SQL Server を Azure SQL Database マネージド インスタンスに移行する

Azure Database Migration Service を使用して、オンプレミスの SQL Server インスタンスから [Azure SQL Database マネージド インスタンス](../sql-database/sql-database-managed-instance.md)にデータベースを移行することができます。 一定の手作業が必要になる可能性のあるその他の方法については、記事「[Azure SQL Database Managed Instance への SQL Server インスタンスの移行](../sql-database/sql-database-managed-instance-migrate.md)」を参照してください。

このチュートリアルでは、Azure Database Migration Service を使用して、オンプレミスの SQL Server インスタンスから Azure SQL Database マネージド インスタンスに **Adventureworks2012** データベースを移行します。

このチュートリアルでは、以下の内容を学習します。
> [!div class="checklist"]
> - Azure Database Migration Service のインスタンスを作成する。
> - Azure Database Migration Service を使用して移行プロジェクトを作成する。
> - 移行を実行する。
> - 移行を監視する。
> - 移行レポートをダウンロードする。

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

この記事では、SQL Server から Azure SQL Database マネージド インスタンスへのオフライン移行について説明します。 オンライン移行については、[DMS を使用して SQL Server を Azure SQL Database Managed Instance にオンラインで移行する](tutorial-sql-server-managed-instance-online.md)チュートリアルを参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下を実行する必要があります。

- Azure Resource Manager デプロイ モデルを使用して、Azure Database Migration Service 用の Azure 仮想ネットワーク (VNET) を作成します。これで、[ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) または [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) を使用したオンプレミスのソース サーバーとのサイト間接続を確立します。 [Azure Database Migration Service を使用した Azure SQL Database マネージド インスタンスの移行のネットワーク トポロジを学習します](https://aka.ms/dmsnetworkformi)。

    > [!NOTE]
    > VNET のセットアップ中、Microsoft へのネットワーク ピアリングに ExpressRoute を使用した場合、サービスのプロビジョニング先となるサブネットに、次のサービス [エンドポイント](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)を追加してください。
    > - ターゲット データベース エンドポイント (SQL エンドポイント、Cosmos DB エンドポイントなど)
    > - ストレージ エンドポイント
    > - サービス バス エンドポイント
    >
    > Azure Database Migration Service にはインターネット接続がないため、この構成が必要となります。

- VNET のネットワーク セキュリティ グループの規則によって、Azure Database Migration Service への以下のインバウンド通信ポートがブロックされないことを確認します: 443、53、9354、445、12000。 Azure VNET NSG トラフィックのフィルター処理の詳細については、「[ネットワーク セキュリティ グループによるネットワーク トラフィックのフィルタリング](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)」を参照してください。
- [ソース データベース エンジンへのアクセスのために Windows ファイアウォール](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)を構成します。
- Azure Database Migration Service がソースの SQL Server にアクセスできるように Windows ファイアウォールを開きます。既定では TCP ポート 1433 が使用されます。
- 動的ポートを使用して複数の名前付き SQL Server インスタンスを実行している場合は、SQL Browser サービスを有効にし、ファイアウォール経由の UDP ポート 1434 へのアクセスを許可することをお勧めします。これにより、Azure Database Migration Service はソース サーバー上の名前付きインスタンスに接続できるようになります。
- ソース データベースの前でファイアウォール アプライアンスを使用する場合は、Azure Database Migration Service が移行のためにソース データベースにアクセスし、SMB ポート 445 経由でファイルにアクセスできるように、ファイアウォール規則を追加することが必要な場合があります。
- Azure SQL Database マネージド インスタンスを作成します。手順の詳細については、[Azure portal での Azure SQL Database マネージド インスタンスの作成](https://aka.ms/sqldbmi)に関するページを参照してください。
- ソース SQL Server とターゲット マネージド インスタンスへの接続に使用するログインが、sysadmin サーバー ロールのメンバーであることを確認してください。
- Azure Database Migration Service がソース データベースのバックアップに使用できるネットワーク共有を作成します。
- 作成したネットワーク共有に対して、ソース SQL Server インスタンスを実行しているサービス アカウントが書き込み特権を持っていること、およびソース サーバーのコンピューター アカウントが読み取り/書き込みアクセス権を持っていることを確認します。
- 作成したネットワーク共有に対するフル コントロール権限を持つ Windows ユーザー (とパスワード) をメモしておきます。 Azure Database Migration Service は、ユーザーの資格情報を借用して、復元操作のために、Azure ストレージ コンテナーにバックアップ ファイルをアップロードします。
- Blob コンテナーを作成して、[ストレージ エクスプローラーを使用した Azure Blob Storage リソースの管理](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container)に関するページの手順に従って、SAS URI を取得します。SAS URI の作成時には、必ずポリシー ウィンドウに表示されるすべてのアクセス許可 (読み取り、書き込み、削除、一覧表示) を選択してください。 これにより、Azure Database Migration Service はストレージ アカウント コンテナーにアクセスし、Azure SQL Database マネージド インスタンスにデータベースを移行するために使用されるバックアップ ファイルをアップロードできます。

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Microsoft.DataMigration リソース プロバイダーを登録する

1. Azure portal にサインインし、**[すべてのサービス]** を選択し、**[サブスクリプション]** を選択します。

    ![ポータルのサブスクリプションの表示](media/tutorial-sql-server-to-managed-instance/portal-select-subscriptions.png)        

2. Azure Database Migration Service のインスタンスを作成するサブスクリプションを選択して、**[リソース プロバイダー]** を選択します。

    ![リソース プロバイダーの表示](media/tutorial-sql-server-to-managed-instance/portal-select-resource-provider.png)

3. 移行を検索し、**Microsoft.DataMigration** の右側にある **[登録]** を選択します。

    ![リソース プロバイダーの登録](media/tutorial-sql-server-to-managed-instance/portal-register-resource-provider.png)   

## <a name="create-an-azure-database-migration-service-instance"></a>Azure Database Migration Service インスタンスを作成する

1. Azure portal で **[+ リソースの作成]** を選択し、**Azure Database Migration Service** を検索して、ドロップダウン リストから **[Azure Database Migration Service]** を選択します。

     ![Azure Marketplace](media/tutorial-sql-server-to-managed-instance/portal-marketplace.png)

2. **[Azure Database Migration Service]** 画面で、**[作成]** を選択します。

    ![Azure Database Migration Service インスタンスを作成する](media/tutorial-sql-server-to-managed-instance/dms-create1.png)

3. **[移行サービスの作成]** 画面で、サービスの名前、サブスクリプション、新規または既存のリソース グループを指定します。

4. DMS のインスタンスの作成先となる場所を選択します。

5. 既存の仮想ネットワーク (VNET) を選択するか、新たに VNET を作成します。

    その VNET によって、Azure Database Migration Service に、ソース SQL Server とターゲット Azure SQL Database マネージド インスタンスへのアクセスが提供されます。

    Azure portal で VNET を作成する方法の詳細については、[Azure portal を使用した仮想ネットワークの作成](https://aka.ms/DMSVnet)に関する記事を参照してください。

    その他の詳細については、記事「[Azure Database Migration Service を使用して Azure SQL DB Managed Instance を移行するためのネットワーク トポロジ](https://aka.ms/dmsnetworkformi)」を参照してください。

6. 価格レベルを選択します。

    コストと価格レベルの詳細については、[価格に関するページ](https://aka.ms/dms-pricing)を参照してください。

    ![DMS サービスを作成する](media/tutorial-sql-server-to-managed-instance/dms-create-service2.png)

7. **[作成]** を選択して、サービスを作成します。

## <a name="create-a-migration-project"></a>移行プロジェクトを作成する

サービスのインスタンスが作成されたら、Azure portal 内でそのサービスを探して開き、新しい移行プロジェクトを作成します。

1. Azure ポータルで、**[All services]\(すべてのサービス\)** を選択し、Azure Database Migration Service を検索して、**Azure Database Migration Service** を選択します。

    ![Azure Database Migration Service のすべてのインスタンスを検索する](media/tutorial-sql-server-to-managed-instance/dms-search.png)

2. **[Azure Database Migration Service]** 画面で、作成したインスタンスの名前を検索して、そのインスタンスを選択します。

3. **[+ 新しい移行プロジェクト]** を選択します。

4. **[新しい移行プロジェクト]** 画面でプロジェクトの名前を指定し、**[Source server type]\(ソース サーバーの種類\)** ボックスで **[SQL Server]** を選択した後、**[ターゲット サーバーの種類]** ボックスで **[Azure SQL Database Managed Instance]** を選択し、**[アクティビティの種類を選択します]** で **[オフライン データの移行]** を選択します。

   ![DMS のプロジェクトを作成する](media/tutorial-sql-server-to-managed-instance/dms-create-project2.png)

5. **[作成]** を選択してプロジェクトを作成します。

## <a name="specify-source-details"></a>ソース詳細を指定する

1. **[移行ソースの詳細]** 画面で、ソース SQL Server の接続の詳細を指定します。

2. 信頼できる証明書をサーバーにインストールしていない場合は、**[サーバー証明書を信頼する]** チェック ボックスをオンにします。

    信頼できる証明書がインストールされていない場合、SQL Server はインスタンスの開始時に自己署名証明書を生成します。 この証明書は、クライアント接続の資格情報の暗号化に使用されます。

    > [!CAUTION]
    > 自己署名証明書を使用して暗号化されている SSL 接続のセキュリティは強力ではありません。 man-in-the-middle (中間者) 攻撃を受ける可能性が高くなります。 運用環境や、インターネットに接続されているサーバーでは、自己署名証明書を使用した SSL を信頼しないでください。

   ![ソースの詳細](media/tutorial-sql-server-to-managed-instance/dms-source-details1.png)

3. **[保存]** を選択します。

4. **[ソース データベースの選択]** 画面で、移行する **Adventureworks2012** データベースを選択します。

   ![ソース データベースを選択する](media/tutorial-sql-server-to-managed-instance/dms-source-database1.png)

    > [!IMPORTANT]
    > SQL Server Integration Services (SSIS) を使用する場合、DMS では現在、SQL Server から Azure SQL Database マネージド インスタンスへの SSIS プロジェクト/パッケージ (SSISDB) 用のカタログ データベースの移行がサポートされていません。 ただし、Azure Data Factory (ADF) に SSIS をプロビジョニングし、Azure SQL Database マネージド インスタンスによってホストされている移行先 SSISDB に SSIS プロジェクト/パッケージを再デプロイすることはできます。 SSIS パッケージの移行の詳細については、記事「[SQL Server Integration Services パッケージを Azure に移行する](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages)」を参照してください。

5. **[保存]** を選択します。

## <a name="specify-target-details"></a>ターゲット詳細を指定する

1. **[移行のターゲットの詳細]** 画面で、ターゲットの接続情報を指定します。このターゲットは、**AdventureWorks2012** データベースの移行先である、事前プロビジョニング済みの Azure SQL Database マネージド インスタンスです。

    Azure SQL Database マネージド インスタンスのプロビジョニングがまだ済んでいない場合は、インスタンスのプロビジョニングに役立つ[リンク](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started)を選択します。 そのままプロジェクトの作成を続行し、Azure SQL Database マネージド インスタンスの準備ができたら、この特定のプロジェクトに戻って移行を実行できます。

     ![ターゲットを選択する](media/tutorial-sql-server-to-managed-instance/dms-target-details2.png)

2. **[保存]** を選択します。

## <a name="select-source-databases"></a>ソース データベースを選択する

1. **[ソース データベースの選択]** 画面で、移行するソース データベースを選択します。

    ![ソース データベースを選択する](media/tutorial-sql-server-to-managed-instance/select-source-databases.png)

2. **[保存]** を選択します。

## <a name="select-logins"></a>ログインを選択する

1. **[ログインの選択]** 画面で、移行するログインを選択します。

    >[!NOTE]
    >このリリースでサポートされるのは SQL ログインの移行のみです。

    ![ログインを選択する](media/tutorial-sql-server-to-managed-instance/select-logins.png)

2. **[保存]** を選択します。

## <a name="configure-migration-settings"></a>移行の設定の構成

1. **[移行の設定の構成]** 画面で、次の情報を入力します。

    | | |
    |--------|---------|
    |**ソースのバックアップ オプションを選択します** | DMS がデータベースの移行に使用できる完全バックアップ ファイルが既に存在する場合は、**[最新のバックアップ ファイルを自分で用意する]** オプションを選択します。 ソース データベースの完全バックアップを最初に DMS で作成し、それを移行に使用する場合は、**[I will let Azure Database Migration Service create backup files]\(バックアップ ファイルを Azure Database Migration Service で作成する\)** を選択します。 |
    |**ネットワーク共有の場所** | Azure Database Migration Service がソース データベース バックアップを移行できるローカル SMB ネットワーク共有です。 ソースの SQL Server インスタンスを実行しているサービス アカウントには、このネットワーク共有に対する書き込み権限が必要です。 たとえば、ネットワーク共有のサーバーの FQDN または IP アドレスを "\\\servername.domainname.com\backupfolder" または "\\\IP address\backupfolder" と指定します。|
    |**ユーザー名** | 上で指定したネットワーク共有に対するフル コントロール権限が Windows ユーザーにあることを確認してください。 Azure Database Migration Service は、ユーザーの資格情報を借用して、復元操作のために、Azure ストレージ コンテナーにバックアップ ファイルをアップロードします。 TDE 対応データベースが選択されている場合、Azure Database Migration Service で証明書ファイルをアップロードしたり削除したりするために、上記の Windows ユーザーはビルトイン Administrator アカウントでなければならず、[ユーザー アカウント制御](https://docs.microsoft.com/windows/security/identity-protection/user-account-control/user-account-control-overview)は無効にする必要があります。 |
    |**パスワード** | ユーザーのパスワード。 |
    |**ストレージ アカウントの設定** | サービスがバックアップ ファイルをアップロードするストレージ アカウント コンテナーへのアクセスを Azure Database Migration Service に提供する SAS URI。これが、データベースを Azure SQL Database マネージド インスタンスに移行するために使用されます。 [Blob コンテナーの SAS URI を取得する方法について説明します](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container)。|
    |**TDE の設定** | Transparent Data Encryption (TDE) が有効になっているソース データベースを移行する場合は、ターゲット Azure SQL Database マネージド インスタンスに対する書き込み特権が必要です。  Azure SQL Database マネージド インスタンスがプロビジョニングされているサブスクリプションをドロップダウン メニューから選択してください。  ドロップダウン メニューでターゲットの **[Azure SQL Database Managed Instance]** を選択します。 |

    ![移行設定の構成](media/tutorial-sql-server-to-managed-instance/dms-configure-migration-settings3.png)

2. **[保存]** を選択します。

## <a name="review-the-migration-summary"></a>移行の概要を確認する

1. **[Migration summary]\(移行の概要\)** 画面の **[アクティビティ名]** ボックスに移行アクティビティの名前を指定します。

2. **[検証オプション]** セクションを展開して **[検証オプションの選択]** 画面を表示し、移行したデータベースでクエリの正確さを検証するかどうかを指定して、**[保存]** を選択します。

3. 移行プロジェクトに関連付けられた詳細を確認します。

    ![移行プロジェクトの概要](media/tutorial-sql-server-to-managed-instance/dms-project-summary2.png)

4. **[保存]** を選択します。

## <a name="run-the-migration"></a>移行を実行する

- **[移行の実行]** を選択します。

  移行アクティビティ ウィンドウが表示され、アクティビティの [状態] は **[Pending]\(保留\)** になっています。

## <a name="monitor-the-migration"></a>移行を監視する

1. 移行アクティビティ画面で、**[更新]** を選択して表示を更新します。
 
   ![進行中の移行アクティビティ](media/tutorial-sql-server-to-managed-instance/dms-monitor-migration1.png)

    データベースとログインのカテゴリを展開して、該当するサーバー オブジェクトの移行状態を監視できます。

   ![進行中の移行アクティビティ](media/tutorial-sql-server-to-managed-instance/dms-monitor-migration-extend.png)

2. 移行が完了したら、**[レポートのダウンロード]** を選択して、移行プロセスに関連する詳細情報を一覧表示したレポートを取得します。

3. ターゲットの Azure SQL Database マネージド インスタンス環境にターゲット データベースがあることを確認します。

## <a name="next-steps"></a>次の手順

- T-SQL RESTORE コマンドを使用してマネージド インスタンスにデータベースを移行する方法を示したチュートリアルについては、[復元コマンドを使用したマネージド インスタンスへのバックアップの復元](../sql-database/sql-database-managed-instance-restore-from-backup-tutorial.md)に関するページを参照してください。
- マネージド インスタンスについては、[マネージド インスタンスの概要](../sql-database/sql-database-managed-instance.md)に関するページを参照してください。
- マネージド インスタンスへのアプリケーションの接続については、[アプリケーションの接続](../sql-database/sql-database-managed-instance-connect-app.md)に関するページを参照してください。
