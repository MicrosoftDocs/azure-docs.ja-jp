---
title: Azure Data Lake Storage Gen1 で Apache Spark を使用してデータを分析する
description: Spark ジョブを実行して、Azure Data Lake Storage Gen1 に格納されているデータを分析します
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/13/2019
ms.openlocfilehash: 5a98cb2356c25329e091514dd1d6181dfc2690be
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67448655"
---
# <a name="use-hdinsight-spark-cluster-to-analyze-data-in-data-lake-storage-gen1"></a>HDInsight Spark クラスターを使用して Data Lake Storage Gen1 内のデータを分析する

この記事では、HDInsight Spark クラスターで利用できる [Jupyter Notebook](https://jupyter.org/) を使用して、Data Lake Storage アカウントからデータを読み取るジョブを実行します。

## <a name="prerequisites"></a>前提条件

* Azure Data Lake Storage Gen1 アカウント。 「[Azure portal で Azure Data Lake Storage Gen1 の使用を開始する](../../data-lake-store/data-lake-store-get-started-portal.md)」の手順に従ってください。

* Data Lake Storage Gen1 をストレージとして使用する Azure HDInsight Spark クラスター。 [HDInsight のクラスターを設定する](../../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)」をご覧ください。

    
## <a name="prepare-the-data"></a>データを準備する

> [!NOTE]  
> Data Lake Storage を既定のストレージとして使用する HDInsight クラスターを作成した場合は、この手順を行う必要はありません。 クラスター作成処理で、クラスター作成中に指定する Data Lake Storage アカウントにいくつかのサンプル データが追加されるためです。 スキップして、「Data Lake Storage で HDInsight Spark クラスターを使用する」のセクションに進みます。

Data Lake Storage を追加ストレージとして使用し、Azure Storage Blob を既定のストレージとして使用する HDInsight クラスターを作成した場合は、まず、いくつかのサンプル データを Data Lake Storage アカウントにコピーする必要があります。 HDInsight クラスターに関連付けられている Azure Storage Blob のサンプル データを使用することができます。 この操作には、 [ADLCopy ツール](https://aka.ms/downloadadlcopy) を使用できます。 リンク先からツールをダウンロードしてインストールします。

1. コマンド プロンプトを開き、AdlCopy がインストールされているディレクトリ (通常は `%HOMEPATH%\Documents\adlcopy`) に移動します。

2. 次のコマンドを実行して、ソース コンテナーの特定の BLOB を Data Lake Storage にコピーします。

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>

    **/HdiSamples/HdiSamples/SensorSampleData/hvac/** にある **HVAC.csv** サンプル データ ファイルを Azure Data Lake Storage アカウントにコピーします。 コード スニペットを次に示します。

        AdlCopy /Source https://mydatastore.blob.core.windows.net/mysparkcluster/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv /dest swebhdfs://mydatalakestore.azuredatalakestore.net/hvac/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

   > [!WARNING]  
   > ファイル名とパス名で大文字と小文字が適切に使用されていることを確認します。

3. Data Lake Storage アカウントがある Azure サブスクリプションの資格情報を入力するように求められます。 次のスニペットのような出力が表示されます。

        Initializing Copy.
        Copy Started.
        100% data copied.
        Copy Completed. 1 file copied.

    データ ファイル (**HVAC.csv**) が、Data Lake Storage アカウントの **/hvac** フォルダーにコピーされます。

## <a name="use-an-hdinsight-spark-cluster-with-data-lake-storage-gen1"></a>Data Lake Storage Gen1 で HDInsight Spark クラスターを使用する

1. [Azure portal](https://portal.azure.com/) のスタート画面で Apache Spark クラスターのタイルをクリックします (スタート画面にピン留めしている場合)。 **[すべて参照]**  >  **[HDInsight クラスター]** でクラスターに移動することもできます。

2. Spark クラスター ブレードで、 **[クイック リンク]** をクリックし、 **[クラスター ダッシュボード]** ブレードで **[Jupyter Notebook]** をクリックします。 入力を求められたら、クラスターの管理者資格情報を入力します。

   > [!NOTE]  
   > ブラウザーで次の URL を開き、クラスターの Jupyter Notebook にアクセスすることもできます。 **CLUSTERNAME** をクラスターの名前に置き換えます。
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

3. 新しい Notebook を作成します。 **[新規]** をクリックし、 **[PySpark]** をクリックします。

    ![新しい Jupyter Notebook の作成](./media/apache-spark-use-with-data-lake-store/hdinsight-create-jupyter-notebook.png "新しい Jupyter Notebook の作成")

4. PySpark カーネルを使用して Notebook を作成したため、コンテキストを明示的に作成する必要はありません。 最初のコード セルを実行すると、Spark および Hive コンテキストが自動的に作成されます。 このシナリオに必要な種類をインポートすることから始めることができます。 このためには、次のコード スニペットをセルに貼り付けて、 **Shift + Enter**キーを押します。

        from pyspark.sql.types import *

    Jupyter でジョブを実行するたびに、Web ブラウザー ウィンドウのタイトルに **[(ビジー)]** ステータスと Notebook のタイトルが表示されます。 また、右上隅にある **PySpark** というテキストの横に塗りつぶされた円も表示されます。 ジョブが完了すると、白抜きの円に変化します。

     ![Jupyter Notebook ジョブのステータス](./media/apache-spark-use-with-data-lake-store/hdinsight-jupyter-job-status.png "Jupyter Notebook ジョブのステータス")

5. Data Lake Storage Gen1 アカウントにコピーした **HVAC.csv** ファイルを使用して、サンプル データを一時テーブルに読み込みます。 Data Lake Storage アカウントのデータにアクセスするには、次の URL パターンを使用します。

   * Data Lake Storage Gen1 を既定のストレージとしている場合、HVAC.csv は次の URL と同じようなパスになります。

           adl://<data_lake_store_name>.azuredatalakestore.net/<cluster_root>/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv

       または、次のように簡略化された形式を使用することもできます。

           adl:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv

   * Data Lake Storage を追加ストレージとしている場合、HVAC.csv は、次のようなコピーした場所にあります。

           adl://<data_lake_store_name>.azuredatalakestore.net/<path_to_file>

     空のセルに次のコード例を貼り付けて、**MYDATALAKESTORE** を Data Lake Storage アカウント名に置き換え、**Shift + Enter** キーを押します。 このコード サンプルは、 **hvac**という一時テーブルにデータを登録します。

           # Load the data. The path below assumes Data Lake Storage is default storage for the Spark cluster
           hvacText = sc.textFile("adl://MYDATALAKESTORE.azuredatalakestore.net/cluster/mysparkcluster/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

           # Create the schema
           hvacSchema = StructType([StructField("date", StringType(), False),StructField("time", StringType(), False),StructField("targettemp", IntegerType(), False),StructField("actualtemp", IntegerType(), False),StructField("buildingID", StringType(), False)])

           # Parse the data in hvacText
           hvac = hvacText.map(lambda s: s.split(",")).filter(lambda s: s[0] != "Date").map(lambda s:(str(s[0]), str(s[1]), int(s[2]), int(s[3]), str(s[6]) ))

           # Create a data frame
           hvacdf = sqlContext.createDataFrame(hvac,hvacSchema)

           # Register the data fram as a table to run queries against
           hvacdf.registerTempTable("hvac")

6. PySpark カーネルを使用しているため、`%%sql` マジックを使用して、作成した一時テーブル **hvac** に対して SQL クエリを直接実行できます。 `%%sql` マジックの詳細と、PySpark カーネルで使用できるその他のマジックの詳細については、[Apache Spark HDInsight クラスターと Jupyter Notebook で使用可能なカーネル](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic)に関する記事を参照してください。

        %%sql
        SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"

7. ジョブが正常に完了すると、既定で次の出力が表示されます。

      ![クエリ結果のテーブル出力](./media/apache-spark-use-with-data-lake-store/jupyter-tabular-output.png "クエリ結果のテーブル出力")

     他の視覚化でも結果を表示できます。 たとえば、ある出力の領域グラフは次のようになります。

     ![クエリ結果の領域グラフ](./media/apache-spark-use-with-data-lake-store/jupyter-area-output.png "クエリ結果の領域グラフ")

8. アプリケーションの実行が完了したら、Notebook をシャットダウンしてリソースを解放する必要があります。 そのためには、Notebook の **[ファイル]** メニューの **[Close and Halt]** (閉じて停止) をクリックします。 これにより、Notebook がシャットダウンされ、閉じられます。


## <a name="next-steps"></a>次の手順

* [スタンドアロン Scala アプリケーションを作成して、Apache Spark クラスターで実行する](apache-spark-create-standalone-application.md)
* [Azure Toolkit for IntelliJ の HDInsight ツールを使用して HDInsight Spark Linux クラスター向けの Apache Spark アプリケーションを作成する](apache-spark-intellij-tool-plugin.md)
* [Azure Toolkit for Eclipse の HDInsight ツールを使用して HDInsight Spark Linux クラスター向けの Apache Spark アプリケーションを作成する](apache-spark-eclipse-tool-plugin.md)
* [Azure HDInsight クラスターで Azure Data Lake Storage Gen2 を使用する](../hdinsight-hadoop-use-data-lake-storage-gen2.md)
