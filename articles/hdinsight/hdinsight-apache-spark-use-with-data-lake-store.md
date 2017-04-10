---
title: "Azure Data Lake Store での Apache Spark を使用したデータ分析 | Microsoft Docs"
description: "Spark ジョブを実行して Azure Data Lake Store に格納されているデータを分析する"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 1f174323-c17b-428c-903d-04f0e272784c
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/21/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 1429bf0d06843da4743bd299e65ed2e818be199d
ms.openlocfilehash: c801dc221d4aaa2c3ed0a7d10c5d58065b26e427
ms.lasthandoff: 03/22/2017


---

# <a name="use-hdinsight-spark-cluster-to-analyze-data-in-data-lake-store"></a>HDInsight Spark クラスターを使用して Data Lake Store のデータを分析する

このチュートリアルでは、HDInsight Spark クラスターで利用できる Jupyter Notebook を使用して、Data Lake Store アカウントからデータを読み取るジョブを実行します。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。

* Azure Data Lake Store アカウント。 「 [Azure ポータルで Azure Data Lake Store の使用を開始する](../data-lake-store/data-lake-store-get-started-portal.md)」の手順に従ってください。

* Data Lake Store をストレージとして使用する Azure HDInsight Spark クラスター。 手順については、「[Azure Portal を使用して、Data Lake Store を使用する HDInsight クラスターを作成する](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)」を参照してください。

    
## <a name="prepare-the-data"></a>データを準備する

