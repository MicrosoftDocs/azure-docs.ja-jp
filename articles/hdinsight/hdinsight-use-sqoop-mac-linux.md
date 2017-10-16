---
title: "Hadoop での Apache Sqoop - Azure HDInsight | Microsoft Docs"
description: "Apache Sqoop を使用して、Hadoop on HDInsight と Azure SQL Database の間でインポートおよびエクスポートを行う方法について説明します。"
editor: cgronlun
manager: jhubbard
services: hdinsight
documentationcenter: 
author: Blackmist
tags: azure-portal
keywords: hadoop sqoop,sqoop
ms.assetid: 303649a5-4be5-4933-bf1d-4b232083c354
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: larryfr
ms.openlocfilehash: 35dcbb91e6af1480685c9fd5b829c54277c1c605
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="use-apache-sqoop-to-import-and-export-data-between-hadoop-on-hdinsight-and-sql-database"></a>Apache Sqoop を使用して、HDInsight の Hadoop と SQL Database の間でデータをインポートおよびエクスポートする

[!INCLUDE [sqoop-selector](../../includes/hdinsight-selector-use-sqoop.md)]

Azure HDInsight の Hadoop クラスターと Azure SQL Database または Microsoft SQL Server データベースの間のインポートとエクスポートに Apache Sqoop を使用する方法について説明します。 このドキュメントの手順では、Hadoop クラスターのヘッド ノードから `sqoop` コマンドを直接使用します。 SSH を使用してヘッド ノードに接続し、このドキュメント内のコマンドを実行します。

