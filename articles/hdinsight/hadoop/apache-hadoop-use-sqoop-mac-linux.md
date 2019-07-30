---
title: Apache Hadoop での Apache Sqoop - Azure HDInsight
description: Apache Sqoop を使用して、HDInsight 上の Apache Hadoop と Azure SQL Database の間でインポートおよびエクスポートを行う方法について説明します。
keywords: hadoop sqoop,sqoop
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: c839aeae77d7e75fb30d82c410c331d21f5868ae
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2019
ms.locfileid: "68406043"
---
# <a name="use-apache-sqoop-to-import-and-export-data-between-apache-hadoop-on-hdinsight-and-sql-database"></a>Apache Sqoop を使用して、HDInsight 上の Apache Hadoop と SQL Database の間でデータをインポートおよびエクスポートする

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Azure HDInsight 上の Apache Hadoop クラスターと Azure SQL Database または Microsoft SQL Server データベースの間のインポートとエクスポートに Apache Sqoop を使用する方法について説明します。 このドキュメントの手順では、Hadoop クラスターのヘッド ノードから `sqoop` コマンドを直接使用します。 SSH を使用してヘッド ノードに接続し、このドキュメント内のコマンドを実行します。 この記事は、「[HDInsight の Hadoop での Apache Sqoop の使用](./hdinsight-use-sqoop.md)」の続きです。

## <a name="prerequisites"></a>前提条件

* 「[HDInsight の Hadoop での Apache Sqoop の使用](./hdinsight-use-sqoop.md)」の「[テスト環境のセットアップ](./hdinsight-use-sqoop.md#create-cluster-and-sql-database)」が完了していること。

* Azure SQL データベースのクエリを実行するクライアント。 [SQL Server Management Studio](../../sql-database/sql-database-connect-query-ssms.md) または [Visual Studio Code](../../sql-database/sql-database-connect-query-vscode.md) の使用を検討してください。

* SSH クライアント 詳細については、[SSH を使用して HDInsight (Apache Hadoop) に接続する方法](../hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

## <a name="sqoop-export"></a>Sqoop のエクスポート

Hive から SQL Server へ

1. SSH を使用して、HDInsight クラスターに接続します。 `CLUSTERNAME` をクラスターの名前に置き換えてから、次のコマンドを入力します。

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. `MYSQLSERVER` を SQL Server の名前に置き換えます。 Sqoop が SQL Database を認識できることを確認するには、開いた SSH 接続に次のコマンドを入力します。 プロンプトが表示されたら、SQL Server ログインのパスワードを入力します。 このコマンドはデータベースの一覧を返します。

    ```bash
    sqoop list-databases --connect jdbc:sqlserver://MYSQLSERVER.database.windows.net:1433 --username sqluser -P
    ```

3. `MYSQLSERVER` を SQL Server の名前に置き換え、`MYDATABASE` を SQL データベースの名前に置き換えます。 Hive `hivesampletable` テーブルから SQL Database 内の `mobiledata` テーブルにデータをエクスポートするには、開いた SSH 接続に次のコマンドを入力します。 プロンプトが表示されたら、SQL Server ログインのパスワードを入力します

    ```bash
    sqoop export --connect 'jdbc:sqlserver://MYSQLSERVER.database.windows.net:1433;database=MYDATABASE' --username sqluser -P -table 'mobiledata' --hcatalog-table hivesampletable
    ```

4. データがエクスポートされたことを確認するには、SQL クライアントから次のクエリを使用して、エクスポートされたデータを表示します。

    ```sql
    SELECT COUNT(*) FROM [dbo].[mobiledata] WITH (NOLOCK);
    SELECT TOP(25) * FROM [dbo].[mobiledata] WITH (NOLOCK);
    ```

## <a name="sqoop-import"></a>Sqoop のインポート

SQL Server から Azure Storage へ

1. `MYSQLSERVER` を SQL Server の名前に置き換え、`MYDATABASE` を SQL データベースの名前に置き換えます。 開いた SSH 接続に次のコマンドを入力して、SQL Database 内の `mobiledata` テーブルから HDInsight 上の `wasb:///tutorials/usesqoop/importeddata` ディレクトリにデータをインポートします。 プロンプトが表示されたら、SQL Server ログインのパスワードを入力します。 データ内のフィールドはタブ文字で区切られていて、行は改行文字で終わっています。

    ```bash
    sqoop import --connect 'jdbc:sqlserver://MYSQLSERVER.database.windows.net:1433;database=MYDATABASE' --username sqluser -P --table 'mobiledata' --target-dir 'wasb:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1
    ```

2. インポートが完了したら、開いた SSH 接続に次のコマンドを入力して、新しいディレクトリ内のデータを列挙します。

    ```bash
    hdfs dfs -text /tutorials/usesqoop/importeddata/part-m-00000
    ```

## <a name="limitations"></a>制限事項

* 一括エクスポート - Linux ベースの HDInsight では、Microsoft SQL Server または Azure SQL Database にデータをエクスポートするために使用する Sqoop コネクタは一括挿入をサポートしません。

* バッチ処理 - Linux ベースの HDInsight で、挿入処理実行時に `-batch` スイッチを使用すると、Sqoop は挿入操作をバッチ処理するのではなく、複数の挿入を行います。

## <a name="important-considerations"></a>重要な考慮事項

* HDInsight と SQL Server の両方が、同じ Azure Virtual Network に存在する必要があります。

    例については、「[Connect HDInsight to your on-premises network](./../connect-on-premises-network.md)」(オンプレミス ネットワークへの HDInsight の接続) をご覧ください。

    Azure Virtual Network での HDInsight の使用に関する詳細情報については、「[Azure Virtual Network を使用した HDInsight 機能の拡張](../hdinsight-plan-virtual-network-deployment.md)」をご覧ください。 Azure Virtual Network の詳細については、「[Virtual Network の概要](../../virtual-network/virtual-networks-overview.md)」を参照してください。

* SQL 認証を許可するよう、SQL Server を構成する必要があります。 詳細については、[認証モードの選択](https://msdn.microsoft.com/ms144284.aspx)に関するドキュメントを参照してください。

* リモート接続を許可するよう、SQL Server を構成する必要がある場合があります。 詳細については、[SQL Server データベース エンジンへの接続に関するトラブルシューティングの方法](https://social.technet.microsoft.com/wiki/contents/articles/2102.how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx)のドキュメントを参照してください。

## <a name="next-steps"></a>次の手順

ここでは Sqoop の使用方法を説明しました。 詳細については、次を参照してください。

* [HDInsight での Apache Oozie の使用](../hdinsight-use-oozie-linux-mac.md):Oozie ワークフローで Sqoop アクションを使用します。
* [HDInsight を使用したフライト遅延データの分析](../interactive-query/interactive-query-tutorial-analyze-flight-data.md):Interactive Query を使用してフライト遅延データを分析し、Sqoop を使用して Azure SQL データベースにデータをエクスポートします。
* [HDInsight へのデータのアップロード](../hdinsight-upload-data.md):HDInsight/Azure Blob Storage にデータをアップロードするその他の方法を説明します。
