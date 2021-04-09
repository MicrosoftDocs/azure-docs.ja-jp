---
title: Azure HDInsight 3.6 Hive ワークロードを Hive HDInsight 4.0 に移行する
description: HDInsight 3.6 上の Apache Hive のワークロードを HDInsight 4.0 に移行する方法について説明します。
author: kevxmsft
ms.author: kevx
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 11/4/2020
ms.openlocfilehash: b13e8e088eff95071247a53ad1a4a18879f94053
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101742196"
---
# <a name="migrate-azure-hdinsight-36-hive-workloads-to-hdinsight-40"></a>Azure HDInsight 3.6 Hive ワークロードを Hive HDInsight 4.0 に移行する

HDInsight 4.0 には、HDInsight 3.6 よりも優れた点がいくつかあります。 こちらに [HDInsight 4.0 の新機能の概要](../hdinsight-version-release.md)があります。

この記事では、Hive ワークロードを HDInsight 3.6 から 4.0 に移行する手順について説明します。以下が含まれます。

* Hive メタストアのコピーとスキーマのアップグレード
* ACID 互換性のための安全な移行
* Hive セキュリティ ポリシーの保持

新旧の HDInsight クラスターは、同じストレージ アカウントにアクセスできる必要があります。

Hive テーブルの新しいストレージ アカウントへの移行は、別の手順として実行する必要があります。 [ストレージ アカウント間での Hive の移行](./hive-migration-across-storage-accounts.md)に関する記事を参照してください。

## <a name="steps-to-upgrade"></a>アップグレードの手順

### <a name="1-prepare-the-data"></a>1.データを準備する

* 既定では、HDInsight 3.6 では ACID テーブルがサポートされていません。 ただし、ACID テーブルが存在する場合は、それらに対して "主要な" 圧縮が実行されます。 圧縮の詳細については、[Hive 言語マニュアル](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-AlterTable/Partition/Compact)を参照してください。

* [Azure Data Lake Storage Gen1](../overview-data-lake-storage-gen1.md) を使用している場合、Hive テーブルの場所が、クラスターの HDFS 構成に依存している可能性があります。 次のスクリプト アクションを実行して、これらの場所を他のクラスターに移植できるようにします。 「[実行中のクラスターに対するスクリプト アクション](../hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster)」を参照してください。

    |プロパティ | 値 |
    |---|---|
    |Bash スクリプト URI|`https://hdiconfigactions.blob.core.windows.net/linuxhivemigrationv01/hive-adl-expand-location-v01.sh`|
    |ノードの種類|Head|
    |パラメーター||

### <a name="2-copy-the-sql-database"></a>2.SQL データベースをコピーする

* クラスターで既定の Hive メタストアを使用する場合は、こちらの[ガイド](./hive-default-metastore-export-import.md)に従って、外部メタストアにメタデータをエクスポートします。 次に、アップグレードのためにその外部メタストアのコピーを作成します。