> [!IMPORTANT]
> このドキュメントの手順は、Linux を使用する HDInsight クラスターでのみ機能します。 Linux は、バージョン 3.4 以上の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、[Windows での HDInsight の提供終了](hdinsight-component-versioning.md#hdinsight-windows-retirement)に関する記事を参照してください。

## <a name="install-freetds"></a>FreeTDS のインストール

1. SSH を使用して、HDInsight クラスターに接続します。 たとえば、次のコマンドは、`mycluster` というクラスターのプライマリ ヘッド ノードに接続します。

    ```bash
    ssh CLUSTERNAME-ssh.azurehdinsight.net
    ```

    詳細については、[HDInsight での SSH の使用](hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

2. 次のコマンドを使用して FreeTDS をインストールします。

    ```bash
    sudo apt --assume-yes install freetds-dev freetds-bin
    ```

    FreeTDS は、SQL Database に接続する際のいくつかの手順で使用します。

## <a name="create-the-table-in-sql-database"></a>SQL Database へのテーブルの作成

> [!IMPORTANT]
> 「[クラスターと SQL データベースを作成する](hdinsight-use-sqoop.md)」で作成した HDInsight クラスターと SQL Database を使用している場合は、このセクションの手順を省略してください。 データベースとテーブルは、ドキュメント「[クラスターと SQL データベースを作成する](hdinsight-use-sqoop.md)」の手順の一部として作成されました。

1. SSH セッションから次のコマンドを使用して、SQL Database サーバーに接続します。

        TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D sqooptest

    次のテキストのような出力が返されます。

        locale is "en_US.UTF-8"
        locale charset is "UTF-8"
        using default charset "UTF-8"
        Default database being set to sqooptest
        1>

2. `1>` プロンプトで、次のクエリを入力します。

    ```sql
    CREATE TABLE [dbo].[mobiledata](
    [clientid] [nvarchar](50),
    [querytime] [nvarchar](50),
    [market] [nvarchar](50),
    [deviceplatform] [nvarchar](50),
    [devicemake] [nvarchar](50),
    [devicemodel] [nvarchar](50),
    [state] [nvarchar](50),
    [country] [nvarchar](50),
    [querydwelltime] [float],
    [sessionid] [bigint],
    [sessionpagevieworder] [bigint])
    GO
    CREATE CLUSTERED INDEX mobiledata_clustered_index on mobiledata(clientid)
    GO
    ```

    `GO` ステートメントを入力すると、前のステートメントが評価されます。 最初に、 **mobiledata** テーブルが作成され、次にクラスター化インデックスがそのテーブルに追加されます (SQL Database が必要)。

    次のクエリを使用して、テーブルが作成されたことを確認します。

    ```sql
    SELECT * FROM information_schema.tables
    GO
    ```

    次のテキストのような出力が表示されます。

        TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
        sqooptest       dbo     mobiledata      BASE TABLE

3. Enter `exit` at the `1>`」と入力して、tsql ユーティリティを終了します。

## <a name="sqoop-export"></a>Sqoop のエクスポート

1. クラスターへの SSH 接続から、次のコマンドを使用して、Sqoop が SQL Database を認識できることを確認します。

    ```bash
    sqoop list-databases --connect jdbc:sqlserver://<serverName>.database.windows.net:1433 --username <adminLogin> -P
    ```
    メッセージが表示されたら、SQL Database ログインのパスワードを入力します。

    このコマンドは、先ほど作成した **sqooptest** データベースを含むデータベースの一覧を返します。

2. **hivesampletable** から **mobiledata** テーブルにデータをエクスポートするには、次のコマンドを使用します。

    ```bash
    sqoop export --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> -P --table 'mobiledata' --export-dir 'wasb:///hive/warehouse/hivesampletable' --fields-terminated-by '\t' -m 1
    ```

    このコマンドによって、**sqooptest** データベースに接続するように Sqoop に指示します。 Sqoop は次に、**wasb:///hive/warehouse/hivesampletable** から **mobiledata** テーブルにデータをエクスポートします。

    > [!IMPORTANT]
    > クラスターの既定のストレージが Azure ストレージ アカウントの場合、`wasb:///` を使用します。 Azure Data Lake Store の場合、`adl:///` を使用します。

3. コマンドが完了したら、次のコマンドを使用して、TSQL によってデータベースに接続します。

    ```bash
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P -p 1433 -D sqooptest
    ```

    接続されたら、次のステートメントを使用して、データが **mobiledata** テーブルにエクスポートされたことを確認します。

    ```sql
    SET ROWCOUNT 50;
    SELECT * FROM mobiledata
    GO
    ```

    テーブル内のデータの一覧が表示されます。 「 `exit` 」と入力して、tsql ユーティリティを終了します。

## <a name="sqoop-import"></a>Sqoop のインポート

1. 次のコマンドを使用して、SQL Database の **mobiledata** テーブルから HDInsight の **wasb:///tutorials/usesqoop/importeddata** ディレクトリにデータをインポートします。

    ```bash
    sqoop import --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --target-dir 'wasb:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1
    ```

    データ内のフィールドはタブ文字で区切られていて、行は改行文字で終わっています。

2. インポートが完了したら、次のコマンドを使用して、新しいディレクトリのデータを列挙します。

    ```bash
    hdfs dfs -text /tutorials/usesqoop/importeddata/part-m-00000
    ```

## <a name="using-sql-server"></a>SQL Server の使用

Sqoop を使用すると、Azure でホストされているデータ センターまたは仮想マシンで、SQL Server からデータをインポートしたり、エクスポートしたりすることもできます。 SQL Database と SQL Server の使用方法には、次の違いがあります。

* HDInsight と SQL Server の両方が、同じ Azure Virtual Network に存在する必要があります。

    例については、「[Connect HDInsight to your on-premises network](./connect-on-premises-network.md)」(オンプレミス ネットワークへの HDInsight の接続) をご覧ください。

    Azure Virtual Network での HDInsight の使用に関する詳細情報については、「[Azure Virtual Network を使用した HDInsight 機能の拡張](hdinsight-extend-hadoop-virtual-network.md)」をご覧ください。 Azure Virtual Network の詳細については、「[Virtual Network の概要](../virtual-network/virtual-networks-overview.md)」を参照してください。

* SQL 認証を許可するよう、SQL Server を構成する必要があります。 詳細については、[認証モードの選択](https://msdn.microsoft.com/ms144284.aspx)に関するドキュメントを参照してください。

* リモート接続を許可するよう、SQL Server を構成する必要がある場合があります。 詳細については、[SQL Server データベース エンジンへの接続に関するトラブルシューティングの方法](http://social.technet.microsoft.com/wiki/contents/articles/2102.how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx)のドキュメントを参照してください。

* **SQL Server Management Studio** や **tsql** などのユーティリティを使用して、SQL Server 内に **sqooptest** データベースを作成します。 Azure CLI を使用する手順は、Azure SQL Database でのみ機能します。

    次の Transact-SQL ステートメントを使用して、**mobiledata** テーブルを作成します。

    ```sql
    CREATE TABLE [dbo].[mobiledata](
    [clientid] [nvarchar](50),
    [querytime] [nvarchar](50),
    [market] [nvarchar](50),
    [deviceplatform] [nvarchar](50),
    [devicemake] [nvarchar](50),
    [devicemodel] [nvarchar](50),
    [state] [nvarchar](50),
    [country] [nvarchar](50),
    [querydwelltime] [float],
    [sessionid] [bigint],
    [sessionpagevieworder] [bigint])
    ```

* HDInsight から SQL Server に接続するときに、SQL Server の IP アドレスの使用が必要な場合があります。 次に例を示します。

    ```bash
    sqoop import --connect 'jdbc:sqlserver://10.0.1.1:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --target-dir 'wasb:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1
    ```

## <a name="limitations"></a>制限事項

* 一括エクスポート - Linux ベースの HDInsight では、Microsoft SQL Server または Azure SQL Database にデータをエクスポートするために使用する Sqoop コネクタは、一括挿入を現在サポートしていません。

* バッチ処理 - Linux ベースの HDInsight で、挿入処理実行時に `-batch` スイッチを使用すると、Sqoop は挿入操作をバッチ処理するのではなく、複数の挿入を行います。

## <a name="next-steps"></a>次のステップ

ここでは Sqoop の使用方法を説明しました。 詳細については、次を参照してください。

* [HDInsight での Oozie の使用][hdinsight-use-oozie]: Oozie ワークフローで Sqoop アクションを使用します。
* [HDInsight を使用したフライト遅延データの分析][hdinsight-analyze-flight-data]: Hive を使用してフライト遅延データを分析し、Sqoop を使用して Azure SQL Database にデータをエクスポートします。
* [HDInsight へのデータのアップロード][hdinsight-upload-data]: HDInsight/Azure Blob Storage にデータをアップロードするその他の方法を説明します。

[hdinsight-versions]:  hdinsight-component-versioning.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md

[sqldatabase-get-started]: ../sql-database-get-started.md
[sqldatabase-create-configue]: ../sql-database-create-configure.md

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: /powershell/azureps-cmdlets-docs
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[sqoop-user-guide-1.4.4]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html
