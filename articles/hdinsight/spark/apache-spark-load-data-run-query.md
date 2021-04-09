---
title: チュートリアル:Apache Spark を使用してデータの読み込みとクエリを実行する - Azure HDInsight
description: チュートリアル - Azure HDInsight で Spark クラスターにデータを読み込み、対話型のクエリを実行する方法を説明します。
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive,mvc
ms.date: 02/12/2020
ms.openlocfilehash: de17bf02392f0bb05820fabba3f9057e067391cf
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104865912"
---
# <a name="tutorial-load-data-and-run-queries-on-an-apache-spark-cluster-in-azure-hdinsight"></a>チュートリアル:Azure HDInsight での Apache Spark クラスターへのデータの読み込みとクエリの実行

このチュートリアルでは、csv ファイルからデータフレームを作成する方法と、Azure HDInsight で [Apache Spark](https://spark.apache.org/) クラスターに対して対話型の Spark SQL クエリを実行する方法を説明します。 Spark で、データフレームは、名前付きの列に編成されたデータの分散型コレクションです。 データフレームは概念的には、リレーショナル データベースのテーブルまたは R/Python のデータ フレームと同等です。

このチュートリアルでは、以下の内容を学習します。
> [!div class="checklist"]
> * csv ファイルからデータフレームを作成する
> * データフレームでクエリを実行する

## <a name="prerequisites"></a>前提条件

HDInsight での Apache Spark クラスター。 [Apache Spark クラスターの作成](./apache-spark-jupyter-spark-sql-use-portal.md)に関するページを参照してください。

## <a name="create-a-jupyter-notebook"></a>Jupyter Notebook の作成

Jupyter Notebook は、さまざまなプログラミング言語をサポートする対話型のノートブック環境です。 ノートブックを使うと、データと対話し、Markdown テキストとコードを組み合わせて、簡単な視覚化を実行できます。

1. URL `https://SPARKCLUSTER.azurehdinsight.net/jupyter` を編集して、`SPARKCLUSTER` を Spark クラスターの名前に置き換えます。 編集した URL を Web ブラウザーに入力します。 入力を求められたら、クラスターのログイン資格情報を入力します。

2. Jupyter の Web ページで **[New]\(新規\)**  >  **[PySpark]** の順に選択して、ノートブックを作成します。

   :::image type="content" source="./media/apache-spark-load-data-run-query/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png " alt-text="Jupyter Notebook を作成して対話型の Spark SQL クエリを実行する" border="true":::

   Untitled (`Untitled.ipynb`) という名前の新しいノートブックが作成されて開かれます。

    > [!NOTE]  
    > PySpark カーネルを使用して Notebook を作成すると、最初のコード セルを実行するときに `spark` セッションが自動的に作成されます。 セッションを明示的に作成する必要はありません。

## <a name="create-a-dataframe-from-a-csv-file"></a>csv ファイルからデータフレームを作成する

アプリケーションは、リモート記憶域 (Azure Storage、Azure Data Lake Storage など) にあるファイルやフォルダーか、Hive テーブル、または Spark でサポートされている他のデータ ソース (Cosmos DB、Azure SQL DB、DW など) から直接データフレームを作成できます。 次のスクリーンショットは、このチュートリアルで使用されている HVAC.csv ファイルのスナップショットを示しています。 csv ファイルには、すべての HDInsight Spark クラスターが付属します。 このデータは、いくつかのビルの温度の変化をキャプチャしています。

:::image type="content" source="./media/apache-spark-load-data-run-query/hdinsight-spark-sample-data-interactive-spark-sql-query.png " alt-text="対話型の Spark SQL クエリのデータのスナップショット" border="true":::

1. 次のコードを Jupyter Notebook の空のセルに貼り付け、**Shift + Enter** キーを押してコードを実行します。 このコードにより、このシナリオに必要な種類がインポートされます。

    ```python
    from pyspark.sql import *
    from pyspark.sql.types import *
    ```

    Jupyter で対話型のクエリを実行すると、Web ブラウザー ウィンドウまたはタブのキャプションに **[(ビジー)]** 状態と Notebook のタイトルが表示されます。 また、右上隅にある **PySpark** というテキストの横に塗りつぶされた円も表示されます。 ジョブが完了すると、白抜きの円に変化します。

    :::image type="content" source="./media/apache-spark-load-data-run-query/hdinsight-spark-interactive-spark-query-status.png " alt-text="対話型の Spark SQL クエリの状態" border="true":::

1. 返されたセッション ID をメモしておきます。 上の図では、セッション ID は 0 です。 必要に応じて、`https://CLUSTERNAME.azurehdinsight.net/livy/sessions/ID/statements` に移動してセッションの詳細を取得できます。ここで、CLUSTERNAME は Spark クラスターの名前、ID はセッション ID 番号です。

1. 次のコードを実行してデータフレームと一時テーブル (**hvac**) を作成します。

    ```python
    # Create a dataframe and table from sample data
    csvFile = spark.read.csv('/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv', header=True, inferSchema=True)
    csvFile.write.saveAsTable("hvac")
    ```

## <a name="run-queries-on-the-dataframe"></a>データフレームでクエリを実行する

テーブルを作成したら、データに対して対話型のクエリを実行できます。

1. Notebook の空のセルで次のコードを実行します。

    ```sql
    %%sql
    SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"
    ```

   次の表形式の出力が表示されます。

     :::image type="content" source="./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result.png " alt-text="対話型の Spark クエリの結果のテーブル出力" border="true":::

2. 他の視覚化でも結果を表示できます。 同じ出力に対する領域グラフを表示するには、 **[領域]** を選択し、他の値を次のように設定します。

    :::image type="content" source="./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result-area-chart.png " alt-text="対話型の Spark クエリの結果の面積グラフ" border="true":::

3. ノートブックのメニュー バーから **[ファイル]**  >  **[Save and Checkpoint]\(保存とチェックポイント)** に移動します。

4. [次のチュートリアル](apache-spark-use-bi-tools.md)をすぐに開始する場合は、Notebook を開いたままにしておきます。 開始しない場合は、ノートブックのメニュー バーから **[ファイル]**  >   **[Close and Halt]\(閉じて停止)** に移動し、ノートブックをシャットダウンしてクラスター リソースを解放します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

HDInsight を使用すると、データと Jupyter Notebook は Azure Storage または Azure Data Lake Storage に格納されるため、クラスターは、使用されていない場合に安全に削除できます。 また、HDInsight クラスターは、使用していない場合でも課金されます。 クラスターの料金は Storage の料金の何倍にもなるため、クラスターを使用しない場合は削除するのが経済的にも合理的です。 すぐに次のチュートリアルに取り掛かる場合は、クラスターを保持することができます。

Azure Portal で、クラスターを開き、 **[削除]** を選択します。

:::image type="content" source="./media/apache-spark-load-data-run-query/hdinsight-azure-portal-delete-cluster.png " alt-text="HDInsight クラスターを削除する" border="true":::

リソース グループ名を選び、リソース グループ ページを開いて、 **[リソース グループの削除]** を選ぶこともできます。 リソース グループを削除すると、HDInsight Spark クラスターと既定のストレージ アカウントの両方が削除されます。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、csv ファイルからデータフレームを作成する方法と、Azure HDInsight で Apache Spark クラスターに対して対話型の Spark SQL クエリを実行する方法を説明しました。 次の記事に進んで、Apache Spark に登録したデータを Power BI などの BI 分析ツールに取り込む方法を確認してください。

> [!div class="nextstepaction"]
> [BI ツールを使用したデータの分析](apache-spark-use-bi-tools.md)
