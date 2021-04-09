---
title: チュートリアル:SQL Server を SQL Managed Instance にオンラインで移行する
titleSuffix: Azure Database Migration Service
description: Azure Database Migration Service を使用して、SQL Server から Azure SQL Managed Instance へのオンライン移行を実行する方法について説明します。
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 08/04/2020
ms.openlocfilehash: 4ee3e9e7d2aa0247011415b43517147fd421902a
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/18/2021
ms.locfileid: "101094935"
---
# <a name="tutorial-migrate-sql-server-to-an-azure-sql-managed-instance-online-using-dms"></a>チュートリアル:DMS を使用してオンラインで SQL Server を Azure SQL Managed Instance に移行する

Azure Database Migration Service を使用して、最小限のダウンタイムで SQL Server インスタンスから [Azure SQL Managed Instance ](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md) にデータベースを移行することができます。 一定の手作業が必要になる可能性のあるその他の方法については、記事「[Azure SQL Managed Instance への SQL Server インスタンスの移行](../azure-sql/managed-instance/migrate-to-instance-from-sql-server.md)」を参照してください。

このチュートリアルでは、Azure Database Migration Service を使用して、SQL Server のオンプレミスのインスタンスから SQL Managed Instance に、最小限のダウンタイムで **Adventureworks2012** データベースを移行します。

このチュートリアルでは、以下の内容を学習します。
> [!div class="checklist"]
>
> * Azure Database Migration Service のインスタンスを作成する。
> * Azure Database Migration Service を使用して、移行プロジェクトを作成し、オンライン移行を開始する。
> * 移行を監視する。
> * 準備ができたら、移行カットオーバーを実行します。

> [!IMPORTANT]
> Azure Database Migration Service を使用した SQL Server から SQL Managed Instance へのオンラインでの移行のためには、そのサービスによるデータベースの移行に使用できる、データベースの完全バックアップとそれに続くログ バックアップを SMB ネットワーク共有上に用意する必要があります。 Azure Database Migration Service によってバックアップが開始されることはなく、移行には既存のバックアップが使用されます。これは、ディザスター リカバリー計画の一部として既に作成されている場合があります。
> 必ず [WITH CHECKSUM オプションを使用してバックアップ](/sql/relational-databases/backup-restore/enable-or-disable-backup-checksums-during-backup-or-restore-sql-server?preserve-view=true&view=sql-server-2017)を作成してください。 また、単一のバックアップ メディアに複数のバックアップ (完全ログとトランザクション ログ) を追加しないでください。各バックアップを別々のバックアップ ファイルに作成します。 最後に、圧縮されたバックアップを使用すると、大きなバックアップの移行に関連する潜在的な問題が発生する可能性を低減できます。

> [!NOTE]
> Azure Database Migration Service を使用してオンライン移行を実行するには、Premium 価格レベルに基づいてインスタンスを作成する必要があります。

> [!IMPORTANT]
> 最適な移行エクスペリエンスのために、ターゲット データベースと同じ Azure リージョンに Azure Database Migration Service のインスタンスを作成することをお勧めします。 リージョンや地域をまたいでデータを移動する場合、移行プロセスが遅くなり、エラーが発生する可能性があります。

> [!IMPORTANT]
> インスタンスの再構成や計画メンテナンスによる中断のリスクを最小限に抑えるために、オンライン移行プロセスの期間をできるだけ短くしてください。 このようなイベントが発生した場合、移行プロセスは最初から開始されます。 計画メンテナンスの場合、移行プロセスが再開されるまでに 36 時間の猶予期間があります。

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

この記事では、SQL Server から SQL Managed Instance へのオンライン移行について説明します。 オフライン移行については、[DMS を使用してオフラインで SQL Server を SQL Managed Instance に移行する方法](tutorial-sql-server-to-managed-instance.md)に関するページを参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下を実行する必要があります。

