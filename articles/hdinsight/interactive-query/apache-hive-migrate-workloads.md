---
title: Azure HDInsight 3.6 Hive ワークロードを Hive HDInsight 4.0 に移行する
description: HDInsight 3.6 上の Apache Hive のワークロードを HDInsight 4.0 に移行する方法について説明します。
author: msft-tacox
ms.author: tacox
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 11/13/2019
ms.openlocfilehash: 313b6afb8bd96f8ae507118cd552110d5f07ff78
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2020
ms.locfileid: "86087521"
---
# <a name="migrate-azure-hdinsight-36-hive-workloads-to-hdinsight-40"></a>Azure HDInsight 3.6 Hive ワークロードを Hive HDInsight 4.0 に移行する

このドキュメントでは、HDInsight 3.6 上の Apache Hive と LLAP のワークロードを HDInsight 4.0 に移行する方法について説明します。 HDInsight 4.0 では、マテリアライズドビューやクエリ結果のキャッシュなどの新しい Hive と LLAP の機能が提供されます。 ワークロードを HDInsight 4.0 に移行すると、HDInsight 3.6 で利用できない Hive 3 の多くの新しい機能を使用できます。

この記事に含まれるサブジェクトは次のとおりです。

* HDInsight 4.0 への Hive メタデータの移行
* ACID テーブルと ACID 以外のテーブルの安全な移行
* HDInsight バージョン間での Hive セキュリティ ポリシーの保持
* HDInsight 3.6 から HDInsight 4.0 へのクエリの実行とデバッグ

Hive の利点の 1 つは、外部データベース (Hive Metastore と呼ばれます) にメタデータをエクスポートする機能です。 **Hive Metastore** は、テーブル ストレージの場所、列名、テーブルのインデックス情報を含む、テーブルの統計情報の格納を担当します。 HDInsight 3.6 と HDInsight 4.0 には異なるメタストア スキーマが必要で、1 つのメタストアを共有することはできません。 Hive メタストアを安全にアップグレードするには、現在の運用環境にある元のメタストアではなく、コピーをアップグレードすることをお勧めします。 このドキュメントでは、元のクラスターと新しいクラスターから、同じストレージ アカウントにアクセスできることを要件としています。 そのため、別のリージョンへのデータ移行については取り上げません。

## <a name="migrate-from-external-metastore"></a>外部メタストアから移行する

### <a name="1-run-major-compaction-on-acid-tables-in-hdinsight-36"></a>1.HDInsight 3.6 で ACID テーブルの主要な圧縮を実行する

HDInsight 3.6 と HDInsight 4.0 の ACID テーブルでは、ACID のデルタの解釈が異なります。 移行前に必要なアクションは、3.6 クラスター上の各 ACID テーブルに対して "主要な" 圧縮を実行することだけです。 圧縮の詳細については、[Hive 言語マニュアル](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-AlterTable/Partition/Compact)を参照してください。

