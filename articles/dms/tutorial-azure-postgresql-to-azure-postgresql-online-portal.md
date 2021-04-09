---
title: チュートリアル:Azure portal を介してオンラインで Azure DB for PostgreSQL を Azure DB for PostgreSQL に移行する
titleSuffix: Azure Database Migration Service
description: Azure portal を介して Azure Database Migration Service を使用し、ある Azure DB for PostgreSQL から別の Azure Database for PostgreSQL へのオンライン移行を実行する方法について説明します。
services: dms
author: arunkumarthiags
ms.author: arthiaga
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 07/21/2020
ms.openlocfilehash: 996523d507f0a4f2850e936df39a38769bc47cde
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/18/2021
ms.locfileid: "101091297"
---
# <a name="tutorial-migrateupgrade-azure-db-for-postgresql---single-server-to-azure-db-for-postgresql---single-server--online-using-dms-via-the-azure-portal"></a>チュートリアル:Azure portal を介して DMS を使用し、Azure DB for PostgreSQL - 単一サーバーを Azure DB for PostgreSQL - 単一サーバーにオンラインで移行または更新する

Azure Database Migration Service を使用すると、[Azure Database for PostgreSQL - 単一サーバー](../postgresql/overview.md#azure-database-for-postgresql---single-server) インスタンスから、同じまたは別のバージョンの Azure Database for PostgreSQL - 単一サーバー インスタンス、または Azure Database for PostgreSQL - フレキシブル サーバーに、最小限のダウンタイムでデータベースを移行できます。 このチュートリアルでは、Azure Database Migration Service のオンライン移行アクティビティを使用して、**DVD Rental** サンプル データベースを Azure Database for PostgreSQL v10 から Azure Database for PostgreSQL - 単一サーバーに移行します。

このチュートリアルでは、以下の内容を学習します。
> [!div class="checklist"]
>
> * pg_dump ユーティリティを使用して、サンプル スキーマを移行する。
> * Azure Database Migration Service のインスタンスを作成する。
> * Azure Database Migration Service で移行プロジェクトを作成する。
> * 移行を実行する。
> * 移行を監視する。
> * 一括移行を実行する。

> [!NOTE]
> Azure Database Migration Service を使用してオンライン移行を実行するには、Premium 価格レベルに基づいてインスタンスを作成する必要があります。 移行プロセス中のデータの盗難を防ぐために、ディスクは暗号化します。

> [!IMPORTANT]
> 最適な移行エクスペリエンスのために、ターゲット データベースと同じ Azure リージョンに Azure Database Migration Service のインスタンスを作成することをお勧めします。 リージョンや地域をまたいでデータを移動する場合、移行プロセスが遅くなり、エラーが発生する可能性があります。

> [!IMPORTANT]
> Azure Database for PostgreSQL からの移行は、PostgreSQL バージョン 10 以降でサポートされています。 また、このチュートリアルを使用して、ある Azure Database for PostgreSQL インスタンスから別の Azure Database for PostgreSQL インスタンスまたは Hyperscale (Citus) インスタンスに移行することもできます。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下を実行する必要があります。

* サポートされている移行とバージョンの組み合わせについては、「[Azure Database Migration Service によってサポートされる移行シナリオの状態](./resource-scenario-status.md)」を確認してください。 
* **DVD Rental** データベースが稼働している既存の [Azure Database for PostgreSQL](../postgresql/index.yml) バージョン 10 以降のインスタンス。 

    また、ターゲットの Azure Database for PostgreSQL のバージョンは、オンプレミスの PostgreSQL バージョンと同じかそれ以降である必要があることに注意してください。 たとえば、PostgreSQL 10 は Azure Database for PostgreSQL 10 または 11 に移行できますが、Azure Database for PostgreSQL 9.6 には移行できません。

* データの移行先となるターゲット データベース サーバーとして、[Azure Database for PostgreSQL サーバーを作成](../postgresql/quickstart-create-server-database-portal.md)するか、[Azure Database for PostgreSQL - Hyperscale (Citus) サーバーを作成](../postgresql/quickstart-create-hyperscale-portal.md)します。
* Azure Resource Manager デプロイ モデルを使用して、Azure Database Migration Service 用の Microsoft Azure 仮想ネットワークを作成します。 仮想ネットワークの作成方法の詳細については、[Virtual Network のドキュメント](../virtual-network/index.yml)を参照してください。特に、詳細な手順が記載されたクイックスタートの記事を参照してください。

* 仮想ネットワークのネットワーク セキュリティ グループ (NSG) の規則によって、ServiceBus、Storage、AzureMonitor の ServiceTag の送信ポート 443 がブロックされていないことを確認します。 仮想ネットワークの NSG トラフィックのフィルター処理の詳細については、[ネットワーク セキュリティ グループによるネットワーク トラフィックのフィルター処理](../virtual-network/virtual-network-vnet-plan-design-arm.md)に関する記事を参照してください。
* Azure Database for PostgreSQL ソースのサーバーレベルの[ファイアウォール規則](../azure-sql/database/firewall-configure.md)を作成して、Azure Database Migration Service がソース データベースにアクセスできるようにします。 Azure Database Migration Service に使用する仮想ネットワークのサブネット範囲を指定します。
* Azure Database for PostgreSQL ターゲットのサーバーレベルの[ファイアウォール規則](../azure-sql/database/firewall-configure.md)を作成して、Azure Database Migration Service がターゲット データベースにアクセスできるようにします。 Azure Database Migration Service に使用する仮想ネットワークのサブネット範囲を指定します。
* Azure DB for PostgreSQL ソースで[論理レプリケーションを有効に](../postgresql/concepts-logical.md)します。 
* ソースとして使用されている Azure Database for PostgreSQL インスタンスで、次のサーバー パラメーターを設定します。

  * max_replication_slots = [スロットの数]。**10 スロット** に設定することをお勧めします
  * max_wal_senders = [同時実行タスク数] - max_wal_senders パラメーターでは同時に実行できるタスクの数を設定します、**10 タスク** に設定することをお勧めします

> [!NOTE]
> 上記のサーバー パラメーターは静的であり、有効にするには Azure Database for PostgreSQL インスタンスを再起動する必要があります。 サーバー パラメーターの切り替えの詳細については、[Azure Database for PostgreSQL のサーバー パラメーターの構成](../postgresql/howto-configure-server-parameters-using-portal.md)に関する記事を参照してください。

> [!IMPORTANT]
> 変更をターゲット データベースに確実に同期できるようにするには、既存のデータベース内のすべてのテーブルに主キーが必要です。

## <a name="migrate-the-sample-schema"></a>サンプル スキーマを移行する

テーブル スキーマ、インデックス、ストアド プロシージャなどのすべてのデータベース オブジェクトを完了するには、ソース データベースからスキーマを抽出し、データベースに適用する必要があります。

1. pg_dump -s コマンドを使用して、データベースのスキーマ ダンプ ファイルを作成します。

    ```
    pg_dump -o -h hostname -U db_username -d db_name -s > your_schema.sql
    ```

    たとえば、**dvdrental** データベース用のスキーマ ダンプ ファイルを作成するには、次のようにします。

    ```
    pg_dump -o -h mypgserver-source.postgres.database.azure.com -U pguser@mypgserver-source -d dvdrental -s -O -x > dvdrentalSchema.sql
    ```

    pg_dump ユーティリティの使用方法の詳細については、 [pg-dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html#PG-DUMP-EXAMPLES) チュートリアルの例を参照してください。

2. ターゲット環境に空のデータベース (Azure Database for PostgreSQL) を作成します。

    データベースの接続および作成方法の詳細については、「[Azure portal で Azure Database for PostgreSQL サーバーを作成する](../postgresql/quickstart-create-server-database-portal.md)」または「[Azure portal で Azure Database for PostgreSQL - Hyperscale (Citus) を作成する](../postgresql/quickstart-create-hyperscale-portal.md)」を参照してください。

    > [!NOTE]
    > Azure Database for PostgreSQL - Hyperscale (Citus) のインスタンスには、**citus** という単一のデータベースのみがあります。

3. スキーマ ダンプ ファイルを復元して作成したターゲット データベースに、スキーマをインポートします。

    ```
    psql -h hostname -U db_username -d db_name < your_schema.sql
    ```

    次に例を示します。

    ```
    psql -h mypgserver-source.postgres.database.azure.com  -U pguser@mypgserver-source -d dvdrental citus < dvdrentalSchema.sql
    ```

4. 外部キー削除スクリプトを抽出し、ターゲット (Azure Database for PostgreSQL) に追加するには、PgAdmin または psql で次のスクリプトを実行します。

   > [!IMPORTANT]
   > スキーマに外部キーがあると、移行の初回の読み込みと継続的同期が失敗します。

    ```
    SELECT Q.table_name
        ,CONCAT('ALTER TABLE ','"', table_schema,'"', '.','"', table_name ,'"', STRING_AGG(DISTINCT CONCAT(' DROP CONSTRAINT ','"', foreignkey,'"'), ','), ';') as DropQuery
            ,CONCAT('ALTER TABLE ','"', table_schema,'"', '.','"', table_name,'"', STRING_AGG(DISTINCT CONCAT(' ADD CONSTRAINT ','"', foreignkey,'"', ' FOREIGN KEY (','"', column_name,'"', ')', ' REFERENCES ','"', foreign_table_schema,'"', '.','"', foreign_table_name,'"', '(','"', foreign_column_name,'"', ')',' ON UPDATE ',update_rule,' ON DELETE ',delete_rule), ','), ';') as AddQuery
    FROM
        (SELECT
        S.table_schema,
        S.foreignkey,
        S.table_name,
        STRING_AGG(DISTINCT S.column_name, ',') AS column_name,
        S.foreign_table_schema,
        S.foreign_table_name,
        STRING_AGG(DISTINCT S.foreign_column_name, ',') AS foreign_column_name,
        S.update_rule,
        S.delete_rule
    FROM
        (SELECT DISTINCT
        tc.table_schema,
        tc.constraint_name AS foreignkey,
        tc.table_name,
        kcu.column_name,
        ccu.table_schema AS foreign_table_schema,
        ccu.table_name AS foreign_table_name,
        ccu.column_name AS foreign_column_name,
        rc.update_rule AS update_rule,
        rc.delete_rule AS delete_rule
        FROM information_schema.table_constraints AS tc
        JOIN information_schema.key_column_usage AS kcu ON tc.constraint_name = kcu.constraint_name AND tc.table_schema = kcu.table_schema
        JOIN information_schema.constraint_column_usage AS ccu ON ccu.constraint_name = tc.constraint_name AND ccu.table_schema = tc.table_schema
        JOIN information_schema.referential_constraints as rc ON rc.constraint_name = tc.constraint_name AND rc.constraint_schema = tc.table_schema
    WHERE constraint_type = 'FOREIGN KEY'
        ) S
        GROUP BY S.table_schema, S.foreignkey, S.table_name, S.foreign_table_schema, S.foreign_table_name,S.update_rule,S.delete_rule
        ) Q
        GROUP BY Q.table_schema, Q.table_name;
    ```

5. クエリの結果内の外部キー削除 (2 列目) を実行します。

6. ターゲット データベース内のトリガーを無効にするには、以下のスクリプトを実行します。

   > [!IMPORTANT]
   > データ内のトリガー (insert または update) により、ソースからデータがレプリケートされる前にターゲットにデータの整合性が適用されます。 そのため、移行時は **ターゲットの** すべてのテーブル内のトリガーを無効にし、移行の完了後にトリガーを再度有効にすることをお勧めします。

    ```
    SELECT DISTINCT CONCAT('ALTER TABLE ', event_object_schema, '.', event_object_table, ' DISABLE TRIGGER ', trigger_name, ';')
    FROM information_schema.triggers
    ```

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Microsoft.DataMigration リソース プロバイダーを登録する

1. Azure portal にサインインし、 **[すべてのサービス]** を選択し、 **[サブスクリプション]** を選択します。

   ![ポータルのサブスクリプションの表示](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/portal-select-subscriptions.png)

2. Azure Database Migration Service のインスタンスを作成するサブスクリプションを選択してから、 **[リソース プロバイダー]** を選びます。

    ![リソース プロバイダーの表示](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/portal-select-resource-provider.png)

3. 移行を検索し、**Microsoft.DataMigration** の右側にある **[登録]** を選択します。

    ![リソース プロバイダーの登録](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/portal-register-resource-provider.png)

## <a name="create-a-dms-instance"></a>DMS インスタンスを作成する

1. Azure portal で **[+ リソースの作成]** を選択し、Azure Database Migration Service を検索して、ドロップダウン リストから **[Azure Database Migration Service]** を選択します。

    ![Azure Marketplace](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/portal-marketplace.png)

2. **[Azure Database Migration Service]** 画面で、 **[作成]** を選択します。

    ![Azure Database Migration Service インスタンスを作成する](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-create1.png)
  
3. **[移行サービスの作成]** 画面で、サービスの名前、サブスクリプション、新規または既存のリソース グループ、および場所を指定します。

4. 既存の仮想ネットワークを選択するか、新しく作成します。

    仮想ネットワークによって、Azure Database Migration Service に、ソースの PostgreSQL サーバーとターゲットの Azure Database for PostgreSQL インスタンスへのアクセスが提供されます。

    Azure portal で仮想ネットワークを作成する方法の詳細については、「[Azure portal を使用した仮想ネットワークの作成](../virtual-network/quick-create-portal.md)」を参照してください。

5. 価格レベルを選択します。

    コストと価格レベルの詳細については、[価格に関するページ](https://aka.ms/dms-pricing)を参照してください。

    ![Azure Database Migration Service インスタンス設定を構成する](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-settings4.png)

6. **[確認および作成]** を選択してサービスを作成します。

   サービスの作成は約 10 分から 15 分以内に完了します。

## <a name="create-a-migration-project"></a>移行プロジェクトを作成する

サービスが作成されたら、Azure portal 内でそのサービスを探して開き、新しい移行プロジェクトを作成します。

1. Azure ポータルで、 **[All services]\(すべてのサービス\)** を選択し、Azure Database Migration Service を検索して、**Azure Database Migration Service** を選択します。

      ![Azure Database Migration Service のすべてのインスタンスを検索する](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-search.png)

2. **[Azure Database Migration Services]** 画面で、作成した Azure Database Migration Service インスタンスの名前を検索し、インスタンスを選択してから、 **[+ 新しい移行プロジェクト]** を選びます。

3. **[新しい移行プロジェクト]** 画面で、プロジェクトの名前を指定し、 **[ソース サーバーの種類]** テキスト ボックスで **[PostgreSQL]** を選択して、 **[ターゲット サーバーの種類]** テキスト ボックスで **[Azure Database for PostgreSQL]** を選択します。
    > [!NOTE]
    > ソース サーバーは **Azure Database for PostgreSQL** インスタンスですが、 **[ソース サーバーの種類]** で **[PostgreSQL]** を選択します。  

4. **[アクティビティの種類を選択します]** セクションで、 **[オンライン データの移行]** を選択します。

    ![Azure Database Migration Service プロジェクトを作成する](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-create-project.png)

    > [!NOTE]
    > または、 **[プロジェクトのみを作成します]** を選択して移行プロジェクトを作成しておき、移行は後で実行することもできます。

5. **[保存]** を選択します。Azure Database Migration Service を使用して正常にデータを移行するための要件を確認してから、 **[アクティビティの作成と実行]** を選択します。

## <a name="specify-source-details"></a>ソース詳細を指定する

1. **[ソースの追加に関する詳細]** 画面で、ソース PostgreSQL インスタンスの接続の詳細を指定します。

    ![[Add Source Details]\(ソースの詳細の追加\) 画面](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-add-source-details.png)

    > [!NOTE]
    > "サーバー名"、"サーバー ポート"、"データベース名" などの詳細については、**Azure Database for PostgreSQL** ポータルを参照してください。

2. **[保存]** を選択します。

## <a name="specify-target-details"></a>ターゲット詳細を指定する

1. **[ターゲットの詳細]** 画面で、事前プロビジョニングされており、pg_dump を使用して **DVD Rentals** スキーマがデプロイされている、ターゲットの Hyperscale (Citus) サーバーの接続の詳細を指定します。

    ![[ターゲットの詳細] 画面](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-add-target-details.png)

    > [!NOTE]
    > ある Azure Database for PostgreSQL インスタンスから別の Azure Database for PostgreSQL シングル サーバー インスタンスまたは Hyperscale (Citus) サーバーに移行することができます。

2. **[保存]** を選択し、 **[ターゲット データベースへマッピング]** 画面で、移行用のソース データベースとターゲット データベースをマップします。

    ターゲット データベースにソース データベースと同じデータベース名が含まれている場合、Azure Database Migration Service では、既定でターゲット データベースが選択されます。

    ![[ターゲット データベースへマッピング] 画面](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-map-target-databases.png)

3. **[保存]** を選択してから、 **[移行の設定]** 画面で既定値をそのまま使用します。

    ![[移行の設定] 画面](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-migration-settings.png)

4. **[保存]** を選択し、 **[移行の概要]** 画面で、 **[アクティビティ名]** テキスト ボックスに移行アクティビティの名前を指定します。概要を見直して、ソースとターゲットの詳細が先ほど指定した内容と一致していることを確認します。

    ![[移行の概要] 画面](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-migration-summary.png)

## <a name="run-the-migration"></a>移行を実行する

* **[移行の実行]** を選択します。

移行アクティビティ ウィンドウが表示され、アクティビティの **[状態]** が更新されて、 **[バックアップが進行中です]** と示されるはずです。 Azure DB for PostgreSQL 9.5 または9.6 からアップグレードすると、次のエラーが発生する場合があります。

**"A scenario reported an unknown error.28000: no pg_hba.conf entry for replication connection from host "40.121.141.121", user "sr" (シナリオで不明なエラーがレポートされました。28000: ホスト "40.121.141.121"、ユーザー "sr" からのレプリケーション接続のための pg_hba.conf エントリがありません)"**

これは、必要な論理レプリケーション アーティファクトを作成するための適切な特権が PostgreSQL にないために起こります。 必要な特権を有効にするには、次の操作を行います。

1. 移行元またはアップグレード元のソース Azure DB for PostgreSQL サーバーの [接続のセキュリティ] 設定を開きます。
2. 名前が "_replrule" で終わる新しいファイアウォール規則を追加し、エラー メッセージの IP アドレスを開始 IP および終了 IP フィールドに追加します。 上記のエラーの場合は、次のようにします。
> ファイアウォール規則 = sr_replrule、開始 IP = 40.121.141.121、終了 IP = 40.121.141.121

3. [保存] をクリックして、変更を完了します。 
4. DMS アクティビティを再試行します。 

## <a name="monitor-the-migration"></a>移行を監視する

1. 移行アクティビティ画面で、移行の **[状態]** が **[完了]** になるまで **[最新の情報に更新]** を選択して表示を更新します。

     ![移行プロセスを監視する](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-monitor-migration.png)

2. 移行が完了したら、 **[データベース名]** で、特定のデータベースを選択して、**データ全体の読み込み** および **増分データ同期** 操作の移行状態を取得します。

   > [!NOTE]
   > **データ全体の読み込み** には初回の読み込みの移行状態が表示され、**増分データ同期** には変更データ キャプチャ (CDC) の状態が表示されます。

     ![データ全体の読み込みの詳細](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-full-data-load-details.png)

     ![増分データ同期の詳細](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-incremental-data-sync-details.png)

## <a name="perform-migration-cutover"></a>一括移行を実行する

初回の全体の読み込みが完了すると、データベースは **[一括準備完了]** とマークされます。

1. データベースの移行を完了する準備ができたら、 **[一括で開始]** を選択します。

2. **[保留中の変更]** カウンターに **0** と表示されるまで待ってソース データベースへのすべての受信トランザクションが停止していることを確認し、 **[確認]** チェックボックスをオンにしてから **[適用]** を選択します。

    ![[一括を完了する] 画面](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-complete-cutover.png)

3. データベースの移行状態に **[完了]** と表示されたら、[順序を再作成](https://wiki.postgresql.org/wiki/Fixing_Sequences) (該当する場合) して、アプリケーションを Azure Database for PostgreSQL の新しいターゲット インスタンスに接続します。
 
> [!NOTE]
> Azure Database Migration Service を使用すると、Azure Database for PostgreSQL - 単一サーバーのダウンタイムを短縮して、メジャー バージョンのアップグレードを実行できます。 最初に、目的とする高バージョンの PostgreSQL、ネットワーク設定およびパラメーターを使用して、ターゲット データベースを構成します。 上記の手順に従うことで、ターゲット データベースへの移行を開始することができます。 ターゲット データベース サーバーに切り替えると、そのターゲット データベース サーバーを指すようにアプリケーションの接続文字列を更新できます。 

## <a name="next-steps"></a>次のステップ

* Azure Database for PostgreSQL へのオンライン移行の実行時の既知の問題と制限事項については、[Azure Database for PostgreSQL のオンライン移行に伴う既知の問題と回避策](known-issues-azure-postgresql-online.md)に関する記事を参照してください。
* Azure Database Migration Service の詳細については、「[Azure Database Migration Service とは](./dms-overview.md)」を参照してください。
* Azure Database for PostgreSQL の詳細については、「[Azure Database for PostgreSQL とは](../postgresql/overview.md)」を参照してください。
