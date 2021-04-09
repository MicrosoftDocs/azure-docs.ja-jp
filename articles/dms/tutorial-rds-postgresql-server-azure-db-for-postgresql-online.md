---
title: チュートリアル:RDS PostgreSQL を Azure Database for PostgreSQL にオンライン移行します
titleSuffix: Azure Database Migration Service
description: Azure Database Migration Service を使用して、RDS PostgreSQL から Azure Database for PostgreSQL にオンライン移行を実行する方法を説明します。
services: dms
author: arunkumarthiags
ms.author: arthiaga
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 04/11/2020
ms.openlocfilehash: c2a6a71365b48fa4349306ce632f5762c38dacf7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104597472"
---
# <a name="tutorial-migrate-rds-postgresql-to-azure-db-for-postgresql-online-using-dms"></a>チュートリアル:DMS を使用してオンラインで RDS PostgreSQL を Azure DB for PostgreSQL に移行する

Azure Database Migration Service を使用して、RDS PostgreSQL インスタンスから [Azure Database for PostgreSQL](../postgresql/index.yml) にデータベースを移行することができます。移行中、ソース データベースはオンラインのままになります。 つまり、アプリケーションにとって最小限のダウンタイムで移行を実現できます。 このチュートリアルでは、Azure Database Migration Service のオンライン移行アクティビティを使用して、**DVD Rental** サンプル データベースを RDS PostgreSQL 9.6 のインスタンスから Azure Database for PostgreSQL に移行します。

このチュートリアルでは、以下の内容を学習します。
> [!div class="checklist"]
>
> * pg_dump ユーティリティを使用して、サンプル スキーマを移行する。
> * Azure Database Migration Service のインスタンスを作成する。
> * Azure Database Migration Service を使用して移行プロジェクトを作成する。
> * 移行を実行する。
> * 移行を監視する。
> * 一括移行を実行する。