* Azure Resource Manager デプロイ モデルを使用して、Azure Database Migration Service 用の Microsoft Azure 仮想ネットワークを作成します。これで、[ExpressRoute](../expressroute/expressroute-introduction.md) または [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) を使用したオンプレミスのソース サーバーとのサイト間接続を確立します。 [Azure Database Migration Service を使用した SQL Managed Instance の移行のネットワーク トポロジについて学習してください](./resource-network-topologies.md)。 仮想ネットワークの作成方法の詳細については、[Virtual Network のドキュメント](../virtual-network/index.yml)を参照してください。特に、詳細な手順が記載されたクイックスタートの記事を参照してください。

    > [!NOTE]
    > 仮想ネットワークのセットアップ中、Microsoft へのネットワーク ピアリングに ExpressRoute を使用する場合は、サービスのプロビジョニング先となるサブネットに、次のサービス [エンドポイント](../virtual-network/virtual-network-service-endpoints-overview.md)を追加してください。
    >
    > * ターゲット データベース エンドポイント (SQL エンドポイント、Cosmos DB エンドポイントなど)
    > * ストレージ エンドポイント
    > * サービス バス エンドポイント
    >
    > Azure Database Migration Service にはインターネット接続がないため、この構成が必要となります。
    >
    >オンプレミス ネットワークと Azure の間にサイト間接続がない場合、またはサイト間接続の帯域幅が制限されている場合は、Azure Database Migration Service をハイブリッド モード (プレビュー) で使用することを検討してください。 ハイブリッド モードでは、オンプレミスの移行 worker と、クラウドで実行されている Azure Database Migration Service のインスタンスを利用します。 ハイブリッド モードで Azure Database Migration Service のインスタンスを作成するには、[Azure portal を使用してハイブリッド モードで Azure Database Migration Service のインスタンスを作成する方法](./quickstart-create-data-migration-service-portal.md)に関する記事を参照してください。

    > [!IMPORTANT]
    > 移行の一環として使用されるストレージ アカウントに関して、次のいずれかの作業を行う必要があります。
    > * ストレージ アカウントへのアクセスをすべてのネットワークに許可します。
    > * MI サブネットで[サブネットの委任](../virtual-network/manage-subnet-delegation.md)をオンにし、このサブネットを許可するようにストレージ アカウントのファイアウォール規則を更新します。

* 仮想ネットワークのネットワーク セキュリティ グループの規則によって、ServiceBus、Storage、AzureMonitor の ServiceTag の送信ポート 443 がブロックされていないことを確認します。 仮想ネットワークの NSG トラフィックのフィルター処理の詳細については、[ネットワーク セキュリティ グループによるネットワーク トラフィックのフィルター処理](../virtual-network/virtual-network-vnet-plan-design-arm.md)に関する記事を参照してください。
* [ソース データベース エンジンへのアクセスのために Windows ファイアウォール](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)を構成します。
* Azure Database Migration Service でソース SQL Server にアクセスできるように Windows ファイアウォールを開放します。既定では TCP ポート 1433 が使用されます。 使用している既定のインスタンスが他のポートでリッスンしている場合は、それをファイアウォールに追加してください。
* 動的ポートを使用して複数の名前付き SQL Server インスタンスを実行している場合は、SQL Browser サービスを有効にし、ファイアウォール経由の UDP ポート 1434 へのアクセスを許可することをお勧めします。これにより、Azure Database Migration Service はソース サーバー上の名前付きインスタンスに接続できるようになります。
* ソース データベースの前でファイアウォール アプライアンスを使用する場合は、Azure Database Migration Service が移行のためにソース データベースにアクセスし、SMB ポート 445 経由でファイルにアクセスできるように、ファイアウォール規則を追加することが必要な場合があります。
* SQL マネージド インスタンスを作成します。手順の詳細については、[Azure portal で SQL マネージド インスタンスを作成する方法](../azure-sql/managed-instance/instance-create-quickstart.md)に関する記事を参照してください。
* ソースの SQL Server とターゲットの SQL Managed Instance の接続に使用するログインが、sysadmin サーバー ロールのメンバーであることを確認します。
* Azure Database Migration Service でデータベースの移行に使用できる、すべてのデータベースの完全なデータベース バックアップ ファイルと、その後のトランザクション ログのバックアップ ファイルが格納されている、SMB ネットワーク共有を提供します。
* 作成したネットワーク共有に対して、ソース SQL Server インスタンスを実行しているサービス アカウントが書き込み特権を持っていること、およびソース サーバーのコンピューター アカウントが読み取り/書き込みアクセス権を持っていることを確認します。
* 作成したネットワーク共有に対するフル コントロール権限を持つ Windows ユーザー (とパスワード) をメモしておきます。 Azure Database Migration Service は、ユーザーの資格情報を借用して、復元操作のために、Azure Storage コンテナーにバックアップ ファイルをアップロードします。
* Azure Active Directory のアプリケーション ID を作成します。これにより、Azure Database Migration Service がターゲットの Azure Database Managed Instance と Azure Storage Container に接続するために使用できる、アプリケーション ID キーが生成されます。 詳細については、[ポータルを使用した、リソースにアクセスできる Azure Active Directory アプリケーションとサービス プリンシパルの作成](../active-directory/develop/howto-create-service-principal-portal.md)に関する記事を参照してください。

  > [!NOTE]
  > Azure Database Migration Service では、指定されたアプリケーション ID のサブスクリプションに対する共同作成者アクセス許可が必要です。 または、Azure Database Migration Service で必要な特定のアクセス許可を付与するカスタム ロールを作成することもできます。 カスタム ロールの使用に関する詳細な手順については、「[SQL Server から SQL Managed Instance にオンライン移行するためのカスタム ロール](./resource-custom-roles-sql-db-managed-instance.md)」を参照してください。

