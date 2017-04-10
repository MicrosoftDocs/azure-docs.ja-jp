---
title: "Sqoop を使用して Data Lake Store と Azure SQL Database の間でデータをコピーする | Microsoft Docs"
description: "Sqoop を使用して Azure SQL Database と Data Lake Store の間でデータをコピーする"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 3f914b2a-83cc-4950-b3f7-69c921851683
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/06/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 7f165111cd089d5f32f309235dcbc24d11fb5d64
ms.lasthandoff: 03/25/2017


---
# <a name="copy-data-between-data-lake-store-and-azure-sql-database-using-sqoop"></a>Sqoop を使用して Data Lake Store と Azure SQL Database の間でデータをコピーする
Apache Sqoop を使用して Azure SQL Database と Data Lake Store の間でデータのインポートおよびエクスポートを行う方法について説明します。

## <a name="what-is-sqoop"></a>Sqoop とは
ログやファイルなどの非構造化データおよび半構造化データを処理する場合は、ビッグ データ アプリケーションが自然な選択です。 ただし、リレーショナル データベースに格納された構造化データを処理する必要が生じることもあります。

[Apache Sqoop](https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html) は、リレーショナル データベースとビッグ データ リポジトリ (Data Lake Store など) の間でデータを転送するために設計されたツールです。 このツールを使用すると、Azure SQL Database などのリレーショナル データベース管理システム (RDBMS) から Data Lake Store にデータをインポートすることができます。 ビッグ データ ワークロードを使用してデータを転送および分析し、再びデータを RDBMS にエクスポートすることができます。 このチュートリアルでは、Azure SQL Database をリレーショナル データベースとして使用し、それに対してインポートまたはエクスポートを行うことができます。

## <a name="prerequisites"></a>前提条件
この記事を読み始める前に、次の項目を用意する必要があります。

* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。
* **Azure Data Lake Store アカウント**。 このアカウントを作成する手順については、「 [Azure Data Lake Store の使用を開始する](data-lake-store-get-started-portal.md)
* Data Lake Store アカウントにアクセスできる **Azure HDInsight クラスター**。 [Data Lake Store を使用する HDInsight クラスターの作成](data-lake-store-hdinsight-hadoop-use-portal.md)に関するページを参照してください。 この記事では、Data Lake Store にアクセスできる HDInsight Linux クラスターがあることを前提とします。
* **Azure SQL データベース**。 Azure SQL Database の作成方法については、 [Azure SQL Database の作成](../sql-database/sql-database-get-started.md)

