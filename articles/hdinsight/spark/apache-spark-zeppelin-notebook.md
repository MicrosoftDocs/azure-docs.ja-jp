---
title: Azure HDInsight の Apache Spark クラスターで Zeppelin Notebook を使用する
description: Azure HDInsight の Apache Spark クラスターで Zeppelin Notebook を使用する手順を説明します。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/04/2019
ms.openlocfilehash: 219cdeea228ae3e334213a0f0654f904592cb09e
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67448737"
---
# <a name="use-apache-zeppelin-notebooks-with-apache-spark-cluster-on-azure-hdinsight"></a>Azure HDInsight 上の Apache Spark クラスターで Apache Zeppelin Notebook を使用する

HDInsight Spark クラスターには、[Apache Spark](https://spark.apache.org/) ジョブを実行するために使用できる [Apache Zeppelin](https://zeppelin.apache.org/) Notebook が含まれています。 この記事では、HDInsight クラスターで Zeppelin Notebook を使用する方法について説明します。

**前提条件:**

* Azure サブスクリプション。 [Azure 無料試用版の取得](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。
* HDInsight での Apache Spark クラスター。 手順については、「 [Create Apache Spark clusters in Azure HDInsight (Azure HDInsight での Apache Spark クラスターの作成)](apache-spark-jupyter-spark-sql.md)」を参照してください。
* クラスターのプライマリ ストレージの URI スキーム。 Azure Blob Storage では `wasb://`、Azure Data Lake Storage Gen2 では `abfs://`、Azure Data Lake Storage Gen1 では `adl://` です。 Blob Storage または Data Lake Storage Gen2 で安全な転送が有効になっている場合、URI はそれぞれ `wasbs://` または `abfss://` になります。  詳細については、「[Azure Storage で安全な転送が必要](../../storage/common/storage-require-secure-transfer.md)」も参照してください。

## <a name="launch-an-apache-zeppelin-notebook"></a>Apache Zeppelin Notebook を起動する

1. Spark クラスターの **[概要]** で、 **[クラスター ダッシュボード]** から **[Zeppelin Notebook]** を選択します。 クラスターの管理者資格情報を入力します。  

   > [!NOTE]  
   > ブラウザーで次の URL を開き、クラスターの Zeppelin Notebook にアクセスすることもできます。 **CLUSTERNAME** をクラスターの名前に置き換えます。
   >
   > `https://CLUSTERNAME.azurehdinsight.net/zeppelin`

2. 新しい Notebook を作成します。 ヘッダー ウィンドウから、 **[Notebook]**  >  **[新しいメモを作成します]** の順に移動します。

    ![新しい Zeppelin Notebook を作成する](./media/apache-spark-zeppelin-notebook/hdinsight-create-zeppelin-notebook.png "新しい Zeppelin Notebook を作成する")

    Notebook の名前を入力し、 **[Note の作成]** を選択します。

3. Notebook のヘッダーに [接続] というステータスが表示されることを確認します。 これは、右上隅の緑色の点で示されます。

    ![Zeppelin Notebook のステータス](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-connected.png "Zeppelin Notebook のステータス")

4. サンプル データを一時テーブルに読み込みます。 HDInsight の Spark クラスターを作成すると、サンプル データ ファイル `hvac.csv` が、関連するストレージ アカウントの `\HdiSamples\SensorSampleData\hvac` にコピーされます。

    新しい Notebook に既定で作成される空の段落に、次のスニペットを貼り付けます。

    ```scala
    %livy2.spark
    //The above magic instructs Zeppelin to use the Livy Scala interpreter

    // Create an RDD using the default Spark context, sc
    val hvacText = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

    // Define a schema
    case class Hvac(date: String, time: String, targettemp: Integer, actualtemp: Integer, buildingID: String)
   
    // Map the values in the .csv file to the schema
    val hvac = hvacText.map(s => s.split(",")).filter(s => s(0) != "Date").map(
        s => Hvac(s(0), 
                s(1),
                s(2).toInt,
                s(3).toInt,
                s(6)
        )
    ).toDF()

    // Register as a temporary table called "hvac"
    hvac.registerTempTable("hvac")
    ```

    **Shift + Enter** キーを押すか、段落の **[プレイ]** ボタンをクリックして、スニペットを実行します。 段落の右上隅にあるステータスが、[準備完了]、[保留中]、[実行中]、[完了] の順に進行します。 出力が同じ段落の下に表示されます。 スクリーンショットは次のようになります。

    ![生データから一時テーブルを作成する](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-load-data.png "生データから一時テーブルを作成する")

    各段落にタイトルを指定することもできます。 段落の右上隅から **[設定]** アイコン (鎖歯車) を選択し、 **[タイトルの表示]** を選択します。  

    > [!NOTE]  
    > %spark2 インタープリターは、いずれの HDInsight バージョンの Zeppelin Notebook でもサポートされていません。また、%sh インタープリターは HDInsight 4.0 以降ではサポートされなくなります。

5. `hvac` テーブルに対して Spark SQL ステートメントを実行できます。 次のクエリを新しい段落に貼り付けます。 このクエリは、ビル ID と、特定の日の各ビルの目標温度と実温度の差を取得します。 **Shift + Enter**キーを押します。

    ```sql
    %sql
    select buildingID, (targettemp - actualtemp) as temp_diff, date from hvac where date = "6/1/13" 
    ```  

    先頭にある **%sql** ステートメントは、Livy Scala インタープリターを使用するように Notebook に指示します。

6. **棒グラフ** アイコンを選択し、表示を変更します。  **棒グラフ**を選択した後に表示される **[設定]** で、 **[キー]** と **[値]** を選択できます。  次のスクリーンショットでは出力を示します。

    ![Notebook を使用して Spark SQL ステートメントを実行する](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-1.png "Notebook を使用して Spark SQL ステートメントを実行する")

7. クエリの変数を使用して Spark SQL ステートメントを実行することもできます。 次のスニペットでは、クエリで変数 `Temp` と照会できる値を定義する方法を示します。 初めてクエリを実行すると、変数に指定した値がドロップダウンに自動的に設定されます。

    ```sql
    %sql  
    select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff from hvac where targettemp > "${Temp = 65,65|75|85}"
    ```

    このスニペットを新しい段落に貼り付けて、 **Shift + Enter**キーを押します。 **[Temp]** ドロップダウン リスト ボックスから **[65]** を選択します。 

8. **棒グラフ** アイコンを選択し、表示を変更します。  次に、 **[設定]** を選択し、次の変更を行います。

   * **[グループ]:** **targettemp** を追加します。  
   * **[値]:** 1. **date** を削除します。  2. **temp_diff** を追加します。  手順 3.  アグリゲーターを **SUM** から **AVG** に変更します。  

     次のスクリーンショットでは出力を示します。

     ![Notebook を使用して Spark SQL ステートメントを実行する](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-2.png "Notebook を使用して Spark SQL ステートメントを実行する")

9. Livy インタープリターを再起動して、アプリケーションを終了します。 再起動するには、右上隅のログインしている [ユーザー名] を選択してインタープリターの [設定] を開き、 **[インタープリター]** を選択します。  

    ![インタープリターを起動する](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Hive の出力")

10. **livy** までスクロールし、 **[再起動]** を選択します。  プロンプトで **[OK]** を選択します。

    ![Livy インタープリターを再起動する](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-restart-interpreter.png "Zeppelin インタープリターを再起動します")

## <a name="how-do-i-use-external-packages-with-the-notebook"></a>Notebook で外部のパッケージを使用する方法
HDInsight 上の Apache Spark クラスターに標準では搭載されていない外部のコミュニティから提供されているパッケージを使用するようにクラスター内の Zeppelin Notebook を構成することができます。 利用できるすべてのパッケージは、 [Maven リポジトリ](https://search.maven.org/) で検索できます。 公開されているパッケージの一覧を他のソースから入手してもかまいません。 たとえば、コミュニティから提供されている全パッケージの一覧を [Spark Packages](https://spark-packages.org/)で入手できます。

この記事では、Jupyter Notebook で [spark-csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) パッケージを使用する方法について説明します。

1. インタープリターの [設定] を開きます。 右上隅のログインしている [ユーザー名] を選択し、 **[インタープリター]** を選択します。

    ![インタープリターを起動する](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Hive の出力")

2. **livy** までスクロールし、 **[編集]** を選択します。

    ![インタープリターの設定を変更する](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-1.png "インタープリターの設定を変更する")

3. `livy.spark.jars.packages` という新しいキーを追加し、`group:id:version` 形式で値を設定します。 したがって、[spark-csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) パッケージを使用する場合は、キーの値を `com.databricks:spark-csv_2.10:1.4.0` に設定する必要があります。

    ![インタープリターの設定を変更する](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-2.png "インタープリターの設定を変更する")

    **[保存]** を選択し、Livy インタープリターを再起動します。

4. 上記で入力したキーの値に到達する方法を理解するには、次のようにします。
   
    a. Maven リポジトリから目的のパッケージを探します。 この記事では [spark-csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) を使用しました。
   
    b. リポジトリで **GroupId**、**ArtifactId**、**Version** の値を確認します。
   
    ![Jupyter Notebook で外部パッケージを使用する](./media/apache-spark-zeppelin-notebook/use-external-packages-with-jupyter.png "Jupyter Notebook で外部パッケージを使用する")
   
    c. 3 つの値をコロン ( **:** ) で区切って連結します。
   
        com.databricks:spark-csv_2.10:1.4.0

## <a name="where-are-the-zeppelin-notebooks-saved"></a>Zeppelin Notebook の保存場所
Zeppelin Notebook は、クラスターのヘッドノードに保存されます。 そのため、クラスターを削除すると、Notebook も同様に削除されます。 後で別のクラスターで使用するように Notebook を保存する場合は、ジョブの実行を完了した後に Notebook をエクスポートする必要があります。 Notebook をエクスポートするには、下の図に示すように **[エクスポート]** アイコンを選択します。

![Notebook のダウンロード](./media/apache-spark-zeppelin-notebook/zeppelin-download-notebook.png "Notebook のダウンロード")

これにより、Notebook は JSON ファイルとしてダウンロード先に保存されます。

## <a name="livy-session-management"></a>Livy セッションを管理する
コードの最初の段落を Zeppelin Notebook で実行すると、HDInsight Spark クラスターに新しい Livy セッションが作成されます。 このセッションは、後に作成するすべての Zeppelin Notebooks 間で共有されます。 何らかの理由 (クラスターの再起動など) で Livy セッションが強制終了すると、Zeppelin Notebook からジョブを実行できなくなります。

このような場合は、Zeppelin Notebook からジョブの実行を開始する前に、次の手順を実行する必要があります。  

1. Zeppelin Notebook から Livy インタープリターを再起動します。 再起動するには、右上隅のログインしている [ユーザー名] を選択してインタープリターの [設定] を開き、 **[インタープリター]** を選択します。

    ![インタープリターを起動する](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Hive の出力")

2. **livy** までスクロールし、 **[再起動]** を選択します。

    ![Livy インタープリターを再起動する](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-restart-interpreter.png "Zeppelin インタープリターを再起動します")

3. 既存の Zeppelin Notebook からコードのセルを実行します。 これにより、HDInsight クラスター内に新しい Livy セッションが作成されます。

## <a name="seealso"></a>関連項目
* [概要: Azure HDInsight での Apache Spark](apache-spark-overview.md)

### <a name="scenarios"></a>シナリオ
* [Apache Spark と BI:HDInsight と BI ツールで Spark を使用した対話型データ分析の実行](apache-spark-use-bi-tools.md)
* [Apache Spark と Machine Learning:HDInsight で Spark を使用して、HVAC データを使用して建物の温度を分析する](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark と Machine Learning:HDInsight で Spark を使用して食品の検査結果を予測する](apache-spark-machine-learning-mllib-ipython.md)
* [HDInsight 上での Apache Spark を使用した Web サイト ログ分析](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>アプリケーションの作成と実行
* [Scala を使用してスタンドアロン アプリケーションを作成する](apache-spark-create-standalone-application.md)
* [Apache Livy を使用して Apache Spark クラスターでジョブをリモートから実行する](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>ツールと拡張機能
* [IntelliJ IDEA 用の HDInsight Tools プラグインを使用して Apache Spark Scala アプリケーションを作成および送信する](apache-spark-intellij-tool-plugin.md)
* [IntelliJ IDEA 用の HDInsight Tools プラグインを使用して Apache Spark アプリケーションをリモートでデバッグする](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [HDInsight 用の Apache Spark クラスター内の Jupyter Notebook で使用可能なカーネル](apache-spark-jupyter-notebook-kernels.md)
* [Jupyter Notebook で外部のパッケージを使用する](apache-spark-jupyter-notebook-use-external-packages.md)
* [Jupyter をコンピューターにインストールして HDInsight Spark クラスターに接続する](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>リソースの管理
* [Azure HDInsight での Apache Spark クラスターのリソースの管理](apache-spark-resource-manager.md)
* [HDInsight の Apache Spark クラスターで実行されるジョブの追跡とデバッグ](apache-spark-job-debugging.md)

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[azure-create-storageaccount]:../../storage/common/storage-create-storage-account.md 
