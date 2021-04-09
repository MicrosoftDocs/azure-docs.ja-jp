---
title: Apache Spark を使用して Azure SQL Database のデータを読み書きする
description: HDInsight Spark クラスターと Azure SQL Database の間の接続を設定する方法について説明します。 SQL データベースに対するデータの読み取り、データの書き込み、データのストリーミングを行うには
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/20/2020
ms.openlocfilehash: 0c9c3b2d915e54cf954703c56c2087637cc80aa0
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104864620"
---
# <a name="use-hdinsight-spark-cluster-to-read-and-write-data-to-azure-sql-database"></a>HDInsight Spark クラスターを使用して Azure SQL Database のデータを読み書きする

Azure HDInsight の Apache Spark クラスターを Azure SQL Database と接続する方法について説明します。 次に、SQL データベースに対するデータの読み取り、書き込み、ストリーミングを行います。 この記事の説明では、Jupyter Notebook を使って Scala コード スニペットを実行します。 ただし、Scala または Python でスタンドアロン アプリケーションを作成して、同じタスクを実行することもできます。

## <a name="prerequisites"></a>前提条件

* Azure HDInsight Spark クラスター。  手順については、「[Azure HDInsight での Apache Spark クラスターの作成](apache-spark-jupyter-spark-sql.md)」をご覧ください。

* Azure SQL Database。 [Azure SQL Database でのデータベースの作成](../../azure-sql/database/single-database-create-quickstart.md)に関するページの手順に従ってください。 **AdventureWorksLT** サンプルのスキーマとデータを使って、データベースを作成します。 また、サーバー レベルのファイアウォール規則を作成し、クライアントの IP アドレスが SQL Database にアクセスすることを許可します。 ファイアウォール規則を追加する方法についても、同じ記事をご覧ください。 SQL データベースを作成したら、以下の値がすぐにわかるようにしておきます。 Spark クラスターからデータベースに接続するときに必要です。

    * サーバー名。
    * データベース名。
    * Azure SQL Database の管理者のユーザー名 / パスワード。

* SQL Server Management Studio (SSMS)。 「[SQL Server Management Studio を使って接続とデータの照会を行う](../../azure-sql/database/connect-query-ssms.md)」の手順に従います。

## <a name="create-a-jupyter-notebook"></a>Jupyter Notebook の作成

最初に、Spark クラスターに関連付けられた Jupyter Notebook を作成します。 この Notebook を使って、この記事で使われているコード スニペットを実行します。

