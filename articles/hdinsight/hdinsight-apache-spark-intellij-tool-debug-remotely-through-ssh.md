---
title: "Azure Toolkit for IntelliJ: SSH を使用して Spark アプリケーションをリモートでデバッグする | Microsoft Docs"
description: "Azure Toolkit for IntelliJ のHDInsight ツールを使用し、HDInsight クラスターで SSH によりアプリケーションをリモート デバッグする方法の手順を示すガイダンス"
keywords: "デバッグ、intellij のリモート デバッグ、ssh、intellij、hdinsight、intellij のデバッグ、デバッグ"
services: hdinsight
documentationcenter: 
author: jejiang
manager: DJ
editor: Jenny Jiang
tags: azure-portal
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: 
ms.devlang: 
ms.topic: article
ms.date: 08/24/2017
ms.author: Jenny Jiang
ms.translationtype: HT
ms.sourcegitcommit: 7456da29aa07372156f2b9c08ab83626dab7cc45
ms.openlocfilehash: 19053e31d6eb097bc91a04ef9c6af5772aaa16da
ms.contentlocale: ja-jp
ms.lasthandoff: 08/28/2017

---
# <a name="debug-spark-applications-on-an-hdinsight-cluster-with-azure-toolkit-for-intellij-through-ssh"></a>Azure Toolkit for IntelliJ を使用して HDInsight クラスター上で SSH により Spark アプリケーションをデバッグする

この記事では、Azure Toolkit for IntelliJ の HDInsight ツールを使用して HDInsight クラスター上でアプリケーションをリモート デバッグするための詳細な手順を紹介します。 プロジェクトをデバッグするために、「[Debug HDInsight Spark applications with Azure Toolkit for IntelliJ (Azure Toolkit for IntelliJ を使用して HDInsight Spark アプリケーションをデバッグする)](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)」ビデオを参照することもできます。

**前提条件**

