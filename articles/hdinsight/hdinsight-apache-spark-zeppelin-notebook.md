---
title: "Azure HDInsight の Apache Spark クラスターで Zeppelin Notebook を使用する | Microsoft Docs"
description: "Azure HDInsight の Apache Spark クラスターで Zeppelin Notebook を使用する手順を説明します。"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: df489d70-7788-4efa-a089-e5e5006421e2
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/03/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: a939a0845d7577185ff32edd542bcb2082543a26
ms.openlocfilehash: a5494f16e3398be507080dd4fac591144f69d9fc
ms.lasthandoff: 01/24/2017


---
# <a name="use-zeppelin-notebooks-with-apache-spark-cluster-on-azure-hdinsight"></a>Azure HDInsight の Apache Spark クラスターで Zeppelin Notebook を使用する

HDInsight の Spark クラスターには、Spark ジョブの実行に使用できる Zeppelin Notebook が含まれています。 この記事では、HDInsight クラスターで Zeppelin Notebook を使用する方法について説明します。

> [!NOTE]
> 既定では、Zeppelin Notebook は、HDInsight クラスター バージョン 3.5 での Spark 1.6.2 に対してのみ使用できます。 他のバージョンの HDInsight Spark クラスターで Zeppelin を使用する場合は、スクリプト アクションを使って Zeppelin をインストールできます。 詳細については、[HDInsight Linux の Apache Spark クラスターに Zeppelin Notebook をインストールする](hdinsight-apache-spark-use-zeppelin-notebook.md)に関する記事をご覧ください。
> 
>

**前提条件:**