1. [Azure Portal](https://portal.azure.com/) でクラスターを開きます。
1. 右側の **クラスター ダッシュボード** の下で、 **[Jupyter Notebook]** を選択します。  **クラスター ダッシュボード** が表示されない場合は、左側のメニューの **[概要]** を選択します。 入力を求められたら、クラスターの管理者資格情報を入力します。

    :::image type="content" source="./media/apache-spark-connect-to-sql-database/hdinsight-spark-cluster-dashboard-jupyter-notebook.png " alt-text="Azure Spark 上の Jupyter Notebook" border="true":::

   > [!NOTE]  
   > ブラウザーで次の URL を開き、Spark クラスターの Jupyter Notebook にアクセスすることもできます。 **CLUSTERNAME** をクラスターの名前に置き換えます。
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

1. Jupyter Notebook で、右上隅の **[New]\(新規\)** をクリックし、 **[Spark]** をクリックして Scala ノートブックを作成します。 HDInsight Spark クラスター上の Jupyter Notebook では、Python2 アプリケーション用の **PySpark** カーネル、Python3 アプリケーション用の **PySpark3** カーネルも提供されます。 この記事では、Scala Notebook を作成します。

    :::image type="content" source="./media/apache-spark-connect-to-sql-database/kernel-jupyter-notebook-on-spark.png " alt-text="Spark 上の Jupyter Notebook 用のカーネル" border="true":::

    これらのカーネルの詳細については、[HDInsight での Apache Spark クラスターと Jupyter Notebook カーネルの使用](apache-spark-jupyter-notebook-kernels.md)に関する記事を参照してください。

   > [!NOTE]  
   > Spark から SQL Database へのデータのストリーミングは、現在 Scala と Java でのみサポートされているため、この記事では Spark (Scala) カーネルを使います。 SQL からの読み取りや SQL への書き込みは Python を使って行うこともできますが、この記事での一貫性を保つため、3 つの操作すべてに Scala を使います。

1. **Untitled** という既定の名前を持つ新しいノートブックが開かれます。 Notebook の名前をクリックし、任意の名前を入力します。

    :::image type="content" source="./media/apache-spark-connect-to-sql-database/hdinsight-spark-jupyter-notebook-name.png " alt-text="Notebook の名前を指定する" border="true":::

アプリケーションの作成を始められるようになります。

## <a name="read-data-from-azure-sql-database"></a>Azure SQL Database からデータを読み取る

このセクションでは、AdventureWorks データベースに存在するテーブル (たとえば、**SalesLT.Address**) からデータを読み取ります。

1. 新しい Jupyter Notebook で、コード セルに次のスニペットを貼り付けて、プレースホルダーの値をデータベースの値で置き換えます。

    ```scala
    // Declare the values for your database

    val jdbcUsername = "<SQL DB ADMIN USER>"
    val jdbcPassword = "<SQL DB ADMIN PWD>"
    val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
    val jdbcPort = 1433
    val jdbcDatabase ="<AZURE SQL DB NAME>"
    ```

    **Shift + Enter** キーを押して、コード セルを実行します。  

1. 下のスニペットを使用して作成される JDBC URL を Spark データフレーム API に渡します。 このコードでは、パラメーターを保持する `Properties` オブジェクトを作成します。 次のスニペットをコード セルに貼り付け、**Shift + Enter** キーを押して実行します。

    ```scala
    import java.util.Properties

    val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=60;"
    val connectionProperties = new Properties()
    connectionProperties.put("user", s"${jdbcUsername}")
    connectionProperties.put("password", s"${jdbcPassword}")
    ```

1. 下のスニペットを使用して、データベース内のテーブルのデータでデータフレームを作成します。 このスニペットでは、**AdventureWorksLT** データベースの一部として利用可能な `SalesLT.Address` テーブルを使います。 次のスニペットをコード セルに貼り付け、**Shift + Enter** キーを押して実行します。

    ```scala
    val sqlTableDF = spark.read.jdbc(jdbc_url, "SalesLT.Address", connectionProperties)
    ```

1. データ スキーマの取得など、データフレームに対する操作を実行できるようになります。

    ```scala
    sqlTableDF.printSchema
    ```

    次の図のような出力が表示されます。

    :::image type="content" source="./media/apache-spark-connect-to-sql-database/read-from-sql-schema-output.png " alt-text="スキーマの出力" border="true":::

1. 上位 10 行の取得のような操作も実行できます。

    ```scala
    sqlTableDF.show(10)
    ```

1. または、特定の列をデータセットから取得します。

    ```scala
    sqlTableDF.select("AddressLine1", "City").show(10)
    ```

## <a name="write-data-into-azure-sql-database"></a>Azure SQL Database にデータを書き込む

このセクションでは、クラスターで利用可能なサンプル CSV ファイルを使ってデータベース内にテーブルを作成し、そこにデータを格納します。 `HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv` にあるサンプルの CSV ファイル (**HVAC.csv**) は、すべての HDInsight クラスターで使うことができます。

1. 新しい Jupyter Notebook で、コード セルに次のスニペットを貼り付けて、プレースホルダーの値をデータベースの値で置き換えます。

    ```scala
    // Declare the values for your database

    val jdbcUsername = "<SQL DB ADMIN USER>"
    val jdbcPassword = "<SQL DB ADMIN PWD>"
    val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
    val jdbcPort = 1433
    val jdbcDatabase ="<AZURE SQL DB NAME>"
    ```

    **Shift + Enter** キーを押して、コード セルを実行します。  

1. 下のスニペットを使用して作成される JDBC URL を Spark データフレーム API に渡すことができます。 このコードでは、パラメーターを保持する `Properties` オブジェクトを作成します。 次のスニペットをコード セルに貼り付け、**Shift + Enter** キーを押して実行します。

    ```scala
    import java.util.Properties

    val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=60;"
    val connectionProperties = new Properties()
    connectionProperties.put("user", s"${jdbcUsername}")
    connectionProperties.put("password", s"${jdbcPassword}")
    ```

1. 次のスニペットを使用して、HVAC.csv のデータのスキーマを抽出し、そのスキーマを使って、CSV からデータフレーム `readDf` にデータを読み込みます。 次のスニペットをコード セルに貼り付け、**Shift + Enter** キーを押して実行します。

    ```scala
    val userSchema = spark.read.option("header", "true").csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv").schema
    val readDf = spark.read.format("csv").schema(userSchema).load("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
    ```

1. `readDf` データフレームを使って、一時テーブル `temphvactable` を作成します。 次に、一時テーブルを使って、Hive テーブル `hvactable_hive` を作成します。

    ```scala
    readDf.createOrReplaceTempView("temphvactable")
    spark.sql("create table hvactable_hive as select * from temphvactable")
    ```

1. 最後に、Hive テーブルを使ってデータベース内にテーブルを作成します。 次のスニペットは、Azure SQL Database に `hvactable` を作成します。

    ```scala
    spark.table("hvactable_hive").write.jdbc(jdbc_url, "hvactable", connectionProperties)
    ```

1. SSMS を使って Azure SQL Database に接続し、`dbo.hvactable` がそこにあることを確認します。

    a. SSMS を開始し、次のスクリーンショットに示すように、接続の詳細を指定して Azure SQL Database に接続します。

    :::image type="content" source="./media/apache-spark-connect-to-sql-database/connect-to-sql-db-ssms.png " alt-text="SSMS1 を使用して SQL Database に接続する" border="true":::

    b. **オブジェクト エクスプローラー** でデータベースとテーブル ノードを展開して、**dbo.hvactable** が作成されていることを確認します。

    :::image type="content" source="./media/apache-spark-connect-to-sql-database/connect-to-sql-db-ssms-locate-table.png " alt-text="SSMS2 を使用して SQL Database に接続する" border="true":::

1. SSMS でクエリを実行して、テーブルの列を表示します。

    ```sql
    SELECT * from hvactable
    ```

## <a name="stream-data-into-azure-sql-database"></a>Azure SQL Database にデータをストリーミングする

このセクションでは、前のセクションで作成した `hvactable` にデータをストリーミングします。

1. 最初のステップとして、`hvactable` 内にレコードがないことを確認します。 SSMS を使い、テーブルに対して次のクエリを実行します。

    ```sql
    TRUNCATE TABLE [dbo].[hvactable]
    ```

1. HDInsight Spark クラスターに新しい Jupyter Notebook を作成します。 コード セルに次のスニペットを貼り付けてから、**Shift + Enter** キーを押します。

    ```scala
    import org.apache.spark.sql._
    import org.apache.spark.sql.types._
    import org.apache.spark.sql.functions._
    import org.apache.spark.sql.streaming._
    import java.sql.{Connection,DriverManager,ResultSet}
    ```

1. **HVAC.csv** から `hvactable` にデータをストリーミングします。 HVAC.csv ファイルは、クラスター (`/HdiSamples/HdiSamples/SensorSampleData/HVAC/`) で入手できます。 次のスニペットでは最初に、ストリーミングするデータのスキーマを取得します。 次に、そのスキーマを使ってストリーミング データフレームを作成します。 次のスニペットをコード セルに貼り付け、**Shift + Enter** キーを押して実行します。

    ```scala
    val userSchema = spark.read.option("header", "true").csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv").schema
    val readStreamDf = spark.readStream.schema(userSchema).csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/") 
    readStreamDf.printSchema
    ```

1. 出力に、**HVAC.csv** のスキーマが表示されます。 `hvactable` にも同じスキーマがあります。 出力にテーブルの列が一覧表示されます。

    :::image type="content" source="./media/apache-spark-connect-to-sql-database/hdinsight-schema-table.png " alt-text="`hdinsight Apache Spark スキーマ テーブル`" border="true":::

1. 最後に、次のスニペットを使って HVAC.csv からデータを読み取り、それをデータベース内の `hvactable` にストリーミングします。 コード セルにスニペットを貼り付けて、プレースホルダーの値をデータベースの値に置き換えてから、**Shift キーを押しながら Enter** キーを押して実行します。

    ```scala
    val WriteToSQLQuery  = readStreamDf.writeStream.foreach(new ForeachWriter[Row] {
        var connection:java.sql.Connection = _
        var statement:java.sql.Statement = _

        val jdbcUsername = "<SQL DB ADMIN USER>"
        val jdbcPassword = "<SQL DB ADMIN PWD>"
        val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
        val jdbcPort = 1433
        val jdbcDatabase ="<AZURE SQL DB NAME>"
        val driver = "com.microsoft.sqlserver.jdbc.SQLServerDriver"
        val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;"

        def open(partitionId: Long, version: Long):Boolean = {
        Class.forName(driver)
        connection = DriverManager.getConnection(jdbc_url, jdbcUsername, jdbcPassword)
        statement = connection.createStatement
        true
        }

        def process(value: Row): Unit = {
        val Date  = value(0)
        val Time = value(1)
        val TargetTemp = value(2)
        val ActualTemp = value(3)
        val System = value(4)
        val SystemAge = value(5)
        val BuildingID = value(6)  

        val valueStr = "'" + Date + "'," + "'" + Time + "'," + "'" + TargetTemp + "'," + "'" + ActualTemp + "'," + "'" + System + "'," + "'" + SystemAge + "'," + "'" + BuildingID + "'"
        statement.execute("INSERT INTO " + "dbo.hvactable" + " VALUES (" + valueStr + ")")
        }

        def close(errorOrNull: Throwable): Unit = {
        connection.close
        }
        })

    var streamingQuery = WriteToSQLQuery.start()
    ```

1. SQL Server Management Studio (SSMS) で、次のクエリを実行し、`hvactable` にデータがストリーミングされていることを確認します。 クエリを実行するたびに、表示されるテーブル行数の値が増加します。

    ```sql
    SELECT COUNT(*) FROM hvactable
    ```

## <a name="next-steps"></a>次のステップ

* [HDInsight Spark クラスターを使用して Data Lake Storage 内のデータを分析する](apache-spark-use-with-data-lake-store.md)
* [Azure HDInsight での Apache Spark クラスターへのデータの読み込みとクエリの実行](apache-spark-load-data-run-query.md)
* [HDInsight で Apache Kafka による Apache Spark 構造化ストリーミングを使用する](../hdinsight-apache-kafka-spark-structured-streaming.md)