### <a name="2-copy-sql-database"></a>2.SQL データベースをコピーする
外部メタストアの新しいコピーを作成します。 外部メタストアを使用している場合、メタストアのコピーを作成する安全で簡単な方法の 1 つは、`RESTORE` 関数を使用して、別の名前で[データベースを復元](../../azure-sql/database/recovery-using-backups.md#point-in-time-restore)することです。  HDInsight クラスターへの外部メタストアのアタッチについて詳しくは、「[Azure HDInsight での外部メタデータ ストアの使用](../hdinsight-use-external-metadata-stores.md)」をご覧ください。

### <a name="3-upgrade-metastore-schema"></a>3.メタストア スキーマをアップグレードする
メタストアの**コピー**が完了したら、既存の HDInsight 3.6 クラスター上の[スクリプト アクション](../hdinsight-hadoop-customize-cluster-linux.md)でスキーマ アップグレード スクリプトを実行して、新しいメタストアを Hive 3 スキーマにアップグレードします。 (この手順では、新しいメタストアがクラスターに接続されている必要はありません。)これにより、データベースを HDInsight 4.0 メタストアとして接続できるようになります。

下にある表の値を使用してください。 `SQLSERVERNAME DATABASENAME USERNAME PASSWORD` は、スペース区切りで、Hive メタストアの**コピー**用に適切な値に置き換えます。 SQL サーバー名を指定するときに ".database.windows.net" を含めないでください。

|プロパティ | 値 |
|---|---|
|スクリプトの種類|- Custom|
|名前|Hive アップグレード|
|Bash スクリプト URI|`https://hdiconfigactions.blob.core.windows.net/hivemetastoreschemaupgrade/launch-schema-upgrade.sh`|
|ノードの種類|Head|
|パラメーター|SQLSERVERNAME DATABASENAME USERNAME PASSWORD|

> [!Warning]  
> HDInsight 3.6 のメタデータ スキーマを HDInsight 4.0 のスキーマに変換するアップグレードを元に戻すことはできません。

アップグレードを確認するには、データベースに対して次の SQL クエリを実行します。

```sql
select * from dbo.version
```

### <a name="4-deploy-a-new-hdinsight-40-cluster"></a>4.新しい HDInsight 4.0 クラスターをデプロイする

1. アップグレードされたメタストアを新しいクラスターの Hive メタストアとして指定します。

1. ただし、テーブルからの実際のデータは、クラスターが必要なストレージ アカウントへのアクセス権を持つようになるまでアクセスできません。
HDInsight 3.6 クラスター内の Hive テーブルのストレージ アカウントが、新しい HDInsight 4.0 クラスターのプライマリまたはセカンダリ ストレージ アカウントのどちらかとして指定されていることを確認します。
HDInsight クラスターへのストレージ アカウントの追加について詳しくは、「[HDInsight にストレージ アカウントを追加する](../hdinsight-hadoop-add-storage.md)」をご覧ください。

### <a name="5-complete-migration-with-a-post-upgrade-tool-in-hdinsight-40"></a>5.HDInsight 4.0 のアップグレード後ツールを使用して移行を完了する

マネージド テーブルは既定で、HDInsight 4.0 の ACID に準拠している必要があります。 メタストアの移行が完了したら、アップグレード後ツールを実行して、以前の非 ACID のマネージド テーブルを HDInsight 4.0 クラスターと互換性がある状態にします。 このツールでは、次の変換が適用されます。

|3.6 |4.0 |
|---|---|
|外部テーブル|外部テーブル|
|非 ACID マネージド テーブル|プロパティ 'external.table.purge'='true' を備えた外部テーブル|
|ACID マネージド テーブル|ACID マネージド テーブル|

SSH シェルを使用して、HDInsight 4.0 クラスターから Hive のアップグレード後ツールを実行します。

1. SSH を使用して、そのクラスター ヘッドノードに接続します。 手順については、「[SSH を使用して HDInsight に接続する](../hdinsight-hadoop-linux-use-ssh-unix.md)」をご覧ください。
1. Hive ユーザーとして、`sudo su - hive` を実行することで、ログイン シェルを開きます。
1. シェルから次のコマンドを実行します。

    ```bash
    STACK_VERSION=$(hdp-select status hive-server2 | awk '{ print $3; }')
    /usr/hdp/$STACK_VERSION/hive/bin/hive --config /etc/hive/conf --service  strictmanagedmigration --hiveconf hive.strict.managed.tables=true -m automatic --modifyManagedTables
    ```

ツールが完了すると、Hive ウェアハウスは HDInsight 4.0 で利用できるようになります。

## <a name="migrate-from-internal-metastore"></a>内部メタストアから移行する

HDInsight 3.6 クラスターによって内部 Hive メタストアが使用される場合、以下の手順に従ってスクリプトを実行します。これにより、メタストアからオブジェクト定義をエクスポートするための Hive クエリが生成されます。

HDInsight 3.6 および 4.0 のクラスターでは、同じストレージ アカウントが使用される必要があります。

> [!NOTE]
>
> * ACID テーブルの場合、テーブルのデータの新しいコピーが作成されます。
>
> * このスクリプトでは、Hive データベース、テーブル、およびパーティションの移行のみがサポートされます。 ビュー、UDF、テーブル制約などの他のメタデータ オブジェクトは、手動でコピーすることが想定されています。
>
> * このスクリプトの完了後は、スクリプトで参照されているテーブルやデータベースにアクセスするために古いクラスターは使用されなくなることが想定されています。
>
> * マネージド テーブルはすべて、HDInsight 4.0 ではトランザクションになります。 必要に応じて、プロパティ 'external. table. purge' = 'true' を使用して外部テーブルにデータをエクスポートすることによって、テーブルを非トランザクションのままにします。 たとえば、次のように入力します。
>
>    ```SQL
>    create table tablename_backup like tablename;
>    insert overwrite table tablename_backup select * from tablename;
>    create external table tablename_tmp like tablename;
>    insert overwrite table tablename_tmp select * from tablename;
>    alter table tablename_tmp set tblproperties('external.table.purge'='true');
>    drop table tablename;
>    alter table tablename_tmp rename to tablename;
>    ```

1. [Secure Shell (SSH) クライアント](../hdinsight-hadoop-linux-use-ssh-unix.md)を使用して HDInsight 3.6 クラスターに接続します。

1. 開いている SSH セッションから、次のスクリプト ファイルをダウンロードして、**alltables.sql** という名前のファイルを生成します。

    ```bash
    wget https://hdiconfigactions.blob.core.windows.net/hivemetastoreschemaupgrade/exporthive_hdi_3_6.sh
    chmod 755 exporthive_hdi_3_6.sh
    ```

    * 通常の HDInsight クラスターの場合は、ESP を使用せずに、単純に `exporthive_hdi_3_6.sh` を実行します。

    * ESP を使用するクラスターの場合は、kinit を実行して引数を Beeline に変更します。以下を実行し、完全な Hive アクセス許可を持つ Azure AD ユーザーに対して USER および DOMAIN を定義します。

        ```bash
        USER="USER"  # replace USER
        DOMAIN="DOMAIN"  # replace DOMAIN
        DOMAIN_UPPER=$(printf "%s" "$DOMAIN" | awk '{ print toupper($0) }')
        kinit "$USER@$DOMAIN_UPPER"
        ```

        ```bash
        hn0=$(grep hn0- /etc/hosts | xargs | cut -d' ' -f4)
        BEE_CMD="beeline -u 'jdbc:hive2://$hn0:10001/default;principal=hive/_HOST@$DOMAIN_UPPER;auth-kerberos;transportMode=http' -n "$USER@$DOMAIN" --showHeader=false --silent=true --outputformat=tsv2 -e"
        ./exporthive_hdi_3_6.sh "$BEE_CMD"
        ```

1. SSH セッションを終了します。 次に、scp コマンドを入力して **alltables.hql** をローカルにダウンロードします。

    ```bash
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:alltables.hql c:/hdi
    ```

1. **alltables.hql** を "*新しい*" HDInsight クラスターにアップロードします。

    ```bash
    scp c:/hdi/alltables.hql sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/home/sshuser/
    ```

1. 次に SSH を使用して、"*新しい*" HDInsight 4.0 クラスターに接続します。 このクラスターへの SSH セッションから、次のコードを実行します。

    ESP を使用しない場合:

    ```bash
    beeline -u "jdbc:hive2://localhost:10001/;transportMode=http" -f alltables.hql
    ```

    ESP を使用する場合:

    ```bash
    USER="USER"  # replace USER
    DOMAIN="DOMAIN"  # replace DOMAIN
    DOMAIN_UPPER=$(printf "%s" "$DOMAIN" | awk '{ print toupper($0) }')
    kinit "$USER@$DOMAIN_UPPER"
    ```

    ```bash
    hn0=$(grep hn0- /etc/hosts | xargs | cut -d' ' -f4)
    beeline -u "jdbc:hive2://$hn0:10001/default;principal=hive/_HOST@$DOMAIN_UPPER;auth-kerberos;transportMode=http" -n "$USER@$DOMAIN" -f alltables.hql
    ```

HDInsight 3.6 の非 ACID マネージド テーブルは HDInsight 4.0 では ACID マネージド テーブルに変換されるため、外部メタストア移行用のアップグレード後ツールは、ここでは適用されません。

> [!Important]  
> HDInsight 4.0 でのマネージド テーブル (3.6 から移行されたテーブルを含む) に対して、その他のサービスまたはアプリケーション (HDInsight 3.6 クラスターを含む) からアクセスすることはできません。

## <a name="secure-hive-across-hdinsight-versions"></a>HDInsight バージョン間で Hive をセキュリティ保護する

HDInsight 3.6 以降では、HDInsight は、HDInsight Enterprise セキュリティ パッケージ (ESP) を使用して Azure Active Directory と統合されます。 ESP では、Kerberos と Apache Ranger を使用して、クラスター内の特定のリソースのアクセス許可が管理されます。 次の手順で、HDInsight 3.6 内で Hive に対してデプロイした Ranger ポリシーを HDInsight 4.0 に移行できます。

1. HDInsight 3.6 クラスター内の Ranger Service Manager パネルに移動します。
2. **HIVE** という名前のポリシーに移動し、json ファイルにポリシーをエクスポートします。
3. エクスポートされたポリシーの json 内で参照されているすべてのユーザーが新しいクラスターに存在することを確認します。 ユーザーがポリシーの json 内で参照されているのに新しいクラスターに存在しない場合は、新しいクラスターにユーザーを追加するか、ポリシーから参照を削除します。
4. HDInsight 4.0 クラスター内の **Ranger Service Manager** パネルに移動します。
5. **HIVE** という名前のポリシーに移動し、手順 2 からの ranger ポリシーの json をインポートします。

## <a name="check-compatibility-and-modify-codes-as-needed-in-test-app"></a>互換性を確認し、テスト アプリで必要に応じてコードを変更する

既存のプログラムやクエリなどのワークロードを移行する場合は、リリース ノートとドキュメントで変更点を確認し、必要に応じて変更を適用してください。 HDInsight 3.6 クラスターで共有の Spark と Hive メタストアが使用されている場合は、[Hive Warehouse Connector を使用した追加の構成](./apache-hive-warehouse-connector.md)が必要です。

## <a name="deploy-new-app-for-production"></a>運用環境用の新しいアプリをデプロイする

新しいクラスターに切り替えるには、たとえば新しいクライアント アプリケーションをインストールし、それを新しい運用環境として使用するか、または既存のクライアント アプリケーションをアップグレードして HDInsight 4.0 に切り替えることができます。

## <a name="switch-hdinsight-40-to-the-production"></a>HDInsight 4.0 を運用環境に切り替える

テスト中にメタストアに差異が生じた場合は、切り替えの直前に変更を更新する必要があります。 この場合、メタストアのエクスポートとインポートを実行してから、もう一度アップグレードすることができます。

## <a name="remove-the-old-production"></a>以前の運用環境を削除する

リリースが完了し、完全に動作していることを確認したら、バージョン3.6 と前のメタストアを削除できます。 環境を削除する前に、すべてが移行されていることを確認してください。

## <a name="query-execution-across-hdinsight-versions"></a>HDInsight バージョン間でのクエリの実行

HDInsight 3.6 クラスター内で Hive/LLAP クエリを実行し、デバッグする方法は 2 つあります。 HiveCLI ではコマンドライン エクスペリエンスが提供され、Tez ビュー/Hive ビューでは GUI ベースのワークフローが提供されます。

HDInsight 4.0 では、Hive CLI は BeeLine に置き換えられています。 HiveCLI は Hiveserver 1 用の Thrift クライアントであり、Beeline は Hiveserver 2 へのアクセスを提供する JDBC クライアントです。 Beeline は、その他の JDBC 互換のデータベース エンドポイントへの接続にも使用できます。 Beeline は、HDInsight 4.0 上ですぐに使用可能で、インストールは必要ありません。

HDInsight 3.6 では、Hive サーバーと対話するための GUI クライアントは Ambari Hive ビューです。 HDInsight 4.0 には Ambari ビューは付属していません。 お客様が Data Analytics Studio (DAS) を使用する方法を提供しています。これは、中核となる HDInsight サービスではありません。 DAS には HDInsight クラスターは標準では付属せず、公式にサポートされているパッケージではありません。 ただし、次のように[スクリプト アクション](../hdinsight-hadoop-customize-cluster-linux.md)を使用して DAS をクラスターにインストールできます。

|プロパティ | 値 |
|---|---|
|スクリプトの種類|- Custom|
|名前|DAS|
|Bash スクリプト URI|`https://hdiconfigactions.blob.core.windows.net/dasinstaller/LaunchDASInstaller.sh`|
|ノードの種類|Head|

10 - 15 分待機してから、URL `https://CLUSTERNAME.azurehdinsight.net/das/` を使用して Data Analytics Studio を起動します。

DAS にアクセスする前に、Ambari UI の更新やすべての Ambari コンポーネントの再起動が必要になる場合があります。

DAS がインストールされた後、クエリ ビューアーで実行したクエリが表示されない場合は、次の手順を実行します。

1. [DAS のインストールのトラブルシューティングに関するこのガイド](https://docs.hortonworks.com/HDPDocuments/DAS/DAS-1.2.0/troubleshooting/content/das_queries_not_appearing.html)の説明に従って、Hive、Tez、DAS の構成を設定します。
2. 次の Azure ストレージ ディレクトリ構成がページ BLOB であり、`fs.azure.page.blob.dirs` の下に表示されることを確認します。
    * `hive.hook.proto.base-directory`
    * `tez.history.logging.proto-base-dir`
3. 両方のヘッドノード上の HDFS、Hive、Tez、DAS を再起動します。

## <a name="next-steps"></a>次のステップ

* [HDInsight 4.0 のお知らせ](../hdinsight-version-release.md)
* [HDInsight 4.0 の詳細情報](https://azure.microsoft.com/blog/deep-dive-into-azure-hdinsight-4-0/)
* [Hive 3 ACID テーブル](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.1.0/using-hiveql/content/hive_3_internals.html)