> [!NOTE]
> Azure Database Migration Service を使用してオンライン移行を実行するには、Premium 価格レベルに基づいてインスタンスを作成する必要があります。 詳しくは、Azure Database Migration Service の[価格](https://azure.microsoft.com/pricing/details/database-migration/)に関するページをご覧ください。 移行プロセス中のデータの盗難を防ぐために、ディスクは暗号化します。

> [!IMPORTANT]
> 最適な移行エクスペリエンスのために、ターゲット データベースと同じ Azure リージョンに Azure Database Migration Service のインスタンスを作成することをお勧めします。 リージョンや地域をまたいでデータを移動する場合、移行プロセスが遅くなり、エラーが発生する可能性があります。

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

この記事では、PostgreSQL のオンプレミス インスタンスから Azure Database for PostgreSQL へのオンライン移行の実行方法について説明します。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下を実行する必要があります。

* [PostgreSQL コミュニティ エディション](https://www.postgresql.org/download/) 9.5、9.6、または 10 をダウンロードしてインストールします。 ソースの PostgreSQL サーバーのバージョンが 9.5.11、9.6.7、10、またはそれ以降のバージョンである必要があります 詳しくは、「[サポートされている PostgreSQL Database バージョン](../postgresql/concepts-supported-versions.md)」の記事をご覧ください。

   また、ターゲットの Azure Database for PostgreSQL バージョンは、RDS PostgreSQL のバージョンと同じかそれ以降である必要があることに注意してください。 たとえば、RDS PostgreSQL 9.6 は Azure Database for PostgreSQL 9.6、10、または 11 にのみ移行でき、Azure Database for PostgreSQL 9.5 には移行できません。

* [Azure Database for PostgreSQL](../postgresql/quickstart-create-server-database-portal.md) または [Azure Database for PostgreSQL - Hyperscale (Citus) サーバー](../postgresql/quickstart-create-hyperscale-portal.md)のインスタンスを作成します。 pgAdmin を使用して PostgreSQL サーバーに接続する方法の詳細については、ドキュメントのこの[セクション](../postgresql/quickstart-create-server-database-portal.md#connect-to-the-server-with-psql)を参照してください。
* Azure Resource Manager デプロイ モデルを使用して、Azure Database Migration Service 用の Microsoft Azure Virtual Network を作成します。これで、[ExpressRoute](../expressroute/expressroute-introduction.md) または [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) を使用したオンプレミスのソース サーバーとのサイト間接続を確立します。 仮想ネットワークの作成方法の詳細については、[Virtual Network のドキュメント](../virtual-network/index.yml)を参照してください。特に、詳細な手順が記載されたクイックスタートの記事を参照してください。
* 仮想ネットワークのネットワーク セキュリティ グループの規則によって、ServiceBus、Storage、AzureMonitor の ServiceTag の送信ポート 443 がブロックされていないことを確認します。 仮想ネットワークの NSG トラフィックのフィルター処理の詳細については、[ネットワーク セキュリティ グループによるネットワーク トラフィックのフィルター処理](../virtual-network/virtual-network-vnet-plan-design-arm.md)に関する記事を参照してください。
* [データベース エンジン アクセスのために Windows ファイアウォール](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)を構成します。
* Azure Database Migration Service がソース PostgreSQL サーバーにアクセスできるように Windows ファイアウォールを開きます。既定では TCP ポート 5432 が使用されています。
* ソース データベースの前でファイアウォール アプライアンスを使用する場合は、Azure Database Migration Service が移行のためにソース データベースにアクセスできるように、ファイアウォール規則を追加することが必要な場合があります。
* Azure Database for PostgreSQL サーバーのサーバーレベルの[ファイアウォール規則](../postgresql/concepts-firewall-rules.md)を作成して、Azure Database Migration Service でターゲット データベースにアクセスできるようにします。 Azure Database Migration Service に使用する仮想ネットワークのサブネット範囲を指定します。

### <a name="set-up-aws-rds-postgresql-for-replication"></a>レプリケーションのために AWS RDS PostgreSQL を設定する

1. 新しいパラメーター グループを作成するには、「[DB パラメーター グループを使用する](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_WorkingWithParamGroups.html)」の記事で AWS で提供される指示に従ってください。
2. Azure Database Migration Service からソースに接続するには、マスター ユーザー名を使用します。 マスター ユーザー アカウント以外のアカウントを使用する場合、アカウントには rds_superuser ロールと rds_replication ロールが必要です。 rds_replication ロールは、論理スロットを管理し、論理スロットを使用してデータをストリーミングするアクセス許可を付与します。
3. 次の構成で新しいパラメーター グループを作成します。

    a. DB パラメーター グループ内の rds.logical_replication パラメーターを 1 に設定します。

    b. max_wal_senders = [同時実行タスク数] - max_wal_senders パラメーターでは同時に実行できるタスクの数を設定します。10 タスクに設定することをお勧めします。

    c. max_replication_slots = [スロットの数]、5 スロットに設定することをお勧めします。

4. 作成したパラメーター グループを RDS PostgreSQL のインスタンスに関連付けます。

## <a name="migrate-the-schema"></a>スキーマを移行する

1. ソース データベースからスキーマを抽出し、テーブル スキーマ、インデックス、ストアド プロシージャなどのすべてのデータベース オブジェクトの移行を完了するためにターゲット データベースに適用します。

    スキーマのみを移行する最も簡単な方法は、-s オプションを指定した pg_dump を使用することです。 詳細については、Postgres pg_dump チュートリアルの[例](https://www.postgresql.org/docs/9.6/app-pgdump.html#PG-DUMP-EXAMPLES)をご覧ください。

    ```
    pg_dump -o -h hostname -U db_username -d db_name -s > your_schema.sql
    ```

    たとえば、**dvdrental** データベースのスキーマ ファイルをダンプするには、次のコマンドを使用します。

    ```
    pg_dump -o -h localhost -U postgres -d dvdrental -s  > dvdrentalSchema.sql
    ```

2. ターゲット サービスに空のデータベース (Azure Database for PostgreSQL) を作成します。 データベースの接続と作成については、次のいずれかの記事を参照してください。

    * [Azure portal で Azure Database for PostgreSQL サーバーを作成する](../postgresql/quickstart-create-server-database-portal.md)
    * [Azure portal を使用して Azure Database for PostgreSQL - Hyperscale (Citus) を作成する](../postgresql/quickstart-create-hyperscale-portal.md)

3. ターゲット サービス (Azure Database for PostgreSQL) にスキーマをインポートします。 スキーマのダンプ ファイルを復元するには、次のコマンドを実行します。

    ```
    psql -h hostname -U db_username -d db_name < your_schema.sql
    ```

    次に例を示します。

    ```
    psql -h mypgserver-20170401.postgres.database.azure.com  -U postgres -d dvdrental < dvdrentalSchema.sql
    ```

4. スキーマに外部キーが含まれている場合、移行の初回の読み込みと継続的同期は失敗します。 外部キー削除スクリプトを抽出し、同期先 (Azure Database for PostgreSQL) に外部キー スクリプトを追加するには、PgAdmin または psql で次のスクリプトを実行します。
  
    ```
    SELECT Queries.tablename
           ,concat('alter table ', Queries.tablename, ' ', STRING_AGG(concat('DROP CONSTRAINT ', Queries.foreignkey), ',')) as DropQuery
                ,concat('alter table ', Queries.tablename, ' ',
                                                STRING_AGG(concat('ADD CONSTRAINT ', Queries.foreignkey, ' FOREIGN KEY (', column_name, ')', 'REFERENCES ', foreign_table_name, '(', foreign_column_name, ')' ), ',')) as AddQuery
        FROM
        (SELECT
        tc.table_schema,
        tc.constraint_name as foreignkey,
        tc.table_name as tableName,
        kcu.column_name,
        ccu.table_schema AS foreign_table_schema,
        ccu.table_name AS foreign_table_name,
        ccu.column_name AS foreign_column_name
    FROM
        information_schema.table_constraints AS tc
        JOIN information_schema.key_column_usage AS kcu
          ON tc.constraint_name = kcu.constraint_name
          AND tc.table_schema = kcu.table_schema
        JOIN information_schema.constraint_column_usage AS ccu
          ON ccu.constraint_name = tc.constraint_name
          AND ccu.table_schema = tc.table_schema
    WHERE constraint_type = 'FOREIGN KEY') Queries
      GROUP BY Queries.tablename;
    ```

5. クエリ結果内の外部キー削除 (2 列目) を実行して、外部キーを削除します。

6. データにトリガー (insert または update トリガー) が含まれている場合は、ソースからデータをレプリケートする前にターゲットでデータの整合性が適用されます。 移行時は *ターゲットの* すべてのテーブル内のトリガーを無効にし、移行の完了後にトリガーを有効にすることをお勧めします。

    ターゲット データベース内のトリガーを無効にするには、次のように指定します。

    ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name,' ON ', event_object_table, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
    ```

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Microsoft.DataMigration リソース プロバイダーを登録する

1. Azure portal にサインインし、 **[すべてのサービス]** を選択し、 **[サブスクリプション]** を選択します。

   ![ポータルのサブスクリプションの表示](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-select-subscription1.png)

2. Azure Database Migration Service のインスタンスを作成するサブスクリプションを選択してから、 **[リソース プロバイダー]** を選びます。

    ![リソース プロバイダーの表示](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-select-resource-provider.png)

3. 移行を検索し、**Microsoft.DataMigration** の右側にある **[登録]** を選択します。

    ![リソース プロバイダーの登録](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-register-resource-provider.png)

## <a name="create-an-instance-of-azure-database-migration-service"></a>Azure Database Migration Service のインスタンスを作成する

1. Azure portal で **[+ リソースの作成]** を選択し、Azure Database Migration Service を検索して、ドロップダウン リストから **[Azure Database Migration Service]** を選択します。

    ![Azure Marketplace](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-marketplace.png)

2. **[Azure Database Migration Service]** 画面で、 **[作成]** を選択します。

    ![Azure Database Migration Service インスタンスを作成する](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-create1.png)
  
3. **[移行サービスの作成]** 画面で、サービスの名前、サブスクリプション、新規または既存のリソース グループを指定します。

4. Azure Database Migration Service のインスタンスを作成する場所を選択します。

5. 既存の仮想ネットワークを選択するか、新しく作成します。

    この仮想ネットワークによって、Azure Database Migration Service に、ソース PostgreSQL インスタンスとターゲット Azure Database for PostgreSQL インスタンスへのアクセスが提供されます。

    Azure portal で仮想ネットワークを作成する方法の詳細については、「[Azure portal を使用した仮想ネットワークの作成](../virtual-network/quick-create-portal.md)」の記事を参照してください。

6. 価格レベルを選択します。このオンライン移行では、Premium:4 仮想コア価格レベルを選択してください。

    ![Azure Database Migration Service インスタンス設定を構成する](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-settings5.png)

7. **[作成]** を選択して、サービスを作成します。

## <a name="create-a-migration-project"></a>移行プロジェクトを作成する

サービスが作成されたら、Azure portal 内でそのサービスを探して開き、新しい移行プロジェクトを作成します。

1. Azure ポータルで、 **[All services]\(すべてのサービス\)** を選択し、Azure Database Migration Service を検索して、**Azure Database Migration Service** を選択します。

      ![Azure Database Migration Service のすべてのインスタンスを検索する](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-search.png)

2. **[Azure Database Migration Services]** 画面で、作成した Azure Database Migration Service インスタンスの名前を検索し、インスタンスを選択してから、 **[+ 新しい移行プロジェクト]** を選びます。
3. **[新しい移行プロジェクト]** 画面で、プロジェクトの名前を指定し、 **[ソース サーバーの種類]** テキスト ボックスで **[AWS RDS for PostgreSQL]** を選択した後、 **[ターゲット サーバーの種類]** テキスト ボックスで **[Azure Database for PostgreSQL]** を選択します。
4. **[アクティビティの種類を選択します]** セクションで、 **[オンライン データの移行]** を選択します。

    > [!IMPORTANT]
    > 必ず **[オンライン データの移行]** を選択してください。オフライン移行は、このシナリオではサポートされていません。

    ![Database Migration Service プロジェクトを作成する](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-create-project5.png)

    > [!NOTE]
    > または、 **[プロジェクトのみを作成します]** を選択して移行プロジェクトを作成しておき、移行は後で実行することもできます。

5. **[保存]** を選択します。

6. **[アクティビティの作成と実行]** を選択してプロジェクトを作成し、移行アクティビティを実行します。

    > [!NOTE]
    > プロジェクトの作成ブレードでオンライン移行を設定するのに必要な前提条件をメモしておいてください。

## <a name="specify-source-details"></a>ソース詳細を指定する

* **[ソースの追加に関する詳細]** 画面で、ソース PostgreSQL インスタンスの接続の詳細を指定します。

   ![ソースの詳細](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-source-details5.png)

## <a name="specify-target-details"></a>ターゲット詳細を指定する

1. **[保存]** を選択し、 **[ターゲットの詳細]** 画面で、事前プロビジョニングされており、pg_dump を使用して **DVD Rentals** スキーマがデプロイされている、ターゲットの Azure Database for PostgreSQL サーバーの接続の詳細を指定します。

    ![ターゲットの詳細](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-target-details.png)

2. **[保存]** を選択し、 **[ターゲット データベースへマッピング]** 画面で、移行用のソース データベースとターゲット データベースをマップします。

    ターゲット データベースにソース データベースと同じデータベース名が含まれている場合、Azure Database Migration Service では、既定でターゲット データベースが選択されます。

    ![ターゲット データベースにマップする](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-map-target-databases.png)

3. **[保存]** を選択し、 **[移行の概要]** 画面で、 **[アクティビティ名]** テキスト ボックスに移行アクティビティの名前を指定します。概要を見直して、ソースとターゲットの詳細が先ほど指定した内容と一致していることを確認します。

    ![移行の概要](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-migration-summary1.png)

## <a name="run-the-migration"></a>移行を実行する

* **[移行の実行]** を選択します。

    移行アクティビティ ウィンドウが表示されます。アクティビティの **[状態]** は **[初期化中]** になります。

## <a name="monitor-the-migration"></a>移行を監視する

1. 移行アクティビティ画面で、移行の **[状態]** が **[実行中]** になるまで **[最新の情報に更新]** を選択して表示を更新します。

    ![アクティビティの状態 - 実行中](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-activity-status3.png)

2. **[データベース名]** で、特定のデータベースを選択して、 **[データ全体の読み込み]** 操作と **[増分データ同期]** 操作の移行状態を取得します。

    **データ全体の読み込み** には初回の読み込みの移行状態が表示され、**増分データ同期** には変更データ キャプチャ (CDC) の状態が表示されます。

    ![インベントリ画面 - データ全体の読み込み](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-inventory-full-load.png)

    ![インベントリ画面 - 増分データ同期](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-inventory-incremental.png)

## <a name="perform-migration-cutover"></a>一括移行を実行する

初回の全体の読み込みが完了した後、データベースは **[一括準備完了]** とマークされます。

1. データベースの移行を完了する準備ができたら、 **[一括で開始]** を選択します。

2. **[保留中の変更]** カウンターに **0** と表示されるまで待ってソース データベースへのすべての受信トランザクションが停止していることを確認し、 **[確認]** チェックボックスをオンにしてから **[適用]** を選択します。

    ![[一括を完了する] 画面](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-complete-cutover.png)

3. データベースの移行状態に **[完了]** が表示されたら、アプリケーションを新しいターゲット Azure Database for PostgreSQL データベースに接続します。

RDS PostgreSQL のオンプレミス インスタンスの Azure Database for PostgreSQL へのオンライン移行が完了しました。

## <a name="next-steps"></a>次のステップ

* Azure Database Migration Service の詳細については、「[Azure Database Migration Service とは](./dms-overview.md)」を参照してください。
* Azure Database for PostgreSQL の詳細については、「[Azure Database for PostgreSQL とは](../postgresql/overview.md)」を参照してください。
* その他の質問については、[Ask Azure Database Migrations](mailto:AskAzureDatabaseMigrations@service.microsoft.com) エイリアスにメールを送信してください。