* DMS サービスがデータベース バックアップ ファイルをアップロードしてデータベースの移行に使用できるように、**標準パフォーマンス レベル** と Azure Storage Account のメモを作成します。  Azure Storage アカウントは、Azure Database Migration Service インスタンスの作成先と同じリージョンに作成してください。

  > [!NOTE]
  > オンライン移行を使用して、[Transparent Data Encryption](../azure-sql/database/transparent-data-encryption-tde-overview.md) によって保護されたデータベースをマネージド インスタンスに移行する場合は、データベースの復元の前に、オンプレミスまたは Azure VM SQL Server インスタンスの対応する証明書を移行する必要があります。 詳細な手順については、[TDE 証明書のマネージド インスタンスへの移行](../azure-sql/database/transparent-data-encryption-tde-overview.md)に関するページを参照してください。

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Microsoft.DataMigration リソース プロバイダーを登録する

1. Azure portal にサインインし、 **[すべてのサービス]** を選択し、 **[サブスクリプション]** を選択します。

    ![ポータルのサブスクリプションの表示](media/tutorial-sql-server-to-managed-instance-online/portal-select-subscriptions.png)

2. Azure Database Migration Service のインスタンスを作成するサブスクリプションを選択してから、 **[リソース プロバイダー]** を選びます。

    ![リソース プロバイダーの表示](media/tutorial-sql-server-to-managed-instance-online/portal-select-resource-provider.png)

3. 移行を検索し、**Microsoft.DataMigration** の右側にある **[登録]** を選択します。

    ![リソース プロバイダーの登録](media/tutorial-sql-server-to-managed-instance-online/portal-register-resource-provider.png)

## <a name="create-an-azure-database-migration-service-instance"></a>Azure Database Migration Service インスタンスを作成する

1. Azure portal で **[+ リソースの作成]** を選択し、**Azure Database Migration Service** を検索して、ドロップダウン リストから **[Azure Database Migration Service]** を選択します。

     ![Azure Marketplace](media/tutorial-sql-server-to-managed-instance-online/portal-marketplace.png)

2. **[Azure Database Migration Service]** 画面で、 **[作成]** を選択します。

    ![Azure Database Migration Service インスタンスを作成する](media/tutorial-sql-server-to-managed-instance-online/dms-create1.png)

3. **[移行サービスの作成]** 画面で、サービスの名前、サブスクリプション、新規または既存のリソース グループを指定します。

4. DMS のインスタンスの作成先となる場所を選択します。