## <a name="do-you-learn-fast-with-videos"></a>ビデオで速習する
[こちらのビデオ](https://mix.office.com/watch/1butcdjxmu114) をご覧ください。

## <a name="create-sample-tables-in-the-azure-sql-database"></a>Azure SQL Database にサンプル テーブルを作成する
1. 最初に、Azure SQL Database で 2 つのサンプル テーブルを作成します。 [SQL Server Management Studio](../sql-database/sql-database-connect-query-ssms.md) または Visual Studio を使用して、Azure SQL Database に接続してから次のクエリを実行します。

    **Table1 の作成**

        CREATE TABLE [dbo].[Table1](
        [ID] [int] NOT NULL,
        [FName] [nvarchar](50) NOT NULL,
        [LName] [nvarchar](50) NOT NULL,
         CONSTRAINT [PK_Table_4] PRIMARY KEY CLUSTERED
            (
                   [ID] ASC
            )
        ) ON [PRIMARY]
        GO

    **Table2 の作成**

        CREATE TABLE [dbo].[Table2](
        [ID] [int] NOT NULL,
        [FName] [nvarchar](50) NOT NULL,
        [LName] [nvarchar](50) NOT NULL,
         CONSTRAINT [PK_Table_4] PRIMARY KEY CLUSTERED
            (
                   [ID] ASC
            )
        ) ON [PRIMARY]
        GO
2. **Table1**で、サンプル データを追加します。 **Table2** は空のままにします。 **Table1** から Data Lake Store にデータをインポートします。 次に、Data Lake Store から **Table2**にデータをエクスポートします。 次のスニペットを実行します。

        INSERT INTO [dbo].[Table1] VALUES (1,'Neal','Kell'), (2,'Lila','Fulton'), (3, 'Erna','Myers'), (4,'Annette','Simpson');


## <a name="use-sqoop-from-an-hdinsight-cluster-with-access-to-data-lake-store"></a>Data Lake Store にアクセスできる HDInsight クラスターから Sqoop を使用する
HDInsight クラスターには、使用可能な Sqoop パッケージが既に存在します。 追加のストレージとして Data Lake Store を使用するように HDInsight クラスターを構成した場合は、Sqoop を使用して (構成の変更なしに)、リレーショナル データベース (この例では、Azure SQL Database) と、Data Lake Store アカウントとの間でインポートまたはエクスポートを行うことができます。

1. このチュートリアルでは、Linux クラスターが作成されていて、SSH を使用してクラスターに接続する必要があることを前提とします。 「 [Linux ベースの HDInsight クラスターへの接続](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)」を参照してください。
2. クラスターから Data Lake Store アカウントにアクセスできるかどうかを確認します。 SSH プロンプトで、次のコマンドを実行します。

        hdfs dfs -ls adl://<data_lake_store_account>.azuredatalakestore.net/

    これにより、Data Lake Store アカウントのファイル/フォルダーの一覧が表示されます。

### <a name="import-data-from-azure-sql-database-into-data-lake-store"></a>Azure SQL Database から Data Lake Store にデータをインポートする
1. Sqoop パッケージを利用できるディレクトリに移動します。 通常、これは `/usr/hdp/<version>/sqoop/bin`にあります。
2. **Table1** から Data Lake Store アカウントにデータをインポートします。 次の構文を使用します。

        sqoop-import --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table1 --target-dir adl://<data-lake-store-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1

    **sql-database-server-name** プレースホルダーは、Azure SQL Database が実行されているサーバーの名前を表していることに注意してください。 **sql-database-name** プレース ホルダーは、実際のデータベース名を表します。

    たとえば、次のように入力します。


        sqoop-import --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=nitinme@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table1 --target-dir adl://myadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1

1. Data Lake Store アカウントにデータが転送済みであることを確認します。 次のコマンドを実行します。

        hdfs dfs -ls adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/

    次の出力が表示されます。


        -rwxrwxrwx   0 sshuser hdfs          0 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/_SUCCESS
        -rwxrwxrwx   0 sshuser hdfs         12 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00000
        -rwxrwxrwx   0 sshuser hdfs         14 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00001
        -rwxrwxrwx   0 sshuser hdfs         13 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00002
        -rwxrwxrwx   0 sshuser hdfs         18 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00003

    各 **part-m-***ファイルは、ソース テーブル**Table1**内の行に対応します。検証する part-m-* ファイルのコンテンツを表示できます。


### <a name="export-data-from-data-lake-store-into-azure-sql-database"></a>Data Lake Store から Azure SQL Database にデータをエクスポートします。
1. Data Lake Store アカウントから Azure SQL Database 内の空のテーブル **Table2**にデータをエクスポートします。 次の構文を使用します。

        sqoop-export --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table2 --export-dir adl://<data-lake-store-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","

    たとえば、次のように入力します。


        sqoop-export --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=nitinme@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table2 --export-dir adl://myadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","

1. SQL Database テーブルにデータがアップロードされていることを確認します。 [SQL Server Management Studio](../sql-database/sql-database-connect-query-ssms.md) または Visual Studio を使用して、Azure SQL Database に接続してから次のクエリを実行します。

        SELECT * FROM TABLE2

    これによる出力は次のとおりです。

         ID  FName   LName
        ------------------
        1    Neal    Kell
        2    Lila    Fulton
        3    Erna    Myers
        4    Annette    Simpson

## <a name="performance-considerations-while-using-sqoop"></a>Sqoop を使用するときのパフォーマンスに関する考慮事項

Data Lake Store にデータをコピーするときの Sqoop ジョブのパフォーマンス調整については、[Sqoop パフォーマンス ドキュメント](https://blogs.msdn.microsoft.com/bigdatasupport/2015/02/17/sqoop-job-performance-tuning-in-hdinsight-hadoop/)をご覧ください。

## <a name="see-also"></a>関連項目
* [Azure Storage BLOB から Data Lake Store へのデータのコピー](data-lake-store-copy-data-azure-storage-blob.md)
* [Data Lake Store のデータをセキュリティで保護する](data-lake-store-secure-data.md)
* [Data Lake Store で Azure Data Lake Analytics を使用する](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Data Lake Store で Azure HDInsight を使用する](data-lake-store-hdinsight-hadoop-use-portal.md)