> [!NOTE]
> Data Lake Store を既定のストレージとして使用する HDInsight クラスターを作成した場合は、この手順を行う必要はありません。 クラスター作成処理で、クラスター作成中に指定する Data Lake Store アカウントにいくつかのサンプル データが追加されるためです。 スキップして、「[Data Lake Store を使用する HDInsight Spark クラスターを使用する](#use-an-hdinsight-spark-cluster-with-data-lake-store)」に進みます。
>
>

Data Lake Store を追加ストレージとして使用し、Azure Storage Blob を既定のストレージとして使用する HDInsight クラスターを作成した場合は、まず、いくつかのサンプル データを Data Lake Store アカウントにコピーする必要があります。 HDInsight クラスターに関連付けられている Azure Storage Blob のサンプル データを使用することができます。 この操作には、 [ADLCopy ツール](http://aka.ms/downloadadlcopy) を使用できます。 リンク先からツールをダウンロードしてインストールします。

1. コマンド プロンプトを開き、AdlCopy がインストールされているディレクトリ (通常は `%HOMEPATH%\Documents\adlcopy`) に移動します。

2. 次のコマンドを実行して、ソース コンテナーの特定の BLOB を Data Lake Store にコピーします。

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>

    **/HdiSamples/HdiSamples/SensorSampleData/hvac/** にある **HVAC.csv** サンプル データ ファイルを Azure Data Lake Store アカウントにコピーします。 コード スニペットを次に示します。

        AdlCopy /Source https://mydatastore.blob.core.windows.net/mysparkcluster/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv /dest swebhdfs://mydatalakestore.azuredatalakestore.net/hvac/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

   > [!WARNING]
   > ファイル名とパス名の大文字/小文字が正しいことを確認します。
   >
   >
3. Data Lake Store アカウントがある Azure サブスクリプションの資格情報を入力するように求められます。 次のような出力が表示されます。

        Initializing Copy.
        Copy Started.
        100% data copied.
        Copy Completed. 1 file copied.

    データ ファイル (**HVAC.csv**) が、Data Lake Store アカウントの **/hvac** フォルダーにコピーされます。

## <a name="use-an-hdinsight-spark-cluster-with-data-lake-store"></a>Data Lake Store を使用する HDInsight Spark クラスターを使用する

1. [Azure ポータル](https://portal.azure.com/)のスタート画面で Spark クラスターのタイルをクリックします (スタート画面にピン留めしている場合)。 **[すべて参照]** > **[HDInsight クラスター]** でクラスターに移動することもできます。

2. Spark クラスター ブレードで、**[クイック リンク]** をクリックし、**[クラスター ダッシュボード]** ブレードで **[Jupyter Notebook]** をクリックします。 入力を求められたら、クラスターの管理者資格情報を入力します。

   > [!NOTE]
   > ブラウザーで次の URL を開き、クラスターの Jupyter Notebook にアクセスすることもできます。 **CLUSTERNAME** をクラスターの名前に置き換えます。
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   >
   >

3. 新しい Notebook を作成します。 **[新規]** をクリックし、**[PySpark]** をクリックします。

    ![新しい Jupyter Notebook の作成](./media/hdinsight-apache-spark-use-with-data-lake-store/hdispark.note.jupyter.createnotebook.png "新しい Jupyter Notebook の作成")

4. PySpark カーネルを使用して Notebook を作成したため、コンテキストを明示的に作成する必要はありません。 最初のコード セルを実行すると、Spark および Hive コンテキストが自動的に作成されます。 このシナリオに必要な種類をインポートすることから始めることができます。 このためには、次のコード スニペットをセルに貼り付けて、 **Shift + Enter**キーを押します。

        from pyspark.sql.types import *

    Jupyter でジョブを実行するたびに、Web ブラウザー ウィンドウのタイトルに **[(ビジー)]** ステータスと Notebook のタイトルが表示されます。 また、右上隅にある **PySpark** というテキストの横に塗りつぶされた円も表示されます。 ジョブが完了すると、白抜きの円に変化します。

     ![Jupyter Notebook ジョブのステータス](./media/hdinsight-apache-spark-use-with-data-lake-store/hdispark.jupyter.job.status.png "Jupyter Notebook ジョブのステータス")

5. Data Lake Store アカウントにコピーした **HVAC.csv** ファイルを使用して、サンプル データを一時テーブルに読み込みます。 Data Lake Store アカウントのデータにアクセスするには、次の URL パターンを使用します。

    * Data Lake Store を既定のストレージとしている場合、HVAC.csv は次の URL と同じようなパスになります。

            adl://<data_lake_store_name>.azuredatalakestore.net/<cluster_root>/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv

        または、次のように簡略化された形式を使用することもできます。

            adl:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv

    * Data Lake Store を追加ストレージとしている場合、HVAC.csv は、次のようなコピーした場所にあります。

            adl://<data_lake_store_name>.azuredatalakestore.net/<path_to_file>

     空のセルに次のコード例を貼り付けて、**MYDATALAKESTORE** を Data Lake Store アカウント名に置き換え、**Shift + Enter** キーを押します。 このコード サンプルは、 **hvac**という一時テーブルにデータを登録します。

            # Load the data. The path below assumes Data Lake Store is default storage for the Spark cluster
            hvacText = sc.textFile("adl://MYDATALAKESTORE.azuredatalakestore.net/cluster/mysparkcluster/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

            # Create the schema
            hvacSchema = StructType([StructField("date", StringType(), False),StructField("time", StringType(), False),StructField("targettemp", IntegerType(), False),StructField("actualtemp", IntegerType(), False),StructField("buildingID", StringType(), False)])

            # Parse the data in hvacText
            hvac = hvacText.map(lambda s: s.split(",")).filter(lambda s: s[0] != "Date").map(lambda s:(str(s[0]), str(s[1]), int(s[2]), int(s[3]), str(s[6]) ))

            # Create a data frame
            hvacdf = sqlContext.createDataFrame(hvac,hvacSchema)

            # Register the data fram as a table to run queries against
            hvacdf.registerTempTable("hvac")

6. PySpark カーネルを使用しているため、`%%sql` マジックを使用して、作成した一時テーブル **hvac** に対して SQL クエリを直接実行できます。 `%%sql` マジックの詳細と、PySpark カーネルで使用できるその他のマジックの詳細については、 [Spark HDInsight クラスターと Jupyter Notebook で使用可能なカーネル](hdinsight-apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic)に関する記事を参照してください。

        %%sql
        SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"

7. ジョブが正常に完了すると、既定で次の出力が表示されます。

      ![クエリ結果のテーブル出力](./media/hdinsight-apache-spark-use-with-data-lake-store/tabular.output.png "クエリ結果のテーブル出力")

     他の視覚化でも結果を表示できます。 たとえば、ある出力の領域グラフは次のようになります。

     ![クエリ結果の領域グラフ](./media/hdinsight-apache-spark-use-with-data-lake-store/area.output.png "クエリ結果の領域グラフ")

8. アプリケーションの実行が完了したら、Notebook をシャットダウンしてリソースを解放する必要があります。 そのためには、Notebook の **[ファイル]** メニューの **[Close and Halt]** (閉じて停止) をクリックします。 これにより、Notebook がシャットダウンされ、閉じられます。


## <a name="next-steps"></a>次のステップ

* [スタンドアロン Scala アプリケーションを作成して、Apache Spark クラスターで実行する](hdinsight-apache-spark-create-standalone-application.md)
* [Azure Toolkit for IntelliJ の HDInsight ツールを使用して HDInsight Spark Linux クラスター向けの Spark アプリケーションを作成する](hdinsight-apache-spark-intellij-tool-plugin.md)
* [Azure Toolkit for Eclipse の HDInsight ツールを使用して HDInsight Spark Linux クラスター向けの Spark アプリケーションを作成する](hdinsight-apache-spark-eclipse-tool-plugin.md)

