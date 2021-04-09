---
title: チュートリアル:オンラインで Oracle を Azure Database for PostgreSQL に移行する
titleSuffix: Azure Database Migration Service
description: Azure Database Migration Service を使用して、オンプレミスまたは仮想マシンの Oracle から Azure Database for PostgreSQL にオンライン移行を実行する方法を説明します。
services: dms
author: arunkumarthiags
ms.author: arthiaga
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.subservice: migration-guide
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 01/24/2020
ms.openlocfilehash: 2be824c89ac1e6b900dc9b67e046e2be2c7f8919
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103562826"
---
# <a name="tutorial-migrate-oracle-to-azure-database-for-postgresql-online-using-dms-preview"></a>チュートリアル:DMS を使用して Oracle を Azure Database for PostgreSQL にオンラインで移行する (プレビュー)

> [!IMPORTANT]
> 2021 年 5 月 1 以降、「**Oracle から Azure Database for PostgreSQL へ**」の移行シナリオ (現在プレビュー段階) は使用できなくなります。 引き続き別のツール (Ora2pg など) を使用してサポートを提供し、Oracle から PostgreSQL への移行に最適な移行エクスペリエンスを提供します。 移行のベスト プラクティスについては、「[Oracle から Azure Database for PostgreSQL への移行ガイド](https://aka.ms/OracletoPGguide)」を参照してください。 

Azure Database Migration Service を使用して、最小限のダウンタイムでデータベースをオンプレミスまたは仮想マシンでホストされている Oracle データベースから [Azure Database for PostgreSQL](../postgresql/index.yml) に移行できます。 つまり、アプリケーションにとって最小限のダウンタイムで移行を完了できます。 このチュートリアルでは、Azure Database Migration Service のオンライン移行アクティビティを使用して、**HR** サンプル データベースを Oracle 11g のオンプレミスまたは仮想マシン インスタンスから Azure Database for PostgreSQL に移行します。

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
* サンプルの **HR** データベースを [ここ](https://docs.oracle.com/database/121/COMSC/installation.htm#COMSC00002)からダウンロードします。
* [ora2pg をダウンロードし、Windows または Linux のいずれかにインストール](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Steps%20to%20Install%20ora2pg%20on%20Windows%20and%20Linux.pdf)します。
* [Azure Database for PostgreSQL のインスタンスを作成します](../postgresql/quickstart-create-server-database-portal.md)。
* この[ドキュメント](../postgresql/tutorial-design-database-using-azure-portal.md)の指示に従ってインスタンスに接続しデータベースを作成します。
* Azure Resource Manager デプロイ モデルを使用して、Azure Database Migration Service 用の Microsoft Azure 仮想ネットワークを作成します。これで、[ExpressRoute](../expressroute/expressroute-introduction.md) または [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) を使用したオンプレミスのソース サーバーとのサイト間接続を確立します。 仮想ネットワークの作成方法の詳細については、[Virtual Network のドキュメント](../virtual-network/index.yml)を参照してください。特に、詳細な手順が記載されたクイックスタートの記事を参照してください。

  > [!NOTE]
  > 仮想ネットワークのセットアップ中、Microsoft へのネットワーク ピアリングに ExpressRoute を使用する場合は、サービスのプロビジョニング先となるサブネットに、次のサービス [エンドポイント](../virtual-network/virtual-network-service-endpoints-overview.md)を追加してください。
  >
  > * ターゲット データベース エンドポイント (SQL エンドポイント、Cosmos DB エンドポイントなど)
  > * ストレージ エンドポイント
  > * サービス バス エンドポイント
  >
  > Azure Database Migration Service にはインターネット接続がないため、この構成が必要となります。

* 仮想ネットワークのネットワーク セキュリティ グループ (NSG) の規則によって、ServiceBus、Storage、AzureMonitor の ServiceTag の送信ポート 443 がブロックされていないことを確認します。 仮想ネットワークの NSG トラフィックのフィルター処理の詳細については、[ネットワーク セキュリティ グループによるネットワーク トラフィックのフィルター処理](../virtual-network/virtual-network-vnet-plan-design-arm.md)に関する記事を参照してください。
* [データベース エンジン アクセスのために Windows ファイアウォール](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)を構成します。
* Azure Database Migration Service がソースの Oracle Server にアクセスできるように Windows ファイアウォールを開きます。既定では TCP ポート 1521 が使用されます。
* ソース データベースの前でファイアウォール アプライアンスを使用する場合は、Azure Database Migration Service が移行のためにソース データベースにアクセスできるように、ファイアウォール規則を追加することが必要な場合があります。
* Azure Database for PostgreSQL のサーバー レベルの[ファイアウォール規則](../azure-sql/database/firewall-configure.md)を作成して、Azure Database Migration Service がターゲット データベースにアクセスできるようにします。 Azure Database Migration Service に使用する仮想ネットワークのサブネット範囲を指定します。
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

## <a name="assess-the-effort-for-an-oracle-to-azure-database-for-postgresql-migration"></a>Oracle から Azure Database for PostgreSQL への移行作業の評価

Oracle から Azure Database for PostgreSQL への移行に必要な作業の評価には、ora2pg を使用することをお勧めします。 `ora2pg -t SHOW_REPORT` ディレクティブを使用して、すべての Oracle オブジェクト、推定移行コスト (開発者の日数単位)、変換の一部として特別な注意が必要になる可能性がある特定のデータベース オブジェクトがリストされたレポートを作成します。

ほとんどのお客様は、評価レポートのレビューと自動および手動の変換作業の検討にかなりの時間を費やします。

ora2pg を構成して実行し、評価レポートを作成する方法については、『[Oracle to Azure Database for PostgreSQL Cookbook](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf)』 (Oracle から Azure Database for PostgreSQL への移行手順) **の「Premigration: Assessment**」(移行前: 評価) セクションを参照してください。 サンプルの ora2pg 評価レポートは、[ここ](https://ora2pg.darold.net/report.html)で参照できます。

## <a name="export-the-oracle-schema"></a>Oracle スキーマのエクスポート

Oracle スキーマとその他の Oracle オブジェクト (型、プロシージャ、関数など) を、Azure Database for PostgreSQL と互換性のあるスキーマに変換するには、ora2pg の使用をお勧めします。 ora2pg には、特定のデータ型の事前定義に役立つ多くのディレクティブが含まれています。 たとえば、`DATA_TYPE` ディレクティブを使用して、すべての NUMBER(*,0) を NUMERIC(38) ではなく bigint に置き換えることができます。

ora2pg を実行して、.sql ファイル内の各データベース オブジェクトをエクスポートすることができます。 その後、psql を使用して .sql ファイルを Azure Database for PostgreSQL にインポートする前にレビューすることができます。または、PgAdmin で .SQL スクリプトを実行することができます。

```
psql -f [FILENAME] -h [AzurePostgreConnection] -p 5432 -U [AzurePostgreUser] -d database 
```

次に例を示します。

```
psql -f %namespace%\schema\sequences\sequence.sql -h server1-server.postgres.database.azure.com -p 5432 -U username@server1-server -d database
```

スキーマ変換用に ora2pg を構成して実行する方法については、『[Oracle to Azure Database for PostgreSQL Cookbook](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf)』 (Oracle から Azure Database for PostgreSQL への移行手順) の **「Migration: Schema and data**」(移行: スキーマとデータ) セクションを参照してください。

## <a name="set-up-the-schema-in-azure-database-for-postgresql"></a>Azure Database for PostgreSQL でのスキーマの設定

Azure Database Migration Service で移行パイプラインを開始する前に、ora2pg を使用して、Oracle のテーブル スキーマ、ストアド プロシージャ、パッケージなどのデータベース オブジェクトを変換して、Postgres との互換性を確保することができます。 ora2pg の操作方法については、以下のリンクを参照してください。

* [ora2pg を Windows にインストールする](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Steps%20to%20Install%20ora2pg%20on%20Windows%20and%20Linux.pdf)
* [Oracle から Azure PostgreSQL への移行に関するクックブック](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf)

Azure Database Migration Service で PostgreSQL のテーブル スキーマを作成することもできます。 このサービスでは、接続されている Oracle ソースのテーブル スキーマにアクセスし、互換性のあるテーブル スキーマを Azure Database for PostgreSQL に作成します。 Azure Database Migration Service によるスキーマの作成とデータの移動が完了したら、Azure Database for PostgreSQL のスキーマ形式を検証して確認してください。

> [!IMPORTANT]
> Azure Database Migration Service によって作成されるのはテーブル スキーマだけです。ストアド プロシージャ、パッケージ、インデックスなど、他のデータベース オブジェクトは作成されません。

また、全体の読み込みを実行するため、ターゲット データベース内の外部キーはドロップしてください。 外部キーのドロップに使用できるスクリプトについては、[ここ](./tutorial-postgresql-azure-postgresql-online.md)の記事の「**サンプル スキーマを移行する**」セクションを参照してください。 Azure Database Migration Service を使い、全体の読み込みと同期を実行します。

### <a name="when-the-postgresql-table-schema-already-exists"></a>PostgreSQL のテーブル スキーマが既に存在する場合

Azure Database Migration Service を使用したデータ移動を開始する前に、ora2pg などのツールを使用して PostgreSQL スキーマを作成する場合、Azure Database Migration Service 内のターゲット テーブルにソース テーブルをマップします。

1. Oracle から Azure Database for PostgreSQL への新しい移行プロジェクトを作成するとき、[スキーマの選択] ステップでターゲット データベースとターゲット スキーマを選択するように求められます。 ターゲット データベースとターゲット スキーマを入力します。

   ![[ターゲット データベースへマッピング] のスクリーンショット。](media/tutorial-oracle-azure-postgresql-online/dms-map-to-target-databases.png)

2. **[移行の設定]** 画面に Oracle ソース内のテーブルが一覧表示されます。 Azure Database Migration Service は、テーブル名に基づいてソース テーブルとターゲット テーブルの照合を試みます。 一致するものの大文字と小文字が異なるターゲット テーブルが複数存在した場合は、マップ先となるターゲット テーブルを選択することができます。

    ![[移行の設定] のスクリーンショット](media/tutorial-oracle-azure-postgresql-online/dms-migration-settings.png)

> [!NOTE]
> ソース テーブルの名前を別の名前のテーブルにマップする必要がある場合は、メール [dmsfeedback@microsoft.com](mailto:dmsfeedbac@microsoft.com) でご連絡ください。そのプロセスを自動化するスクリプトを Microsoft が提供いたします。

### <a name="when-the-postgresql-table-schema-doesnt-exist"></a>PostgreSQL のテーブル スキーマが存在しない場合

ターゲット PostgreSQL データベースにテーブル スキーマ情報が存在しない場合、Azure Database Migration Service はソース スキーマを変換してターゲット データベースに再作成します。 Azure Database Migration Service によって作成されるのはテーブル スキーマだけであることに注意してください。ストアド プロシージャ、パッケージ、インデックスなど、他のデータベース オブジェクトは作成されません。
Azure Database Migration Service でスキーマを自動的に作成するには、既存のテーブルを備えていないスキーマがターゲット環境に含まれている必要があります。 Azure Database Migration Service によってなんらかのテーブルが検出された場合、ora2pg などの外部ツールによってスキーマが作成されたものと見なされます。

> [!IMPORTANT]
> Azure Database Migration Service では、Azure Database Migration Service またはツール (ora2pg など) のどちらか一方のみを使用し、同じ方法ですべてのテーブルが作成されている必要があります。

作業を開始するには:

1. 実際のアプリケーションの要件に基づいて、ターゲット データベースにスキーマを作成します。 既定では、PostgreSQL のテーブル スキーマと列の名前には小文字が使用されます。 一方、Oracle のテーブル スキーマと列には、既定で全大文字が使用されます。
2. [スキーマの選択] ステップで、ターゲット データベースとターゲット スキーマを指定します。
3. Azure Database for PostgreSQL で作成するスキーマに基づいて、Azure Database Migration Service では次の変換規則が使用されます。

    Oracle ソースのスキーマ名が Azure Database for PostgreSQL のものと一致する場合、Azure Database Migration Service では、"*大文字と小文字の区別をターゲット側に合わせてテーブル スキーマを作成*" します。

    次に例を示します。

    | ソース Oracle スキーマ | ターゲット PostgreSQL Database.Schema | DMS によって作成される schema.table.column |
    | ------------- | ------------- | ------------- |
    | HR | targetHR.public | public.countries.country_id |
    | HR | targetHR.trgthr | trgthr.countries.country_id |
    | HR | targetHR.TARGETHR | "TARGETHR"."COUNTRIES"."COUNTRY_ID" |
    | HR | targetHR.HR | "HR"."COUNTRIES"."COUNTRY_ID" |
    | HR | targetHR.Hr | *大文字と小文字が混在する名前はマップできません |

    *大文字と小文字が混在するスキーマ名とテーブル名をターゲット PostgreSQL に作成する場合は、[dmsfeedback@microsoft.com](mailto:dmsfeedback@microsoft.com) にお問い合わせください。 大文字と小文字が混在するテーブル スキーマをターゲット PostgreSQL データベースに設定するためのスクリプトを提供いたします。

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

4. 既存の仮想ネットワークを選択するか、新しく作成します。

    この仮想ネットワークによって、Azure Database Migration Service に、ソース Oracle とターゲット Azure Database for PostgreSQL インスタンスへのアクセスが提供されます。

    Azure portal で仮想ネットワークを作成する方法の詳細については、「[Azure portal を使用した仮想ネットワークの作成](../virtual-network/quick-create-portal.md)」を参照してください。

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

1. **[保存]** を選択し、 **[OCI ドライバーのインストール]** 画面でご自分の Oracle アカウントにサインインして、ドライバーの **instantclient-basiclite-windows.x64-12.2.0.1.0.zip** (37,128,586 Byte(s)) (SHA1 Checksum:865082268) を [ここ](https://www.oracle.com/technetwork/topics/winx64soft-089540.html#ic_winx64_inst)からダウンロードします。
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

2. **[データベース名]** で、特定のデータベースを選択して、**データ全体の読み込み** 操作と **増分データ同期** 操作の移行状態を取得します。

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

## <a name="next-steps"></a>次のステップ

* Azure Database for PostgreSQL へのオンライン移行の実行時の既知の問題と制限事項については、[Azure Database for PostgreSQL のオンライン移行に伴う既知の問題と回避策](known-issues-azure-postgresql-online.md)に関する記事を参照してください。
* Azure Database Migration Service の詳細については、「[Azure Database Migration Service とは](./dms-overview.md)」を参照してください。
* Azure Database for PostgreSQL の詳細については、「[Azure Database for PostgreSQL とは](../postgresql/overview.md)」を参照してください。
