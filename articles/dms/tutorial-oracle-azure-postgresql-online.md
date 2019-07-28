---
title: チュートリアル:Azure Database Migration Service を使用して Oracle の Azure Database for PostgreSQL へのオンライン移行を実行する | Microsoft Docs
description: Azure Database Migration Service を使用して、オンプレミスまたは仮想マシンの Oracle から Azure Database for PostgreSQL にオンライン移行を実行する方法を説明します。
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
ms.openlocfilehash: 0b3af3d29e6e938f0301d751a79170c7c1964b45
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/27/2019
ms.locfileid: "66243792"
---
# <a name="tutorial-migrate-oracle-to-azure-database-for-postgresql-online-using-dms-preview"></a>チュートリアル:DMS を使用して Oracle を Azure Database for PostgreSQL にオンラインで移行する (プレビュー)

Azure Database Migration Service を使用して、最小限のダウンタイムでデータベースをオンプレミスまたは仮想マシンでホストされている Oracle データベースから [Azure Database for PostgreSQL](https://docs.microsoft.com/azure/postgresql/) に移行できます。 つまり、アプリケーションにとって最小限のダウンタイムで移行を完了できます。 このチュートリアルでは、Azure Database Migration Service のオンライン移行アクティビティを使用して、**HR** サンプル データベースを Oracle 11g のオンプレミスまたは仮想マシン インスタンスから Azure Database for PostgreSQL に移行します。

このチュートリアルでは、以下の内容を学習します。
> [!div class="checklist"]
>
> * ora2pg ツールを使用して移行作業を評価する。
> * ora2pg ツールを使用して、サンプル スキーマを移行する。
> * Azure Database Migration Service のインスタンスを作成する。
> * Azure Database Migration Service を使用して移行プロジェクトを作成する。
> * 移行を実行する。
> * 移行を監視する。

> [!NOTE]
> Azure Database Migration Service を使用してオンライン移行を実行するには、Premium 価格レベルに基づいてインスタンスを作成する必要があります。

> [!IMPORTANT]
> 最適な移行エクスペリエンスのために、ターゲット データベースと同じ Azure リージョンに Azure Database Migration Service のインスタンスを作成することをお勧めします。 リージョンや地域をまたいでデータを移動する場合、移行プロセスが遅くなり、エラーが発生する可能性があります。

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

この記事では、Oracle から Azure Database for PostgreSQL へのオンライン移行の実行方法について説明します。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下を実行する必要があります。

* [Oracle 11g Release 2 (Standard Edition、Standard Edition One、または Enterprise Edition)](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/index.html) をダウンロードしてインストールします。
* サンプルの **HR** データベースを[ここ](https://docs.oracle.com/database/121/COMSC/installation.htm#COMSC00002)からダウンロードします。
* ora2pg をダウンロードして [Windows](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Steps%20to%20Install%20ora2pg%20on%20Windows.pdf) または [Linux](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Steps%20to%20Install%20ora2pg%20on%20Linux.pdf) のいずれかにインストールします。
* [Azure Database for PostgreSQL のインスタンスを作成します](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal)。
* この[ドキュメント](https://docs.microsoft.com/azure/postgresql/tutorial-design-database-using-azure-portal)の指示に従ってインスタンスに接続しデータベースを作成します。
* Azure Resource Manager デプロイ モデルを使用して、Azure Database Migration Service 用の Azure 仮想ネットワーク (VNet) を作成します。これで、[ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) または [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) を使用したオンプレミスのソース サーバーへのサイト間接続が提供されます。 VNet の作成方法の詳細については、[Virtual Network のドキュメント](https://docs.microsoft.com/azure/virtual-network/)、特に詳細な手順を提供するクイックスタートの記事を参照してください。

  > [!NOTE]
  > VNet のセットアップ中、Microsoft へのネットワーク ピアリングに ExpressRoute を使用する場合は、サービスのプロビジョニング先となるサブネットに、次のサービス [エンドポイント](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)を追加してください。
  > * ターゲット データベース エンドポイント (SQL エンドポイント、Cosmos DB エンドポイントなど)
  > * ストレージ エンドポイント
  > * サービス バス エンドポイント
  >
  > Azure Database Migration Service にはインターネット接続がないため、この構成が必要となります。

* VNet のネットワーク セキュリティ グループ (NSG) の規則によって、Azure Database Migration Service への以下のインバウンド通信ポートがブロックされないことを確認します: 443、53、9354、445、12000。 Azure VNet NSG トラフィックのフィルター処理の詳細については、[ネットワーク セキュリティ グループによるネットワーク トラフィックのフィルター処理](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm)に関する記事を参照してください。
* [データベース エンジン アクセスのために Windows ファイアウォール](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)を構成します。
* Azure Database Migration Service がソースの Oracle Server にアクセスできるように Windows ファイアウォールを開きます。既定では TCP ポート 1521 が使用されます。
* ソース データベースの前でファイアウォール アプライアンスを使用する場合は、Azure Database Migration Service が移行のためにソース データベースにアクセスできるように、ファイアウォール規則を追加することが必要な場合があります。
* Azure Database for PostgreSQL のサーバー レベルの[ファイアウォール規則](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)を作成して、Azure Database Migration Service がターゲット データベースにアクセスできるようにします。 Azure Database Migration Service に使用される VNet のサブネット範囲を指定します。
* ソースの Oracle データベースへのアクセスを有効にします。

  > [!NOTE]
  > ソースに接続するユーザーには、DBA ロールが必要です。

  * データ変更をキャプチャするため、Azure Database Migration Service での増分同期にはアーカイブ再実行ログが必要です。 次の手順に従って Oracle ソースを構成します。
    * 次のコマンドを実行して、SYSDBA 権限を使用してサインインします。

      ```
      sqlplus (user)/(password) as sysdba
      ```

    * 次のコマンドを実行して、データベース インスタンスをシャットダウンします。

      ```
      SHUTDOWN IMMEDIATE;
      ```

      確認 `'ORACLE instance shut down'` を待ちます。

    * 次のコマンドを実行して、新しいインスタンスを開始し、データベースをマウントして (ただし開かないでください)、アーカイブを有効または無効にします。

      ```
      STARTUP MOUNT;
      ```

      データベースをマウントする必要があります。確認 'Oracle instance started' を待ちます。

    * 次のコマンドを実行して、データベースのアーカイブ モードを変更します。

      ```
      ALTER DATABASE ARCHIVELOG;
      ```

    * 次のコマンドを実行して、データベースを通常操作用に開きます。

      ```
      ALTER DATABASE OPEN;
      ```

      ARC ファイルを表示するために再起動が必要な場合があります。

    * 確認するには、次のコマンドを実行します。

      ```
      SELECT log_mode FROM v$database;
      ```

      `'ARCHIVELOG'` 応答を受け取るはずです。 応答が `'NOARCHIVELOG'` の場合は、要件が満たされていません。

  * 次のオプションのいずれかを使用して、レプリケーション用の補足ログを有効にします。

    * **オプション 1**。
      主キーと一意のインデックスを持つすべてのテーブルをカバーするよう、データベース レベルの補足ログを変更します。 検出クエリにより `'IMPLICIT'` が返されます。

      ```
      ALTER DATABASE ADD SUPPLEMENTAL LOG DATA (PRIMARY KEY, UNIQUE) COLUMNS;
      ```

      テーブル レベルの補足ログを変更します。 データ操作がある、主キーまたは一意のインデックスを持たないテーブルに対してのみ実行します。

      ```
      ALTER TABLE [TABLENAME] ADD SUPPLEMENTAL LOG DATA (ALL) COLUMNS;
      ```

    * **オプション 2**。
      すべてのテーブルをカバーするよう、データベース レベルの補足ログを変更します。検出クエリにより `'YES'` が返されます。

      ```
      ALTER DATABASE ADD SUPPLEMENTAL LOG DATA;
      ```

      テーブル レベルの補足ログを変更します。 次のロジックに従って、各テーブルに対し 1 つのステートメントのみ実行します。

      テーブルに主キーがある場合:

      ```
      ALTER TABLE xxx ADD SUPPLEMENTAL LOG DATA (PRIMARY KEY) COLUMNS;
      ```

      テーブルに一意のインデックスがある場合:

      ```
      ALTER TABLE xxx ADD SUPPLEMENTAL LOG GROUP (first unique index columns) ALWAYS;
      ```

      それ以外の場合、次のコマンドを実行します:

      ```
      ALTER TABLE xxx ADD SUPPLEMENTAL LOG DATA (ALL) COLUMNS;
      ```

    確認するには、次のコマンドを実行します。

      ```
      SELECT supplemental_log_data_min FROM v$database;
      ```

    `'YES'` 応答を受け取るはずです。

> [!IMPORTANT]
> このシナリオのパブリック プレビュー リリースでは、Azure Database Migration Service は Oracle バージョン 10g または 11g をサポートしています。 Oracle バージョン 12c 以降を実行しているお客様は、ODBC ドライバーの Oracle への接続に許可される最低限の認証プロトコルが 8 であることに注意してください。 バージョン 12c 以降の Oracle ソースの場合、次のように認証プロトコルを構成する必要があります。
>
> * SQLNET.ORA を更新します。
>
>    ```
>    SQLNET.ALLOWED_LOGON_VERSION_CLIENT = 8
>    SQLNET.ALLOWED_LOGON_VERSION_SERVER = 8
>    ```
>
> * 新しい設定を反映するために、コンピューターを再起動します。
> * 既存ユーザーのパスワードを変更します。
>
>    ```
>    ALTER USER system IDENTIFIED BY {pswd}
>    ```
>
>   詳細については、[この](http://www.dba-oracle.com/t_allowed_login_version_server.htm)ページを参照してください。
>
> 最後に、認証プロトコルを変更するとクライアント認証に影響する可能性があることに注意してください。

## <a name="assess-the-effort-for-an-oracle-to-azure-database-for-postgresql-migration"></a>Oracle から Azure Database for PostgreSQL への移行作業の評価

Oracle から Azure Database for PostgreSQL への移行に必要な作業の評価には、ora2pg を使用することをお勧めします。 `ora2pg -t SHOW_REPORT` ディレクティブを使用して、すべての Oracle オブジェクト、推定移行コスト (開発者の日数単位)、変換の一部として特別な注意が必要になる可能性がある特定のデータベース オブジェクトがリストされたレポートを作成します。

ほとんどのお客様は、評価レポートのレビューと自動および手動の変換作業の検討にかなりの時間を費やします。

ora2pg を構成して実行し、評価レポートを作成する方法については、『[Oracle to Azure Database for PostgreSQL Cookbook](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf)』 (Oracle から Azure Database for PostgreSQL への移行手順) の「**Premigration: Assessment**」(移行前: 評価) セクションを参照してください。 サンプルの ora2pg 評価レポートは、[ここ](http://ora2pg.darold.net/report.html)で参照できます。

## <a name="export-the-oracle-schema"></a>Oracle スキーマのエクスポート

Oracle スキーマとその他の Oracle オブジェクト (型、プロシージャ、関数など) を、Azure Database for PostgreSQL と互換性のあるスキーマに変換するには、ora2pg の使用をお勧めします。 ora2pg には、特定のデータ型の事前定義に役立つ多くのディレクティブが含まれています。 たとえば、`DATA_TYPE` ディレクティブを使用して、すべての NUMBER(*,0) を NUMERIC(38) ではなく bigint に置き換えることができます。

ora2pg を実行して、.sql ファイル内の各データベース オブジェクトをエクスポートすることができます。 その後、psql を使用して .sql ファイルを Azure Database for PostgreSQL にインポートする前にレビューすることができます。または、PgAdmin で .SQL スクリプトを実行することができます。

```
psql -f [FILENAME] -h [AzurePostgreConnection] -p 5432 -U [AzurePostgreUser] -d database 
```

例:

```
psql -f %namespace%\schema\sequences\sequence.sql -h server1-server.postgres.database.azure.com -p 5432 -U username@server1-server -d database
```

スキーマ変換用に ora2pg を構成して実行する方法については、『[Oracle to Azure Database for PostgreSQL Cookbook](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf)』 (Oracle から Azure Database for PostgreSQL への移行手順) の「**Migration: Schema and data**」(移行: スキーマとデータ) セクションを参照してください。

## <a name="set-up-the-schema-in-azure-database-for-postgresql"></a>Azure Database for PostgreSQL でのスキーマの設定

既定では、Oracle では schema.table.column はすべて大文字で保持されますが、PostgreSQL では schema.table.column は小文字で保持されます。 Azure Database Migration Service で Oracle から Azure Database for PostgreSQL へのデータの移動を開始するには、schema.table.column を Oracle ソースと同じ文字形式にする必要があります。

たとえば、Oracle ソースのスキーマが “HR”.”EMPLOYEES”.”EMPLOYEE_ID” の場合、PostgreSQL スキーマでも同じ形式を使用する必要があります。

schema.table.column の文字形式が Oracle と Azure Database for PostgreSQL で確実に同じとなるようにするため、次の手順を行うことをお勧めします。

> [!NOTE]
> 別のアプローチを使用して大文字のスキーマを派生させることができます。 Microsoft は、この手順の改善と自動化に取り組んでいます。

1. ora2pg を使用してスキーマを小文字でエクスポートします。 テーブル作成 sql スクリプトで、手動によりスキーマを大文字 "SCHEMA" で作成します。
2. トリガー、シーケンス、プロシージャ、型、関数などの残りの Oracle オブジェクトを、Azure Database for PostgreSQL にインポートします。
3. TABLE と COLUMN を大文字にするには、次のスクリプトを実行します。

   ```
   -- INPUT: schema name
   set schema.var = “HR”;

   -- Generate statements to rename tables and columns
   SELECT 1, 'SET search_path = "' ||current_setting('schema.var')||'";'
   UNION ALL 
   SELECT 2, 'alter table "'||c.relname||'" rename '||a.attname||' to "'||upper(a.attname)||'";'
   FROM pg_class c
   JOIN pg_attribute a ON a.attrelid = c.oid
   JOIN pg_type t ON a.atttypid = t.oid
   LEFT JOIN pg_catalog.pg_constraint r ON c.oid = r.conrelid
    AND r.conname = a.attname
   WHERE c.relnamespace = (select oid from pg_namespace where nspname=current_setting('schema.var')) AND a.attnum > 0 AND c.relkind ='r'
   UNION ALL
   SELECT 3, 'alter table '||c.relname||' rename to "'||upper(c.relname)||'";'
   FROM pg_catalog.pg_class c
    LEFT JOIN pg_catalog.pg_namespace n ON n.oid = c.relnamespace
   WHERE c.relkind ='r' AND n.nspname=current_setting('schema.var')
   ORDER BY 1;
   ```

* 全体の読み込みを実行するため、外部キーをターゲット データベースにドロップします。 外部キーのドロップに使用できるスクリプトについては、[ここ](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online)の記事の「**サンプル スキーマを移行する**」セクションを参照してください。
* Azure Database Migration Service を使い、全体の読み込みと同期を実行します。
* ターゲットの Azure Database for PostgreSQL インスタンス内のデータがソースに取り込まれたら、Azure Database Migration Service でデータベース カットオーバーを実行します。
* SCHEMA、TABLE、COLUMN を小文字にするには (アプリケーションのクエリのために Azure Database for PostgreSQL のスキーマをこのようにする必要がある場合)、次のスクリプトを実行します。

  ```
  -- INPUT: schema name
  set schema.var = hr;
  
  -- Generate statements to rename tables and columns
  SELECT 1, 'SET search_path = "' ||current_setting('schema.var')||'";'
  UNION ALL
  SELECT 2, 'alter table "'||c.relname||'" rename "'||a.attname||'" to '||lower(a.attname)||';'
  FROM pg_class c
  JOIN pg_attribute a ON a.attrelid = c.oid
  JOIN pg_type t ON a.atttypid = t.oid
  LEFT JOIN pg_catalog.pg_constraint r ON c.oid = r.conrelid
     AND r.conname = a.attname
  WHERE c.relnamespace = (select oid from pg_namespace where nspname=current_setting('schema.var')) AND a.attnum > 0 AND c.relkind ='r'
  UNION ALL
  SELECT 3, 'alter table "'||c.relname||'" rename to '||lower(c.relname)||';'
  FROM pg_catalog.pg_class c
     LEFT JOIN pg_catalog.pg_namespace n ON n.oid = c.relnamespace
  WHERE c.relkind ='r' AND n.nspname=current_setting('schema.var')
  ORDER BY 1;
  ```

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Microsoft.DataMigration リソース プロバイダーを登録する

1. Azure portal にサインインし、 **[すべてのサービス]** を選択し、 **[サブスクリプション]** を選択します。

   ![ポータルのサブスクリプションの表示](media/tutorial-oracle-azure-postgresql-online/portal-select-subscriptions.png)

2. Azure Database Migration Service のインスタンスを作成するサブスクリプションを選択して、 **[リソース プロバイダー]** を選択します。

    ![リソース プロバイダーの表示](media/tutorial-oracle-azure-postgresql-online/portal-select-resource-provider.png)

3. 移行を検索し、**Microsoft.DataMigration** の右側にある **[登録]** を選択します。

    ![リソース プロバイダーの登録](media/tutorial-oracle-azure-postgresql-online/portal-register-resource-provider.png)

## <a name="create-a-dms-instance"></a>DMS インスタンスを作成する

1. Azure portal で **[+ リソースの作成]** を選択し、Azure Database Migration Service を検索して、ドロップダウン リストから **[Azure Database Migration Service]** を選択します。

    ![Azure Marketplace](media/tutorial-oracle-azure-postgresql-online/portal-marketplace.png)

2. **[Azure Database Migration Service]** 画面で、 **[作成]** を選択します。

    ![Azure Database Migration Service インスタンスを作成する](media/tutorial-oracle-azure-postgresql-online/dms-create2.png)
  
3. **[移行サービスの作成]** 画面で、サービスの名前、サブスクリプション、新規または既存のリソース グループを指定します。

4. 既存の VNet を選択するか、新しいものを作成します。

    この VNet によって、Azure Database Migration Service に、ソース Oracle とターゲット Azure Database for PostgreSQL インスタンスへのアクセスが提供されます。

    Azure portal で VNet を作成する方法の詳細については、「[Azure portal を使用した仮想ネットワークの作成](https://aka.ms/DMSVnet)」を参照してください。

5. 価格レベルを選択します。

    コストと価格レベルの詳細については、[価格に関するページ](https://aka.ms/dms-pricing)を参照してください。

    ![Azure Database Migration Service インスタンス設定を構成する](media/tutorial-oracle-azure-postgresql-online/dms-settings5.png)

6. **[作成]** を選択して、サービスを作成します。

## <a name="create-a-migration-project"></a>移行プロジェクトを作成する

サービスが作成されたら、Azure portal 内でそのサービスを探して開き、新しい移行プロジェクトを作成します。

1. Azure ポータルで、 **[All services]\(すべてのサービス\)** を選択し、Azure Database Migration Service を検索して、**Azure Database Migration Service** を選択します。

    ![Azure Database Migration Service のすべてのインスタンスを検索する](media/tutorial-oracle-azure-postgresql-online/dms-search.png)

2. **[Azure Database Migration Services]** 画面で、作成した Azure Database Migration Service インスタンスの名前を検索して選択します。

    ![Azure Database Migration Service のインスタンスを検索する](media/tutorial-oracle-azure-postgresql-online/dms-instance-search.png)

3. **[+ 新しい移行プロジェクト]** を選択します。
4. **[新しい移行プロジェクト]** 画面で、プロジェクトの名前を指定し、 **[ソース サーバーの種類を選択する]** テキスト ボックスで **Oracle** を選択した後、 **[ターゲット サーバーの種類]** テキスト ボックスで **Azure Database for PostgreSQL** を選択します。
5. **[アクティビティの種類を選択します]** セクションで、 **[オンライン データの移行]** を選択します。

   ![Database Migration Service プロジェクトを作成する](media/tutorial-oracle-azure-postgresql-online/dms-create-project5.png)

   > [!NOTE]
   > または、 **[プロジェクトのみを作成します]** を選択して移行プロジェクトを作成しておき、移行は後で実行することもできます。

6. **[保存]** を選択します。Azure Database Migration Service を使用して正常にオンライン移行を実行するための要件を確認した後、 **[アクティビティの作成と実行]** を選択します。

## <a name="specify-source-details"></a>ソース詳細を指定する

* **[ソースの追加に関する詳細]** 画面で、ソース Oracle インスタンスの接続の詳細を指定します。

  ![[Add Source Details]\(ソースの詳細の追加\) 画面](media/tutorial-oracle-azure-postgresql-online/dms-add-source-details.png)

## <a name="upload-oracle-oci-driver"></a>Oracle OCI ドライバーをアップロードする

1. **[保存]** を選択し、 **[OCI ドライバーのインストール]** 画面でご自分の Oracle アカウントにサインインして、ドライバーの **instantclient-basiclite-windows.x64-12.2.0.1.0.zip** (37,128,586 Byte(s)) (SHA1 Checksum:865082268) を[ここ](https://www.oracle.com/technetwork/topics/winx64soft-089540.html#ic_winx64_inst)からダウンロードします。
2. ドライバーを共有フォルダーにダウンロードします。

   このフォルダーが、最低限の読み取り専用アクセスで指定したユーザー名に共有されていることを確認します。 Azure Database Migration Service は指定したユーザー名を偽装してこの共有にアクセスして読み取り、OCI ドライバーを Azure にアップロードします。

   指定するユーザー名は Windows ユーザー アカウントである必要があります。

   ![OCI ドライバーをインストールする](media/tutorial-oracle-azure-postgresql-online/dms-oci-driver-install.png)

## <a name="specify-target-details"></a>ターゲット詳細を指定する

1. **[保存]** を選択し、 **[ターゲットの詳細]** 画面でターゲット Azure Database for PostgreSQL の接続の詳細を指定します。これは、**HR** スキーマがデプロイされた Azure Database for PostgreSQL の事前プロビジョニング済みのインスタンスです。

    ![[ターゲットの詳細] 画面](media/tutorial-oracle-azure-postgresql-online/dms-add-target-details1.png)

2. **[保存]** を選択し、 **[ターゲット データベースへマッピング]** 画面で、移行用のソース データベースとターゲット データベースをマップします。

    ターゲット データベースにソース データベースと同じデータベース名が含まれている場合、Azure Database Migration Service では、既定でターゲット データベースが選択されます。

    ![ターゲット データベースにマップする](media/tutorial-oracle-azure-postgresql-online/dms-map-target-details.png)

3. **[保存]** を選択し、 **[移行の概要]** 画面で、 **[アクティビティ名]** テキスト ボックスに移行アクティビティの名前を指定します。概要を見直して、ソースとターゲットの詳細が先ほど指定した内容と一致していることを確認します。

    ![移行の概要](media/tutorial-oracle-azure-postgresql-online/dms-migration-summary.png)

## <a name="run-the-migration"></a>移行を実行する

* **[移行の実行]** を選択します。

  移行アクティビティ ウィンドウが表示されます。アクティビティの **[状態]** は **[初期化中]** になります。

## <a name="monitor-the-migration"></a>移行を監視する

1. 移行アクティビティ画面で、移行の **[状態]** が **[実行中]** になるまで **[最新の情報に更新]** を選択して表示を更新します。

     ![アクティビティの状態 - 実行中](media/tutorial-oracle-azure-postgresql-online/dms-activity-running.png)

2. **[データベース名]** で、特定のデータベースを選択して、**データ全体の読み込み**操作と**増分データ同期**操作の移行状態を取得します。

    データ全体の読み込みには初回の読み込みの移行状態が表示され、データ増分同期には変更データ キャプチャ (CDC) の状態が表示されます。

     ![アクティビティの状態 - 全体の読み込み完了](media/tutorial-oracle-azure-postgresql-online/dms-activity-full-load-completed.png)

     ![アクティビティの状態 - 増分データ同期](media/tutorial-oracle-azure-postgresql-online/dms-activity-incremental-data-sync.png)

## <a name="perform-migration-cutover"></a>一括移行を実行する

初回の全体の読み込みが完了すると、データベースは **[一括準備完了]** とマークされます。

1. データベースの移行を完了する準備ができたら、 **[一括で開始]** を選択します。

2. ソース データベースに対するすべての受信トランザクションを必ず停止してください。 **[保留中の変更]** カウンターが **0** を示すまで待ってください。

   ![一括で開始](media/tutorial-oracle-azure-postgresql-online/dms-start-cutover.png)

3. **[確認]** を選択したら、 **[適用]** を選択します。
4. データベースの移行状態に **[完了]** が表示されたら、アプリケーションを新しいターゲット Azure Database for PostgreSQL インスタンスに接続します。

 > [!NOTE]
 > 既定では PostgreSQL では schema.table.column が小文字で保持されるため、この記事の前出の「**Azure Database for PostgreSQL でのスキーマの設定**」セクションにあるスクリプトを使用して大文字を小文字に戻すことができます。

## <a name="next-steps"></a>次の手順

* Azure Database for PostgreSQL へのオンライン移行の実行時の既知の問題と制限事項については、[Azure Database for PostgreSQL のオンライン移行に伴う既知の問題と回避策](known-issues-azure-postgresql-online.md)に関する記事を参照してください。
* Azure Database Migration Service の詳細については、「[Azure Database Migration Service とは](https://docs.microsoft.com/azure/dms/dms-overview)」を参照してください。
* Azure Database for PostgreSQL の詳細については、「[Azure Database for PostgreSQL とは](https://docs.microsoft.com/azure/postgresql/overview)」を参照してください。
