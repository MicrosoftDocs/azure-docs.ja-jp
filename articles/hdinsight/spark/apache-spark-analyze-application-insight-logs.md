---
title: Spark を使用した Application Insights ログの分析 - Azure HDInsight
description: Application Insight のログを BLOB ストレージにエクスポートし、HDInsight 上の Spark を使用してこのログを分析する方法について説明します。
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/17/2019
ms.openlocfilehash: 9ae06565039e53c0096ff0541f7b28a5b041d9e8
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104864552"
---
# <a name="analyze-application-insights-telemetry-logs-with-apache-spark-on-hdinsight"></a>HDInsight 上の Apache Spark で Application Insights テレメトリ ログを分析する

HDInsight 上の [Apache Spark](https://spark.apache.org/) を使用して Application Insight テレメトリ データを分析する方法について説明します。

[Visual Studio Application Insights](../../azure-monitor/app/app-insights-overview.md) は、お使いの Web アプリケーションを監視する分析サービスです。 Application Insights で生成されたテレメトリ データは Azure Storage にエクスポートできます。 データが Azure Storage にあれば、HDInsight を使用して分析することができます。

## <a name="prerequisites"></a>前提条件

* Application Insights を使用するように構成済みのアプリケーション。

* Linux ベースの HDInsight クラスターの作成に慣れていること。 詳細については、[HDInsight 上の Apache Spark の作成](apache-spark-jupyter-spark-sql.md)に関するページを参照してください。

* Web ブラウザー。

このドキュメントの作成およびテストでは次のリソースを使用しました。

* Application Insights のテレメトリ データは、 [Application Insights を使用するように構成した Node.js Web アプリ](../../azure-monitor/app/nodejs.md)により生成しました。

* データの分析には、バージョン 3.5 の HDInsight クラスターにある Linux ベースの Spark を使用しました。

## <a name="architecture-and-planning"></a>アーキテクチャと計画

以下の図に、この例のサービス アーキテクチャを示します。

:::image type="content" source="./media/apache-spark-analyze-application-insight-logs/application-insights.png" alt-text="Application Insights から Blob Storage を経て Spark に流れるデータ" border="false":::

### <a name="azure-storage"></a>Azure Storage

Application Insights は、BLOB にテレメトリ情報を連続してエクスポートするように構成できます。 こうすることで、HDInsight が BLOB に格納されたデータを読み取ることができます。 ただし、守る必要のある要件がいくつかあります。

* **場所**: ストレージ アカウントと HDInsight が別の場所にあると、待ち時間が長くなることがあります。 また、地域間のデータ移動に対して料金が適用されるので、コストも大きくなります。

    > [!WARNING]  
    > HDInsight 以外の場所でストレージ アカウントを使用することはできません。

* **BLOB の種類**: HDInsight でサポートされるのは、ブロック BLOB のみです。 Application Insights は、既定ではブロック BLOB を使用するので、既定のままであれば HDInsight と連携可能です。

既存のクラスターにストレージを追加する方法については、[ストレージ アカウントの追加](../hdinsight-hadoop-add-storage.md)に関するドキュメントを参照してください。

### <a name="data-schema"></a>データ スキーマ

Application Insights には、BLOB にエクスポートされるテレメトリ データ形式に関する [エクスポート データ モデル](../../azure-monitor/app/export-data-model.md) 情報があります。 このドキュメントの各手順では、データの処理に Spark SQL を使用します。 Spark SQL では、Application Insights によって記録された JSON データ構造のスキーマを自動的に生成できます。

## <a name="export-telemetry-data"></a>テレメトリ データをエクスポートする

[連続エクスポートの構成](../../azure-monitor/app/export-telemetry.md)に関するページにある手順に従って、テレメトリ情報を Azure Storage BLOB にエクスポートするように Application Insights を構成します。

## <a name="configure-hdinsight-to-access-the-data"></a>データにアクセスするように HDInsight を構成する

HDInsight クラスターを作成している場合は、クラスターの作成中にストレージ アカウントを追加します。

既存のクラスターに Azure ストレージ アカウントを追加する方法については、[ストレージ アカウントの追加](../hdinsight-hadoop-add-storage.md)に関するドキュメントをご覧ください。

## <a name="analyze-the-data-pyspark"></a>データの分析: PySpark

1. Web ブラウザーから、`https://CLUSTERNAME.azurehdinsight.net/jupyter` に移動します。ここで、CLUSTERNAME はクラスターの名前です。

2. Jupyter のページの右上隅にある **[New]** をクリックし、**[PySpark]** を選択します。 新しいブラウザーのタブが開き、Python ベースの Jupyter Notebook が表示されます。

3. ページの 1 番目のフィールド (**セル** と呼びます) に、次のテキストを入力します。

   ```python
   sc._jsc.hadoopConfiguration().set('mapreduce.input.fileinputformat.input.dir.recursive', 'true')
   ```

    このコードにより、Spark が入力データのディレクトリ構造へ再帰的にアクセスするよう構成されます。 Application Insights テレメトリは、`/{telemetry type}/YYYY-MM-DD/{##}/` のようなディレクトリ構造で記録されます。

4. **Shift + Enter** キーを押してコードを実行します。 このセルのコードが実行中であることを示す '\*' が、セルの左側にかっこで囲まれて表示されます。 実行が完了すると、'\*' が数字に変わり、セルの下に次のようなテキストが表示されます。

    ```output
    Creating SparkContext as 'sc'

    ID    YARN Application ID    Kind    State    Spark UI    Driver log    Current session?
    3    application_1468969497124_0001    pyspark    idle    Link    Link    ✔

    Creating HiveContext as 'sqlContext'
    SparkContext and HiveContext created. Executing user code ...
    ```

5. 最初のセルの下に新しいセルが作成されます。 この新しいセルに次のテキストを入力します。 `CONTAINER` と `STORAGEACCOUNT` を、Azure Storage アカウント名と Application Insights データを含む BLOB コンテナー名に置き換えます。

   ```python
   %%bash
   hdfs dfs -ls wasbs://CONTAINER@STORAGEACCOUNT.blob.core.windows.net/
   ```

    **Shift + Enter** キーを押してこのセルを実行します。 次のような結果が表示されます。

    ```output
    Found 1 items
    drwxrwxrwx   -          0 1970-01-01 00:00 wasbs://appinsights@contosostore.blob.core.windows.net/contosoappinsights_2bededa61bc741fbdee6b556571a4831
    ```

    返された wasbs パスは、Application Insights テレメトリ データの場所です。 セル内の `hdfs dfs -ls` 行を返された wasbs パスを使用するように変更してから、**Shift + Enter** キーを使用してそのセルを再度実行します。 今回は、テレメトリ データを含むディレクトリが結果に表示されます。

   > [!NOTE]  
   > このセクションの残りの手順では、`wasbs://appinsights@contosostore.blob.core.windows.net/contosoappinsights_{ID}/Requests` ディレクトリを使用しました。 ディレクトリ構造は異なる場合があります。

6. 次のセルに、次のコードを入力します。`WASB_PATH` を前の手順のパスに置き換えます。

   ```python
   jsonFiles = sc.textFile('WASB_PATH')
   jsonData = sqlContext.read.json(jsonFiles)
   ```

    このコードにより、連続エクスポート プロセスでエクスポートされた JSON ファイルからデータフレームが作成されます。 **Shift + Enter** キーを押してこのセルを実行します。
7. 隣のセルに次のコードを入力して実行し、JSON ファイル用に Spark で作成されたスキーマを表示します。

   ```python
   jsonData.printSchema()
   ```

    スキーマは、テレメトリの種類ごとに異なります。 Web 要求 ( `Requests` サブディレクトリに保管されているデータ) について生成されたスキーマを次に示します。

    ```output
    root
    |-- context: struct (nullable = true)
    |    |-- application: struct (nullable = true)
    |    |    |-- version: string (nullable = true)
    |    |-- custom: struct (nullable = true)
    |    |    |-- dimensions: array (nullable = true)
    |    |    |    |-- element: string (containsNull = true)
    |    |    |-- metrics: array (nullable = true)
    |    |    |    |-- element: string (containsNull = true)
    |    |-- data: struct (nullable = true)
    |    |    |-- eventTime: string (nullable = true)
    |    |    |-- isSynthetic: boolean (nullable = true)
    |    |    |-- samplingRate: double (nullable = true)
    |    |    |-- syntheticSource: string (nullable = true)
    |    |-- device: struct (nullable = true)
    |    |    |-- browser: string (nullable = true)
    |    |    |-- browserVersion: string (nullable = true)
    |    |    |-- deviceModel: string (nullable = true)
    |    |    |-- deviceName: string (nullable = true)
    |    |    |-- id: string (nullable = true)
    |    |    |-- osVersion: string (nullable = true)
    |    |    |-- type: string (nullable = true)
    |    |-- location: struct (nullable = true)
    |    |    |-- city: string (nullable = true)
    |    |    |-- clientip: string (nullable = true)
    |    |    |-- continent: string (nullable = true)
    |    |    |-- country: string (nullable = true)
    |    |    |-- province: string (nullable = true)
    |    |-- operation: struct (nullable = true)
    |    |    |-- name: string (nullable = true)
    |    |-- session: struct (nullable = true)
    |    |    |-- id: string (nullable = true)
    |    |    |-- isFirst: boolean (nullable = true)
    |    |-- user: struct (nullable = true)
    |    |    |-- anonId: string (nullable = true)
    |    |    |-- isAuthenticated: boolean (nullable = true)
    |-- internal: struct (nullable = true)
    |    |-- data: struct (nullable = true)
    |    |    |-- documentVersion: string (nullable = true)
    |    |    |-- id: string (nullable = true)
    |-- request: array (nullable = true)
    |    |-- element: struct (containsNull = true)
    |    |    |-- count: long (nullable = true)
    |    |    |-- durationMetric: struct (nullable = true)
    |    |    |    |-- count: double (nullable = true)
    |    |    |    |-- max: double (nullable = true)
    |    |    |    |-- min: double (nullable = true)
    |    |    |    |-- sampledValue: double (nullable = true)
    |    |    |    |-- stdDev: double (nullable = true)
    |    |    |    |-- value: double (nullable = true)
    |    |    |-- id: string (nullable = true)
    |    |    |-- name: string (nullable = true)
    |    |    |-- responseCode: long (nullable = true)
    |    |    |-- success: boolean (nullable = true)
    |    |    |-- url: string (nullable = true)
    |    |    |-- urlData: struct (nullable = true)
    |    |    |    |-- base: string (nullable = true)
    |    |    |    |-- hashTag: string (nullable = true)
    |    |    |    |-- host: string (nullable = true)
    |    |    |    |-- protocol: string (nullable = true)
    ```

8. 次のコードを使用して、データフレームを一時テーブルとして登録し、データに対してクエリを実行します。

   ```python
   jsonData.registerTempTable("requests")
   df = sqlContext.sql("select context.location.city from requests where context.location.city is not null")
   df.show()
   ```

    このクエリは、context.location.city が null でない上位 20 レコードの都市情報を返します。

   > [!NOTE]  
   > context 構造は Application Insights で記録されるすべてのテレメトリに存在していますが、 お使いのログでは city 要素が設定されていない場合があります。 スキーマを使用して、ログのデータが含まれるクエリ可能な他の要素を特定してください。

    このクエリでは、次のテキストのような情報が返されます。

    ```output
    +---------+
    |     city|
    +---------+
    | Bellevue|
    |  Redmond|
    |  Seattle|
    |Charlotte|
    ...
    +---------+
    ```

## <a name="analyze-the-data-scala"></a>データの分析: Scala

1. Web ブラウザーから、`https://CLUSTERNAME.azurehdinsight.net/jupyter` に移動します。ここで、CLUSTERNAME はクラスターの名前です。

2. Jupyter のページの右上隅にある **[新規作成]** をクリックし、**[Scala]** を選択します。 新しいブラウザーのタブが開き、Scala ベースの Jupyter Notebook が表示されます。

3. ページの 1 番目のフィールド (**セル** と呼びます) に、次のテキストを入力します。

   ```scala
   sc.hadoopConfiguration.set("mapreduce.input.fileinputformat.input.dir.recursive", "true")
   ```

    このコードにより、Spark が入力データのディレクトリ構造へ再帰的にアクセスするよう構成されます。 Application Insights テレメトリは、`/{telemetry type}/YYYY-MM-DD/{##}/` のようなディレクトリ構造で記録されます。

4. **Shift + Enter** キーを押してコードを実行します。 このセルのコードが実行中であることを示す '\*' が、セルの左側にかっこで囲まれて表示されます。 実行が完了すると、'\*' が数字に変わり、セルの下に次のようなテキストが表示されます。

    ```output
    Creating SparkContext as 'sc'

    ID    YARN Application ID    Kind    State    Spark UI    Driver log    Current session?
    3    application_1468969497124_0001    spark    idle    Link    Link    ✔

    Creating HiveContext as 'sqlContext'
    SparkContext and HiveContext created. Executing user code ...
    ```

5. 最初のセルの下に新しいセルが作成されます。 この新しいセルに次のテキストを入力します。 `CONTAINER` と `STORAGEACCOUNT` を、Azure Storage アカウント名と Application Insights ログを含む BLOB コンテナー名に置き換えます。

   ```scala
   %%bash
   hdfs dfs -ls wasbs://CONTAINER@STORAGEACCOUNT.blob.core.windows.net/
   ```

    **Shift + Enter** キーを押してこのセルを実行します。 次のような結果が表示されます。

    ```output
    Found 1 items
    drwxrwxrwx   -          0 1970-01-01 00:00 wasbs://appinsights@contosostore.blob.core.windows.net/contosoappinsights_2bededa61bc741fbdee6b556571a4831
    ```

    返された wasbs パスは、Application Insights テレメトリ データの場所です。 セル内の `hdfs dfs -ls` 行を返された wasbs パスを使用するように変更してから、**Shift + Enter** キーを使用してそのセルを再度実行します。 今回は、テレメトリ データを含むディレクトリが結果に表示されます。

   > [!NOTE]  
   > このセクションの残りの手順では、`wasbs://appinsights@contosostore.blob.core.windows.net/contosoappinsights_{ID}/Requests` ディレクトリを使用しました。 テレメトリ データが Web アプリのものでない場合、このディレクトリは存在しない可能性があります。

6. 次のセルに、次のコードを入力します。`WASB\_PATH` を前の手順のパスに置き換えます。

   ```scala
   var jsonFiles = sc.textFile('WASB_PATH')
   val sqlContext = new org.apache.spark.sql.SQLContext(sc)
   var jsonData = sqlContext.read.json(jsonFiles)
   ```

    このコードにより、連続エクスポート プロセスでエクスポートされた JSON ファイルからデータフレームが作成されます。 **Shift + Enter** キーを押してこのセルを実行します。

7. 隣のセルに次のコードを入力して実行し、JSON ファイル用に Spark で作成されたスキーマを表示します。

   ```scala
   jsonData.printSchema
   ```

    スキーマは、テレメトリの種類ごとに異なります。 Web 要求 ( `Requests` サブディレクトリに保管されているデータ) について生成されたスキーマを次に示します。

    ```output
    root
    |-- context: struct (nullable = true)
    |    |-- application: struct (nullable = true)
    |    |    |-- version: string (nullable = true)
    |    |-- custom: struct (nullable = true)
    |    |    |-- dimensions: array (nullable = true)
    |    |    |    |-- element: string (containsNull = true)
    |    |    |-- metrics: array (nullable = true)
    |    |    |    |-- element: string (containsNull = true)
    |    |-- data: struct (nullable = true)
    |    |    |-- eventTime: string (nullable = true)
    |    |    |-- isSynthetic: boolean (nullable = true)
    |    |    |-- samplingRate: double (nullable = true)
    |    |    |-- syntheticSource: string (nullable = true)
    |    |-- device: struct (nullable = true)
    |    |    |-- browser: string (nullable = true)
    |    |    |-- browserVersion: string (nullable = true)
    |    |    |-- deviceModel: string (nullable = true)
    |    |    |-- deviceName: string (nullable = true)
    |    |    |-- id: string (nullable = true)
    |    |    |-- osVersion: string (nullable = true)
    |    |    |-- type: string (nullable = true)
    |    |-- location: struct (nullable = true)
    |    |    |-- city: string (nullable = true)
    |    |    |-- clientip: string (nullable = true)
    |    |    |-- continent: string (nullable = true)
    |    |    |-- country: string (nullable = true)
    |    |    |-- province: string (nullable = true)
    |    |-- operation: struct (nullable = true)
    |    |    |-- name: string (nullable = true)
    |    |-- session: struct (nullable = true)
    |    |    |-- id: string (nullable = true)
    |    |    |-- isFirst: boolean (nullable = true)
    |    |-- user: struct (nullable = true)
    |    |    |-- anonId: string (nullable = true)
    |    |    |-- isAuthenticated: boolean (nullable = true)
    |-- internal: struct (nullable = true)
    |    |-- data: struct (nullable = true)
    |    |    |-- documentVersion: string (nullable = true)
    |    |    |-- id: string (nullable = true)
    |-- request: array (nullable = true)
    |    |-- element: struct (containsNull = true)
    |    |    |-- count: long (nullable = true)
    |    |    |-- durationMetric: struct (nullable = true)
    |    |    |    |-- count: double (nullable = true)
    |    |    |    |-- max: double (nullable = true)
    |    |    |    |-- min: double (nullable = true)
    |    |    |    |-- sampledValue: double (nullable = true)
    |    |    |    |-- stdDev: double (nullable = true)
    |    |    |    |-- value: double (nullable = true)
    |    |    |-- id: string (nullable = true)
    |    |    |-- name: string (nullable = true)
    |    |    |-- responseCode: long (nullable = true)
    |    |    |-- success: boolean (nullable = true)
    |    |    |-- url: string (nullable = true)
    |    |    |-- urlData: struct (nullable = true)
    |    |    |    |-- base: string (nullable = true)
    |    |    |    |-- hashTag: string (nullable = true)
    |    |    |    |-- host: string (nullable = true)
    |    |    |    |-- protocol: string (nullable = true)
    ```

8. 次のコードを使用して、データフレームを一時テーブルとして登録し、データに対してクエリを実行します。

   ```scala
   jsonData.registerTempTable("requests")
   var city = sqlContext.sql("select context.location.city from requests where context.location.city isn't null limit 10").show()
   ```

    このクエリは、context.location.city が null でない上位 20 レコードの都市情報を返します。

   > [!NOTE]  
   > context 構造は Application Insights で記録されるすべてのテレメトリに存在していますが、 お使いのログでは city 要素が設定されていない場合があります。 スキーマを使用して、ログのデータが含まれるクエリ可能な他の要素を特定してください。

    このクエリでは、次のテキストのような情報が返されます。

    ```output
    +---------+
    |     city|
    +---------+
    | Bellevue|
    |  Redmond|
    |  Seattle|
    |Charlotte|
    ...
    +---------+
    ```

## <a name="next-steps"></a>次のステップ

Apache Spark を使用して Azure のデータやサービスを操作するその他の例については、次のドキュメントを参照してください。

* [Apache Spark と BI:HDInsight と BI ツールで Spark を使用した対話型データ分析の実行](apache-spark-use-bi-tools.md)
* [Apache Spark と Machine Learning: HDInsight で Spark を使用して、HVAC データを使用して建物の温度を分析する](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark と Machine Learning:HDInsight で Spark を使用して食品の検査結果を予測する](apache-spark-machine-learning-mllib-ipython.md)
* [HDInsight 上での Apache Spark を使用した Web サイト ログ分析](apache-spark-custom-library-website-log-analysis.md)

Spark アプリケーションを作成および実行する方法については、次のドキュメントを参照してください。

* [Scala を使用してスタンドアロン アプリケーションを作成する](apache-spark-create-standalone-application.md)
* [Livy を使用して Apache Spark クラスター上でジョブをリモートで実行する](apache-spark-livy-rest-interface.md)