5. 既存の仮想ネットワークを選択するか、新たに作成します。

    この仮想ネットワークによって、Azure Database Migration Service に、ソース SQL Server とターゲット SQL マネージド インスタンスへのアクセスが提供されます。

    Azure portal で仮想ネットワークを作成する方法の詳細については、[Azure portal を使用した仮想ネットワークの作成](../virtual-network/quick-create-portal.md)に関する記事を参照してください。

    その他の詳細については、[Azure Database Migration Service を使用した SQL Managed Instance の移行のネットワーク トポロジ](./resource-network-topologies.md)に関する記事を参照してください。

6. Premium 価格レベルの SKU を選択します。

    > [!NOTE]
    > オンライン移行は、Premium レベルを使用している場合にのみサポートされます。

    コストと価格レベルの詳細については、[価格に関するページ](https://aka.ms/dms-pricing)を参照してください。

    ![DMS サービスを作成する](media/tutorial-sql-server-to-managed-instance-online/dms-create-service3.png)

7. **[作成]** を選択して、サービスを作成します。

## <a name="create-a-migration-project"></a>移行プロジェクトを作成する

サービスのインスタンスが作成されたら、Azure portal 内でそのサービスを探して開き、新しい移行プロジェクトを作成します。

1. Azure ポータルで、 **[All services]\(すべてのサービス\)** を選択し、Azure Database Migration Service を検索して、**Azure Database Migration Service** を選択します。

    ![Azure Database Migration Service のすべてのインスタンスを検索する](media/tutorial-sql-server-to-managed-instance-online/dms-search.png)

2. **[Azure Database Migration Service]** 画面で、作成したインスタンスの名前を検索して、そのインスタンスを選択します。

3. **[+ 新しい移行プロジェクト]** を選択します。

4. **[新しい移行プロジェクト]** 画面で、プロジェクトの名前を指定します。 **[ソース サーバーの種類]** ボックスで **[SQL Server]** を選択し、 **[ターゲット サーバーの種類]** ボックスで **[Azure SQL Managed Instance]** を選択します。次に、 **[アクティビティの種類を選択します]** で **[オンライン データの移行]** を選択します。

   ![Azure Database Migration Service プロジェクトを作成する](media/tutorial-sql-server-to-managed-instance-online/dms-create-project3.png)

5. **[アクティビティの作成と実行]** を選択してプロジェクトを作成ます。

## <a name="specify-source-details"></a>ソース詳細を指定する

1. **[移行ソースの詳細]** 画面で、ソース SQL Server の接続の詳細を指定します。

2. 信頼できる証明書をサーバーにインストールしていない場合は、 **[サーバー証明書を信頼する]** チェック ボックスをオンにします。

    信頼できる証明書がインストールされていない場合、SQL Server はインスタンスの開始時に自己署名証明書を生成します。 この証明書は、クライアント接続の資格情報の暗号化に使用されます。

    > [!CAUTION]
    > 自己署名証明書で暗号化されている TLS 接続のセキュリティは強力ではありません。 man-in-the-middle (中間者) 攻撃を受ける可能性が高くなります。 実稼働環境やインターネットに接続しているサーバーでは、自己署名証明書を使用した TLS 接続は使用しないでください。

   ![ソースの詳細](media/tutorial-sql-server-to-managed-instance-online/dms-source-details2.png)

3. **[保存]** を選択します。

4. **[ソース データベースの選択]** 画面で、移行する **Adventureworks2012** データベースを選択します。

   ![ソース データベースを選択する](media/tutorial-sql-server-to-managed-instance-online/dms-source-database1.png)

    > [!IMPORTANT]
    > SQL Server Integration Services (SSIS) を使用する場合、DMS では現在、SQL Server から SQL Managed Instance への SSIS プロジェクトまたはパッケージ (SSISDB) 用のカタログ データベースの移行がサポートされていません。 ただし、Azure Data Factory (ADF) に SSIS をプロビジョニングした後、SQL Managed Instance によってホストされている移行先 SSISDB に SSIS プロジェクトまたはパッケージを再デプロイすることはできます。 SSIS パッケージの移行の詳細については、記事「[SQL Server Integration Services パッケージを Azure に移行する](./how-to-migrate-ssis-packages.md)」を参照してください。

5. **[保存]** を選択します。

## <a name="specify-target-details"></a>ターゲット詳細を指定する

1. **[移行のターゲットの詳細]** 画面で **アプリケーション ID** と **キー** を指定します。DMS インスタンスは、これらを使用することで、SQL Managed Instance のターゲット インスタンスと Azure Storage アカウントに接続できます。

    詳細については、[ポータルを使用した、リソースにアクセスできる Azure Active Directory アプリケーションとサービス プリンシパルの作成](../active-directory/develop/howto-create-service-principal-portal.md)に関する記事を参照してください。

2. SQL Managed Instance のターゲット インスタンスを収納する **サブスクリプション** を選択した後、ターゲット インスタンスを選択します。

    SQL Managed Instance のプロビジョニングがまだ済んでいない場合は、インスタンスのプロビジョニングに役立つ[リンク](../azure-sql/managed-instance/instance-create-quickstart.md)を選択します。 SQL Managed Instance の準備ができたら、この特定のプロジェクトに戻って移行を実行します。

3. SQL Managed Instance に接続するための **SQL ユーザー** と **パスワード** を入力します。

    ![ターゲットを選択する](media/tutorial-sql-server-to-managed-instance-online/dms-target-details3.png)

4. **[保存]** を選択します。

## <a name="select-source-databases"></a>ソース データベースを選択する

1. **[ソース データベースの選択]** 画面で、移行するソース データベースを選択します。

    ![ソース データベースを選択する](media/tutorial-sql-server-to-managed-instance-online/dms-select-source-databases2.png)

2. **[保存]** を選択します。

## <a name="configure-migration-settings"></a>移行の設定の構成

1. **[移行の設定の構成]** 画面で、次の情報を入力します。

    | | |
    |--------|---------|
    |**SMB ネットワーク場所の共有** | Azure Database Migration Service が移行に使用できる、完全なデータベース バックアップ ファイルとトランザクション ログのバックアップ ファイルを収納する、ローカルな SMB ネットワーク共有または Azure ファイル共有です。 ソースの SQL Server インスタンスを実行しているサービス アカウントには、このネットワーク共有での読み取り/書き込み権限がなければなりません。 たとえば、ネットワーク共有のサーバーの FQDN または IP アドレスを "\\\servername.domainname.com\backupfolder" または "\\\IP address\backupfolder" と指定します。 パフォーマンスを向上するために、移行するデータベースごとに個別のフォルダーを使用することをお勧めします。 **[詳細設定]** オプションを使用すると、データベース レベルのファイル共有パスを指定できます。 SMB 共有への接続時に問題が発生した場合は、[SMB 共有](known-issues-azure-sql-db-managed-instance-online.md#smb-file-share-connectivity)に関するページを参照してください。 |
    |**ユーザー名** | 上で指定したネットワーク共有に対するフル コントロール権限が Windows ユーザーにあることを確認してください。 Azure Database Migration Service により、ユーザーの資格情報を借用することで、復元操作のために、Azure Storage コンテナーにバックアップ ファイルがアップロードされます。 Azure ファイル共有を使用している場合は、先頭に AZURE\ を付けたストレージ アカウント名をユーザー名として使用してください。 |
    |**パスワード** | ユーザーのパスワード。 Azure ファイル共有を使用している場合は、ストレージ アカウント キーをパスワードとして使用してください。 |
    |**Azure Storage Account のサブスクリプション** | Azure Storage Account を収納するサブスクリプションを選択します。 |
    |**Azure Storage アカウント** | DMS が SMB ネットワーク共有からバックアップ ファイルをアップロードして、データベース移行に使用できるように、Azure Storage Account を選択します。  最適なファイル アップロードのパフォーマンスを得るには、DMS サービスと同じ地域にある Storage Account を選択することをお勧めします。 |

    ![移行設定の構成](media/tutorial-sql-server-to-managed-instance-online/dms-configure-migration-settings4.png)

    > [!NOTE]
    > Azure Database Migration Service で "システム エラー 53" または "システム エラー 57" が表示される場合、Azure Database Migration Service が Azure ファイル共有にアクセスできないことが原因である可能性があります。 このいずれかのエラーが発生した場合は、[こちら](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json#grant-access-from-a-virtual-network)の手順に従って、ストレージ アカウントに仮想ネットワークからアクセスできるようにしてください。

    > [!IMPORTANT]
    > ループバック チェック機能が有効になっていて、ソース SQL Server とファイル共有が同じコンピューター上にある場合、ソースは FQDN を使用してファイル共有にアクセスできません。 この問題を解決するには、[こちら](https://support.microsoft.com/help/926642/error-message-when-you-try-to-access-a-server-locally-by-using-its-fqd)の手順を使用して、ループバック チェック機能を無効にしてください。

2. **[保存]** を選択します。

## <a name="review-the-migration-summary"></a>移行の概要を確認する

1. **[Migration summary]\(移行の概要\)** 画面の **[アクティビティ名]** ボックスに移行アクティビティの名前を指定します。

2. 移行プロジェクトに関連付けられた詳細を確認します。

    ![移行プロジェクトの概要](media/tutorial-sql-server-to-managed-instance-online/dms-project-summary3.png)

## <a name="run-and-monitor-the-migration"></a>移行を実行して監視する

1. **[移行の実行]** を選択します。

2. 移行アクティビティ画面で、 **[更新]** を選択して表示を更新します。

   ![進行中の移行アクティビティ](media/tutorial-sql-server-to-managed-instance-online/dms-monitor-migration2.png)

    データベースとログインのカテゴリを展開して、該当するサーバー オブジェクトの移行状態を監視できます。

   ![移行アクティビティの状態](media/tutorial-sql-server-to-managed-instance-online/dms-monitor-migration-extend2.png)

## <a name="performing-migration-cutover"></a>移行カットオーバーの実行

SQL Managed Instance のターゲット インスタンスで完全なデータベース バックアップが復元されたら、データベースは移行カットオーバーを実行するために使用できます。

1. オンライン データベースの移行を完了する準備が整ったら、 **[カットオーバーの開始]** を選択します。

2. ソース データベースへの着信トラフィックをすべて停止します。

3. [ログ末尾のバックアップ] を採用し、バックアップ ファイルを SMB ネットワーク共有で使用できるようにしたら、この最後のトランザクション ログのバックアップが復元されるまで待機します。

    その時点で、 **[保留中の変更]** が 0 に設定されたことがわかります。

4. **[確認]** を選択したら、 **[適用]** を選択します。

    ![カットオーバー完了の準備](media/tutorial-sql-server-to-managed-instance-online/dms-complete-cutover.png)

    > [!IMPORTANT]
    > カットオーバーの後、Business Critical サービス レベルでの SQL Managed Instance の可用性だけは、AlwaysOn 高可用性グループに対して 3 つのセカンダリ レプリカをシードする必要があるため、General Purpose よりかなり長くかかることがあります。 この操作の所要時間は、データのサイズに依存します。詳細については、「[管理操作の所要時間](../azure-sql/managed-instance/management-operations-overview.md#duration)」を参照してください。

5. データベース移行の状態が **[完了]** と表示されたら、SQL Managed Instance の新しいターゲット インスタンスにアプリケーションを接続します。

    ![カットオーバー完了](media/tutorial-sql-server-to-managed-instance-online/dms-cutover-complete.png)

## <a name="next-steps"></a>次のステップ

* T-SQL RESTORE コマンドを使用して SQL Managed Instance にデータベースを移行する方法を示したチュートリアルについては、[復元コマンドを使用した SQL Managed Instance へのバックアップの復元](../azure-sql/managed-instance/restore-sample-database-quickstart.md)に関するページを参照してください。
* SQL Managed Instance については、「[Azure SQL Managed Instance とは](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)」を参照してください。
* SQL Managed Instance へのアプリの接続については、[アプリケーションの接続](../azure-sql/managed-instance/connect-application-instance.md)に関するページを参照してください。