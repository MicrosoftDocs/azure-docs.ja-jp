---
title: Data Lake Storage Gen1 と Azure SQL の間でデータをコピーする - Sqoop | Microsoft Docs
description: Sqoop を使用して Azure SQL Database と Azure Data Lake Storage Gen1 の間でデータをコピーする
services: data-lake-store
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 07/30/2019
ms.author: twooley
ms.openlocfilehash: 9bb787138267fd8a9fab4dea233c1c828b457d67
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92109189"
---
# <a name="copy-data-between-data-lake-storage-gen1-and-azure-sql-database-using-sqoop"></a>Sqoop を使用して Data Lake Storage Gen1 と Azure SQL Database の間でデータをコピーする

Apache Sqoop を使用して Azure SQL Database と Azure Data Lake Storage Gen1 の間でデータのインポートおよびエクスポートを行う方法について説明します。

## <a name="what-is-sqoop"></a>Sqoop とは

ログやファイルなどの非構造化データおよび半構造化データを処理する場合は、ビッグ データ アプリケーションが自然な選択です。 ただし、リレーショナル データベースに格納された構造化データを処理する必要が生じることもあります。

[Apache Sqoop](https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html) は、リレーショナル データベースとビッグ データ レポジトリ (Data Lake Storage Gen1 など) の間でデータを転送するために設計されたツールです。 このツールを使用すると、Azure SQL Database などのリレーショナル データベース管理システム (RDBMS) から Data Lake Storage Gen1 にデータをインポートすることができます。 ビッグ データ ワークロードを使用してデータを転送および分析し、再びデータを RDBMS にエクスポートすることができます。 この記事では、インポートまたはエクスポートを行う対象のリレーショナル データベースとして、Azure SQL Database のデータベースを使用します。

## <a name="prerequisites"></a>前提条件

開始する前に、次の項目を用意する必要があります。

* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。
* **Azure Data Lake Storage Gen1 アカウント**。 アカウントを作成する手順については、「[Azure Data Lake Storage Gen1 の使用を開始する](data-lake-store-get-started-portal.md)」を参照してください
* Data Lake Storage Gen1 アカウントにアクセスできる **Azure HDInsight クラスター**。 [Data Lake Storage Gen1 を使用する HDInsight クラスターの作成](data-lake-store-hdinsight-hadoop-use-portal.md)に関するページを参照してください。 この記事では、Data Lake Storage Gen1 にアクセスできる HDInsight Linux クラスターがあることを前提とします。
* **Azure SQL データベース**。 Azure SQL Database でデータベースを作成する方法については、「[Azure SQL Database にデータベースを作成する](../azure-sql/database/single-database-create-quickstart.md)」を参照してください

## <a name="create-sample-tables-in-the-database"></a>データベースにサンプル テーブルを作成する

1. 開始するには、データベースに 2 つのサンプル テーブルを作成します。 [SQL Server Management Studio](../azure-sql/database/connect-query-ssms.md) または Visual Studio を使用して、データベースに接続してから次のクエリを実行します。

    **Table1 の作成**

    ```tsql
    CREATE TABLE [dbo].[Table1](
    [ID] [int] NOT NULL,
    [FName] [nvarchar](50) NOT NULL,
    [LName] [nvarchar](50) NOT NULL,
     CONSTRAINT [PK_Table_1] PRIMARY KEY CLUSTERED
           (
                  [ID] ASC
           )
    ) ON [PRIMARY]
    GO
    ```

    **Table2 の作成**

    ```tsql
    CREATE TABLE [dbo].[Table2](
    [ID] [int] NOT NULL,
    [FName] [nvarchar](50) NOT NULL,
    [LName] [nvarchar](50) NOT NULL,
     CONSTRAINT [PK_Table_2] PRIMARY KEY CLUSTERED
           (
                  [ID] ASC
           )
    ) ON [PRIMARY]
    GO
    ```

1. 次のコマンドを実行して、**Table1** にサンプル データを追加します。 **Table2** は空のままにします。 後で、**Table1** から Data Lake Storage Gen1 にデータをインポートします。 次に、Data Lake Storage Gen1 から **Table2** にデータをエクスポートします。

    ```tsql
    INSERT INTO [dbo].[Table1] VALUES (1,'Neal','Kell'), (2,'Lila','Fulton'), (3, 'Erna','Myers'), (4,'Annette','Simpson');
    ```

## <a name="use-sqoop-from-an-hdinsight-cluster-with-access-to-data-lake-storage-gen1"></a>Data Lake Storage Gen1 にアクセスできる HDInsight クラスターから Sqoop を使用する

