---
title: "Python ライブラリを使用した Azure Spark クラスターでの Web サイト ログの分析 | Microsoft Docs"
description: "HDInsight の Spark クラスターとカスタム ライブラリを使用して、Web サイト ログを分析する"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 8c61c70f-fe7f-4f0f-a4ab-0cccee5668c9
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: 3f5b7409c5245ee975b0208f812668a0c35ff1c3
ms.lasthandoff: 03/18/2017


---
# <a name="analyze-website-logs-using-a-custom-library-with-apache-spark-cluster-on-hdinsight"></a>HDInsight でカスタム ライブラリと Apache Spark クラスターを使用して Web サイト ログを分析する

この Notebook では、HDInsight の Spark でカスタム ライブラリを使用してログ データを分析する方法を示します。 使用するカスタム ライブラリは、 **iislogparser.py**と呼ばれる Python ライブラリです。

> [!TIP]
> このチュートリアルは、HDInsight で作成する Spark (Linux) クラスター上の Jupyter Notebook としても利用できます。 Notebook エクスペリエンスにより、Notebook 自体から Python のスニペットを実行することができます。 Notebook からチュートリアルを実行するには、Spark クラスターを作成し、Jupyter Notebook (`https://CLUSTERNAME.azurehdinsight.net/jupyter`) を起動し、**PySpark** フォルダーにある **Analyze logs with Spark using a custom library.ipynb (カスタム library.ipynb を使用した Spark でのログの分析)** Notebook を実行します。
>
>

**前提条件:**

次のものが必要です。