* Azure サブスクリプション。 [Azure 無料試用版の取得](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。
* HDInsight での Apache Spark クラスター。 手順については、 [Azure HDInsight での Apache Spark クラスターの作成](hdinsight-apache-spark-jupyter-spark-sql.md)に関するページを参照してください。

## <a name="launch-a-zeppelin-notebook"></a>Zeppelin Notebook を起動する
1. Spark クラスター ブレードから **[クラスター ダッシュボード]** をクリックし、**[Zeppelin Notebook]** をクリックします。 入力を求められたら、クラスターの管理者資格情報を入力します。
   
   > [!NOTE]
   > ブラウザーで次の URL を開き、クラスターの Zeppelin Notebook にアクセスすることもできます。 **CLUSTERNAME** をクラスターの名前に置き換えます。
   > 
   > `https://CLUSTERNAME.azurehdinsight.net/zeppelin`
   > 
   > 
2. 新しい Notebook を作成します。 ヘッダー ウィンドウで **[Notebook]** をクリックし、**[Note の新規作成]** をクリックします。
   
    ![新しい Zeppelin Notebook を作成する](./media/hdinsight-apache-spark-zeppelin-notebook/hdispark.createnewnote.png "新しい Zeppelin Notebook を作成する")
   
    Notebook の名前を入力して、**[Note の作成]** をクリックします。
3. また、Notebook のヘッダーに [接続] というステータスが表示されることを確認します。 これは、右上隅の緑色の点で示されます。
   
    ![Zeppelin Notebook のステータス](./media/hdinsight-apache-spark-zeppelin-notebook/hdispark.newnote.connected.png "Zeppelin Notebook のステータス")
4. サンプル データを一時テーブルに読み込みます。 HDInsight の Spark クラスターを作成すると、サンプル データ ファイル **hvac.csv** が、関連するストレージ アカウントの **\HdiSamples\SensorSampleData\hvac** にコピーされます。
   
    新しい Notebook に既定で作成される空の段落に、次のスニペットを貼り付けます。
   
        %livy.spark
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
   
    **Shift + Enter** キーを押すか、段落の **[プレイ]** ボタンをクリックして、スニペットを実行します。 段落の右上隅にあるステータスが、[準備完了]、[保留中]、[実行中]、[完了] の順に進行します。 出力が同じ段落の下に表示されます。 スクリーンショットは次のようになります。
   
    ![生データから一時テーブルを作成する](./media/hdinsight-apache-spark-zeppelin-notebook/hdispark.note.loaddDataintotable.png "生データから一時テーブルを作成する")
   
    各段落にタイトルを指定することもできます。 右上隅の **[設定]** アイコンをクリックし、**[タイトルの表示]** をクリックします。
5. **hvac** テーブルに対して Spark SQL ステートメントを実行できます。 次のクエリを新しい段落に貼り付けます。 このクエリは、ビル ID と、特定の日の各ビルの目標温度と実温度の差を取得します。 **Shift + Enter**キーを押します。
   
        %sql
        select buildingID, (targettemp - actualtemp) as temp_diff, date from hvac where date = "6/1/13" 
   
    先頭にある **%sql** ステートメントは、Livy Scala インタープリターを使用するように Notebook に指示します。
   
    次のスクリーンショットでは出力を示します。
   
    ![Notebook を使用して Spark SQL ステートメントを実行する](./media/hdinsight-apache-spark-zeppelin-notebook/hdispark.note.sparksqlquery1.png "Notebook を使用して Spark SQL ステートメントを実行する")
   
     表示オプション (四角で囲ってある部分) をクリックして、同じ出力の異なる表現に切り替えることができます。 **[設定]** をクリックして、出力のキーと値の構成を選択します。 上記の画面キャプチャでは、**buildingID** をキーとして使用し、**temp_diff** の平均を値として使用しています。
6. クエリの変数を使用して Spark SQL ステートメントを実行することもできます。 次のスニペットでは、クエリで変数 **Temp**と照会できる値を定義する方法を示します。 初めてクエリを実行すると、変数に指定した値がドロップダウンに自動的に設定されます。
   
        %sql
        select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff from hvac where targettemp > "${Temp = 65,65|75|85}" 
   
    このスニペットを新しい段落に貼り付けて、 **Shift + Enter**キーを押します。 次のスクリーンショットでは出力を示します。
   
    ![Notebook を使用して Spark SQL ステートメントを実行する](./media/hdinsight-apache-spark-zeppelin-notebook/hdispark.note.sparksqlquery2.png "Notebook を使用して Spark SQL ステートメントを実行する")
   
    後続のクエリでは、ドロップダウンから新しい値を選択し、クエリを再実行できます。 **[設定]** をクリックして、出力のキーと値の構成を選択します。 上の画面キャプチャでは、**buildingID** をキーとして、**temp_diff** の平均を値として、**targettemp** をグループとして使用しています。
7. Livy インタープリターを再起動して、アプリケーションを終了します。 再起動するには、右上隅のログインしている [ユーザー名] をクリックしてインタープリターの [設定] を開き、**[インタープリター]** をクリックします。
   
    ![インタープリターを起動する](./media/hdinsight-apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Hive の出力")
8. Livy インタープリターの [設定] までスクロールし、**[再起動]** をクリックします。
   
    ![Livy インタープリターを再起動する](./media/hdinsight-apache-spark-zeppelin-notebook/hdispark.zeppelin.restart.interpreter.png "Zeppelin インタープリターを再起動します")

## <a name="how-do-i-use-external-packages-with-the-notebook"></a>Notebook で外部のパッケージを使用する方法
HDInsight (Linux) 上の Apache Spark クラスターに標準では搭載されていない外部のコミュニティから提供されているパッケージを使用するようにクラスター内の Zeppelin Notebook を構成することができます。 利用できるすべてのパッケージは、 [Maven リポジトリ](http://search.maven.org/) で検索できます。 公開されているパッケージの一覧を他のソースから入手してもかまいません。 たとえば、コミュニティから提供されている全パッケージの一覧を [Spark Packages](http://spark-packages.org/)で入手できます。

この記事では、Jupyter Notebook で [spark-csv](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) パッケージを使用する方法について説明します。

1. インタープリターの [設定] を開きます。 右上隅のログインしている [ユーザー名] をクリックし、**[インタープリター]** をクリックします。
   
    ![インタープリターを起動する](./media/hdinsight-apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Hive の出力")
2. Livy インタープリターの [設定] までスクロールし、**[編集]** をクリックします。
   
    ![インタープリターの設定を変更する](./media/hdinsight-apache-spark-zeppelin-notebook/zeppelin-use-external-package-1.png "インタープリターの設定を変更する")
3. **livy.spark.jars.packages** という新しいキーを追加し、`group:id:version` 形式で値を設定します。 したがって、[spark-csv](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) パッケージを使用する場合は、キーの値を `com.databricks:spark-csv_2.10:1.4.0` に設定する必要があります。
   
    ![インタープリターの設定を変更する](./media/hdinsight-apache-spark-zeppelin-notebook/zeppelin-use-external-package-2.png "インタープリターの設定を変更する")
   
    **[保存]** をクリックし、Livy インタープリターを再起動します。
4. **ヒント**: 上記で入力したキーの値に到達する方法を理解する場合は、次のようにします。
   
    a. Maven リポジトリから目的のパッケージを探します。 このチュートリアルでは [spark-csv](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) を使用しました。
   
    b. リポジトリで **GroupId**、**ArtifactId**、**Version** の値を確認します。
   
    ![Jupyter Notebook で外部パッケージを使用する](./media/hdinsight-apache-spark-zeppelin-notebook/use-external-packages-with-jupyter.png "Jupyter Notebook で外部パッケージを使用する")
   
    c. 3 つの値をコロン (**:**) で区切って連結します。
   
        com.databricks:spark-csv_2.10:1.4.0

## <a name="where-are-the-zeppelin-notebooks-saved"></a>Zeppelin Notebook の保存場所
Zeppelin Notebook は、クラスターのヘッドノードに保存されます。 そのため、クラスターを削除すると、Notebook も同様に削除されます。 後で別のクラスターで使用するように Notebook を保存する場合は、ジョブの実行を完了した後に Notebook をエクスポートする必要があります。 Notebook をエクスポートするには、下の図に示すように **[エクスポート]** アイコンをクリックします。

![Notebook のダウンロード](./media/hdinsight-apache-spark-zeppelin-notebook/zeppelin-download-notebook.png "Notebook のダウンロード")

これにより、Notebook は JSON ファイルとしてダウンロード先に保存されます。

## <a name="livy-session-management"></a>Livy セッションを管理する
コードの最初の段落を Zeppelin Notebook で実行すると、HDInsight Spark クラスターに新しい Livy セッションが作成されます。 このセッションは、後に作成するすべての Zeppelin Notebooks 間で共有されます。 何らかの理由 (クラスターの再起動など) で Livy セッションが強制終了すると、Zeppelin Notebook からジョブを実行できなくなります。

このような場合は、Zeppelin Notebook からジョブの実行を開始する前に、次の手順を実行する必要があります。 

1. Zeppelin Notebook から Livy インタープリターを再起動します。 再起動するには、右上隅のログインしている [ユーザー名] をクリックしてインタープリターの [設定] を開き、**[インタープリター]** をクリックします。
   
    ![インタープリターを起動する](./media/hdinsight-apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Hive の出力")
2. Livy インタープリターの [設定] までスクロールし、**[再起動]** をクリックします。
   
    ![Livy インタープリターを再起動する](./media/hdinsight-apache-spark-zeppelin-notebook/hdispark.zeppelin.restart.interpreter.png "Zeppelin インタープリターを再起動します")
3. 既存の Zeppelin Notebook からコードのセルを実行します。 これにより、HDInsight クラスター内に新しい Livy セッションが作成されます。

## <a name="seealso"></a>関連項目
* [概要: Azure HDInsight での Apache Spark](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>シナリオ
* [Spark と BI: HDInsight と BI ツールで Spark を使用した対話型データ分析の実行](hdinsight-apache-spark-use-bi-tools.md)
* [Spark と Machine Learning: HDInsight で Spark を使用して HVAC データを基に建物の温度を分析する](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Spark と Machine Learning: HDInsight で Spark を使用して食品の検査結果を予測する](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Spark ストリーミング: リアルタイム ストリーミング アプリケーションを作成するための HDInsight での Spark の使用](hdinsight-apache-spark-eventhub-streaming.md)
* [Website log analysis using Spark in HDInsight (HDInsight での Spark を使用した Web サイト ログ分析)](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>アプリケーションの作成と実行
* [Scala を使用してスタンドアロン アプリケーションを作成する](hdinsight-apache-spark-create-standalone-application.md)
* [Livy を使用して Spark クラスターでジョブをリモートで実行する](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>ツールと拡張機能
* [Use HDInsight Tools Plugin for IntelliJ IDEA to create and submit Spark Scala applicatons (Linux)](hdinsight-apache-spark-intellij-tool-plugin.md)
* [IntelliJ IDEA 用の HDInsight Tools プラグインを使用して Spark アプリケーションをリモートでデバッグする](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [HDInsight 用の Spark クラスターの Jupyter Notebook で使用可能なカーネル](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Jupyter Notebook で外部のパッケージを使用する](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Jupyter をコンピューターにインストールして HDInsight Spark クラスターに接続する](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>リソースの管理
* [Azure HDInsight での Apache Spark クラスターのリソースの管理](hdinsight-apache-spark-resource-manager.md)
* [HDInsight の Apache Spark クラスターで実行されるジョブの追跡とデバッグ](hdinsight-apache-spark-job-debugging.md)

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: storage-create-storage-account.md 