HDInsight クラスターには、使用可能な Sqoop パッケージが既に存在します。 追加のストレージとして Data Lake Storage Gen1 を使用するように HDInsight クラスターを構成した場合は、Sqoop を使用して (構成の変更なしに)、Azure SQL Database などのリレーショナル データベースと Data Lake Storage Gen1 アカウントの間でデータのインポートまたはエクスポートを行うことができます。

1. この記事では、Linux クラスターが作成されていて、SSH を使用してクラスターに接続する必要があることを前提とします。 「 [Linux ベースの HDInsight クラスターへの接続](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)」を参照してください。

1. クラスターから Data Lake Storage Gen1 アカウントにアクセスできるかどうかを確認します。 SSH プロンプトで、次のコマンドを実行します。

    ```console
    hdfs dfs -ls adl://<data_lake_storage_gen1_account>.azuredatalakestore.net/
    ```

   このコマンドにより、Data Lake Storage Gen1 アカウントのファイル/フォルダーの一覧が提供されます。

### <a name="import-data-from-azure-sql-database-into-data-lake-storage-gen1"></a>Azure SQL Database から Data Lake Storage Gen1 にデータをインポートする

1. Sqoop パッケージを利用できるディレクトリに移動します。 通常、この場所は `/usr/hdp/<version>/sqoop/bin` です。

1. **Table1** から Data Lake Storage Gen1 アカウントにデータをインポートします。 次の構文を使用します。

    ```console
    sqoop-import --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table1 --target-dir adl://<data-lake-storage-gen1-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1
    ```

   **sql-database-server-name** プレースホルダーは、データベースが実行されているサーバーの名前を表しています。 **sql-database-name** プレース ホルダーは、実際のデータベース名を表します。

   たとえば、次のように入力します。

    ```console
    sqoop-import --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=user1@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table1 --target-dir adl://myadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1
    ```

1. Data Lake Storage Gen1 アカウントにデータが転送済みであることを確認します。 次のコマンドを実行します。

    ```console
    hdfs dfs -ls adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/
    ```

   次の出力が表示されます。

    ```console
    -rwxrwxrwx   0 sshuser hdfs          0 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/_SUCCESS
    -rwxrwxrwx   0 sshuser hdfs         12 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00000
    -rwxrwxrwx   0 sshuser hdfs         14 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00001
    -rwxrwxrwx   0 sshuser hdfs         13 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00002
    -rwxrwxrwx   0 sshuser hdfs         18 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00003
    ```

   各 **part-m-** _ ファイルは、ソース テーブル _ *Table1** 内の行に対応します。検証する part-m-* ファイルのコンテンツを表示できます。

### <a name="export-data-from-data-lake-storage-gen1-into-azure-sql-database"></a>Data Lake Storage Gen1 から Azure SQL Database にデータをエクスポートする

1. Data Lake Storage Gen1 アカウントから Azure SQL Database 内の空のテーブル **Table2** にデータをエクスポートします。 次の構文を使用します。

    ```console
    sqoop-export --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table2 --export-dir adl://<data-lake-storage-gen1-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","
    ```

   たとえば、次のように入力します。

    ```console
    sqoop-export --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=user1@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table2 --export-dir adl://myadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","
    ```

1. SQL Database テーブルにデータがアップロードされていることを確認します。 [SQL Server Management Studio](../azure-sql/database/connect-query-ssms.md) または Visual Studio を使用して、Azure SQL Database に接続してから次のクエリを実行します。

    ```tsql
    SELECT * FROM TABLE2
    ```

   このコマンドの出力は次のようになります。

    ```output
     ID  FName    LName
    -------------------
    1    Neal     Kell
    2    Lila     Fulton
    3    Erna     Myers
    4    Annette  Simpson
    ```

## <a name="performance-considerations-while-using-sqoop"></a>Sqoop を使用するときのパフォーマンスに関する考慮事項

Data Lake Storage Gen1 にデータをコピーする Sqoop ジョブのパフォーマンス調整の詳細については、[Sqoop のパフォーマンスに関するブログ記事](/archive/blogs/shanyu/performance-tuning-for-hdinsight-storm-and-microsoft-azure-eventhubs)を参照してください。

## <a name="next-steps"></a>次のステップ

* [Azure Storage Blob から Data Lake Storage Gen1 へのデータのコピー](data-lake-store-copy-data-azure-storage-blob.md)
* [Data Lake Storage Gen1 でのデータのセキュリティ保護](data-lake-store-secure-data.md)
* [Data Lake Storage Gen1 で Azure Data Lake Analytics を使用する](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Data Lake Storage Gen1 で Azure HDInsight を使用する](data-lake-store-hdinsight-hadoop-use-portal.md)