* Azure サブスクリプション。 [Azure 無料試用版の取得](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。

* HDInsight での Apache Spark クラスター。 手順については、 [Azure HDInsight での Apache Spark クラスターの作成](hdinsight-apache-spark-jupyter-spark-sql.md)に関するページを参照してください。

## <a name="save-raw-data-as-an-rdd"></a>生データを RDD として保存する
このセクションでは、HDInsight の Apache Spark クラスターと関連付けられた [Jupyter](https://jupyter.org) Notebook を使用して、生のサンプル データを処理して Hive テーブルとして保存するジョブを実行します。 サンプル データは、すべてのクラスターにおいて既定で使用できる .csv ファイル (hvac.csv) です。

データを Hive テーブルとして保存した後、次のセクションでは、Power BI や Tableau などの BI ツールを使用して Hive テーブルに接続します。

1. [Azure ポータル](https://portal.azure.com/)のスタート画面で Spark クラスターのタイルをクリックします (スタート画面にピン留めしている場合)。 **[すべて参照]** > **[HDInsight クラスター]** でクラスターに移動することもできます。   
2. Spark クラスター ブレードから **[クラスター ダッシュボード]** をクリックし、**[Jupyter Notebook]** をクリックします。 入力を求められたら、クラスターの管理者資格情報を入力します。

   > [!NOTE]
   > ブラウザーで次の URL を開き、クラスターの Jupyter Notebook にアクセスすることもできます。 **CLUSTERNAME** をクラスターの名前に置き換えます。
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   >
   >
3. 新しい Notebook を作成します。 **[新規]** をクリックし、**[PySpark]** をクリックします。

    ![新しい Jupyter Notebook の作成](./media/hdinsight-apache-spark-custom-library-website-log-analysis/hdispark.note.jupyter.createnotebook.png "新しい Jupyter Notebook の作成")
4. Untitled.pynb という名前の新しい Notebook が作成されて開かれます。 上部の Notebook 名をクリックし、わかりやすい名前を入力します。

    ![Notebook の名前を指定](./media/hdinsight-apache-spark-custom-library-website-log-analysis/hdispark.note.jupyter.notebook.name.png "Notebook の名前を指定")
5. PySpark カーネルを使用して Notebook を作成したため、コンテキストを明示的に作成する必要はありません。 最初のコード セルを実行すると、Spark および Hive コンテキストが自動的に作成されます。 このシナリオに必要な種類をインポートすることから始めることができます。 次のスニペットを空のセルに貼り付けて、 **Shift + Enter**キーを押します。

        from pyspark.sql import Row
        from pyspark.sql.types import *


1. クラスターにあらかじめ用意されているサンプル ログ データを使用して、RDD を作成します。 クラスターに関連付けられている既定のストレージ アカウント内のデータ (**\HdiSamples\HdiSamples\WebsiteLogSampleData\SampleLog\909f2b.log**) にアクセスすることができます。

        logs = sc.textFile('wasbs:///HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b.log')


1. 前の手順が正常に完了したことを確認するために、サンプル ログ セットを取得します。

        logs.take(5)

    次のような出力が表示されます。

        # -----------------
        # THIS IS AN OUTPUT
        # -----------------

        [u'#Software: Microsoft Internet Information Services 8.0',
         u'#Fields: date time s-sitename cs-method cs-uri-stem cs-uri-query s-port cs-username c-ip cs(User-Agent) cs(Cookie) cs(Referer) cs-host sc-status sc-substatus sc-win32-status sc-bytes cs-bytes time-taken',
         u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step2.png X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 53175 871 46',
         u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step3.png X-ARR-LOG-ID=9eace870-2f49-4efd-b204-0d170da46b4a 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 51237 871 32',
         u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step4.png X-ARR-LOG-ID=4bea5b3d-8ac9-46c9-9b8c-ec3e9500cbea 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 72177 871 47']

## <a name="analyze-log-data-using-a-custom-python-library"></a>カスタム Python ライブラリを使用してログ データを分析する
1. 上の出力の最初の数行にはヘッダー情報が含まれており、残りの各行は、そのヘッダーで説明されているスキーマと一致します。 このようなログの解析は、複雑である場合があります。 そこで、このようなログの解析を簡単にするために、カスタム Python ライブラリ (**iislogparser.py**) を使用します。 既定では、このライブラリは、HDInsight の Spark クラスターの **/HdiSamples/HdiSamples/WebsiteLogSampleData/iislogparser.py**に含まれます。

    しかし、このライブラリは `PYTHONPATH` に含まれていないため、`import iislogparser` のような import ステートメントで使用することはできません。 このライブラリを使用するには、すべてのワーカー ノードに配布する必要があります。 次のスニペットを実行します。

        sc.addPyFile('wasbs:///HdiSamples/HdiSamples/WebsiteLogSampleData/iislogparser.py')


1. `iislogparser` には `parse_log_line` 関数があり、この関数はログ行がヘッダー行である場合は `None` を返し、ログ行に到達すると `LogLine` クラスのインスタンスを返します。 次のように `LogLine` クラスを使用して、RDD からログ行だけを抽出します。

        def parse_line(l):
            import iislogparser
            return iislogparser.parse_log_line(l)
        logLines = logs.map(parse_line).filter(lambda p: p is not None).cache()
2. 手順が正常に完了したことを確認するために、いくつかの抽出されたログ行を取得します。

       logLines.take(2)

   出力は次のようになります。

       # -----------------
       # THIS IS AN OUTPUT
       # -----------------

       [2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step2.png X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 53175 871 46,
        2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step3.png X-ARR-LOG-ID=9eace870-2f49-4efd-b204-0d170da46b4a 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 51237 871 32]
3. `LogLine` クラスには、いくつかの便利なメソッドもあります。たとえば、`is_error()` は、ログ エントリにエラー コードが含まれているかどうかを返します。 これを使用して、抽出したログ行内のエラー数を計算し、別のファイルにすべてのエラーを記録します。

       errors = logLines.filter(lambda p: p.is_error())
       numLines = logLines.count()
       numErrors = errors.count()
       print 'There are', numErrors, 'errors and', numLines, 'log entries'
       errors.map(lambda p: str(p)).saveAsTextFile('wasbs:///HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b-2.log')

   出力は次のように表示されます。

       # -----------------
       # THIS IS AN OUTPUT
       # -----------------

       There are 30 errors and 646 log entries
4. **Matplotlib** を使用して、データを視覚化することもできます。 たとえば、要求の実行が長時間になる原因を特定するために、平均して最も処理時間がかかっているファイルを見つけたい場合があります。
   次のスニペットでは、要求を処理するために最も時間がかかっている上位 25 件のリソースを取得します。

       def avgTimeTakenByKey(rdd):
           return rdd.combineByKey(lambda line: (line.time_taken, 1),
                                   lambda x, line: (x[0] + line.time_taken, x[1] + 1),
                                   lambda x, y: (x[0] + y[0], x[1] + y[1]))\
                     .map(lambda x: (x[0], float(x[1][0]) / float(x[1][1])))

       avgTimeTakenByKey(logLines.map(lambda p: (p.cs_uri_stem, p))).top(25, lambda x: x[1])

   出力は次のように表示されます。

       # -----------------
       # THIS IS AN OUTPUT
       # -----------------

       [(u'/blogposts/mvc4/step13.png', 197.5),
        (u'/blogposts/mvc2/step10.jpg', 179.5),
        (u'/blogposts/extractusercontrol/step5.png', 170.0),
        (u'/blogposts/mvc4/step8.png', 159.0),
        (u'/blogposts/mvcrouting/step22.jpg', 155.0),
        (u'/blogposts/mvcrouting/step3.jpg', 152.0),
        (u'/blogposts/linqsproc1/step16.jpg', 138.75),
        (u'/blogposts/linqsproc1/step26.jpg', 137.33333333333334),
        (u'/blogposts/vs2008javascript/step10.jpg', 127.0),
        (u'/blogposts/nested/step2.jpg', 126.0),
        (u'/blogposts/adminpack/step1.png', 124.0),
        (u'/BlogPosts/datalistpaging/step2.png', 118.0),
        (u'/blogposts/mvc4/step35.png', 117.0),
        (u'/blogposts/mvcrouting/step2.jpg', 116.5),
        (u'/blogposts/aboutme/basketball.jpg', 109.0),
        (u'/blogposts/anonymoustypes/step11.jpg', 109.0),
        (u'/blogposts/mvc4/step12.png', 106.0),
        (u'/blogposts/linq8/step0.jpg', 105.5),
        (u'/blogposts/mvc2/step18.jpg', 104.0),
        (u'/blogposts/mvc2/step11.jpg', 104.0),
        (u'/blogposts/mvcrouting/step1.jpg', 104.0),
        (u'/blogposts/extractusercontrol/step1.png', 103.0),
        (u'/blogposts/sqlvideos/sqlvideos.jpg', 102.0),
        (u'/blogposts/mvcrouting/step21.jpg', 101.0),
        (u'/blogposts/mvc4/step1.png', 98.0)]
5. また、この情報をプロットの形式で表示することもできます。 プロットを作成する最初の手順として、まず、一時テーブル **AverageTime**を作成します。 テーブル グループは、特定の時間に異常な遅延の急増があったかどうかがわかるように、ログを時間でグループ化します。

       avgTimeTakenByMinute = avgTimeTakenByKey(logLines.map(lambda p: (p.datetime.minute, p))).sortByKey()
       schema = StructType([StructField('Minutes', IntegerType(), True),
                            StructField('Time', FloatType(), True)])

       avgTimeTakenByMinuteDF = sqlContext.createDataFrame(avgTimeTakenByMinute, schema)
       avgTimeTakenByMinuteDF.registerTempTable('AverageTime')
6. 次に、以下の SQL クエリを実行して、 **AverageTime** テーブル内のすべてのレコードを取得できます。

       %%sql -o averagetime
       SELECT * FROM AverageTime

   `%%sql` マジックの後に `-o averagetime` と入力して、クエリの出力が Jupyter サーバー (通常はクラスターのヘッドノード) にローカルに保持されるようにします。 出力は、 [Pandas](http://pandas.pydata.org/) データフレームとして、 **averagetime**という名前で保持されます。

   出力は次のように表示されます。

   ![SQL クエリ出力](./media/hdinsight-apache-spark-custom-library-website-log-analysis/sql.output.png "SQL クエリ出力")

   `%%sql` マジックについて詳しくは、「[%%sql マジックでサポートされるパラメーター](hdinsight-apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic)」をご覧ください。
7. データの視覚効果の構築に使用するライブラリ、Matplotlib を使用して、プロットを作成できます。 プロットはローカルに保持された **averagetime** データフレームから作成する必要があるため、コード スニペットは `%%local` マジックで始める必要があります。 これにより、コードは Jupyter サーバーでローカルに実行されます。

       %%local
       %matplotlib inline
       import matplotlib.pyplot as plt

       plt.plot(averagetime['Minutes'], averagetime['Time'], marker='o', linestyle='--')
       plt.xlabel('Time (min)')
       plt.ylabel('Average time taken for request (ms)')

   出力は次のように表示されます。

   ![Matplotlib の出力](./media/hdinsight-apache-spark-custom-library-website-log-analysis/hdi-apache-spark-web-log-analysis-plot.png "Matplotlib の出力")
8. アプリケーションの実行が完了したら、Notebook をシャットダウンしてリソースを解放する必要があります。 そのためには、Notebook の **[ファイル]** メニューの **[Close and Halt]** (閉じて停止) をクリックします。 これにより、Notebook がシャットダウンされ、閉じられます。

## <a name="seealso"></a>関連項目
* [概要: Azure HDInsight での Apache Spark](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>シナリオ
* [Spark と BI: HDInsight と BI ツールで Spark を使用した対話型データ分析の実行](hdinsight-apache-spark-use-bi-tools.md)
* [Spark と Machine Learning: HDInsight で Spark を使用して HVAC データを基に建物の温度を分析する](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Spark と Machine Learning: HDInsight で Spark を使用して食品の検査結果を予測する](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Spark ストリーミング: リアルタイム ストリーミング アプリケーションを作成するための HDInsight での Spark の使用](hdinsight-apache-spark-eventhub-streaming.md)

### <a name="create-and-run-applications"></a>アプリケーションの作成と実行
* [Scala を使用してスタンドアロン アプリケーションを作成する](hdinsight-apache-spark-create-standalone-application.md)
* [Livy を使用して Spark クラスターでジョブをリモートで実行する](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>ツールと拡張機能
* [Use HDInsight Tools Plugin for IntelliJ IDEA to create and submit Spark Scala applicatons (Linux)](hdinsight-apache-spark-intellij-tool-plugin.md)
* [IntelliJ IDEA 用の HDInsight Tools プラグインを使用して Spark アプリケーションをリモートでデバッグする](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [HDInsight の Spark クラスターで Zeppelin Notebook を使用する](hdinsight-apache-spark-use-zeppelin-notebook.md)
* [HDInsight 用の Spark クラスターの Jupyter Notebook で使用可能なカーネル](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Jupyter Notebook で外部のパッケージを使用する](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Jupyter をコンピューターにインストールして HDInsight Spark クラスターに接続する](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>リソースの管理
* [Azure HDInsight での Apache Spark クラスターのリソースの管理](hdinsight-apache-spark-resource-manager.md)
* [HDInsight の Apache Spark クラスターで実行されるジョブの追跡とデバッグ](hdinsight-apache-spark-job-debugging.md)