* **Azure Toolkit for IntelliJ のHDInsight ツール**。 このツールは Azure Toolkit for IntelliJ に付属しています。 詳細については、「[Azure Toolkit for IntelliJ のインストール](https://docs.microsoft.com/en-us/azure/azure-toolkit-for-intellij-installation)」を参照してください。
* **Azure Toolkit for IntelliJ**。 このツールキットは、HDInsight クラスター向けの Spark アプリケーションの作成に使用します。 詳細については、「[Azure Toolkit for IntelliJ を使用して HDInsight クラスター向けの Spark アプリケーションを作成する](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-apache-spark-intellij-tool-plugin)」を参照してください。
* **ユーザー名とパスワードを使って管理された HDInsight SSH サービス**。 詳細については、「[SSH を使用して HDInsight (Hadoop) に接続する](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix)」と「[SSH トンネリングを使用して Ambari Web UI、JobHistory、NameNode、Oozie、およびその他の Web UI にアクセスする](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-linux-ambari-ssh-tunnel)」を参照してください。 
 

## <a name="create-a-spark-scala-application-and-configure-it-for-remote-debugging"></a>Spark Scala アプリケーションを作成し、リモート デバッグ用に構成する

1. IntelliJ IDEA を起動し、プロジェクトを作成します。 **[新しいプロジェクト]** ダイアログ ボックスで、次の操作を行います。

   a. **[HDInsight]** を選択します。 

   b. 設定に基づいて Java または Scala テンプレートを選択します。 以下のオプションから選択してください。

      - **HDInsight の Spark (Scala)**

      - **HDInsight の Spark (Java)**

      - **HDInsight の Spark のクラスター実行サンプル (Scala)**

      この例では、**[HDInsight の Spark のクラスター実行サンプル (Scala)]** テンプレートを使用します。

   c. **[Build tool]\(ビルド ツール\)** ボックスの一覧で、ニーズに応じて次のいずれかを選択します。

      - **Maven**: Scala プロジェクト作成ウィザードをサポートする場合

      -  **SBT**: 依存関係を管理し、Scala プロジェクトをビルドする場合 

      ![デバッグ プロジェクトを作成します。](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-create-projectfor-debug-remotely.png)

   d. **[次へ]**を選択します。     
 
3. 次の **[新しいプロジェクト]** ウィンドウで、以下の手順を実行します。

   ![Spark SDK を選択する](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-new-project.png)

   a. プロジェクト名とプロジェクトの場所を入力します。

   b. **[Project SDK]\(プロジェクトのSDK\)** ボックスの一覧で、**Spark 2.x** クラスターの場合は **Java 1.8** を選択し、**Spark 1.x** クラスターの場合は **Java 1.7** を選択します。

   c. **[Spark version]\(Spark のバージョン\)** ボックスの一覧には、Spark SDK と Scala SDK の適切なバージョンが組み合わされて表示されます。 Spark クラスターのバージョンが 2.0 より前の場合は、**Spark 1.x** を選択します。 それ以外の場合は、**Spark 2.x** を選択します。 この例では、**Spark 2.0.2 (Scala 2.11.8)** を使用します。

   d. **[完了]** を選択します。

4. **[src]** > **[main]** > **[scala]** を選択してプロジェクトのコードを開きます。 この例では **SparkCore_wasbloTest** スクリプトを使用します。

5. **[構成の編集]** メニューにアクセスするには、右上のアイコンを選択します。 このメニューでは、リモート デバッグの構成を作成または編集できます。

   ![構成の編集](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-edit-configurations.png) 

6. **[実行/デバッグ構成]** ダイアログ ボックスで、プラス記号 (**+**) を選択します。 次に、**[Submit Spark Job]\(Spark ジョブの送信\)** を選択します。

   ![新しい構成を追加する](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-add-new-Configuration.png)
7. **[名前]**、**[Spark cluster]\(Spark クラスター\)**、**[Main class name]\(メイン クラス名\)** に情報を入力します。 次に、**[詳細構成]** を選択します。 

   ![デバッグ構成の実行](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-run-debug-configurations.png)

8. **[Spark Submission Advanced Configuration]\(Spark 送信の詳細構成\)** ダイアログ ボックスで、**[Enable Spark remote debug]\(Spark のリモート デバッグを有効化\)** を選択します。 SSH ユーザー名を入力し、次にパスワードを入力するか、秘密キー ファイルを使用します。 構成を保存するには、**[OK]** をクリックします。

   ![[Enable Spark remote debug]\(Spark のリモート デバッグを有効化\)](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-enable-spark-remote-debug.png)

9. 指定した名前で構成が保存されます。 構成の詳細を表示するには、構成名を選択します。 変更するには、**[構成の編集]** を選択します。 

10. 構成の設定が完了したら、リモート クラスターでプロジェクトを実行したり、リモート デバッグを実行したりすることができます。

## <a name="learn-how-to-perform-remote-debugging"></a>リモート デバッグの実行方法
### <a name="scenario-1-perform-remote-run"></a>シナリオ 1: リモートで実行する

このセクションでは、ドライバーと Executor のデバッグ方法について説明します。

    import org.apache.spark.{SparkConf, SparkContext}

    object LogQuery {
      val exampleApacheLogs = List(
        """10.10.10.10 - "FRED" [18/Jan/2013:17:56:07 +1100] "GET http://images.com/2013/Generic.jpg
          | HTTP/1.1" 304 315 "http://referall.com/" "Mozilla/4.0 (compatible; MSIE 7.0; Windows NT 5.1;
          | GTB7.4; .NET CLR 2.0.50727; .NET CLR 3.0.04506.30; .NET CLR 3.0.04506.648; .NET CLR
          | 3.5.21022; .NET CLR 3.0.4506.2152; .NET CLR 1.0.3705; .NET CLR 1.1.4322; .NET CLR
          | 3.5.30729; Release=ARP)" "UD-1" - "image/jpeg" "whatever" 0.350 "-" - "" 265 923 934 ""
          | 62.24.11.25 images.com 1358492167 - Whatup""".stripMargin.lines.mkString,
        """10.10.10.10 - "FRED" [18/Jan/2013:18:02:37 +1100] "GET http://images.com/2013/Generic.jpg
          | HTTP/1.1" 304 306 "http:/referall.com" "Mozilla/4.0 (compatible; MSIE 7.0; Windows NT 5.1;
          | GTB7.4; .NET CLR 2.0.50727; .NET CLR 3.0.04506.30; .NET CLR 3.0.04506.648; .NET CLR
          | 3.5.21022; .NET CLR 3.0.4506.2152; .NET CLR 1.0.3705; .NET CLR 1.1.4322; .NET CLR
          | 3.5.30729; Release=ARP)" "UD-1" - "image/jpeg" "whatever" 0.352 "-" - "" 256 977 988 ""
          | 0 73.23.2.15 images.com 1358492557 - Whatup""".stripMargin.lines.mkString
      )
      def main(args: Array[String]) {
        val sparkconf = new SparkConf().setAppName("Log Query")
        val sc = new SparkContext(sparkconf)
        val dataSet = sc.parallelize(exampleApacheLogs)
        // scalastyle:off
        val apacheLogRegex =
          """^([\d.]+) (\S+) (\S+) \[([\w\d:/]+\s[+\-]\d{4})\] "(.+?)" (\d{3}) ([\d\-]+) "([^"]+)" "([^"]+)".*""".r
        // scalastyle:on
        /** Tracks the total query count and number of aggregate bytes for a particular group. */
        class Stats(val count: Int, val numBytes: Int) extends Serializable {
          def merge(other: Stats): Stats = new Stats(count + other.count, numBytes + other.numBytes)
          override def toString: String = "bytes=%s\tn=%s".format(numBytes, count)
        }
        def extractKey(line: String): (String, String, String) = {
          apacheLogRegex.findFirstIn(line) match {
            case Some(apacheLogRegex(ip, _, user, dateTime, query, status, bytes, referer, ua)) =>
              if (user != "\"-\"") (ip, user, query)
              else (null, null, null)
            case _ => (null, null, null)
          }
        }
        def extractStats(line: String): Stats = {
          apacheLogRegex.findFirstIn(line) match {
            case Some(apacheLogRegex(ip, _, user, dateTime, query, status, bytes, referer, ua)) =>
              new Stats(1, bytes.toInt)
            case _ => new Stats(1, 0)
          }
        }
        
        dataSet.map(line => (extractKey(line), extractStats(line)))
          .reduceByKey((a, b) => a.merge(b))
          .collect().foreach{
          case (user, query) => println("%s\t%s".format(user, query))}

        sc.stop()
      }
    }


1. ブレークポイントを設定し、**[デバッグ]** アイコンを選択します。

   ![デバッグ アイコンを選択する](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-debug-icon.png)

2. プログラムの実行がブレークポイントに達すると、**[ドライバー]** タブと 2 つの **[Executor]** タブが **[デバッガー]** ウィンドウに表示されます。 **[Resume Program]\(プログラムの再開\)** アイコンを選択してコードの実行を続けます。次のブレークポイントに達すると、対応する **[Executor]** タブにフォーカスが置かれます。対応する **[Console]\(コンソール\)** タブで実行ログをレビューできます。

   ![[デバッグ] タブ](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-debugger-tab.png)

### <a name="scenario-2-perform-remote-debugging-and-bug-fixing"></a>シナリオ 2: リモート デバッグを実行し、バグを修正する
このセクションでは、IntelliJ の簡易修正用デバッグ機能を使って、変数の値を動的に更新する方法を説明します。 次のコード例では、ターゲット ファイルが既に存在するため、例外が発生します。
  
        import org.apache.spark.SparkConf
        import org.apache.spark.SparkContext

        object SparkCore_WasbIOTest {
          def main(arg: Array[String]): Unit = {
            val conf = new SparkConf().setAppName("SparkCore_WasbIOTest")
            val sc = new SparkContext(conf)
            val rdd = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

            // Find the rows that have only one digit in the sixth column.
            val rdd1 = rdd.filter(s => s.split(",")(6).length() == 1)

            try {
              var target = "wasb:///HVACout2_testdebug1";
              rdd1.saveAsTextFile(target);
            } catch {
              case ex: Exception => {
                throw ex;
              }
            }
          }
        }


#### <a name="to-perform-remote-debugging-and-bug-fixing"></a>リモート デバッグを実行してバグを修正するには
1. 2 つのブレークポイントを設定し、**[デバッグ]** アイコンを選択して、リモート デバッグ プロセスを開始します。

2. 最初のブレークポイントでコードが停止し、**[変数]** ウィンドウにパラメーターと変数の情報が表示されます。 

3. **[Resume Program]\(プログラムの再開\)** アイコンを選択して続行します。 2 番めのブレーク ポイントでコードが停止します。 例外が想定どおりにキャッチされます。

  ![エラーのスロー](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-throw-error.png) 

4. もう一度 **[Resume Program]\(プログラムの再開\)** アイコンを選択します。 **[HDInsight Spark Submission]\(HDInsight Spark の送信\)** ウィンドウに、"ジョブ実行失敗" エラーが表示されます。

  ![エラーの送信](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-error-submission.png) 

5. IntelliJ のデバッグ機能を使って変数の値を動的に更新するには、もう一度 **[デバッグ]** を選択します。 **[変数]** ウィンドウが再度表示されます。 

6. **[デバッグ]** タブでターゲットを右クリックし、**[値の設定]** を選択します。 次に、変数に新しい値を入力します。 **[Enter]\(確定\)** を選択して値を保存します。 

  ![値の設定](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-set-value.png) 

7. **[Resume Program]\(プログラムの再開\)** アイコンを選択して、プログラムの実行を続けます。 今回は例外はキャッチされません。 プロジェクトは例外なしに正常に実行されていることが表示されます。

  ![デバッグで例外が発生しない場合](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-debug-without-exception.png)

## <a name="seealso"></a>次のステップ
* [概要: Azure HDInsight での Apache Spark](hdinsight-apache-spark-overview.md)

### <a name="demo"></a>デモ
* Scala プロジェクトの作成 (ビデオ): [Spark Scala アプリケーションの作成](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* リモート デバッグ (ビデオ): [Azure Toolkit for IntelliJ を使用して HDInsight クラスター上で Spark アプリケーションをリモートでデバッグする](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>シナリオ
* [Spark と BI: HDInsight で BI ツールと Spark を使用して対話型データ分析を実行する](hdinsight-apache-spark-use-bi-tools.md)
* [Spark と Machine Learning: HDInsight で Spark を使用して HVAC データを基に建物の温度を分析する](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Spark with Machine Learning: Use Spark in HDInsight to predict food inspection results (Spark と Machine Learning: HDInsight で Spark を使用して食品の検査結果を予測する)](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Spark ストリーミング: HDInsight で Spark を使用してリアルタイム ストリーミング アプリケーションを作成する](hdinsight-apache-spark-eventhub-streaming.md)
* [Website log analysis using Spark in HDInsight (HDInsight での Spark を使用した Web サイト ログ分析)](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>アプリケーションの作成と実行
* [Scala を使用してスタンドアロン アプリケーションを作成する](hdinsight-apache-spark-create-standalone-application.md)
* [Livy を使用して Spark クラスターでジョブをリモートで実行する](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>ツールと拡張機能
* [Azure Toolkit for IntelliJ を使用して HDInsight クラスター向けの Spark アプリケーションを作成する](hdinsight-apache-spark-intellij-tool-plugin.md)
* [Azure Toolkit for IntelliJ を使用して VPN を介して Spark アプリケーションをリモートでデバッグする](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Hortonworks Sandbox と IntelliJ 用 HDInsight ツールを使用する](hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Azure Toolkit for Eclipse の HDInsight ツールを使用して Spark アプリケーションを作成する](hdinsight-apache-spark-eclipse-tool-plugin.md)
* [HDInsight の Spark クラスターで Zeppelin Notebook を使用する](hdinsight-apache-spark-zeppelin-notebook.md)
* [HDInsight の Spark クラスターの Jupyter Notebook で使用可能なカーネル](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Jupyter Notebook で外部のパッケージを使用する](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Jupyter をコンピューターにインストールして HDInsight Spark クラスターに接続する](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>リソースの管理
* [Azure HDInsight での Apache Spark クラスターのリソースの管理](hdinsight-apache-spark-resource-manager.md)
* [HDInsight の Apache Spark クラスターで実行されるジョブの追跡とデバッグ](hdinsight-apache-spark-job-debugging.md)

