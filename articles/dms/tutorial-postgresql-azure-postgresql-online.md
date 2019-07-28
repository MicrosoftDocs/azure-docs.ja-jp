---
title: チュートリアル:Azure Database Migration Service を使用して PostgreSQL の Azure Database for PostgreSQL へのオンライン移行を実行する | Microsoft Docs
description: Azure Database Migration Service を使用して、オンプレミスの PostgreSQL から Azure Database for PostgreSQL にオンライン移行を実行する方法を説明します。
services: dms
author: hjtoland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 06/28/2019
ms.openlocfilehash: 96bfb80602efe8e63f814fc9bf6cff3ae52e5983
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67461535"
---
# <a name="tutorial-migrate-postgresql-to-azure-database-for-postgresql-online-using-dms"></a>チュートリアル:DMS を使用して PostgreSQL をオンラインで Azure Database for PostgreSQ に移行する

Azure Database Migration Service を使用して、最小限のダウンタイムでデータベースをオンプレミスの PostgreSQL インスタンスから [Azure Database for PostgreSQL](https://docs.microsoft.com/azure/postgresql/) に移行できます。 つまり、アプリケーションにとって最小限のダウンタイムで移行を実現できます。 このチュートリアルでは、Azure Database Migration Service のオンライン移行アクティビティを使用して、**DVD Rental** サンプル データベースを PostgreSQL 9.6 のオンプレミス インスタンスから Azure Database for PostgreSQL に移行します。

このチュートリアルでは、以下の内容を学習します。
> [!div class="checklist"]
>
> * pg_dump ユーティリティを使用してサンプル スキーマを移行する。
> * Azure Database Migration Service のインスタンスを作成する。
> * Azure Database Migration Service を使用して移行プロジェクトを作成する。
> * 移行を実行する。
> * 移行を監視する。

> [!NOTE]
> Azure Database Migration Service を使用してオンライン移行を実行するには、Premium 価格レベルに基づいてインスタンスを作成する必要があります。

> [!IMPORTANT]
> 最適な移行エクスペリエンスのために、ターゲット データベースと同じ Azure リージョンに Azure Database Migration Service のインスタンスを作成することをお勧めします。 リージョンや地域をまたいでデータを移動する場合、移行プロセスが遅くなり、エラーが発生する可能性があります。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下を実行する必要があります。

* [PostgreSQL コミュニティ エディション](https://www.postgresql.org/download/) 9.5、9.6、または 10 をダウンロードしてインストールします。 ソースの PostgreSQL サーバーのバージョンが 9.5.11、9.6.7、10、またはそれ以降のバージョンである必要があります 詳しくは、「[サポートされている PostgreSQL Database バージョン](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions)」の記事をご覧ください。

    また、オンプレミスの PostgreSQL のバージョンが、Azure Database for PostgreSQL のバージョンと一致する必要があります。 たとえば、PostgreSQL 9.5.11.5 は Azure Database for PostgreSQL 9.5.11 にのみ移行でき、 バージョン 9.6.7 には移行できません。

    > [!NOTE]
    > PostgreSQL バージョン 10 では、現在、バージョン 10.3 から Azure Database for PostgreSQL への移行をサポートしているのは DMS のみです。

* [Azure Database for PostgreSQL のインスタンスを作成します](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal)。  
* Azure Resource Manager デプロイ モデルを使用して、Azure Database Migration Service 用の Azure 仮想ネットワーク (VNet) を作成します。これで、[ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) または [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) を使用したオンプレミスのソース サーバーへのサイト間接続が提供されます。 VNet の作成方法の詳細については、[Virtual Network のドキュメント](https://docs.microsoft.com/azure/virtual-network/)、特に詳細な手順を提供するクイックスタートの記事を参照してください。

    > [!NOTE]
    > VNet のセットアップ中、Microsoft へのネットワーク ピアリングに ExpressRoute を使用する場合は、サービスのプロビジョニング先となるサブネットに、次のサービス [エンドポイント](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)を追加してください。
    > * ターゲット データベース エンドポイント (SQL エンドポイント、Cosmos DB エンドポイントなど)
    > * ストレージ エンドポイント
    > * サービス バス エンドポイント
    >
    > Azure Database Migration Service にはインターネット接続がないため、この構成が必要となります。

* VNet のネットワーク セキュリティ グループ (NSG) 規則によって Azure Database Migration Service への以下のインバウンド通信ポートがブロックされないことを確認します: 443、53、9354、445、12000。 Azure VNet NSG トラフィックのフィルター処理の詳細については、[ネットワーク セキュリティ グループによるネットワーク トラフィックのフィルター処理](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm)に関する記事を参照してください。
* [データベース エンジン アクセスのために Windows ファイアウォール](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)を構成します。
* Azure Database Migration Service がソース PostgreSQL サーバーにアクセスできるように Windows ファイアウォールを開きます。既定では TCP ポート 5432 です。
* ソース データベースの前でファイアウォール アプライアンスを使用する場合は、Azure Database Migration Service が移行のためにソース データベースにアクセスできるように、ファイアウォール規則を追加することが必要な場合があります。
* Azure Database for PostgreSQL のサーバーレベルの[ファイアウォール規則](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)を作成して、Azure Database Migration Service がターゲット データベースにアクセスできるようにします。 Azure Database Migration Service に使用される VNet のサブネット範囲を指定します。
* CLI を呼び出すには、次の 2 つの方法があります。

  * Azure Portal の右上隅にある [Cloud Shell] ボタンを選択します。

       ![Azure Portal の [Cloud Shell] ボタン](media/tutorial-postgresql-to-azure-postgresql-online/cloud-shell-button.png)

  * CLI をローカルにインストールして実行します。 CLI 2.0 は、Azure リソースを管理するためのコマンドライン ツールです。

       CLI をダウンロードするには、「[Azure CLI 2.0 のインストール](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)」の記事の手順に従ってください。 この記事には、CLI 2.0 をサポートするプラットフォームも一覧表示されています。

       Windows Subsystem for Linux (WSL) を設定するには、[Windows 10 インストール ガイド](https://docs.microsoft.com/windows/wsl/install-win10)の指示に従います。

* postgresql.config ファイルで論理レプリケーションを有効にし、次のパラメーターを設定します。

  * wal_level = **logical**
  * max_replication_slots = [スロットの数]、 **5 スロット**に設定することをお勧めします
  * max_wal_senders = [同時実行タスク数] - max_wal_senders パラメーターでは同時に実行できるタスクの数を設定します、**10 タスク**に設定することをお勧めします

## <a name="migrate-the-sample-schema"></a>サンプル スキーマを移行する

テーブル スキーマ、インデックス、ストアド プロシージャなどのすべてのデータベース オブジェクトを完了するには、ソース データベースからスキーマを抽出し、データベースに適用する必要があります。

1. pg_dump -s コマンドを使用して、データベースのスキーマ ダンプ ファイルを作成します。 

    ```
    pg_dump -o -h hostname -U db_username -d db_name -s > your_schema.sql
    ```

    たとえば、スキーマ ファイル dvdrental データベースをダンプするには:
    ```
    pg_dump -o -h localhost -U postgres -d dvdrental -s  > dvdrentalSchema.sql
    ```

    pg_dump ユーティリティの使用方法の詳細については、 [pg-dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html#PG-DUMP-EXAMPLES) チュートリアルの例を参照してください。

2. ターゲット環境に空のデータベース (Azure Database for PostgreSQL) を作成します。

    データベースの接続と作成の詳細については、「[Azure Portal で Azure Database for PostgreSQL サーバーを作成する](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal)」の記事を参照してください。

3. スキーマ ダンプ ファイルを復元して作成したターゲット データベースに、スキーマをインポートします。

    ```
    psql -h hostname -U db_username -d db_name < your_schema.sql 
    ```

    例:

    ```
    psql -h mypgserver-20170401.postgres.database.azure.com  -U postgres -d dvdrental < dvdrentalSchema.sql
    ```

4. スキーマに外部キーが含まれている場合、移行の初回の読み込みと継続的同期は失敗します。 PgAdmin または psql で次のスクリプトを実行して、ドロップ外部キー スクリプトを抽出し、同期先 (Azure Database for PostgreSQL) に外部キースクリプトを追加します。
  
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

    クエリの結果内の外部キー削除 (2 列目) を実行します。

5. データ内のトリガー (insert または update トリガー) により、ソースからのデータのレプリケート前に先立って、ターゲットにデータの整合性が適用されます。 移行時は**ターゲットの**すべてのテーブル内のトリガーを無効にし、移行の完了後にトリガーを再度有効にすることをお勧めします。

    ターゲット データベース内のトリガーを無効にするには、次のコマンドを使用します。

    ```
    select concat ('alter table ', event_object_table, ' disable trigger ', trigger_name)
    from information_schema.triggers;
    ```

6. テーブル内に ENUM データ型がある場合は、ターゲット テーブルでそれを一時的に 'character varying' データ型に更新することをお勧めします。 データのレプリケーションが完了したら、データ型を ENUM に戻します。

## <a name="provisioning-an-instance-of-dms-using-the-cli"></a>CLI を使用した DMS のインスタンスのプロビジョニング

1. dms の同期の拡張機能をインストールします。
   * 次のコマンドを実行して Azure にサインインします。
       ```
       az login
       ```

   * プロンプトが表示されたら、Web ブラウザーを開き、デバイスを認証するためのコードを入力します。 記載されている手順に従います。
   * dms の拡張機能を追加します。
       * 使用可能な拡張機能を一覧表示するには、次のコマンドを実行します。

           ```
           az extension list-available –otable
           ```

       * 拡張機能をインストールするには、次のコマンドを実行します。

           ```
           az extension add –n dms-preview
           ```

   * dms の拡張機能が正しくインストールされていることを確認するには、次のコマンドを実行します。

       ```
       az extension list -otable
       ```
       次の出力が表示されます。

       ```
       ExtensionType    Name
       ---------------  ------
       whl              dms
       ```

   * DMS でサポートされているすべてのコマンドを表示するには、次のコマンドを実行します。

       ```
       az dms -h
       ```

   * 複数の Azure サブスクリプションがある場合は、次のコマンドを実行して、DMS サービスのインスタンスのプロビジョニングに使用するサブスクリプションを設定します。

        ```
       az account set -s 97181df2-909d-420b-ab93-1bff15acb6b7
        ```

2. 次のコマンドを実行して、DMS のインスタンスをプロビジョニングします。

   ```
   az dms create -l [location] -n <newServiceName> -g <yourResourceGroupName> --sku-name BusinessCritical_4vCores --subnet/subscriptions/{vnet subscription id}/resourceGroups/{vnet resource group}/providers/Microsoft.Network/virtualNetworks/{vnet name}/subnets/{subnet name} –tags tagName1=tagValue1 tagWithNoValue
   ```

   たとえば、次の設定でサービスを作成するには、下記のコマンドを実行します。

   * 場所:米国東部 2
   * サブスクリプション:97181df2-909d-420b-ab93-1bff15acb6b7
   * リソース グループ名:PostgresDemo
   * DMS サービス名:PostgresCLI

   ```
   az dms create -l eastus2 -g PostgresDemo -n PostgresCLI --subnet /subscriptions/97181df2-909d-420b-ab93-1bff15acb6b7/resourceGroups/ERNetwork/providers/Microsoft.Network/virtualNetworks/AzureDMS-CORP-USC-VNET-5044/subnets/Subnet-1 --sku-name BusinessCritical_4vCores
   ```

   DMS サービスのインスタンスを作成するには、約 10 ～ 12 分かかります。

3. DMS エージェントの IP アドレスを特定して Postgres pg_hba.conf ファイルに追加できるようにするには、次のコマンドを実行します。

    ```
    az network nic list -g <ResourceGroupName>--query '[].ipConfigurations | [].privateIpAddress'
    ```

    例:

    ```
    az network nic list -g PostgresDemo --query '[].ipConfigurations | [].privateIpAddress'
    ```

    次のアドレスのような結果が表示されます。 

    ```
    [
      "172.16.136.18"
    ]
    ```

4. Postgres pg_hba.conf ファイルに、DMS エージェントの IP アドレスを追加します。

    * DMS でのプロビジョニングが完了したら、DMS の IP アドレスをメモしておきます。
    * 次のエントリのように、ソース上の pg_hba.conf ファイルに IP アドレスを追加します。

        ```
        host    all     all     172.16.136.18/10    md5
        host    replication     postgres    172.16.136.18/10    md5
        ```

5. 次に、以下のコマンドを実行して、PostgreSQL の移行プロジェクトを作成します。
    
    ```
    az dms project create -l <location> -g <ResourceGroupName> --service-name <yourServiceName> --source-platform PostgreSQL --target-platform AzureDbforPostgreSQL -n <newProjectName>
    ```

    たとえば、次のパラメーターを使用してプロジェクトを作成するには、下記のコマンドを実行します。

   * 場所:米国中西部
   * リソース グループ名:PostgresDemo
   * サービス名:PostgresCLI
   * プロジェクト名:PGMigration
   * ソース プラットフォーム:PostgreSQL
   * ターゲット プラットフォーム:AzureDbForPostgreSql

     ```
     az dms project create -l eastus2 -n PGMigration -g PostgresDemo --service-name PostgresCLI --source-platform PostgreSQL --target-platform AzureDbForPostgreSql
     ```

6. 次の手順を使用して PostgreSQL 移行タスクを作成します。

    この手順では、接続元 IP、ユーザー ID とパスワード、接続先 IP、ユーザー ID、パスワード、およびタスクの種類を使用して接続を確立します。

   * オプションの完全な一覧を表示するには、次のコマンドを実行します。

       ```
       az dms project task create -h
       ```

       接続元と接続先の両方について、入力パラメーターはオブジェクトの一覧を含む json ファイルを参照します。

       PostgreSQL の接続に対する接続 JSON オブジェクトの形式。
        
       ```
       {
                   "userName": "user name",    // if this is missing or null, you will be prompted
                   "password": null,           // if this is missing or null (highly recommended) you will
               be prompted
                   "serverName": "server name",
                   "databaseName": "database name", // if this is missing, it will default to the 'postgres'
               server
                   "port": 5432                // if this is missing, it will default to 5432
               }
       ```

   * json オブジェクトを一覧表示するデータベース オプションの json ファイルもあります。 PostgreSQL の場合、データベース オプションの JSON オブジェクトの形式は次のとおりです。

       ```
       [
           {
               "name": "source database",
               "target_database_name": "target database",
           },
           ...n
       ]
       ```

   * メモ帳で json ファイルを作成し、次のコマンドをコピーしてこのファイルに貼り付け、C:\DMS\source.json にこのファイルを保存します。

        ```
       {
                   "userName": "postgres",    
                   "password": null,           
               be prompted
                   "serverName": "13.51.14.222",
                   "databaseName": "dvdrental", 
                   "port": 5432                
               }
        ```

   * target.json という別のファイルを作成し、C:\DMS\target.json として保存します。 次のコマンドを含めます。

       ```
       {
               "userName": " dms@builddemotarget",    
               "password": null,           
               "serverName": " builddemotarget.postgres.database.azure.com",
               "databaseName": "inventory", 
               "port": 5432                
           }
       ```

   * 移行するデータベースとしてインベントリを一覧表示する、データベース オプションの json ファイルを作成します。

       ``` 
       [
           {
               "name": "dvdrental",
               "target_database_name": "dvdrental",
           }
       ]
       ```

   * 次のコマンドを実行します。このコマンドは、接続元、接続先、および DB オプションの json ファイルを取り込みます。

       ``` 
       az dms project task create -g PostgresDemo --project-name PGMigration --source-platform postgresql --target-platform azuredbforpostgresql --source-connection-json c:\DMS\source.json --database-options-json C:\DMS\option.json --service-name PostgresCLI --target-connection-json c:\DMS\target.json –task-type OnlineMigration -n runnowtask    
       ```

     この時点で、移行タスクが正常に送信されました。

7. タスクの進行状況を表示するには、次のコマンドを実行します。

   ```
   az dms project task show --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask
   ```

   または

    ```
   az dms project task show --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask --expand output
    ```

8. 展開の出力から migrationState をクエリすることもできます。

    ```
    az dms project task show --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask --expand output --query 'properties.output[].migrationState | [0]' "READY_TO_COMPLETE"
    ```

## <a name="understanding-migration-task-status"></a>移行タスクの状態の理解

出力ファイルには、移行の進行状況を示すいくつかのパラメーターがあります。 たとえば、次の出力ファイルを参照してください。

    ```
    "output": [                                 Database Level
          {
            "appliedChanges": 0,        //Total incremental sync applied after full load
            "cdcDeleteCounter": 0       // Total delete operation  applied after full load
            "cdcInsertCounter": 0,      // Total insert operation applied after full load
            "cdcUpdateCounter": 0,      // Total update operation applied after full load
            "databaseName": "inventory",
            "endedOn": null,
            "fullLoadCompletedTables": 2,   //Number of tables completed full load
            "fullLoadErroredTables": 0, //Number of tables that contain migration error
            "fullLoadLoadingTables": 0, //Number of tables that are in loading status
            "fullLoadQueuedTables": 0,  //Number of tables that are in queued status
            "id": "db|inventory",
            "incomingChanges": 0,       //Number of changes after full load
            "initializationCompleted": true,
            "latency": 0,
            "migrationState": "READY_TO_COMPLETE",  //Status of migration task. READY_TO_COMPLETE means the database is ready for cutover
            "resultType": "DatabaseLevelOutput",
            "startedOn": "2018-07-05T23:36:02.27839+00:00"
          },
          {
            "databaseCount": 1,
            "endedOn": null,
            "id": "dd27aa3a-ed71-4bff-ab34-77db4261101c",
            "resultType": "MigrationLevelOutput",
            "sourceServer": "138.91.123.10",
            "sourceVersion": "PostgreSQL",
            "startedOn": "2018-07-05T23:36:02.27839+00:00",
            "state": "PENDING",
            "targetServer": "builddemotarget.postgres.database.azure.com",
            "targetVersion": "Azure Database for PostgreSQL"
          },
          {                                     Table 1
            "cdcDeleteCounter": 0,
            "cdcInsertCounter": 0,
            "cdcUpdateCounter": 0,
            "dataErrorsCount": 0,
            "databaseName": "inventory",
            "fullLoadEndedOn": "2018-07-05T23:36:20.740701+00:00",  //Full load completed time
            "fullLoadEstFinishTime": "1970-01-01T00:00:00+00:00",
            "fullLoadStartedOn": "2018-07-05T23:36:15.864552+00:00",    //Full load started time
            "fullLoadTotalRows": 10,                    //Number of rows loaded in full load
            "fullLoadTotalVolumeBytes": 7056,               //Volume in Bytes in full load
            "id": "or|inventory|public|actor",          
            "lastModifiedTime": "2018-07-05T23:36:16.880174+00:00",
            "resultType": "TableLevelOutput",
            "state": "COMPLETED",                   //State of migration for this table
            "tableName": "public.catalog",              //Table name
            "totalChangesApplied": 0                //Total sync changes that applied after full load
          },
          {                                     Table 2
            "cdcDeleteCounter": 0,
            "cdcInsertCounter": 50,
            "cdcUpdateCounter": 0,
            "dataErrorsCount": 0,
            "databaseName": "inventory",
            "fullLoadEndedOn": "2018-07-05T23:36:23.963138+00:00",
            "fullLoadEstFinishTime": "1970-01-01T00:00:00+00:00",
            "fullLoadStartedOn": "2018-07-05T23:36:19.302013+00:00",
            "fullLoadTotalRows": 112,
            "fullLoadTotalVolumeBytes": 46592,
            "id": "or|inventory|public|address",
            "lastModifiedTime": "2018-07-05T23:36:20.308646+00:00",
            "resultType": "TableLevelOutput",
            "state": "COMPLETED",
            "tableName": "public.orders",
            "totalChangesApplied": 0
          }
        ],                          DMS migration task state
        "state": "Running", //Migration task state – Running means it is still listening to any changes that might come in                  
        "taskType": null
      },
      "resourceGroup": "PostgresDemo",
      "type": "Microsoft.DataMigration/services/projects/tasks"
    ```

## <a name="cutover-migration-task"></a>一括移行タスク

全体の読み込みが完了すると、データベースの一括移行の準備が整います。 新しいトランザクションによるソース サーバーのビジー状態によっては、DMS タスクで、全体の読み込みが完了した後でも変更が適用されている可能性があります。

すべてのデータを確実に取り込むには、ソース データベースとターゲット データベース間の行数を検証します。 たとえば、次のコマンドを使用できます。

```
"migrationState": "READY_TO_COMPLETE", //Status of migration task. READY_TO_COMPLETE means database is ready for cutover
 "incomingChanges": 0,  //continue to check for a period of 5-10 minutes to make sure no new incoming changes that need to be applied to the target server
   "fullLoadTotalRows": 10, //full load for table 1
    "cdcDeleteCounter": 0,  //delete, insert and update counter on incremental sync after full load
    "cdcInsertCounter": 50,
    "cdcUpdateCounter": 0,
     "fullLoadTotalRows": 112,  //full load for table 2
```

1. 次のコマンドを使用して、データベースの一括移行タスクを実行します。

    ```
    az dms project task cutover -h
    ```

    例:

    ```
    az dms project task cutover --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask  --database-name Inventory
    ```

2. 一括移行の進行状況を監視するには、次のコマンドを実行します。

    ```
    az dms project task show --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask
    ```

## <a name="service-project-task-cleanup"></a>サービス、プロジェクト、タスクのクリーンアップ

DMS タスク、プロジェクト、またはサービスをキャンセルまたは削除する必要がある場合は、次の順序でキャンセルを実行します。

* 実行中のタスクを取り消す
* ジョブを削除する
* プロジェクトを削除する
* DMS サービスを削除する

1. 実行中のタスクをキャンセルするには、次のコマンドを使用します。

    ```
    az dms project task cancel --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask
     ```

2. 実行中のタスクを削除するには、次のコマンドを使用します。
    ```
    az dms project task delete --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask
    ```

3. 実行中のプロジェクトをキャンセルするには、次のコマンドを使用します。
     ```
    az dms project task cancel -n runnowtask --project-name PGMigration -g PostgresDemo --service-name PostgresCLI
     ```

4. 実行中のプロジェクトを削除するには、次のコマンドを使用します。
    ```
    az dms project task delete -n runnowtask --project-name PGMigration -g PostgresDemo --service-name PostgresCLI
    ```

5. DMS サービスを削除するには、次のコマンドを使用します。

     ```
    az dms delete -g ProgresDemo -n PostgresCLI
     ```

## <a name="next-steps"></a>次の手順

* Azure Database for PostgreSQL へのオンライン移行の実行時の既知の問題と制限事項については、[Azure Database for PostgreSQL のオンライン移行に伴う既知の問題と回避策](known-issues-azure-postgresql-online.md)に関する記事を参照してください。
* Azure Database Migration Service の詳細については、「[Azure Database Migration Service とは](https://docs.microsoft.com/azure/dms/dms-overview)」を参照してください。
* Azure Database for PostgreSQL の詳細については、「[Azure Database for PostgreSQL とは](https://docs.microsoft.com/azure/postgresql/overview)」を参照してください。