* クラスターで外部の Hive メタストアを使用する場合は、そのコピーを作成します。 オプションとしては、[エクスポートおよびインポート](../../azure-sql/database/database-export.md)と[ポイントインタイム リストア](../../azure-sql/database/recovery-using-backups.md#point-in-time-restore)があります。

### <a name="3-upgrade-the-metastore-schema"></a>3.メタストア スキーマをアップグレードする

この手順では、HDInsight 4.0 の [`Hive Schema Tool`](https://cwiki.apache.org/confluence/display/Hive/Hive+Schema+Tool) を使用して、メタストア スキーマをアップグレードします。

> [!Warning]
> この手順は元に戻せません。 これは、メタストアのコピーに対してのみ実行してください。

1. HDInsight 4.0 の一時クラスターを作成し、4.0 Hive `schematool` にアクセスします。 この手順では、[既定の Hive メタストア](../hdinsight-use-external-metadata-stores.md#default-metastore)を使用できます。

1. HDInsight 4.0 クラスターから、`schematool` を実行して、ターゲットの HDInsight 3.6 メタストアをアップグレードします。

    ```sh
    SERVER='servername.database.windows.net'  # replace with your SQL Server
    DATABASE='database'  # replace with your 3.6 metastore SQL Database
    USERNAME='username'  # replace with your 3.6 metastore username
    PASSWORD='password'  # replace with your 3.6 metastore password
    STACK_VERSION=$(hdp-select status hive-server2 | awk '{ print $3; }')
    /usr/hdp/$STACK_VERSION/hive/bin/schematool -upgradeSchema -url "jdbc:sqlserver://$SERVER;databaseName=$DATABASE;trustServerCertificate=false;encrypt=true;hostNameInCertificate=*.database.windows.net;" -userName "$USERNAME" -passWord "$PASSWORD" -dbType "mssql" --verbose
    ```

    > [!NOTE]
    > このユーティリティでは、クライアント `beeline` を使用して、`/usr/hdp/$STACK_VERSION/hive/scripts/metastore/upgrade/mssql/upgrade-*.mssql.sql` で SQL スクリプトを実行します。
    >
    > これらのスクリプトの SQL 構文は、必ずしも他のクライアント ツールと互換性がありません。 たとえば、[SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) と [Azure portal のクエリ エディター](../../azure-sql/database/connect-query-portal.md)では、各コマンドの後にキーワード `GO` が必要です。
    >
    > リソースの容量またはトランザクションのタイムアウトが原因でスクリプトが失敗した場合は、SQL データベースをスケールアップします。

1. クエリ `select schema_version from dbo.version` を使用して最終バージョンを確認します。

    出力は、HDInsight 4.0 クラスターからの次の bash コマンドの出力と一致しているはずです。

    ```bash
    grep . /usr/hdp/$(hdp-select --version)/hive/scripts/metastore/upgrade/mssql/upgrade.order.mssql | tail -n1 | rev | cut -d'-' -f1 | rev
    ```

1. HDInsight 4.0 の一時クラスターを削除します。

### <a name="4-deploy-a-new-hdinsight-40-cluster"></a>4.新しい HDInsight 4.0 クラスターをデプロイする

新しい HDInsight 4.0 クラスターを作成し、[アップグレードされた Hive メタストア](../hdinsight-use-external-metadata-stores.md#select-a-custom-metastore-during-cluster-creation)および同じストレージ アカウントを選択します。

* 新しいクラスターでは、同じ既定のファイルシステムを持つ必要はありません。

* メタストアに、複数のストレージ アカウントに存在するテーブルが含まれている場合は、それらのストレージ アカウントを新しいクラスターに追加して、これらのテーブルにアクセスする必要があります。 「[HDInsight にストレージ アカウントを追加する](../hdinsight-hadoop-add-storage.md)」を参照してください。

* ストレージにアクセスできないことが原因で Hive ジョブが失敗する場合は、テーブルの場所が、クラスターに追加されたストレージ アカウントにあることを確認します。

    次の Hive コマンドを使用して、テーブルの場所を確認します。

    ```sql
    SHOW CREATE TABLE ([db_name.]table_name|view_name);
    ```

### <a name="5-convert-tables-for-acid-compliance"></a>5.ACID 準拠のテーブルを変換する

マネージド テーブルは、HDInsight 4.0 の ACID に準拠している必要があります。 HDInsight 4.0 で `strictmanagedmigration` を実行して、すべての非 ACID マネージド テーブルを、プロパティ `'external.table.purge'='true'` を使用して外部テーブルに変換します。 次をヘッド ノードから実行します。

```bash
sudo su - hive
STACK_VERSION=$(hdp-select status hive-server2 | awk '{ print $3; }')
/usr/hdp/$STACK_VERSION/hive/bin/hive --config /etc/hive/conf --service strictmanagedmigration --hiveconf hive.strict.managed.tables=true -m automatic --modifyManagedTables
```

## <a name="secure-hive-across-hdinsight-versions"></a>HDInsight バージョン間で Hive をセキュリティ保護する

HDInsight は、必要に応じて、HDInsight Enterprise セキュリティ パッケージ (ESP) を使用して Azure Active Directory と統合されます。 ESP では、Kerberos と Apache Ranger を使用して、クラスター内の特定のリソースのアクセス許可が管理されます。 次の手順で、HDInsight 3.6 内で Hive に対してデプロイした Ranger ポリシーを HDInsight 4.0 に移行できます。

1. HDInsight 3.6 クラスター内の Ranger Service Manager パネルに移動します。
2. **HIVE** という名前のポリシーに移動し、json ファイルにポリシーをエクスポートします。
3. エクスポートされたポリシーの json 内で参照されているすべてのユーザーが新しいクラスターに存在することを確認します。 ユーザーがポリシーの json 内で参照されているのに新しいクラスターに存在しない場合は、新しいクラスターにユーザーを追加するか、ポリシーから参照を削除します。
4. HDInsight 4.0 クラスター内の **Ranger Service Manager** パネルに移動します。
5. **HIVE** という名前のポリシーに移動し、手順 2 からの ranger ポリシーの json をインポートします。

## <a name="hive-changes-in-hdinsight-40-that-may-require-application-changes"></a>アプリケーションの変更が必要になる場合がある HDInsight 4.0 での Hive の変更点

* Spark と Hive の間で ACID テーブル用にメタストアを共有する方法については、[Hive Warehouse Connector を使用した追加の構成](./apache-hive-warehouse-connector.md)に関する記事を参照してください。

* HDInsight 4.0 では、[ストレージ ベースの承認](https://cwiki.apache.org/confluence/display/Hive/Storage+Based+Authorization+in+the+Metastore+Server)が使用されます。 ファイルのアクセス許可を変更したり、Hive とは別のユーザーとしてフォルダーを作成したりすると、ストレージのアクセス許可に基づいて Hive エラーが発生する可能性があります。 修正するには、ユーザーに `rw-` アクセス権を付与します。 「[HDFS アクセス許可ガイド](https://hadoop.apache.org/docs/r2.7.1/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html)」を参照してください。

* `HiveCLI` が `Beeline` に置き換えられます。

その他の変更内容については、[HDInsight 4.0 に関するお知らせ](../hdinsight-version-release.md)を参照してください。

## <a name="further-reading"></a>関連項目

* [HDInsight 4.0 のお知らせ](../hdinsight-version-release.md)
* [HDInsight 4.0 の詳細情報](https://azure.microsoft.com/blog/deep-dive-into-azure-hdinsight-4-0/)
* [Hive 3 ACID テーブル](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.1.0/using-hiveql/content/hive_3_internals.html)
