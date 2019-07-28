---
title: チュートリアル:Azure Database Migration Service を使用して MySQL の Azure Database for MySQL へのオンライン移行を実行する | Microsoft Docs
description: Azure Database Migration Service を使用して、オンプレミスの MySQL から Azure Database for MySQL にオンライン移行を実行する方法を説明します。
services: dms
author: hjtoland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 05/24/2019
ms.openlocfilehash: 5a35df5b72f51f4ef725b3d764e7dc2c80c19ec2
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/27/2019
ms.locfileid: "66240763"
---
# <a name="tutorial-migrate-mysql-to-azure-database-for-mysql-online-using-dms"></a>チュートリアル:DMS を使用して MySQL をオンラインの Azure Database for MySQL に移行する

Azure Database Migration Service を使用して、最小限のダウンタイムでデータベースをオンプレミスの MySQL インスタンスから [Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/) に移行できます。 つまり、アプリケーションにとって最小限のダウンタイムで移行を実現できます。 このチュートリアルでは、Azure Database Migration Service のオンライン移行アクティビティを使用して、**Employees** サンプル データベースを MySQL 5.7 のオンプレミス インスタンスから Azure Database for MySQL に移行します。

このチュートリアルでは、以下の内容を学習します。
> [!div class="checklist"]
>
> * mysqldump ユーティリティを使用して、サンプル スキーマを移行する。
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

