---
title: "HDInsight の Spark を使用した Application Insights ログの分析 | Microsoft Docs"
description: "Application Insight のログを BLOB ストレージにエクスポートし、HDInsight 上の Spark を使用してこのログを分析する方法について説明します。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 883beae6-9839-45b5-94f7-7eb0f4534ad5
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/10/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 110f3aa9ce4848c9350ea2e560205aa762decf7a
ms.openlocfilehash: 6fed646d81bf583a0c3ecefcd2bc716fe65ecdc6
ms.lasthandoff: 02/21/2017


---
# <a name="analyze-application-insights-telemetry-logs-with-spark-on-hdinsight"></a>HDInsight の Spark を使用した Application Insights テレメトリ ログの分析

HDInsight で Spark を使用して、Application Insight テレメトリ データを分析する方法について説明します。

[Visual Studio Application Insights](../application-insights/app-insights-overview.md) は、お使いの Web アプリケーションを監視する分析サービスです。 Application Insights で生成されたテレメトリ データは Azure Storage にエクスポート可能であり、Azure Storage で HDInsight を使用して分析することができます。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。

* Application Insights を使用するように構成済みのアプリケーション。 

* Linux ベースの HDInsight クラスターの作成に慣れていること。 詳細については、[HDInsight での Spark の作成](hdinsight-apache-spark-jupyter-spark-sql.md)に関する記事をご覧ください。
  
  > [!IMPORTANT]
  > このドキュメントの手順では、Linux を使用する HDInsight クラスターが必要です。 Linux は、バージョン 3.4 以上の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、[Window での HDInsight の廃止](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date)に関する記事を参照してください。

* Web ブラウザー。

このドキュメントの作成およびテストでは次のリソースを使用しました。

* Application Insights のテレメトリ データは、 [Application Insights を使用するように構成した Node.js Web アプリ](../application-insights/app-insights-nodejs.md)により生成しました。

* データの分析には、バージョン 3.5 の HDInsight クラスターにある Linux ベースの Spark を使用しました。

## <a name="architecture-and-planning"></a>アーキテクチャと計画

以下の図に、この例のサービス アーキテクチャを示します。

![データが Application Insights から BLOB ストレージに流れ、HDInsight 上の Spark で処理される様子を示した図](./media/hdinsight-spark-analyze-application-insight-logs/appinsightshdinsight.png)

### <a name="azure-storage"></a>Azure Storage

Application Insights は、BLOB にテレメトリ情報を連続してエクスポートするように構成できます。 こうすることで、HDInsight が BLOB に格納されたデータを読み取ることができます。 ただし、守る必要のある要件がいくつかあります。

* **場所**: ストレージ アカウントと HDInsight が別の場所にあると、待ち時間が長くなることがあります。 また、地域間のデータ移動に対して料金が適用されるので、コストも大きくなります。
* **BLOB の種類**: HDInsight でサポートされるのは、ブロック BLOB のみです。 Application Insights は、既定ではブロック BLOB を使用するので、既定のままであれば HDInsight と連携可能です。
* **アクセス許可**: Application Insights での連続エクスポートと HDInsight の既定ストレージの両方に同じストレージ アカウントを使用する場合、HDInsight には Application Insight テレメトリ データへのフル アクセスが付与されます。 つまり、HDInsight クラスターからテレメトリ データを削除できるということです。
  
    代わりに、HDInsight と Application Insights テレメトリのストレージ アカウントを分け、 [Shared Access Signature (SAS) を使用して HDInsight からデータへのアクセスを制限する](hdinsight-storage-sharedaccesssignature-permissions.md)ことをお勧めします。 SAS を使用すると、テレメトリ データへの読み取り専用アクセスを HDInsight に付与することができます。

### <a name="data-schema"></a>データ スキーマ

Application Insights には、BLOB にエクスポートされるテレメトリ データ形式に関する [エクスポート データ モデル](../application-insights/app-insights-export-data-model.md) 情報があります。 このドキュメントの各手順では、データの処理に Spark SQL を使用します。 Spark SQL では、Application Insights で記録された JSON データ構造に関するスキーマを自動で生成できるため、分析の実行時にスキーマを手作業で定義する必要はありません。

