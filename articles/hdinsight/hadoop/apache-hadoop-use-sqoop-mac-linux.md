---
title: Apache Hadoop での Apache Sqoop - Azure HDInsight
description: Apache Sqoop を使用して、HDInsight 上の Apache Hadoop と Azure SQL Database の間でインポートとエクスポートを行う方法について説明します。
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 11/28/2019
ms.openlocfilehash: 2d0d38dee15817e56c2784981365ea331b6a8459
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98943154"
---
# <a name="use-apache-sqoop-to-import-and-export-data-between-apache-hadoop-on-hdinsight-and-azure-sql-database"></a>Apache Sqoop を使用して、HDInsight 上の Apache Hadoop と Azure SQL Database の間でデータをインポートおよびエクスポートする

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Apache Sqoop を使用して、Azure HDInsight 上の Apache Hadoop クラスターと Azure SQL Database または Microsoft SQL Server の間でインポートとエクスポートを行う方法について説明します。 このドキュメントの手順では、Hadoop クラスターのヘッド ノードから `sqoop` コマンドを直接使用します。 SSH を使用してヘッド ノードに接続し、このドキュメント内のコマンドを実行します。 この記事は、「[HDInsight の Hadoop での Apache Sqoop の使用](./hdinsight-use-sqoop.md)」の続きです。

## <a name="prerequisites"></a>前提条件

