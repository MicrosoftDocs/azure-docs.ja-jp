---
title: チュートリアル:Azure Database Migration Service を使用して SQL Server から Azure SQL Database の単一データベース/プールされたデータベースにオンラインで移行する | Microsoft Docs
description: Azure Database Migration Service を使用して、オンプレミスの SQL Server から Azure SQL Database の単一データベースまたはプールされたデータベースへのオンライン移行を実行する方法について学習します。
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 07/09/2019
ms.openlocfilehash: e5666a64e4160964e2c1b35707a0f064edb72460
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706904"
---
# <a name="tutorial-migrate-sql-server-to-a-single-database-or-pooled-database-in-azure-sql-database-online-using-dms"></a>チュートリアル:DMS を使用して SQL Server を Azure SQL Database の単一データベースまたはプールされたデータベースにオンラインで移行する

Azure Database Migration Service を使用して、最短のダウンタイムでオンプレミスの SQL Server インスタンスから [Azure SQL Database](https://docs.microsoft.com/azure/sql-database/) にデータベースを移行することができます。 このチュートリアルでは、Azure Database Migration Service を使用して、SQL Server 2016 (以降) のオンプレミス インスタンスに復元された **Adventureworks2012** データベースを Azure SQL Database の単一データベースまたはプールされたデータベースに移行します。

このチュートリアルでは、以下の内容を学習します。
> [!div class="checklist"]
> - Data Migration Assistant を使用して、オンプレミス データベースを評価する。
> - Data Migration Assistant を使用して、サンプル スキーマを移行する。
> - Azure Database Migration Service のインスタンスを作成する。
> - Azure Database Migration Service を使用して移行プロジェクトを作成する。
> - 移行を実行する。
> - 移行を監視する。
> - 移行レポートをダウンロードする。

> [!NOTE]
> Azure Database Migration Service を使用してオンライン移行を実行するには、Premium 価格レベルに基づいてインスタンスを作成する必要があります。 詳しくは、Azure Database Migration Service の[価格](https://azure.microsoft.com/pricing/details/database-migration/)に関するページをご覧ください。

> [!IMPORTANT]
> 最適な移行エクスペリエンスのために、ターゲット データベースと同じ Azure リージョンに Azure Database Migration Service のインスタンスを作成することをお勧めします。 リージョンや地域をまたいでデータを移動する場合、移行プロセスが遅くなり、エラーが発生する可能性があります。

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

この記事では、SQL Server から Azure SQL Database の単一データベースまたはプールされたデータベースへのオンライン移行について説明します。 オフライン移行については、「[DMS を使用して SQL Server を Azure SQL Database にオフラインで移行する](tutorial-sql-server-to-azure-sql.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下を実行する必要があります。

- [SQL Server 2012 以降](https://www.microsoft.com/sql-server/sql-server-downloads)をダウンロードしてインストールします。
- SQL Server Express のインストール時に既定では無効になっている TCP/IP プロトコルを有効にします。有効にする手順については、[サーバー ネットワーク プロトコルの有効化または無効化](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure)に関する記事を参照してください。
- 「[Azure portal を使用して Azure SQL Database で単一データベースを作成する](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-get-started)」の詳細な手順に従って、Azure SQL Database の単一 (またはプールされた) データベースを作成します。

    > [!NOTE]
    > SQL Server Integration Services (SSIS) を使用していて、SSIS プロジェクト/パッケージ (SSISDB) のカタログ データベースを SQL Server から Azure SQL Database に移行する場合は、SSIS を Azure Data Factory (ADF) にプロビジョニングしたときに移行先 SSISDB が自動的に作成および管理されます。 SSIS パッケージの移行の詳細については、記事「[SQL Server Integration Services パッケージを Azure に移行する](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages)」を参照してください。

- [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) (DMA) v3.3 以降をダウンロードしてインストールします。
- Azure Resource Manager デプロイ モデルを使用して、Azure Database Migration Service 用の Azure 仮想ネットワーク (VNet) を作成します。これで、[ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) または [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) を使用したオンプレミスのソース サーバーとのサイト間接続を確立します。 VNet の作成方法の詳細については、[Virtual Network のドキュメント](https://docs.microsoft.com/azure/virtual-network/)を参照してください。特に、詳細な手順が記載されたクイックスタートの記事を参照してください。

    > [!NOTE]
    > VNet のセットアップ中、Microsoft へのネットワーク ピアリングに ExpressRoute を使用する場合は、サービスのプロビジョニング先となるサブネットに、次のサービス [エンドポイント](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)を追加してください。
    > - ターゲット データベース エンドポイント (SQL エンドポイント、Cosmos DB エンドポイントなど)
    > - ストレージ エンドポイント
    > - サービス バス エンドポイント
    >
    > Azure Database Migration Service にはインターネット接続がないため、この構成が必要となります。

- VNet ネットワーク セキュリティ グループの規則によって、Azure Database Migration Service への以下のインバウンド通信ポートが確実にブロックされないようにします:443、53、9354、445、12000。 Azure VNet NSG トラフィックのフィルター処理の詳細については、[ネットワーク セキュリティ グループによるネットワーク トラフィックのフィルター処理](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)に関する記事を参照してください。
- [データベース エンジン アクセスのために Windows ファイアウォール](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)を構成します。
- Azure Database Migration Service がソースの SQL Server にアクセスできるように Windows ファイアウォールを開きます。既定では TCP ポート 1433 が使用されます。
- 動的ポートを使用して複数の名前付き SQL Server インスタンスを実行している場合は、SQL Browser サービスを有効にし、ファイアウォール経由の UDP ポート 1434 へのアクセスを許可することをお勧めします。これにより、Azure Database Migration Service はソース サーバー上の名前付きインスタンスに接続できるようになります。
- ソース データベースの前でファイアウォール アプライアンスを使用する場合は、Azure Database Migration Service が移行のためにソース データベースにアクセスできるように、ファイアウォール規則を追加することが必要な場合があります。
- Azure SQL Database サーバーのサーバー レベルの[ファイアウォール規則](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)を作成して、Azure Database Migration Service がターゲット データベースにアクセスできるようにします。 Azure Database Migration Service に使用される VNet のサブネット範囲を指定します。
- ソースの SQL Server インスタンスへの接続に使用される資格情報に、[CONTROL SERVER](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql) アクセス許可を含めます。
- ターゲットの Azure SQL データベース インスタンスへの接続に使用される資格情報に、ターゲットの Azure SQL データベースに対する CONTROL DATABASE アクセス許可を含めます。
- ソース SQL Server のバージョンは SQL Server 2005 以降である必要があります。 SQL Server インスタンスが実行されているバージョンを確認する方法については、「[バージョン、エディション、および SQL Server の更新プログラム レベルとそのコンポーネントを確認する方法](https://support.microsoft.com/help/321185/how-to-determine-the-version-edition-and-update-level-of-sql-server-an)」の記事を参照してください。
- データベースは、一括ログ復旧モードか完全復旧モードのいずれかでなければなりません。 SQL Server インスタンス用に構成された回復モデルを確認する方法については、[データベースの回復モデルを表示または変更する方法 (SQL Server)](https://docs.microsoft.com/sql/relational-databases/backup-restore/view-or-change-the-recovery-model-of-a-database-sql-server?view=sql-server-2017) に関する記事を参照してください。
- データベースの完全データベース バックアップを必ず作成してください。 完全データベース バックアップを作成するには、「[データベースの完全バックアップを作成する方法 (Transact-SQL)](https://docs.microsoft.com/previous-versions/sql/sql-server-2008-r2/ms191304(v=sql.105))」の記事を参照してください。
- いずれかのテーブルに主キーがない場合は、データベースと特定のテーブルで変更データ キャプチャ (CDC) を有効にします。
    > [!NOTE]
    > 以下のスクリプトを使用すると、主キーがないテーブルを見つけることができます。

    ```sql
    USE <DBName>;
    go
    SELECT is_tracked_by_cdc, name AS TableName
    FROM sys.tables WHERE type = 'U' and is_ms_shipped = 0 AND
    OBJECTPROPERTY(OBJECT_ID, 'TableHasPrimaryKey') = 0;
     ```

    >結果に 'is_tracked_by_cdc' が '0' のテーブルが 1 つ以上表示される場合は、「[変更データ キャプチャの有効化と無効化 (SQL Server)](https://docs.microsoft.com/sql/relational-databases/track-changes/enable-and-disable-change-data-capture-sql-server?view=sql-server-2017)」の記事で説明されているプロセスを使用して、データベースと特定のテーブルの変更キャプチャを有効にします。

- ソース SQL Server のディストリビューター ロールを構成します。

    >[!NOTE]
    > 以下のクエリを使用して、レプリケーション コンポーネントがインストールされているかどうかを判断できます。

    ```sql
    USE master;
    DECLARE @installed int;
    EXEC @installed = sys.sp_MS_replication_installed;
    SELECT @installed as installed;
    ```

    結果としてエラー メッセージが返されて、レプリケーション コンポーネントをインストールするよう提案された場合は、[SQL Server レプリケーションのインストール](https://docs.microsoft.com/sql/database-engine/install-windows/install-sql-server-replication?view=sql-server-2017)の記事のプロセスを使用して SQL Server レプリケーション コンポーネントをインストールします。

    レプリケーションが既にインストールされている場合は、次の T-SQL コマンドを使用して、ソース SQL Server にディストリビューション ロールが構成されているかどうかを確認します。

    ```sql
    EXEC sp_get_distributor;
    ```

    ディストリビューションが設定されていない場合、前述のコマンド出力に対してディストリビューション サーバーに NULL が表示される場合は、[ディストリビューションの構成](https://docs.microsoft.com/sql/relational-databases/replication/configure-publishing-and-distribution?view=sql-server-2017)に関する記事で説明されているガイダンスを参照して、ディストリビューションを構成します。

- ターゲットの Azure SQL Database でデータベース トリガーを無効にします。
    >[!NOTE]
    > 次のクエリを使用して、ターゲットの Azure SQL Database でデータベース トリガーを見つけることができます。

    ```sql
    Use <Database name>
    select * from sys.triggers
    DISABLE TRIGGER (Transact-SQL)
    ```

    詳細については、記事「[DISABLE TRIGGER (Transact-SQL) ](https://docs.microsoft.com/sql/t-sql/statements/disable-trigger-transact-sql?view=sql-server-2017)」を参照してください。 

## <a name="assess-your-on-premises-database"></a>オンプレミス データベースを評価する

オンプレミスの SQL Server インスタンスから Azure SQL Database の単一データベースまたはプールされたデータベースにデータを移行する前に、SQL Server データベースを評価して、移行を妨げる問題がないかどうかを確認する必要があります。 Data Migration Assistant v3.3 以降を使用し、[SQL Server の移行評価の実行](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem)に関する記事の手順に従って、オンプレミス データベースの評価を完了してください。

オンプレミス データベースを評価するには、次の手順を実行します。

1. DMA で、新規 (+) アイコンを選択し、 **[評価]** プロジェクト タイプを選択します。
2. プロジェクト名を指定し、 **[Source server type]\(ソース サーバーの種類\)** テキスト ボックスで **SQL Server**、 **[ターゲット サーバーの種類]** テキスト ボックスで **Azure SQL Database** を選択した後、 **[作成]** を選択してプロジェクトを作成します。

    Azure SQL Database の単一データベースまたはプールされたデータベースに移行するソース SQL Server データベースを評価する際には、次のいずれかまたは両方の評価レポート タイプを選択できます。

   - データベース互換性をチェックする
   - 機能の類似性をチェックする

     どちらのレポート タイプも、既定で選択されています。

3. DMA の **[オプション]** 画面で **[次へ]** を選択します。
4. **[ソースの選択]** 画面の **[サーバーへの接続]** ダイアログ ボックスで、SQL Server への接続詳細を入力し、 **[接続]** を選択します。
5. **[Add sources]\(ソースの追加\)** ダイアログ ボックスで、**AdventureWorks2012**、 **[追加]** 、 **[Start Assessment]\(評価の開始\)** の順に選択します。

    > [!NOTE]
    > SSIS を使用する場合、現在、DMA ではソース SSISDB の評価はサポートされていません。 ただし、SSIS プロジェクト/パッケージは、Azure SQL Database によってホストされている移行先 SSISDB に再デプロイされるため、評価/検証されます。 SSIS パッケージの移行の詳細については、記事「[SQL Server Integration Services パッケージを Azure に移行する](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages)」を参照してください。

    評価が完了すると、次のグラフィックに結果が表示されます。

    ![データ移行の評価](media/tutorial-sql-server-to-azure-sql-online/dma-assessments.png)

    評価では、Azure SQL Database の単一データベースまたはプールされたデータベースについて、単一データベースまたはプールされたデータベースへのデプロイでの機能パリティの問題と移行の障害となっている問題が特定されます。

    - **SQL Server の機能類似性**カテゴリでは、幅広い推奨事項や、Azure で利用できる代替アプローチ、および移行プロジェクトの計画に役立つ移行手順を確認できます。
    - **互換性問題**カテゴリでは、部分的にサポートされている機能やサポートされていない機能を確認できます。この情報は、オンプレミスの SQL Server データベースから Azure SQL Database への移行を妨げる可能性がある互換性の問題を反映しています。 また、それらの問題への対処に役立つ推奨事項も確認できます。

6. 特定のオプションを選択して、移行を妨げる問題と機能の類似性の問題についての評価結果をレビューします。

## <a name="migrate-the-sample-schema"></a>サンプル スキーマを移行する

評価結果をレビューし、選択したデータベースが Azure SQL Database の単一データベースまたはプールされたデータベースに移行可能であることを確認したら、DMA を使用してスキーマを Azure SQL Database に移行します。

> [!NOTE]
> DMA で移行プロジェクトを作成する前に、Azure SQL データベースが前提要件での説明に従って既にプロビジョニングされていることを確認してください。 このチュートリアルでは、Azure SQL Database の名前を **AdventureWorksAzure** としていますが、任意の名前を指定することもできます。

> [!IMPORTANT]
> SSIS を使用する場合、現在 DMA ではソース SSISDB の移行はサポートされていませんが、Azure SQL Database によってホストされている移行先 SSISDB に SSIS プロジェクト/パッケージを再デプロイすることはできます。 SSIS パッケージの移行の詳細については、記事「[SQL Server Integration Services パッケージを Azure に移行する](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages)」を参照してください。

**AdventureWorks2012** スキーマを Azure SQL Database の単一データベースまたはプールされたデータベースに移行するには、次の手順を実行します。

1. Data Migration Assistant で、新規 (+) アイコンを選択し、 **[プロジェクト タイプ]** で **[移行]** を選択します。
2. プロジェクト名を指定し、 **[Source server type]\(ソース サーバーの種類\)** テキスト ボックスで **SQL Server** を選択した後、 **[ターゲット サーバーの種類]** テキスト ボックスで **Azure SQL Database** を選択します。
3. **[移行スコープ]** で、 **[Schema only]\(スキーマのみ\)** を選択します。

    上記の手順を実行すると、DMA のインターフェイスの表示が次の図のようになります。

    ![Data Migration Assistant プロジェクトを作成する](media/tutorial-sql-server-to-azure-sql-online/dma-create-project.png)

4. **[作成]** を選択してプロジェクトを作成します。
5. DMA で、SQL Server のソース接続詳細を指定し、 **[接続]** を選択した後、**AdventureWorks2012** データベースを選択します。

    ![Data Migration Assistant のソース接続詳細](media/tutorial-sql-server-to-azure-sql-online/dma-source-connect.png)

6. **[次へ]** を選択し、 **[Connect to target server]\(対象サーバーへの接続\)** で Azure SQL データベースのターゲット接続の詳細を指定し、 **[接続]** を選択し、Azure SQL Database で事前プロビジョニングした **AdventureWorksAzure** データベースを選択します。

    ![Data Migration Assistant のターゲット接続詳細](media/tutorial-sql-server-to-azure-sql-online/dma-target-connect.png)

7. **[次へ]** を選択して **[オブジェクトの選択]** 画面に進みます。この画面では、Azure SQL Database にデプロイする必要がある **AdventureWorks2012** データベース内のスキーマ オブジェクトを指定できます。

    既定では、すべてのオブジェクトが選択されています。

    ![SQL スクリプトを生成する](media/tutorial-sql-server-to-azure-sql-online/dma-assessment-source.png)

8. **[SQL スクリプトの生成]** を選択して SQL スクリプトを作成し、スクリプトにエラーがないかを確認します。

    ![スキーマ スクリプト](media/tutorial-sql-server-to-azure-sql-online/dma-schema-script.png)

9. **[Deploy schema]\(スキーマのデプロイ\)** を選択して Azure SQL データベースにスキーマをデプロイし、スキーマがデプロイされたら、ターゲット サーバーに異常がないかをチェックします。

    ![スキーマをデプロイする](media/tutorial-sql-server-to-azure-sql-online/dma-schema-deploy.png)

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Microsoft.DataMigration リソース プロバイダーを登録する

1. Azure portal にサインインし、 **[すべてのサービス]** を選択し、 **[サブスクリプション]** を選択します。

   ![ポータルのサブスクリプションの表示](media/tutorial-sql-server-to-azure-sql-online/portal-select-subscription1.png)

2. Azure Database Migration Service のインスタンスを作成するサブスクリプションを選択して、 **[リソース プロバイダー]** を選択します。

    ![リソース プロバイダーの表示](media/tutorial-sql-server-to-azure-sql-online/portal-select-resource-provider.png)

3. 移行を検索し、**Microsoft.DataMigration** の右側にある **[登録]** を選択します。

    ![リソース プロバイダーの登録](media/tutorial-sql-server-to-azure-sql-online/portal-register-resource-provider.png)

## <a name="create-an-instance"></a>インスタンスを作成する

1. Azure portal で **[+ リソースの作成]** を選択し、Azure Database Migration Service を検索して、ドロップダウン リストから **[Azure Database Migration Service]** を選択します。

    ![Azure Marketplace](media/tutorial-sql-server-to-azure-sql-online/portal-marketplace.png)

2. **[Azure Database Migration Service]** 画面で、 **[作成]** を選択します。

    ![Azure Database Migration Service インスタンスを作成する](media/tutorial-sql-server-to-azure-sql-online/dms-create1.png)
  
3. **[移行サービスの作成]** 画面で、サービスの名前、サブスクリプション、新規または既存のリソース グループを指定します。

4. Azure Database Migration Service のインスタンスを作成する場所を選択します。 

5. 既存の VNet を選択するか、新しいものを作成します。

    この VNet が Azure Database Migration Service に、ソース SQL Server とターゲット Azure SQL データベース インスタンスへのアクセスを提供します。

    Azure portal で VNet を作成する方法の詳細については、「[Azure portal を使用した仮想ネットワークの作成](https://aka.ms/DMSVnet)」を参照してください。

6. 価格レベルを選択します。

    コストと価格レベルの詳細については、[価格に関するページ](https://aka.ms/dms-pricing)を参照してください。

    ![Azure Database Migration Service インスタンス設定を構成する](media/tutorial-sql-server-to-azure-sql-online/dms-settings2.png)

7. **[作成]** を選択して、サービスを作成します。

## <a name="create-a-migration-project"></a>移行プロジェクトを作成する

サービスが作成されたら、Azure portal 内でそのサービスを探して開き、新しい移行プロジェクトを作成します。

1. Azure ポータルで、 **[All services]\(すべてのサービス\)** を選択し、Azure Database Migration Service を検索して、**Azure Database Migration Service** を選択します。

    ![Azure Database Migration Service のすべてのインスタンスを検索する](media/tutorial-sql-server-to-azure-sql-online/dms-search.png)

2. **[Azure Database Migration Services]** 画面で、作成した Azure Database Migration Service インスタンスの名前を検索して選択します。

    ![Azure Database Migration Service のインスタンスを検索する](media/tutorial-sql-server-to-azure-sql-online/dms-instance-search.png)

3. **[+ 新しい移行プロジェクト]** を選択します。
4. **[新しい移行プロジェクト]** 画面で、プロジェクトの名前を指定し、 **[ソース サーバーの種類を選択する]** テキスト ボックスで **SQL Server** を選択した後、 **[ターゲット サーバーの種類]** テキスト ボックスで **Azure SQL Database** を選択します。
5. **[アクティビティの種類を選択します]** セクションで、 **[オンライン データの移行]** を選択します。

    ![Database Migration Service プロジェクトを作成する](media/tutorial-sql-server-to-azure-sql-online/dms-create-project3.png)

    > [!NOTE]
    > または、 **[プロジェクトのみを作成します]** を選択して移行プロジェクトを作成しておき、移行は後で実行することもできます。

6. **[保存]** を選択します。

7. **[アクティビティの作成と実行]** を選択してプロジェクトを作成し、移行アクティビティを実行します。

    ![データベース移行サービス アクティビティの作成と実行](media/tutorial-sql-server-to-azure-sql-online/dms-create-and-run-activity.png)

## <a name="specify-source-details"></a>ソース詳細を指定する

1. **[移行ソースの詳細]** 画面で、ソース SQL Server インスタンスの接続の詳細を指定します。

    ソース SQL Server インスタンス名には、必ず完全修飾ドメイン名 (FQDN) を使用してください。 DNS の名前解決ができない場合は、IP アドレスを使用することもできます。

2. 信頼できる証明書をソース サーバーにインストールしていない場合は、 **[サーバー証明書を信頼する]** チェック ボックスをオンにします。

    信頼できる証明書がインストールされていない場合、SQL Server はインスタンスの開始時に自己署名証明書を生成します。 この証明書は、クライアント接続の資格情報の暗号化に使用されます。

    > [!CAUTION]
    > 自己署名証明書を使用して暗号化されている SSL 接続のセキュリティは強力ではありません。 man-in-the-middle (中間者) 攻撃を受ける可能性が高くなります。 運用環境や、インターネットに接続されているサーバーでは、自己署名証明書を使用した SSL を信頼しないでください。

   ![ソースの詳細](media/tutorial-sql-server-to-azure-sql-online/dms-source-details3.png)

    > [!IMPORTANT]
    > SSIS を使用する場合、現在 DMS ではソース SSISDB の移行はサポートされていませんが、Azure SQL Database によってホストされている移行先 SSISDB に SSIS プロジェクト/パッケージを再デプロイすることはできます。 SSIS パッケージの移行の詳細については、記事「[SQL Server Integration Services パッケージを Azure に移行する](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages)」を参照してください。

## <a name="specify-target-details"></a>ターゲット詳細を指定する

1. **[保存]** を選択し、 **[Migration target details]\(移行ターゲットの詳細\)** 画面でターゲット Azure SQL Database サーバーの接続の詳細を指定します。これは、DMA を使用して **AdventureWorks2012** スキーマをデプロイした、事前プロビジョニング済みの Azure SQL Database です。

    ![ターゲットを選択する](media/tutorial-sql-server-to-azure-sql-online/dms-select-target3.png)

2. **[保存]** を選択し、 **[ターゲット データベースへマッピング]** 画面で、移行用のソース データベースとターゲット データベースをマップします。

    ターゲット データベースにソース データベースと同じデータベース名が含まれている場合、Azure Database Migration Service は既定でターゲット データベースを選択します。

    ![ターゲット データベースにマップする](media/tutorial-sql-server-to-azure-sql-online/dms-map-targets-activity3.png)

3. **[保存]** を選択し、 **[テーブルの選択]** 画面でテーブルの一覧を展開して、影響を受けるフィールドの一覧を確認します。

    Azure Database Migration Service では、ターゲット Azure SQL データベース インスタンスに存在する空のソース テーブルがすべて自動的に選択されます。 データが既に含まれているテーブルを再移行する場合は、このブレードでテーブルを明示的に選択する必要があります。

    ![テーブルを選択する](media/tutorial-sql-server-to-azure-sql-online/dms-configure-setting-activity3.png)

4. **[保存]** を選択し、 **[移行の概要]** 画面で、 **[アクティビティ名]** テキスト ボックスに移行アクティビティの名前を指定します。概要を見直して、ソースとターゲットの詳細が先ほど指定した内容と一致していることを確認します。

    ![移行の概要](media/tutorial-sql-server-to-azure-sql-online/dms-migration-summary.png)

## <a name="run-the-migration"></a>移行を実行する

- **[移行の実行]** を選択します。

    移行アクティビティ ウィンドウが表示されます。アクティビティの **[状態]** は **[初期化中]** になります。

    ![アクティビティの状態 - 初期化中](media/tutorial-sql-server-to-azure-sql-online/dms-activity-status2.png)

## <a name="monitor-the-migration"></a>移行を監視する

1. 移行アクティビティ画面で、移行の **[状態]** が **[実行中]** になるまで **[最新の情報に更新]** を選択して表示を更新します。

2. 特定のデータベースを選択して、**データ全体の読み込み**操作と**増分データ同期**操作の移行状態を取得します。

    ![アクティビティの状態 - 進行中](media/tutorial-sql-server-to-azure-sql-online/dms-activity-in-progress.png)

## <a name="perform-migration-cutover"></a>一括移行を実行する

初回の全体の読み込みが完了すると、データベースは **[一括準備完了]** とマークされます。

1. データベースの移行を完了する準備ができたら、 **[一括で開始]** を選択します。

    ![一括で開始](media/tutorial-sql-server-to-azure-sql-online/dms-start-cutover.png)

2. ソース データベースに対するすべての受信トランザクションを必ず停止してください。 **[保留中の変更]** カウンターが **0** を示すまで待ってください。
3. **[確認]** を選択し、 **[適用]** を選択します。
4. データベースの移行の状態に **[完了]** が表示されたら、アプリケーションを新しいターゲット Azure SQL Database に接続します。

    ![アクティビティの状態 - 完了](media/tutorial-sql-server-to-azure-sql-online/dms-activity-completed.png)

## <a name="next-steps"></a>次の手順

- [Azure Data Migration Service (DMS) を使用した SQL 移行](https://www.microsoft.com/handsonlabs/SelfPacedLabs/?storyGuid=3b671509-c3cd-4495-8e8f-354acfa09587)に関するハンズオン ラボ。
- Azure SQL Database へのオンライン移行を実行する際の既知の問題と制限事項については、[Azure SQL Database のオンライン移行に伴う既知の問題と回避策](known-issues-azure-sql-online.md)に関する記事を参照してください。
- Azure Database Migration Service の詳細については、「[Azure Database Migration Service とは](https://docs.microsoft.com/azure/dms/dms-overview)」を参照してください。
- Azure SQL Database については、「[Azure SQL Database サービスとは](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview)」を参照してください。