## <a name="export-telemetry-data"></a>テレメトリ データをエクスポートする
[連続エクスポートの構成](../application-insights/app-insights-export-telemetry.md) に関する記事の手順に従って、Azure Storage BLOB へテレメトリ情報をエクスポートするように Application Insights を構成します。

## <a name="configure-hdinsight-to-access-the-data"></a>データにアクセスするように HDInsight を構成する
[Shared Access Signature (SAS) を使用した HDInsight からデータへのアクセスの制限](hdinsight-storage-sharedaccesssignature-permissions.md) に関する記事を参考に、エクスポートされたテレメトリ データを保持する BLOB コンテナー用の SAS を作成します。 SAS では、データへの読み取り専用アクセスを提供する必要があります。

既存の Linux ベースの HDInsight クラスターに SAS ストレージを追加する方法の詳細は、Shared Access Signature に関するドキュメントに記載されています。 このドキュメントには、HDInsight クラスターの新規作成時にこのストレージを追加する方法も記載されています。

## <a name="analyze-the-data-using-python-pyspark"></a>Python (PySpark) を使用してデータを分析する

1. [Azure ポータル](https://portal.azure.com)で、HDInsight クラスター上の Spark を選択します。 **[Quick Links (クイック リンク)]** セクションで **[Cluster Dashboards (クラスター ダッシュボード)]** を選択してから、[Cluster__ Dashboard (クラスター ダッシュボード)] ブレードで **[Jupyter Notebook]** を選択します。
   
    ![クラスター ダッシュボード](./media/hdinsight-spark-analyze-application-insight-logs/clusterdashboards.png)

2. Jupyter のページの右上隅にある **[New]** をクリックし、**[PySpark]** を選択します。 新しいブラウザーのタブが開き、Python ベースの Jupyter Notebook が表示されます。

3. ページの&1; 番目のフィールド (**セル**と呼びます) に、次のテキストを入力します。
   
        sc._jsc.hadoopConfiguration().set('mapreduce.input.fileinputformat.input.dir.recursive', 'true')
   
    このコードにより、Spark が入力データのディレクトリ構造へ再帰的にアクセスするよう構成されます。 Application Insights テレメトリは、`/{telemetry type}/YYYY-MM-DD/{##}/` のようなディレクトリ構造で記録されます。

4. **Shift + Enter** キーを押してコードを実行します。 このセルのコードが実行中であることを示す '\*' が、セルの左側にかっこで囲まれて表示されます。 実行が完了すると、'\*' が数字に変わり、セルの下に次のようなテキストが表示されます。
   
        Creating SparkContext as 'sc'
   
        ID    YARN Application ID    Kind    State    Spark UI    Driver log    Current session?
        3    application_1468969497124_0001    pyspark    idle    Link    Link    ✔
   
        Creating HiveContext as 'sqlContext'
        SparkContext and HiveContext created. Executing user code ...
5. 最初のセルの下に新しいセルが作成されます。 この新しいセルに次のテキストを入力します。 **CONTAINER** および **STORAGEACCOUNT** を、Application Insights の連続エクスポートを構成するときに使用した Azure ストレージ アカウント名と BLOB コンテナー名に置き換えてください。
   
        %%bash
        hdfs dfs -ls wasb://CONTAINER@STORAGEACCOUNT.blob.core.windows.net/
   
    **Shift + Enter** キーを押してこのセルを実行します。 次のような結果が表示されます。
   
        Found 1 items
        drwxrwxrwx   -          0 1970-01-01 00:00 wasb://appinsights@contosostore.blob.core.windows.net/contosoappinsights_2bededa61bc741fbdee6b556571a4831
   
    返された wasb パスが、Application Insights のテレメトリ データの場所です。 返された wasb パスを使用するようにセルの `hdfs dfs -ls` 行を変更し、**Shift + Enter** キーを押してセルをもう一度実行します。 今回は、テレメトリ データを含むディレクトリが結果に表示されます。
   
   > [!NOTE]
   > このセクションの残りの手順では、`wasb://appinsights@contosostore.blob.core.windows.net/contosoappinsights_{ID}/Requests` ディレクトリを使用しました。 ディレクトリ構造は異なる場合があります。

6. 隣のセルに次のコードを入力します。 **WASB\_PATH** を、前の手順のパスに置き換えてください。
   
        jsonFiles = sc.textFile('WASB_PATH')
        jsonData = sqlContext.read.json(jsonFiles)
   
    このコードにより、連続エクスポート プロセスでエクスポートされた JSON ファイルからデータフレームが作成されます。 **Shift + Enter** キーを押してこのセルを実行します。
7. 隣のセルに次のコードを入力して実行し、JSON ファイル用に Spark で作成されたスキーマを表示します。
   
        jsonData.printSchema()
   
    スキーマは、テレメトリの種類ごとに異なります。 Web 要求 ( `Requests` サブディレクトリに保管されているデータ) について生成されたスキーマを次に示します。
   
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
8. 次のコードを使用して、データフレームを一時テーブルとして登録し、データに対してクエリを実行します。
   
        jsonData.registerTempTable("requests")
        sqlContext.sql("select context.location.city from requests where context.location.city is not null")
   
    このクエリでは、context.location.city が null ではない上位 20 レコードの都市情報が返されます。
   
   > [!NOTE]
   > context 構造は Application Insights で記録されるすべてのテレメトリに存在していますが、お使いのログでは city 要素が設定されていない場合があります。 スキーマを使用して、ログのデータが含まれるクエリ可能な他の要素を特定してください。
   > 
   > 
   
    このクエリでは、次のテキストのような情報が返されます。
   
        +---------+
        |     city|
        +---------+
        | Bellevue|
        |  Redmond|
        |  Seattle|
        |Charlotte|
        ...
        +---------+

## <a name="analyze-the-data-using-scala"></a>Scala を使用してデータを分析する
1. [Azure ポータル](https://portal.azure.com)で、HDInsight クラスター上の Spark を選択します。 **[Quick Links (クイック リンク)]** セクションで **[Cluster Dashboards (クラスター ダッシュボード)]** を選択してから、[Cluster__ Dashboard (クラスター ダッシュボード)] ブレードで **[Jupyter Notebook]** を選択します。
   
    ![クラスター ダッシュボード](./media/hdinsight-spark-analyze-application-insight-logs/clusterdashboards.png)
2. Jupyter のページの右上隅にある **[新規作成]** をクリックし、**[Scala]** を選択します。 新しいブラウザーのタブが開き、Scala ベースの Jupyter Notebook が表示されます。
3. ページの&1; 番目のフィールド (**セル**と呼びます) に、次のテキストを入力します。
   
        sc.hadoopConfiguration.set("mapreduce.input.fileinputformat.input.dir.recursive", "true")
   
    このコードにより、Spark が入力データのディレクトリ構造へ再帰的にアクセスするよう構成されます。 Application Insights テレメトリは、`/{telemetry type}/YYYY-MM-DD/{##}/` のようなディレクトリ構造で記録されます。

4. **Shift + Enter** キーを押してコードを実行します。 このセルのコードが実行中であることを示す '\*' が、セルの左側にかっこで囲まれて表示されます。 実行が完了すると、'\*' が数字に変わり、セルの下に次のようなテキストが表示されます。
   
        Creating SparkContext as 'sc'
   
        ID    YARN Application ID    Kind    State    Spark UI    Driver log    Current session?
        3    application_1468969497124_0001    spark    idle    Link    Link    ✔
   
        Creating HiveContext as 'sqlContext'
        SparkContext and HiveContext created. Executing user code ...
5. 最初のセルの下に新しいセルが作成されます。 この新しいセルに次のテキストを入力します。 **CONTAINER** および **STORAGEACCOUNT** を、Application Insights の連続エクスポートを構成するときに使用した Azure ストレージ アカウント名と BLOB コンテナー名に置き換えてください。
   
        %%bash
        hdfs dfs -ls wasb://CONTAINER@STORAGEACCOUNT.blob.core.windows.net/
   
    **Shift + Enter** キーを押してこのセルを実行します。 次のような結果が表示されます。
   
        Found 1 items
        drwxrwxrwx   -          0 1970-01-01 00:00 wasb://appinsights@contosostore.blob.core.windows.net/contosoappinsights_2bededa61bc741fbdee6b556571a4831
   
    返された wasb パスが、Application Insights のテレメトリ データの場所です。 返された wasb パスを使用するようにセルの `hdfs dfs -ls` 行を変更し、**Shift + Enter** キーを押してセルをもう一度実行します。 今回は、テレメトリ データを含むディレクトリが結果に表示されます。
   
   > [!NOTE]
   > このセクションの残りの手順では、`wasb://appinsights@contosostore.blob.core.windows.net/contosoappinsights_{ID}/Requests` ディレクトリを使用しました。 テレメトリ データが Web アプリのものでない場合、このディレクトリは存在しない可能性があります。 Requests ディレクトリが含まれないテレメトリ データを使用する場合は、別のディレクトリを選択し、以降の手順では、そのディレクトリおよびディレクトリに保管されているデータのスキーマを使用するように変更してください。
   > 
   > 
6. 隣のセルに次のコードを入力します。 **WASB\_PATH** を、前の手順のパスに置き換えてください。
   
        jsonFiles = sc.textFile('WASB_PATH')
        jsonData = sqlContext.read.json(jsonFiles)
   
    このコードにより、連続エクスポート プロセスでエクスポートされた JSON ファイルからデータフレームが作成されます。 **Shift + Enter** キーを押してこのセルを実行します。
7. 隣のセルに次のコードを入力して実行し、JSON ファイル用に Spark で作成されたスキーマを表示します。
   
        jsonData.printSchema
   
    スキーマは、テレメトリの種類ごとに異なります。 Web 要求 ( `Requests` サブディレクトリに保管されているデータ) について生成されたスキーマを次に示します。
   
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
8. 次のコードを使用して、データフレームを一時テーブルとして登録し、データに対してクエリを実行します。
   
        jsonData.registerTempTable("requests")
        var city = sqlContext.sql("select context.location.city from requests where context.location.city is not null limit 10").show()
   
    このクエリでは、context.location.city が null ではない上位 20 レコードの都市情報が返されます。
   
   > [!NOTE]
   > context 構造は Application Insights で記録されるすべてのテレメトリに存在していますが、お使いのログでは city 要素が設定されていない場合があります。 スキーマを使用して、ログのデータが含まれるクエリ可能な他の要素を特定してください。
   > 
   > 
   
    このクエリでは、次のテキストのような情報が返されます。
   
        +---------+
        |     city|
        +---------+
        | Bellevue|
        |  Redmond|
        |  Seattle|
        |Charlotte|
        ...
        +---------+

## <a name="next-steps"></a>次のステップ
Spark でのデータ処理および Azure の各サービスの使用例については、次のドキュメントを参照してください。

* [Spark と BI: HDInsight で BI ツールと Spark を使用した対話型データ分析の実行](hdinsight-apache-spark-use-bi-tools.md)
* [Spark と Machine Learning: HDInsight で Spark を使用して HVAC データを基に建物の温度を分析する](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Spark と Machine Learning: HDInsight で Spark を使用して食品の検査結果を予測する](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Spark ストリーミング: ストリーミング アプリケーションを作成するための HDInsight での Spark の使用](hdinsight-apache-spark-eventhub-streaming.md)
* [Website log analysis using Spark in HDInsight (HDInsight での Spark を使用した Web サイト ログ分析)](hdinsight-apache-spark-custom-library-website-log-analysis.md)

Spark アプリケーションを作成および実行する方法については、次のドキュメントを参照してください。

* [Scala を使用してスタンドアロン アプリケーションを作成する](hdinsight-apache-spark-create-standalone-application.md)
* [Livy を使用して Spark クラスターでジョブをリモートで実行する](hdinsight-apache-spark-livy-rest-interface.md)