* 「[HDInsight の Hadoop での Apache Sqoop の使用](./hdinsight-use-sqoop.md)」の「[テスト環境のセットアップ](./hdinsight-use-sqoop.md#create-cluster-and-sql-database)」が完了していること。

* SSH クライアント 詳細については、[SSH を使用して HDInsight (Apache Hadoop) に接続する方法](../hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

* Sqoop に関する知識。 詳細については、「[OpenFOAM ユーザーガイド](https://sqoop.apache.org/docs/1.4.7/SqoopUserGuide.html)」を参照してください。

## <a name="set-up"></a>設定

1. [ssh コマンド](../hdinsight-hadoop-linux-use-ssh-unix.md)を使用してクラスターに接続します。 次のコマンドを編集して CLUSTERNAME をクラスターの名前に置き換えてから、そのコマンドを入力します。

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. 使いやすくするために、変数を設定します。 `PASSWORD`、`MYSQLSERVER`、および `MYDATABASE` を関連する値に置き換え、次のコマンドを入力します。

    ```bash
    export password='PASSWORD'
    export sqlserver="MYSQLSERVER"
    export database="MYDATABASE"


    export serverConnect="jdbc:sqlserver://$sqlserver.database.windows.net:1433;user=sqluser;password=$password"
    export serverDbConnect="jdbc:sqlserver://$sqlserver.database.windows.net:1433;user=sqluser;password=$password;database=$database"
    ```

## <a name="sqoop-export"></a>Sqoop のエクスポート

Hive から SQL へ。

1. Sqoop でデータベースを認識できることを確認するには、開いている SSH 接続に次のコマンドを入力します。 このコマンドはデータベースの一覧を返します。

    ```bash
    sqoop list-databases --connect $serverConnect
    ```

1. 次のコマンドを入力して、指定したデータベースのテーブルのリストを表示します。

    ```bash
    sqoop list-tables --connect $serverDbConnect
    ```

1. Hive `hivesampletable` テーブルからデータベース内の `mobiledata` テーブルにデータをエクスポートするには、開いている SSH 接続に次のコマンドを入力します。

    ```bash
    sqoop export --connect $serverDbConnect \
    -table mobiledata \
    --hcatalog-table hivesampletable
    ```

1. データがエクスポートされたことを確認するには、SSH 接続から、次のクエリを使用して、エクスポートされたデータを表示します。

    ```bash
    sqoop eval --connect $serverDbConnect \
    --query "SELECT COUNT(*) from dbo.mobiledata WITH (NOLOCK)"


    sqoop eval --connect $serverDbConnect \
    --query "SELECT TOP(10) * from dbo.mobiledata WITH (NOLOCK)"
    ```

## <a name="sqoop-import"></a>Sqoop のインポート

SQL から Azure のストレージへ。

1. 開いている SSH 接続に次のコマンドを入力して、SQL 内の `mobiledata` テーブルから HDInsight 上の `wasbs:///tutorials/usesqoop/importeddata` ディレクトリにデータをインポートします。 データ内のフィールドはタブ文字で区切られていて、行は改行文字で終わっています。

    ```bash
    sqoop import --connect $serverDbConnect \
    --table mobiledata \
    --target-dir 'wasb:///tutorials/usesqoop/importeddata' \
    --fields-terminated-by '\t' \
    --lines-terminated-by '\n' -m 1
    ```

1. または、Hive テーブルを指定することもできます。

    ```bash
    sqoop import --connect $serverDbConnect \
    --table mobiledata \
    --target-dir 'wasb:///tutorials/usesqoop/importeddata2' \
    --fields-terminated-by '\t' \
    --lines-terminated-by '\n' \
    --create-hive-table \
    --hive-table mobiledata_imported2 \
    --hive-import -m 1
    ```

1. インポートが完了したら、開いた SSH 接続に次のコマンドを入力して、新しいディレクトリ内のデータを列挙します。

    ```bash
    hadoop fs -tail /tutorials/usesqoop/importeddata/part-m-00000
    ```

1. [beeline](./apache-hadoop-use-hive-beeline.md) を使用して、テーブルがHiveで作成されたことを確認します。

    1. 接続する

        ```bash
        beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
        ```

    1. 次のクエリを一度に1つずつ実行し、出力を確認します。

        ```hql
        show tables;
        describe mobiledata_imported2;
        SELECT COUNT(*) FROM mobiledata_imported2;
        SELECT * FROM mobiledata_imported2 LIMIT 10;
        ```

    1. Beeline を終了するには、 `!exit`を使用します。

## <a name="limitations"></a>制限事項

* 一括エクスポート - Linux ベースの HDInsight を使用する場合、SQL にデータをエクスポートするために使用する Sqoop コネクタでは、一括挿入はサポートされません。

* バッチ処理 - Linux ベースの HDInsight で、挿入処理実行時に `-batch` スイッチを使用すると、Sqoop は挿入操作をバッチ処理するのではなく、複数の挿入を行います。

## <a name="important-considerations"></a>重要な考慮事項

* HDInsight と SQL Server の両方が、同じ Azure Virtual Network に存在する必要があります。

    例については、「[Connect HDInsight to your on-premises network](./../connect-on-premises-network.md)」(オンプレミス ネットワークへの HDInsight の接続) をご覧ください。

    Azure Virtual Network での HDInsight の使用に関する詳細情報については、「[Azure Virtual Network を使用した HDInsight 機能の拡張](../hdinsight-plan-virtual-network-deployment.md)」をご覧ください。 Azure Virtual Network の詳細については、「[Virtual Network の概要](../../virtual-network/virtual-networks-overview.md)」を参照してください。

* SQL 認証を許可するよう、SQL Server を構成する必要があります。 詳細については、[認証モードの選択](/sql/relational-databases/security/choose-an-authentication-mode)に関するドキュメントを参照してください。

* リモート接続を許可するよう、SQL Server を構成する必要がある場合があります。 詳細については、[SQL Server データベース エンジンへの接続に関するトラブルシューティングの方法](https://social.technet.microsoft.com/wiki/contents/articles/2102.how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx)のドキュメントを参照してください。

## <a name="next-steps"></a>次のステップ

ここでは Sqoop の使用方法を学習しました。 詳細については、次を参照してください。

* [HDInsight での Apache Oozie の使用](../hdinsight-use-oozie-linux-mac.md):Oozie ワークフローで Sqoop アクションを使用します。
* [HDInsight を使用したフライト遅延データの分析](../interactive-query/interactive-query-tutorial-analyze-flight-data.md):Interactive Query を使用してフライト遅延データを分析してから、Sqoop を使用して Azure のデータベースにデータをエクスポートします。
* [HDInsight へのデータのアップロード](../hdinsight-upload-data.md):HDInsight/Azure Blob Storage にデータをアップロードするその他の方法を説明します。