* [MySQL Community Edition](https://dev.mysql.com/downloads/mysql/) 5.6 または 5.7 をダウンロードしてインストールします。 オンプレミスの MySQL のバージョンは、Azure Database for MySQL の バージョンと一致している必要があります。 たとえば、MySQL 5.6 は Azure Database for MySQL 5.6 にのみ移行でき、5.7 にアップグレードすることはできません。
* [Azure Database for MySQL でインスタンスを作成します](https://docs.microsoft.com/azure/mysql/quickstart-create-mysql-server-database-using-azure-portal)。 Azure Portal を使用したデータベースの接続と作成方法の詳細については、「[MySQL Workbench を使用した接続とデータの照会](https://docs.microsoft.com/azure/mysql/connect-workbench)」を参照してください。  
* Azure Resource Manager デプロイ モデルを使用して、Azure Database Migration Service 用の Azure 仮想ネットワーク (VNet) を作成します。これで、[ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) または [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) を使用したオンプレミスのソース サーバーへのサイト間接続が提供されます。 VNet の作成方法の詳細については、[Virtual Network のドキュメント](https://docs.microsoft.com/azure/virtual-network/)を参照してください。特に、詳細な手順が記載されたクイックスタートの記事を参照してください。

    > [!NOTE]
    > VNet のセットアップ中、Microsoft へのネットワーク ピアリングに ExpressRoute を使用する場合は、サービスのプロビジョニング先となるサブネットに、次のサービス [エンドポイント](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)を追加してください。
    > * ターゲット データベース エンドポイント (SQL エンドポイント、Cosmos DB エンドポイントなど)
    > * ストレージ エンドポイント
    > * サービス バス エンドポイント
    >
    > Azure Database Migration Service にはインターネット接続がないため、この構成が必要となります。

* VNet ネットワーク セキュリティ グループの規則によって、Azure Database Migration Service への以下のインバウンド通信ポートが確実にブロックされないようにします:443、53、9354、445、12000。 Azure VNet NSG トラフィックのフィルター処理の詳細については、[ネットワーク セキュリティ グループによるネットワーク トラフィックのフィルター処理](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm)に関する記事を参照してください。
* [データベース エンジン アクセスのために Windows ファイアウォール](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)を構成します。
* Azure Database Migration Service でソース MySQL Server にアクセスできるように Windows ファイアウォールを開きます。既定では TCP ポート 3306 が使用されます。
* ソース データベースの前でファイアウォール アプライアンスを使用する場合は、Azure Database Migration Service が移行のためにソース データベースにアクセスできるように、ファイアウォール規則を追加することが必要な場合があります。
* Azure Database for MySQL のサーバー レベルの[ファイアウォール規則](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)を作成して、Azure Database Migration Service からターゲット データベースにアクセスできるようにします。 Azure Database Migration Service に使用される VNet のサブネット範囲を指定します。
* ソース MySQL は、サポートされている MySQL Community Edition で実行されている必要があります。 MySQL インスタンスのバージョンを確認するには、MySQL ユーティリティまたは MySQL Workbench で、次のコマンドを実行します。

    ```
    SELECT @@version;
    ```

* Azure Database for MySQL は、InnoDB テーブルのみをサポートします。 MyISAM テーブルを InnoDB に変換するには、[MyISAM から InnoDB へのテーブルの変換](https://dev.mysql.com/doc/refman/5.7/en/converting-tables-to-innodb.html)に関する記事を参照してください。

* 次の構成を使用して、ソース データベースの my.ini (Windows) または my.cnf (Unix) ファイルのバイナリ ログを有効にします。

  * **server_id** = 1 以上 (MySQL 5.6 にのみ関連)
  * **log-bin** =\<path> (MySQL 5.6 にのみ関連)    例: log-bin = E:\MySQL_logs\BinLog
  * **binlog_format** = row
  * **Expire_logs_days** = 5 (0 を使用しないことをお勧めします。MySQL 5.6 にのみ関連)
  * **Binlog_row_image** = full (MySQL 5.6 にのみ関連)
  * **log_slave_updates** = 1

* ユーザーは、次の特権がある ReplicationAdmin ロールを持っている必要があります:

  * **レプリケーション クライアント** - 変更処理タスクでのみ必須です。 つまり、全体の読み込みのみのタスクではこの特権は必要ありません。
  * **レプリケーション レプリカ** - 変更処理タスクでのみ必須です。 つまり、全体の読み込みのみのタスクではこの特権は必要ありません。
  * **スーパー** - MySQL 5.6.6 より前のバージョンでのみ必須です。

## <a name="migrate-the-sample-schema"></a>サンプル スキーマを移行する

テーブル スキーマ、インデックス、ストアド プロシージャなどのすべてのデータベース オブジェクトを完了するには、ソース データベースからスキーマを抽出し、データベースに適用する必要があります。 スキーマを抽出するには、mysqldump と `--no-data` パラメーターを使用できます。

オンプレミス システム内に MySQL **Employees** サンプル データベースがあると仮定した場合、mysqldump を使用してスキーマを移行するためのコマンドは次のようになります。

```
mysqldump -h [servername] -u [username] -p[password] --databases [db name] --no-data > [schema file path]
```

例:

```
mysqldump -h 10.10.123.123 -u root -p --databases employees --no-data > d:\employees.sql
```

ターゲットの Azure Database for MySQL にスキーマをインポートするには、次のコマンドを実行します。

```
mysql.exe -h [servername] -u [username] -p[password] [database]< [schema file path]
 ```

例:

```
mysql.exe -h shausample.mysql.database.azure.com -u dms@shausample -p employees < d:\employees.sql
 ```

スキーマに外部キーが含まれている場合、移行の初回の読み込みと継続的同期は失敗します。  MySQL Workbench で次のスクリプトを実行して、外部キー削除スクリプトと外部キー追加スクリプトを抽出します。

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

クエリの結果内の外部キー削除 (2 列目) を実行して、外部キーを削除します。

> [!IMPORTANT]
> バックアップを使用してデータをインポートする場合、mysqldump の実行時に CREATE DEFINER コマンドを手動で、または --skip-definer コマンドを使用して削除します。 DEFINER は、作成するのに SUPER 権限が必要であり、Azure Database for MySQL で制限されています。

データにトリガー (insert または update トリガー) が含まれている場合は、ソースからのレプリケートされたデータに先だって、ターゲットにデータの整合性が適用されます。 移行時はターゲットのすべてのテーブル内のトリガーを無効にし、移行の完了後にトリガーを有効にすることをお勧めします。

ターゲット データベース内のトリガーを無効にするには、次のコマンドを使用します。

```
SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
```

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Microsoft.DataMigration リソース プロバイダーを登録する

1. Azure portal にサインインし、 **[すべてのサービス]** を選択し、 **[サブスクリプション]** を選択します。

   ![ポータルのサブスクリプションの表示](media/tutorial-mysql-to-azure-mysql-online/portal-select-subscriptions.png)

2. Azure Database Migration Service のインスタンスを作成するサブスクリプションを選択してから、 **[リソース プロバイダー]** を選びます。

    ![リソース プロバイダーの表示](media/tutorial-mysql-to-azure-mysql-online/portal-select-resource-provider.png)

3. 移行を検索し、**Microsoft.DataMigration** の右側にある **[登録]** を選択します。

    ![リソース プロバイダーの登録](media/tutorial-mysql-to-azure-mysql-online/portal-register-resource-provider.png)

## <a name="create-a-dms-instance"></a>DMS インスタンスを作成する

1. Azure portal で **[+ リソースの作成]** を選択し、Azure Database Migration Service を検索して、ドロップダウン リストから **[Azure Database Migration Service]** を選択します。

    ![Azure Marketplace](media/tutorial-mysql-to-azure-mysql-online/portal-marketplace.png)

2. **[Azure Database Migration Service]** 画面で、 **[作成]** を選択します。

    ![Azure Database Migration Service インスタンスを作成する](media/tutorial-mysql-to-azure-mysql-online/dms-create1.png)
  
3. **[移行サービスの作成]** 画面で、サービスの名前、サブスクリプション、新規または既存のリソース グループを指定します。

4. 既存の VNet を選択するか、新しいものを作成します。

    この VNet が Azure Database Migration Service に、ソース SQL Server とターゲット Azure SQL データベース インスタンスへのアクセスを提供します。

    Azure portal で VNet を作成する方法の詳細については、「[Azure portal を使用した仮想ネットワークの作成](https://aka.ms/DMSVnet)」を参照してください。

5. 価格レベルを選択します。

    コストと価格レベルの詳細については、[価格に関するページ](https://aka.ms/dms-pricing)を参照してください。

    ![Azure Database Migration Service インスタンス設定を構成する](media/tutorial-mysql-to-azure-mysql-online/dms-settings3.png)

6. **[作成]** を選択して、サービスを作成します。

## <a name="create-a-migration-project"></a>移行プロジェクトを作成する

サービスが作成されたら、Azure portal 内でそのサービスを探して開き、新しい移行プロジェクトを作成します。

1. Azure ポータルで、 **[All services]\(すべてのサービス\)** を選択し、Azure Database Migration Service を検索して、**Azure Database Migration Service** を選択します。

      ![Azure Database Migration Service のすべてのインスタンスを検索する](media/tutorial-mysql-to-azure-mysql-online/dms-search.png)

2. **[Azure Database Migration Services]** 画面で、作成した Azure Database Migration Service インスタンスの名前を検索して選択します。

     ![Azure Database Migration Service のインスタンスを検索する](media/tutorial-mysql-to-azure-mysql-online/dms-instance-search.png)

3. **[+ 新しい移行プロジェクト]** を選択します。
4. **[新しい移行プロジェクト]** 画面で、プロジェクトの名前を指定し、 **[ソース サーバーの種類]** テキスト ボックスで **[MySQL]** を選択した後、 **[ターゲット サーバーの種類]** テキスト ボックスで **[AzureDbForMySQL]** を選択します。
5. **[アクティビティの種類を選択します]** セクションで、 **[オンライン データの移行]** を選択します。

    ![Database Migration Service プロジェクトを作成する](media/tutorial-mysql-to-azure-mysql-online/dms-create-project4.png)

    > [!NOTE]
    > または、 **[プロジェクトのみを作成します]** を選択して移行プロジェクトを作成しておき、移行は後で実行することもできます。

6. **[保存]** を選択します。DMS を使用して正常にデータを移行するための要件を確認した後、 **[アクティビティの作成と実行]** を選択します。

## <a name="specify-source-details"></a>ソース詳細を指定する

1. **[Add Source Details]\(ソースの詳細の追加\)** 画面で、ソース MySQL インスタンスの接続の詳細を指定します。

    ![[Add Source Details]\(ソースの詳細の追加\) 画面](media/tutorial-mysql-to-azure-mysql-online/dms-add-source-details.png)

## <a name="specify-target-details"></a>ターゲット詳細を指定する

1. **[保存]** を選択し、 **[対象の詳細]** 画面でターゲット Azure Database for MySQL の接続の詳細を指定します。これは、mysqldump を使用して **Employees** スキーマがデプロイされた Azure Database for MySQL の事前プロビジョニング済みのインスタンスです。

    ![[ターゲットの詳細] 画面](media/tutorial-mysql-to-azure-mysql-online/dms-add-target-details.png)

2. **[保存]** を選択し、 **[ターゲット データベースへマッピング]** 画面で、移行用のソース データベースとターゲット データベースをマップします。

    ターゲット データベースにソース データベースと同じデータベース名が含まれている場合、Azure Database Migration Service では、既定でターゲット データベースが選択されます。

    ![ターゲット データベースにマップする](media/tutorial-mysql-to-azure-mysql-online/dms-map-target-details.png)

3. **[保存]** を選択し、 **[移行の概要]** 画面で、 **[アクティビティ名]** テキスト ボックスに移行アクティビティの名前を指定します。概要を見直して、ソースとターゲットの詳細が先ほど指定した内容と一致していることを確認します。

    ![移行の概要](media/tutorial-mysql-to-azure-mysql-online/dms-migration-summary.png)

## <a name="run-the-migration"></a>移行を実行する

* **[移行の実行]** を選択します。

    移行アクティビティ ウィンドウが表示されます。アクティビティの **[状態]** は **[初期化中]** になります。

## <a name="monitor-the-migration"></a>移行を監視する

1. 移行アクティビティ画面で、移行の **[状態]** が **[完了]** になるまで **[最新の情報に更新]** を選択して表示を更新します。

     ![アクティビティの状態 - 完了](media/tutorial-mysql-to-azure-mysql-online/dms-activity-completed.png)

2. **[データベース名]** で、特定のデータベースを選択して、**データ全体の読み込み**操作と**増分データ同期**操作の移行状態を取得します。

    データ全体の読み込みには初回の読み込みの移行状態が表示され、データ増分同期には変更データ キャプチャ (CDC) の状態が表示されます。

     ![アクティビティの状態 - 全体の読み込み完了](media/tutorial-mysql-to-azure-mysql-online/dms-activity-full-load-completed.png)

     ![アクティビティの状態 - 増分データ同期](media/tutorial-mysql-to-azure-mysql-online/dms-activity-incremental-data-sync.png)

## <a name="perform-migration-cutover"></a>一括移行を実行する

初回の全体の読み込みが完了すると、データベースは **[一括準備完了]** とマークされます。

1. データベースの移行を完了する準備ができたら、 **[一括で開始]** を選択します。

    ![一括で開始](media/tutorial-mysql-to-azure-mysql-online/dms-start-cutover.png)

2. ソース データベースに対するすべての受信トランザクションを必ず停止してください。 **[保留中の変更]** カウンターが **0** を示すまで待ってください。
3. **[確認]** を選択し、 **[適用]** を選択します。
4. データベースの移行の状態に **[完了]** が表示されたら、アプリケーションを新しいターゲット Azure SQL Database に接続します。

## <a name="next-steps"></a>次の手順

* Azure Database for MySQL へのオンライン移行の実行時の既知の問題と制限事項については、[Azure Database for MySQL のオンライン移行に伴う既知の問題と回避策](known-issues-azure-mysql-online.md)に関する記事を参照してください。
* Azure Database Migration Service の詳細については、「[Azure Database Migration Service とは](https://docs.microsoft.com/azure/dms/dms-overview)」を参照してください。
* Azure Database for MySQL の詳細については、「[Azure Database for MySQL とは](https://docs.microsoft.com/azure/mysql/overview)」の記事を参照してください。
