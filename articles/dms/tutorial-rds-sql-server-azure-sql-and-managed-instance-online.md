---
title: チュートリアル:RDS SQL Server をオンラインで SQL Database に移行する
titleSuffix: Azure Database Migration Service
description: Azure Database Migration Service を使用して、RDS SQL Server から Azure SQL Database 単一データベースまたはマネージド インスタンスへのオンライン移行を実行する方法について学習します。
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 01/08/2020
ms.openlocfilehash: 20ca7f1d9c8322fe9a4d5dd784768bdaaf7cd0d7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85314934"
---
# <a name="tutorial-migrate-rds-sql-server-to-azure-sql-database-or-an-azure-sql-managed-instance-online-using-dms"></a>チュートリアル:DMS を使用して RDS SQL Server を Azure SQL Database または Azure SQL Managed Instance にオンラインで移行する

Azure Database Migration Service を使用すれば、RDS SQL Server インスタンスから [Azure SQL Database](https://docs.microsoft.com/azure/sql-database/) または [Azure SQL Managed Instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index) に最小限のダウンタイムでデータベースを移行できます。 このチュートリアルでは、Azure Database Migration Service を使用して、SQL Server 2012 (以降) の RDS SQL Server インスタンスに復元された **Adventureworks2012** データベースを SQL Database または SQL Managed Instance に移行します。

このチュートリアルでは、以下の内容を学習します。
> [!div class="checklist"]
> * Azure SQL Database または SQL Managed Instance でデータベースを作成する。 
> * Data Migration Assistant を使用して、サンプル スキーマを移行する。
> * Azure Database Migration Service のインスタンスを作成する。
> * Azure Database Migration Service を使用して移行プロジェクトを作成する。
> * 移行を実行する。
> * 移行を監視する。
> * 移行レポートをダウンロードする。

> [!NOTE]
> Azure Database Migration Service を使用してオンライン移行を実行するには、Premium 価格レベルに基づいてインスタンスを作成する必要があります。 詳しくは、Azure Database Migration Service の[価格](https://azure.microsoft.com/pricing/details/database-migration/)に関するページをご覧ください。

> [!IMPORTANT]
> 最適な移行エクスペリエンスのために、ターゲット データベースと同じ Azure リージョンに Azure Database Migration Service のインスタンスを作成することをお勧めします。 リージョンや地域をまたいでデータを移動する場合、移行プロセスが遅くなり、エラーが発生する可能性があります。

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

この記事では、RDS SQL Server から SQL Database または SQL Managed Instance へのオンライン移行について説明します。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下を実行する必要があります。

* [RDS SQL Server データベース](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_GettingStarted.CreatingConnecting.SQLServer.html)を作成します。
* [Azure portal で Azure SQL Database のデータベースを作成](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal)するか、または [SQL Managed Instance でデータベースを作成](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started)してから、**AdventureWorks2012** という名前の空のデータベースを作成します。 
* [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) (DMA) v3.3 以降をダウンロードしてインストールします。
* Azure Resource Manager デプロイ モデルを使用して、Azure Database Migration Service 用の Microsoft Azure 仮想ネットワークを作成します。 SQL Managed Instance に移行する場合は、SQL Managed Instance に使用されるのと同じ仮想ネットワーク内の異なるサブネットに DMS インスタンスを作成します。  または、DMS に異なる仮想ネットワークを使用する場合、2 つの仮想ネットワーク間に仮想ネットワーク ピアリングを作成する必要があります。 仮想ネットワークの作成方法の詳細については、[Virtual Network のドキュメント](https://docs.microsoft.com/azure/virtual-network/)を参照してください。特に、詳細な手順が記載されたクイックスタートの記事を参照してください。

    > [!NOTE]
    > 仮想ネットワークのセットアップ中、Microsoft へのネットワーク ピアリングに ExpressRoute を使用する場合は、サービスのプロビジョニング先となるサブネットに、次のサービス [エンドポイント](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)を追加してください。
    >
    > * ターゲット データベース エンドポイント (SQL エンドポイント、Cosmos DB エンドポイントなど)
    > * ストレージ エンドポイント
    > * サービス バス エンドポイント
    >
    > Azure Database Migration Service にはインターネット接続がないため、この構成が必要となります。 

* 仮想ネットワークのネットワーク セキュリティ グループの規則によって、Azure Database Migration Service への以下のインバウンド通信ポートが確実にブロックされないようにします。443、53、9354、445、12000。 仮想ネットワークの NSG トラフィックのフィルター処理の詳細については、[ネットワーク セキュリティ グループによるネットワーク トラフィックのフィルター処理](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)に関する記事を参照してください。
* [データベース エンジン アクセスのために Windows ファイアウォール](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)を構成します。
* Azure Database Migration Service がソースの SQL Server にアクセスできるように Windows ファイアウォールを開きます。既定では TCP ポート 1433 が使用されます。
* SQL Database の場合は、サーバー レベルの[ファイアウォール規則](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)を作成して、Azure Database Migration Service からターゲット データベースにアクセスできるようにします。 Azure Database Migration Service に使用する仮想ネットワークのサブネット範囲を指定します。
* ソース RDS SQL Server インスタンスへの接続に使用される資格情報が、"Processadmin" サーバー ロールのメンバーであり、移行対象のすべてのデータベースで "db_owner" データベース ロールのメンバーであるアカウントに、関連付けられていることを確認します。
* ターゲットのデータベースへの接続に使用される資格情報に、SQL Database 内のターゲットのデータベースに対する CONTROL DATABASE アクセス許可を確実に含め、SQL Managed Instance に移行する場合は、sysadmin ロールのメンバーを確実に含めます。
* ソース RDS SQL Server のバージョンは SQL Server 2012 以降である必要があります。 SQL Server インスタンスが実行されているバージョンを確認する方法については、「[バージョン、エディション、および SQL Server の更新プログラム レベルとそのコンポーネントを確認する方法](https://support.microsoft.com/help/321185/how-to-determine-the-version-edition-and-update-level-of-sql-server-an)」の記事を参照してください。
* RDS SQL Server データベースおよび移行対象に選択されているすべてのユーザー テーブルで、変更データ キャプチャ (CDC) を有効にします。
    > [!NOTE]
    > 次のスクリプトを使用して、RDS SQL Server データベースで CDC を有効にすることができます。
    ```
    exec msdb.dbo.rds_cdc_enable_db 'AdventureWorks2012'
    ```
    > 次のスクリプトを使用して、すべてのテーブルで CDC を有効にすることができます。
    ```
    use <Database name>
    go
    exec sys.sp_cdc_enable_table 
    @source_schema = N'Schema name', 
    @source_name = N'table name', 
    @role_name = NULL, 
    @supports_net_changes = 1 --for PK table 1, non PK tables 0
    GO
    ```
* ターゲットのデータベースでデータベース トリガーを無効にします。
    > [!NOTE]
    > 次のクエリを使用すれば、ターゲットのデータベースでデータベース トリガーを見つけることができます。
    ```
    Use <Database name>
    go
    select * from sys.triggers
    DISABLE TRIGGER (Transact-SQL)
    ```
    詳細については、記事「[DISABLE TRIGGER (Transact-SQL) ](https://docs.microsoft.com/sql/t-sql/statements/disable-trigger-transact-sql?view=sql-server-2017)」を参照してください。

## <a name="migrate-the-sample-schema"></a>サンプル スキーマを移行する
スキーマを移行するには、DMA を使用します。

> [!NOTE]
> DMA で移行プロジェクトを作成する前に、SQL Database または SQL Managed Instance のデータベースが前提要件での説明に従って既にプロビジョニングされていることを確認してください。 このチュートリアルでは、データベースの名前を **AdventureWorks2012** にしていますが、任意の名前を指定できます。

**AdventureWorks2012** スキーマを移行するには、次の手順を行います。

1. Data Migration Assistant で、新規 (+) アイコンを選択し、 **[プロジェクト タイプ]** で **[移行]** を選択します。
2. プロジェクト名を指定し、 **[Source server type]\(ソース サーバーの種類\)** テキスト ボックスで **SQL Server** を選択した後、 **[ターゲット サーバーの種類]** テキスト ボックスで **Azure SQL Database** を選択します。

    > [!NOTE]
    > Azure SQL Database と SQL Managed Instance のどちらに移行する場合でも、[ターゲット サーバーの種類] で **[Azure SQL Database]** を選択します。

3. **[移行スコープ]** で、 **[Schema only]\(スキーマのみ\)** を選択します。

    上記の手順を実行すると、DMA のインターフェイスの表示が次の図のようになります。

    ![Data Migration Assistant プロジェクトを作成する](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-create-project.png)

4. **[作成]** を選択してプロジェクトを作成します。
5. DMA で、SQL Server のソース接続詳細を指定し、 **[接続]** を選択した後、**AdventureWorks2012** データベースを選択します。

    ![Data Migration Assistant のソース接続詳細](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-source-connect.png)

6. **[次へ]** を選択し、 **[ターゲット サーバーに接続します]** で SQL Database または SQL Managed Instance のデータベースへのターゲット接続の詳細を指定し、 **[接続]** を選択してから、事前にプロビジョニングした **[AdventureWorksAzure]** データベースを選択します。

    ![Data Migration Assistant のターゲット接続詳細](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-target-connect.png)

7. **[次へ]** を選択して **[オブジェクトの選択]** 画面に進みます。そこでは、デプロイする必要がある **AdventureWorks2012** データベース内のスキーマ オブジェクトを指定できます。

    既定では、すべてのオブジェクトが選択されています。

    ![SQL スクリプトを生成する](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-assessment-source.png)

8. **[SQL スクリプトの生成]** を選択して SQL スクリプトを作成し、スクリプトにエラーがないかを確認します。

    ![スキーマ スクリプト](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-schema-script.png)

9. **[スキーマをデプロイする]** を選択してスキーマをデプロイします。スキーマがデプロイされたら、ターゲットに異常がないかをチェックします。

    ![スキーマをデプロイする](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-schema-deploy.png)

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Microsoft.DataMigration リソース プロバイダーを登録する

1. Azure portal にサインインし、 **[すべてのサービス]** を選択し、 **[サブスクリプション]** を選択します。

   ![ポータルのサブスクリプションの表示](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/portal-select-subscription1.png)

2. Azure Database Migration Service のインスタンスを作成するサブスクリプションを選択して、 **[リソース プロバイダー]** を選択します。

    ![リソース プロバイダーの表示](media/tutorial-sql-server-to-azure-sql-online/portal-select-resource-provider.png)

3. 移行を検索し、**Microsoft.DataMigration** の右側にある **[登録]** を選択します。

    ![リソース プロバイダーの登録](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>インスタンスを作成する

1. Azure portal で **[+ リソースの作成]** を選択し、Azure Database Migration Service を検索して、ドロップダウン リストから **[Azure Database Migration Service]** を選択します。

    ![Azure Marketplace](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/portal-marketplace.png)

2. **[Azure Database Migration Service]** 画面で、 **[作成]** を選択します。

    ![Azure Database Migration Service インスタンスを作成する](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-create1.png)
  
3. **[移行サービスの作成]** 画面で、サービスの名前、サブスクリプション、新規または既存のリソース グループを指定します。

4. Azure Database Migration Service のインスタンスを作成する場所を選択します。 

5. 既存の仮想ネットワークを選択するか、新しく作成します。

    この仮想ネットワークによって、Azure Database Migration Service に、ソースの SQL Server とターゲットの SQL Database または SQL Managed Instance へのアクセスが提供されます。

    Azure portal で仮想ネットワークを作成する方法の詳細については、「[Azure portal を使用した仮想ネットワークの作成](https://aka.ms/DMSVnet)」を参照してください。

6. 価格レベルを選択します。このオンライン移行では、Premium 価格レベルを必ず選択します。

    コストと価格レベルの詳細については、[価格に関するページ](https://aka.ms/dms-pricing)を参照してください。

     ![Azure Database Migration Service インスタンス設定を構成する](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-settings3.png)

7. **[作成]** を選択して、サービスを作成します。

## <a name="create-a-migration-project"></a>移行プロジェクトを作成する

サービスが作成されたら、Azure portal 内でそのサービスを探して開き、新しい移行プロジェクトを作成します。

1. Azure ポータルで、 **[All services]\(すべてのサービス\)** を選択し、Azure Database Migration Service を検索して、**Azure Database Migration Service** を選択します。

      ![Azure Database Migration Service のすべてのインスタンスを検索する](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-search.png)

2. **[Azure Database Migration Services]** 画面で、作成した Azure Database Migration Service インスタンスの名前を検索して選択します。

     ![Azure Database Migration Service のインスタンスを検索する](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-instance-search.png)

3. **[+ 新しい移行プロジェクト]** を選択します。
4. **[新しい移行プロジェクト]** 画面で、プロジェクトの名前を指定し、 **[ソース サーバーの種類]** テキスト ボックスで **[AWS RDS for SQL Server]** を選択した後、 **[ターゲット サーバーの種類]** テキスト ボックスで **[Azure SQL Database]** を選択します。

    > [!NOTE]
    > SQL Database と SQL Managed Instance のどちらに移行する場合でも、[ターゲット サーバーの種類] で **[Azure SQL Database]** を選択します。

5. **[アクティビティの種類を選択します]** セクションで、 **[オンライン データの移行]** を選択します。

    > [!IMPORTANT]
    > 必ず **[オンライン データの移行]** を選択してください。オフライン移行は、このシナリオではサポートされていません。

    ![Database Migration Service プロジェクトを作成する](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-create-project4.png)

    > [!NOTE]
    > または、 **[プロジェクトのみを作成します]** を選択して移行プロジェクトを作成しておき、移行は後で実行することもできます。

6. **[保存]** を選択します。

7. **[アクティビティの作成と実行]** を選択してプロジェクトを作成し、移行アクティビティを実行します。

    ![データベース移行サービス アクティビティの作成と実行](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-create-and-run-activity1.png)

## <a name="specify-source-details"></a>ソース詳細を指定する

1. **[移行ソースの詳細]** 画面で、ソース SQL Server インスタンスの接続の詳細を指定します。

    ソース SQL Server インスタンス名には、必ず完全修飾ドメイン名 (FQDN) を使用してください。

2. 信頼できる証明書をソース サーバーにインストールしていない場合は、 **[サーバー証明書を信頼する]** チェック ボックスをオンにします。

    信頼できる証明書がインストールされていない場合、SQL Server はインスタンスの開始時に自己署名証明書を生成します。 この証明書は、クライアント接続の資格情報の暗号化に使用されます。

    > [!CAUTION]
    > 自己署名証明書を使用して暗号化される TLS 接続では、強固なセキュリティが提供されません。 man-in-the-middle (中間者) 攻撃を受ける可能性が高くなります。 実稼働環境やインターネットに接続しているサーバーでは、自己署名証明書を使用した TLS 接続は使用しないでください。

   ![ソースの詳細](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-source-details3.png)

## <a name="specify-target-details"></a>ターゲット詳細を指定する

1. **[保存]** を選択してから、 **[移行のターゲットの詳細]** 画面上で、Azure 内のターゲット データベースに対する接続の詳細を指定します。

    ![ターゲットを選択する](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-select-target3.png)

2. **[保存]** を選択し、 **[ターゲット データベースへマッピング]** 画面で、移行用のソース データベースとターゲット データベースをマップします。

    ターゲット データベースにソース データベースと同じデータベース名が含まれている場合、Azure Database Migration Service は既定でターゲット データベースを選択します。

    ![ターゲット データベースにマップする](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-map-targets-activity4.png)

3. **[保存]** を選択し、 **[テーブルの選択]** 画面でテーブルの一覧を展開して、影響を受けるフィールドの一覧を確認します。

    Azure Database Migration Service では、ターゲットのデータベースに存在する空のソース テーブルがすべて自動的に選択されます。 データが既に含まれているテーブルを再移行する場合は、この画面でテーブルを明示的に選択する必要があります。

    ![テーブルを選択する](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-configure-setting-activity4.png)

4. 次の **[オンライン移行の詳細設定]** を設定した後、 **[保存]** を選択します。

    | 設定 | 説明 |
    | ------------- | ------------- |
    | **同時に読み込むテーブルの最大数** | 移行の間に DMS で並列に実行されるテーブルの数を指定します。 既定値は 5 ですが、POC の移行に基づいて特定の移行ニーズに合った最適な値に設定できます。 |
    | **ソース テーブルが切り捨てられる場合** | 移行の間に、DMS でターゲット テーブルを切り捨てるかどうかを指定します。 この設定は、移行プロセスの一部として 1 つまたは複数のテーブルが切り捨てられる場合に役に立つことがあります。 |
    | **ラージ オブジェクト (LOB) データの設定を構成する** | DMS で移行される LOB データを無制限にするか、または移行される LOB データを特定のサイズに制限するかを指定します。  移行される LOB データに制限がある場合、その制限を超えるすべての LOB データは切り捨てられます。 運用環境の移行では、 **[無制限の LOB サイズを許可する]** を選択してデータ損失を防ぐことをお勧めします。 無制限の LOB サイズの許可を指定する場合は、 **[LOB サイズが以下の (KB) よりも小さい場合は、単一ブロックに LOB データを移行します]** チェック ボックスをオンにして、パフォーマンスを向上させます。 |

    ![オンライン移行の詳細設定を指定する](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-advanced-online-migration-settings.png)

5. **[保存]** を選択し、 **[移行の概要]** 画面で、 **[アクティビティ名]** テキスト ボックスに移行アクティビティの名前を指定します。概要を見直して、ソースとターゲットの詳細が先ほど指定した内容と一致していることを確認します。

    ![移行の概要](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-migration-summary.png)

## <a name="run-the-migration"></a>移行を実行する

* **[移行の実行]** を選択します。

    移行アクティビティ ウィンドウが表示されます。アクティビティの **[状態]** は **[初期化中]** になります。

    ![アクティビティの状態 - 初期化中](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-activity-status2.png)

## <a name="monitor-the-migration"></a>移行を監視する

1. 移行アクティビティ画面で、移行の **[状態]** が **[実行中]** になるまで **[最新の情報に更新]** を選択して表示を更新します。

2. 特定のデータベースを選択して、**データ全体の読み込み**操作と**増分データ同期**操作の移行状態を取得します。

    ![アクティビティの状態 - 進行中](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-activity-in-progress.png)

## <a name="perform-migration-cutover"></a>一括移行を実行する

初回の全体の読み込みが完了すると、データベースは **[一括準備完了]** とマークされます。

1. データベースの移行を完了する準備ができたら、 **[一括で開始]** を選択します。

    ![一括で開始](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-start-cutover.png)

2. ソース データベースに対するすべての受信トランザクションを必ず停止してください。 **[保留中の変更]** カウンターが **0** を示すまで待ってください。
3. **[確認]** を選択し、 **[適用]** を選択します。
4. データベースの移行の状態として **[完了]** が表示されたら、アプリケーションを新しいターゲット データベースに接続します。

    ![アクティビティの状態 - 完了](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-activity-completed.png)

## <a name="next-steps"></a>次のステップ

* Azure へのオンライン移行を実行する場合の既知の問題と制限事項については、[オンライン移行に伴う既知の問題と回避策](known-issues-azure-sql-online.md)に関する記事を参照してください。
* Database Migration Service については、[Database Migration Service の概要](https://docs.microsoft.com/azure/dms/dms-overview)に関する記事を参照してください。
* SQL Database については、[SQL Database サービスの概要](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview)に関する記事を参照してください。
* SQL Managed Instance については、[SQL Managed Instance とは](https://docs.microsoft.com/azure/azure-sql/managed-instance/sql-managed-instance-paas-overview)に関する記事を参照してください。
