---
title: Apache Hadoop での Apache Sqoop - Azure HDInsight
description: Apache Sqoop を使用して、HDInsight 上の Apache Hadoop と Azure SQL Database の間でインポートおよびエクスポートを行う方法について説明します。
keywords: hadoop sqoop,sqoop
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 02/15/2019
ms.openlocfilehash: 0f96ee3a24a811d7e3ab7e65ba4ff14050541638
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2019
ms.locfileid: "58443373"
---
# <a name="use-apache-sqoop-to-import-and-export-data-between-apache-hadoop-on-hdinsight-and-sql-database"></a>Apache Sqoop を使用して、HDInsight 上の Apache Hadoop と SQL Database の間でデータをインポートおよびエクスポートする

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Azure HDInsight 上の Apache Hadoop クラスターと Azure SQL Database または Microsoft SQL Server データベースの間のインポートとエクスポートに Apache Sqoop を使用する方法について説明します。 このドキュメントの手順では、Hadoop クラスターのヘッド ノードから `sqoop` コマンドを直接使用します。 SSH を使用してヘッド ノードに接続し、このドキュメント内のコマンドを実行します。

> [!IMPORTANT]  
> このドキュメントの手順は、Linux を使用する HDInsight クラスターでのみ機能します。 Linux は、バージョン 3.4 以上の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、[Windows での HDInsight の提供終了](../hdinsight-component-versioning.md#hdinsight-windows-retirement)に関する記事を参照してください。

> [!WARNING]  
> このドキュメントで実行する手順は、`sqooptest` という名前の Azure SQL データベースが既に作成されていることを前提としています。
>
> このドキュメントでは、SQL Database に対するテーブルの作成とクエリを実行するために使用される T-SQL ステートメントを示します。 SQL Database でこれらのステートメントを使用できるクライアントは多数あります。 次のクライアントをお勧めします。
>
> * [SQL Server Management Studio](../../sql-database/sql-database-connect-query-ssms.md)
> * [Visual Studio Code](../../sql-database/sql-database-connect-query-vscode.md)
> * [sqlcmd](https://docs.microsoft.com/sql/tools/sqlcmd-utility) ユーティリティ

## <a name="create-the-table-in-sql-database"></a>SQL Database へのテーブルの作成

> [!IMPORTANT]  
> 「[クラスターと SQL データベースを作成する](hdinsight-use-sqoop.md)」で作成した HDInsight クラスターと SQL Database を使用している場合は、このセクションの手順を省略してください。 データベースとテーブルは、ドキュメント「[クラスターと SQL データベースを作成する](hdinsight-use-sqoop.md)」の手順の一部として作成されました。

SQL クライアントを使用して、SQL Database 内の `sqooptest` データベースに接続します。 次の T-SQL コマンドを使用して、`mobiledata` という名前のテーブルを作成します。

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

## <a name="sqoop-export"></a>Sqoop のエクスポート

1. SSH を使用して、HDInsight クラスターに接続します。 たとえば、次のコマンドは、`mycluster` というクラスターのプライマリ ヘッド ノードに接続します。

    ```bash
    ssh mycluster-ssh.azurehdinsight.net
    ```

    詳細については、[HDInsight での SSH の使用](../hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

2. Sqoop が SQL Database を認識できることを確認するには、次のコマンドを使用します。

    ```bash
    sqoop list-databases --connect jdbc:sqlserver://<serverName>.database.windows.net:1433 --username <adminLogin> -P
    ```
    メッセージが表示されたら、SQL Database ログインのパスワードを入力します。

    このコマンドは、先ほど使用した **sqooptest** データベースを含むデータベースの一覧を返します。

3. Hive の **hivesampletable** テーブルから SQL Database の **mobiledata** テーブルにデータをエクスポートするには、次のコマンドを使用します。

    ```bash
    sqoop export --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> -P -table 'mobiledata' --hcatalog-table hivesampletable
    ```

4. データがエクスポートされたことを確認するには、SQL クライアントから次のクエリを使用して、エクスポートされたデータを表示します。

    ```sql
    SET ROWCOUNT 50;
    SELECT * FROM mobiledata;
    ```

    このコマンドは、テーブルにインポートされた 50 行を一覧表示します。

## <a name="sqoop-import"></a>Sqoop のインポート

1. 次のコマンドを使用して、SQL Database の **mobiledata** テーブルから HDInsight の **wasb:///tutorials/usesqoop/importeddata** ディレクトリにデータをインポートします。

    ```bash
    sqoop import --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> -P --table 'mobiledata' --target-dir 'wasb:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1
    ```

    データ内のフィールドはタブ文字で区切られていて、行は改行文字で終わっています。

    > [!IMPORTANT]  
    > `wasb:///` パスは、Azure Storage を既定のクラスター記憶域として使用するクラスターで動作します。 Azure Data Lake Storage Gen2 を使用するクラスターでは、代わりに `abfs:///` を使用します。 Azure Data Lake Storage Gen1 を使用するクラスターでは、代わりに `adl:///` を使用します。

2. インポートが完了したら、次のコマンドを使用して、新しいディレクトリのデータを列挙します。

    ```bash
    hdfs dfs -text /tutorials/usesqoop/importeddata/part-m-00000
    ```

## <a name="using-sql-server"></a>SQL Server の使用

Sqoop を使用して、SQL Server からデータのインポートとエクスポートを実行することもできます。 SQL Database と SQL Server の使用方法には、次の違いがあります。

* HDInsight と SQL Server の両方が、同じ Azure Virtual Network に存在する必要があります。

    例については、「[Connect HDInsight to your on-premises network](./../connect-on-premises-network.md)」(オンプレミス ネットワークへの HDInsight の接続) をご覧ください。

    Azure Virtual Network での HDInsight の使用に関する詳細情報については、「[Azure Virtual Network を使用した HDInsight 機能の拡張](../hdinsight-extend-hadoop-virtual-network.md)」をご覧ください。 Azure Virtual Network の詳細については、「[Virtual Network の概要](../../virtual-network/virtual-networks-overview.md)」を参照してください。

* SQL 認証を許可するよう、SQL Server を構成する必要があります。 詳細については、[認証モードの選択](https://msdn.microsoft.com/ms144284.aspx)に関するドキュメントを参照してください。

* リモート接続を許可するよう、SQL Server を構成する必要がある場合があります。 詳細については、[SQL Server データベース エンジンへの接続に関するトラブルシューティングの方法](https://social.technet.microsoft.com/wiki/contents/articles/2102.how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx)のドキュメントを参照してください。

* 次の Transact-SQL ステートメントを使用して、**mobiledata** テーブルを作成します。

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

* HDInsight から SQL Server に接続するときに、SQL Server の IP アドレスの使用が必要な場合があります。 例: 

    ```bash
    sqoop import --connect 'jdbc:sqlserver://10.0.1.1:1433;database=sqooptest' --username <adminLogin> -P -table 'mobiledata' --target-dir 'wasb:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1
    ```

## <a name="limitations"></a>制限事項

* 一括エクスポート - Linux ベースの HDInsight では、Microsoft SQL Server または Azure SQL Database にデータをエクスポートするために使用する Sqoop コネクタは一括挿入をサポートしません。

* バッチ処理 - Linux ベースの HDInsight で、挿入処理実行時に `-batch` スイッチを使用すると、Sqoop は挿入操作をバッチ処理するのではなく、複数の挿入を行います。

## <a name="next-steps"></a>次の手順

ここでは Sqoop の使用方法を説明しました。 詳細については、次を参照してください。

* [HDInsight での Apache Oozie の使用](../hdinsight-use-oozie-linux-mac.md):Oozie ワークフローで Sqoop アクションを使用します。
* [HDInsight を使用したフライト遅延データの分析](../hdinsight-analyze-flight-delay-data-linux.md):Apache Hive を使用してフライト遅延データを分析し、Sqoop を使用して Azure SQL Database にデータをエクスポートします。
* [HDInsight へのデータのアップロード](../hdinsight-upload-data.md):HDInsight/Azure Blob Storage にデータをアップロードするその他の方法を説明します。

[hdinsight-versions]:  ../hdinsight-component-versioning.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-get-started]:apache-hadoop-linux-tutorial-get-started.md
[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[sqldatabase-get-started]: ../sql-database-get-started.md

[powershell-start]: https://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: /powershell/azureps-cmdlets-docs
[powershell-script]: https://technet.microsoft.com/library/ee176949.aspx

[sqoop-user-guide-1.4.4]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html
