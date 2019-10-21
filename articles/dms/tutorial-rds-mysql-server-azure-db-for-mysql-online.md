---
title: チュートリアル:RDS MySQL の Azure Database for MySQL へのオンライン移行で Azure Database Migration Service を使用する | Microsoft Docs
description: Azure Database Migration Service を使用する RDS MySQL から Azure Database for MySQL へのオンライン移行の実行について学習します。
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 09/21/2019
ms.openlocfilehash: 9bd620ef9664e921aa88792017585b02e44387f8
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/20/2019
ms.locfileid: "71172693"
---
# <a name="tutorial-migrate-rds-mysql-to-azure-database-for-mysql-online-using-dms"></a>チュートリアル:DMS を使用して RDS MySQL を Azure Database for MySQL にオンラインで移行する

Azure Database Migration Service を使用して、RDS MySQL インスタンスから [Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/) にデータベースを移行することができます。移行中、ソース データベースはオンラインのままになります。 つまり、アプリケーションにとって最小限のダウンタイムで移行を実現できます。 このチュートリアルでは、Azure Database Migration Service のオンライン移行アクティビティを使用して、**Employees** サンプル データベースを RDS MySQL のインスタンスから Azure Database for MySQL に移行します。

このチュートリアルでは、以下の内容を学習します。
> [!div class="checklist"]
>
> * mysqldump および mysql ユーティリティを使用して、サンプル スキーマを移行する。
> * Azure Database Migration Service のインスタンスを作成する。
> * Azure Database Migration Service を使用して移行プロジェクトを作成する。
> * 移行を実行する。
> * 移行を監視する。

> [!NOTE]
> Azure Database Migration Service を使用してオンライン移行を実行するには、Premium 価格レベルに基づいてインスタンスを作成する必要があります。 詳しくは、Azure Database Migration Service の[価格](https://azure.microsoft.com/pricing/details/database-migration/)に関するページをご覧ください。

> [!IMPORTANT]
> 最適な移行エクスペリエンスのために、ターゲット データベースと同じ Azure リージョンに Azure Database Migration Service のインスタンスを作成することをお勧めします。 リージョンや地域をまたいでデータを移動する場合、移行プロセスが遅くなり、エラーが発生する可能性があります。

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

この記事では、RDS MySQL のインスタンスから Azure Database for MySQL へのオンライン移行を行う方法について説明します。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下を実行する必要があります。

* ソースの MySQL サーバーにより、サポートされている MySQL コミュニティ エディションが確実に実行されているようにします。 MySQL インスタンスのバージョンを確認するには、mysql ユーティリティまたは MySQL Workbench で、次のコマンドを実行します。

    ```
    SELECT @@version;
    ```

    詳細については、[サポートされている Azure Database for MySQL のバージョン](https://docs.microsoft.com/azure/mysql/concepts-supported-versions)に関する記事を参照してください。

* [MySQL **Employees** サンプル データベース](https://dev.mysql.com/doc/employee/en/employees-installation.html)をダウンロードしてインストールします。
* [Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/quickstart-create-mysql-server-database-using-azure-portal) のインスタンスを作成します。
* Azure Resource Manager デプロイ モデルを使用して、Azure Database Migration Service 用の Azure 仮想ネットワーク (VNet) を作成します。これで、[ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) または [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) を使用したオンプレミスのソース サーバーへのサイト間接続が提供されます。 VNet の作成方法の詳細については、[Virtual Network のドキュメント](https://docs.microsoft.com/azure/virtual-network/)、特に詳細な手順を提供するクイックスタートの記事を参照してください。
* VNet ネットワーク セキュリティ グループの規則によって、Azure Database Migration Service への以下のインバウンド通信ポートが確実にブロックされないようにします:443、53、9354、445、12000。 Azure VNet NSG トラフィックのフィルター処理の詳細については、[ネットワーク セキュリティ グループによるネットワーク トラフィックのフィルター処理](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)に関する記事を参照してください。
* データベース エンジン アクセスを許可するように [Windows ファイアウォール](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access) (または Linux ファイアウォール) を構成します。 MySQL サーバーの場合は、接続用にポート 3306 を許可します。
* Azure Database Migration Service でソース MySQL サーバーにアクセスできるように Windows ファイアウォールを開きます (既定の TCP ポートは 3306 です)。
* ソース データベースの前でファイアウォール アプライアンスを使用する場合は、Azure Database Migration Service が移行のためにソース データベースにアクセスできるように、ファイアウォール規則を追加することが必要な場合があります。
* Azure Database for MySQL サーバーのサーバーレベルの[ファイアウォール規則](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)を作成して、Azure Database Migration Service でターゲット データベースにアクセスできるようにします。 Azure Database Migration Service に使用される VNet のサブネット範囲を指定します。

> [!NOTE]
> Azure Database for MySQL では、InnoDB テーブルのみがサポートされます。 MyISAM テーブルを InnoDB に変換する場合は、[MyISAM から InnoDB へのテーブルの変換](https://dev.mysql.com/doc/refman/5.7/en/converting-tables-to-innodb.html)に関する記事を参照してください。

### <a name="set-up-aws-rds-mysql-for-replication"></a>レプリケーションのために AWS RDS MySQL を設定する

1. 新しいパラメーター グループを作成するには、「[MySQL Database Log Files](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_LogAccess.Concepts.MySQL.html)」 (MySQL データベース ログ ファイル) 記事の「**Binary Logging Format**」 (バイナリ ログ形式) セクションの AWS で提供される手順に従います。
2. 次の構成で新しいパラメーター グループを作成します。
    * binlog_format = row
    * binlog_checksum = NONE
3. 新しいパラメーター グループを保存します。

## <a name="migrate-the-schema"></a>スキーマを移行する

1. ソース データベースからスキーマを抽出し、テーブル スキーマ、インデックス、ストアド プロシージャなどのすべてのデータベース オブジェクトの移行を完了するためにターゲット データベースに適用します。

    スキーマのみを移行する最も簡単な方法は、--no-data パラメーターを指定した mysqldump を使用することです。 スキーマを移行するコマンドは、次のとおりです。

    ```
    mysqldump -h [servername] -u [username] -p[password] --databases [db name] --no-data > [schema file path]
    ```
    
    たとえば、**Employees** データベースのスキーマ ファイルをダンプするには、次のコマンドを使用します。
    
    ```
    mysqldump -h 10.10.123.123 -u root -p --databases employees --no-data > d:\employees.sql
    ```

2. ターゲット サービス (Azure Database for MySQL) にスキーマをインポートします。 スキーマのダンプ ファイルを復元するには、次のコマンドを実行します。

    ```
    mysql.exe -h [servername] -u [username] -p[password] [database]< [schema file path]
    ```

    たとえば、**Employees** データベースのスキーマをインポートするには、次のようにします。

    ```
    mysql.exe -h shausample.mysql.database.azure.com -u dms@shausample -p employees < d:\employees.sql
    ```

3. スキーマに外部キーが含まれている場合、移行の初回の読み込みと継続的同期は失敗します。 外部キー削除スクリプトを抽出し、ターゲット (Azure Database for MySQL) の外部キー スクリプトを追加するには、MySQL Workbench で次のスクリプトを実行します。

    ```
    SET group_concat_max_len = 8192;
        SELECT SchemaName, GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery, GROUP_CONCAT(AddQuery SEPARATOR ';\n') as AddQuery
        FROM
        (SELECT
        KCU.REFERENCED_TABLE_SCHEMA as SchemaName,
                    KCU.TABLE_NAME,
                    KCU.COLUMN_NAME,
                    CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' DROP FOREIGN KEY ', KCU.CONSTRAINT_NAME) AS DropQuery,
        CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' ADD CONSTRAINT ', KCU.CONSTRAINT_NAME, ' FOREIGN KEY (`', KCU.COLUMN_NAME, '`) REFERENCES `', KCU.REFERENCED_TABLE_NAME, '` (`', KCU.REFERENCED_COLUMN_NAME, '`) ON UPDATE ',RC.UPDATE_RULE, ' ON DELETE ',RC.DELETE_RULE) AS AddQuery
                    FROM INFORMATION_SCHEMA.KEY_COLUMN_USAGE KCU, information_schema.REFERENTIAL_CONSTRAINTS RC
                    WHERE
                      KCU.CONSTRAINT_NAME = RC.CONSTRAINT_NAME
                      AND KCU.REFERENCED_TABLE_SCHEMA = RC.UNIQUE_CONSTRAINT_SCHEMA
      AND KCU.REFERENCED_TABLE_SCHEMA = 'SchemaName') Queries
      GROUP BY SchemaName;
    ```

4. クエリ結果内の外部キー削除 (2 列目) を実行して、外部キーを削除します。

5. データにトリガー (insert または update トリガー) が含まれている場合は、ソースからデータをレプリケートする前にターゲットでデータの整合性が適用されます。 移行時は*ターゲットの*すべてのテーブル内のトリガーを無効にし、移行の完了後にトリガーを有効にすることをお勧めします。

    ターゲット データベース内のトリガーを無効にするには、次のように指定します。

    ```
    select concat ('alter table ', event_object_table, ' disable trigger ', trigger_name)
    from information_schema.triggers;
    ```

6. テーブル内に ENUM データ型のインスタンスがある場合は、ターゲット テーブルで一時的に 'character varying' データ型に更新することをお勧めします。 データのレプリケーションが完了したら、データ型を ENUM に戻します。

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Microsoft.DataMigration リソース プロバイダーを登録する

1. Azure portal にサインインし、 **[すべてのサービス]** を選択し、 **[サブスクリプション]** を選択します。

   ![ポータルのサブスクリプションの表示](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-select-subscription1.png)

2. Azure Database Migration Service のインスタンスを作成するサブスクリプションを選択してから、 **[リソース プロバイダー]** を選びます。

    ![リソース プロバイダーの表示](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-select-resource-provider.png)

3. 移行を検索し、**Microsoft.DataMigration** の右側にある **[登録]** を選択します。

    ![リソース プロバイダーの登録](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-register-resource-provider.png)

## <a name="create-an-instance-of-azure-database-migration-service"></a>Azure Database Migration Service のインスタンスを作成する

1. Azure portal で **[+ リソースの作成]** を選択し、Azure Database Migration Service を検索して、ドロップダウン リストから **[Azure Database Migration Service]** を選択します。

    ![Azure Marketplace](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-marketplace.png)

2. **[Azure Database Migration Service]** 画面で、 **[作成]** を選択します。

    ![Azure Database Migration Service インスタンスを作成する](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-create1.png)
  
3. **[移行サービスの作成]** 画面で、サービスの名前、サブスクリプション、新規または既存のリソース グループを指定します。

4. Azure Database Migration Service のインスタンスを作成する場所を選択します。

5. 既存の VNet を選択するか、新しいものを作成します。

    VNet によって、Azure Database Migration Service に、ソース MySQL インスタンスとターゲット Azure Database for MySQL インスタンスへのアクセスが提供されます。

    Azure portal で VNet を作成する方法の詳細については、[Azure portal を使用した仮想ネットワークの作成](https://aka.ms/DMSVnet)に関する記事を参照してください。

6. 価格レベルを選択します。このオンライン移行では、Premium:4 仮想コア価格レベルを選択してください。

    ![Azure Database Migration Service インスタンス設定を構成する](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-settings3.png)

7. **[作成]** を選択して、サービスを作成します。

## <a name="create-a-migration-project"></a>移行プロジェクトを作成する

サービスが作成されたら、Azure portal 内でそのサービスを探して開き、新しい移行プロジェクトを作成します。

1. Azure ポータルで、 **[All services]\(すべてのサービス\)** を選択し、Azure Database Migration Service を検索して、**Azure Database Migration Service** を選択します。

      ![Azure Database Migration Service のすべてのインスタンスを検索する](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-search.png)

2. **[Azure Database Migration Services]** 画面で、作成した Azure Database Migration Service インスタンスの名前を検索して選択します。

     ![Azure Database Migration Service のインスタンスを検索する](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-instance-search.png)

3. **[+ 新しい移行プロジェクト]** を選択します。
4. **[新しい移行プロジェクト]** 画面で、プロジェクトの名前を指定し、 **[ソース サーバーの種類]** テキスト ボックスで **[MySQL]** を選択してから、 **[ターゲット サーバーの種類]** テキスト ボックスで **[AzureDbForMySQL]** を選びます。
5. **[アクティビティの種類を選択します]** セクションで、 **[オンライン データの移行]** を選択します。

    > [!IMPORTANT]
    > 必ず **[オンライン データの移行]** を選択してください。オフライン移行は、このシナリオではサポートされていません。

    ![Database Migration Service プロジェクトを作成する](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-create-project6.png)

    > [!NOTE]
    > または、 **[プロジェクトのみを作成します]** を選択して移行プロジェクトを作成しておき、移行は後で実行することもできます。

6. **[保存]** を選択します。

7. **[アクティビティの作成と実行]** を選択してプロジェクトを作成し、移行アクティビティを実行します。

    > [!NOTE]
    > プロジェクトの作成ブレードでオンライン移行を設定するのに必要な前提条件をメモしておいてください。

## <a name="specify-source-details"></a>ソース詳細を指定する

* **[移行ソースの詳細]** 画面で、ソース MySQL インスタンスの接続の詳細を指定します。

   ![ソースの詳細](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-source-details5.png)

## <a name="specify-target-details"></a>ターゲット詳細を指定する

1. **[保存]** を選択し、 **[ターゲットの詳細]** 画面で、事前プロビジョニングされており、MySQLDump を使用して **Employees** スキーマがデプロイされている、ターゲットの Azure Database for MySQL サーバーの接続の詳細を指定します。

    ![ターゲットを選択する](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-select-target5.png)

2. **[保存]** を選択し、 **[ターゲット データベースへマッピング]** 画面で、移行用のソース データベースとターゲット データベースをマップします。

    ターゲット データベースにソース データベースと同じデータベース名が含まれている場合、Azure Database Migration Service では、既定でターゲット データベースが選択されます。

    ![ターゲット データベースにマップする](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-map-targets-activity5.png)

3. **[保存]** を選択し、 **[移行の概要]** 画面で、 **[アクティビティ名]** テキスト ボックスに移行アクティビティの名前を指定します。概要を見直して、ソースとターゲットの詳細が先ほど指定した内容と一致していることを確認します。

    ![移行の概要](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-migration-summary2.png)

## <a name="run-the-migration"></a>移行を実行する

* **[移行の実行]** を選択します。

    移行アクティビティ ウィンドウが表示されます。アクティビティの **[状態]** は **[初期化中]** になります。

## <a name="monitor-the-migration"></a>移行を監視する

1. 移行アクティビティ画面で、移行の **[状態]** が **[実行中]** になるまで **[最新の情報に更新]** を選択して表示を更新します。

    ![アクティビティの状態 - 実行中](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-activity-status4.png)

2. **[データベース名]** で、特定のデータベースを選択して、 **[データ全体の読み込み]** 操作と **[増分データ同期]** 操作の移行状態を取得します。

    **データ全体の読み込み** には初回の読み込みの移行状態が表示され、**増分データ同期** には変更データ キャプチャ (CDC) の状態が表示されます。

    ![インベントリ画面 - データ全体の読み込み](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-inventory-full-load.png)

    ![インベントリ画面 - 増分データ同期](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-inventory-incremental.png)

## <a name="perform-migration-cutover"></a>一括移行を実行する

初回の全体の読み込みが完了した後、データベースは **[一括準備完了]** とマークされます。

1. データベースの移行を完了する準備ができたら、 **[一括で開始]** を選択します。

    ![一括で開始](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-inventory-start-cutover.png)

2. ソース データベースに対するすべての受信トランザクションを必ず停止してください。 **[保留中の変更]** カウンターが **0** を示すまで待ってください。
3. **[確認]** を選択し、 **[適用]** を選択します。
4. データベースの移行状態に **[完了]** が表示されたら、アプリケーションを新しいターゲット Azure Database for MySQL データベースに接続します。

これで、MySQL のオンプレミス インスタンスの Azure Database for MySQL へのオンライン移行が完了しました。

## <a name="next-steps"></a>次の手順

* Azure Database Migration Service の詳細については、「[Azure Database Migration Service とは](https://docs.microsoft.com/azure/dms/dms-overview)」を参照してください。
* Azure Database for MySQL の詳細については、「[Azure Database for MySQL とは](https://docs.microsoft.com/azure/mysql/overview)」の記事を参照してください。
* その他の質問については、[Ask Azure Database Migrations](mailto:AskAzureDatabaseMigrations@service.microsoft.com) エイリアスにメールを送信してください。
