---
title: 'Azure Toolkit for IntelliJ:SSH を使用した Spark アプリケーションのリモートでのデバッグ '
description: Azure Toolkit for IntelliJ のHDInsight ツールを使用し、HDInsight クラスターで SSH によりアプリケーションをリモート デバッグする方法の手順を示すガイダンス
keywords: デバッグ、intellij のリモート デバッグ、ssh、intellij、hdinsight、intellij のデバッグ、デバッグ
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 11/25/2017
ms.openlocfilehash: 0bca6c16124f886d9df9e88e651f3f7450f51a1a
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "67876368"
---
# <a name="debug-apache-spark-applications-locally-or-remotely-on-an-hdinsight-cluster-with-azure-toolkit-for-intellij-through-ssh"></a>ローカルまたはリモートから SSH 経由で Azure Toolkit for IntelliJ を使用して HDInsight クラスター上の Apache Spark アプリケーションをデバッグする

この記事では、[Azure Toolkit for IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij?view=azure-java-stable) の HDInsight Tools を使用して HDInsight クラスターでアプリケーションをリモートでデバッグする方法に関する詳細な手順について説明します。 プロジェクトをデバッグするために、「[Debug HDInsight Spark applications with Azure Toolkit for IntelliJ (Azure Toolkit for IntelliJ を使用して HDInsight Spark アプリケーションをデバッグする)](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)」ビデオを参照することもできます。

**前提条件**
* **Azure Toolkit for IntelliJ のHDInsight ツール**。 このツールは Azure Toolkit for IntelliJ に付属しています。 詳細については、「[Azure Toolkit for IntelliJ のインストール](https://docs.microsoft.com/azure/azure-toolkit-for-intellij-installation)」を参照してください。 **Azure Toolkit for IntelliJ**。 このツールキットは、HDInsight クラスター向けの Apache Spark アプリケーションを作成するために使用します。 詳細については、「[Azure Toolkit for IntelliJ を使用して HDInsight クラスター向けの Apache Spark アプリケーションを作成する](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-intellij-tool-plugin)」の手順に従ってください。

* **ユーザー名とパスワードを使って管理された HDInsight SSH サービス**。 詳細については、「[SSH を使用して HDInsight (Apache Hadoop) に接続する](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix)」および「[SSH トンネリングを使用して Ambari Web UI、JobHistory、NameNode、Apache Oozie、およびその他の Web UI にアクセスする](https://docs.microsoft.com/azure/hdinsight/hdinsight-linux-ambari-ssh-tunnel)」を参照してください。 
 
## <a name="learn-how-to-perform-local-run-and-debugging"></a>ローカルで実行してデバッグする方法
### <a name="scenario-1-create-a-spark-scala-application"></a>シナリオ 1:Spark Scala アプリケーションを作成する 

1. IntelliJ IDEA を起動し、プロジェクトを作成します。 **[新しいプロジェクト]** ダイアログ ボックスで、次の操作を行います。

   a. **[Azure Spark/HDInsight]** を選択します。 

   b. 設定に基づいて Java または Scala テンプレートを選択します。 以下のオプションから選択してください。

   - **Spark プロジェクト (Java)**

   - **Spark プロジェクト (Scala)**

   - **サンプルありの Spark プロジェクト (Scala)**

   - **エラー タスク デバッグ サンプルありの Spark プロジェクト (プレビュー) (Scala)**

     この例では、**サンプルありの Spark プロジェクト (Scala)** テンプレートを使用します。

   c. **[Build tool]\(ビルド ツール\)** ボックスの一覧で、ニーズに応じて次のいずれかを選択します。

   - **Maven**: Scala プロジェクト作成ウィザードをサポートする場合

   - **SBT**: 依存関係を管理し、Scala プロジェクトをビルドする場合 

     ![デバッグ プロジェクトを作成します。](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-create-projectfor-debug-remotely.png)

   d. **[次へ]** を選択します。     
 
1. 次の **[新しいプロジェクト]** ウィンドウで、以下の手順を実行します。

   ![Spark SDK を選択する](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-new-project.png)

   a. プロジェクト名とプロジェクトの場所を入力します。

   b. **[Project SDK]\(プロジェクトのSDK\)** ボックスの一覧で、**Spark 2.x** クラスターの場合は **Java 1.8** を選択し、**Spark 1.x** クラスターの場合は **Java 1.7** を選択します。

   c. **[Spark version]\(Spark のバージョン\)** ボックスの一覧には、Spark SDK と Scala SDK の適切なバージョンが組み合わされて表示されます。 Spark クラスターのバージョンが 2.0 より前の場合は、**Spark 1.x** を選択します。 それ以外の場合は、**Spark 2.x** を選択します。 この例では、**Spark 2.0.2 (Scala 2.11.8)** を使用します。

   d. **[完了]** を選択します。

1. **[src]**  >  **[main]**  >  **[scala]** を選択してプロジェクトのコードを開きます。 この例では **SparkCore_wasbloTest** スクリプトを使用します。

### <a name="prerequisite-for-windows"></a>Windows の前提条件
Windows コンピューターでローカルの Spark Scala アプリケーションを実行中に、[SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356) で説明されている例外が発生する場合があります。 この例外は、Windows 上に WinUtils.exe がないことが原因で発生します。 

このエラーを回避するには、[実行可能ファイルをダウンロード](https://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe)して、**C:\WinUtils\bin** などの場所に保存します。 次に、環境変数 **HADOOP_HOME** を追加し、この変数の値を **C:\WinUtils** に設定します。

### <a name="scenario-2-perform-local-run"></a>シナリオ 2:ローカルで実行する
1. **SparkCore_wasbloTest** スクリプトを開き、スクリプト エディターを右クリックし、 **[Run '[Spark Job]XXX']\('[Spark Job]XXX' の実行\)** オプションを選択してローカルで実行します。
1. ローカル実行が完了すると、現在のプロジェクト エクスプローラーの **[data]**  >  ** __[default]__** に出力ファイルが保存されていることを確認できます。

    ![ローカル実行の結果](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/local-run-result.png)
1. ローカル実行とローカル デバッグを行うと、既定のローカル実行構成が自動的に設定されます。 右上済の **[Spark on HDInsight] XXX** という構成を開くと、 **[HDInsight の Apache Spark]** の下に **[Spark on HDInsight]XXX** が既に作成されていることがわかります。 **[Locally Run]\(ローカル実行\)** タブに切り替えます。

    ![ローカル実行構成](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/local-run-configuration.png)
    - [環境変数](#prerequisite-for-windows):システム環境変数 **HADOOP_HOME** を **C:\WinUtils** に設定した場合は、自動的に検出されます。手動で追加する必要はありません。
    - [[WinUtils.exe Location]\(WinUtils.exe の場所\)](#prerequisite-for-windows):システム環境変数が未設定である場合、対応するボタンをクリックして場所を探すことができます。
    - 2 つのオプションのどちらかを選択するだけです。これらは MacOS と Linux では必要ありません。
1. ローカル実行とローカル デバッグの前に構成を手動で設定することもできます。 先ほどのスクリーンショットの正符号 ( **+** ) を選択します。 次に **[HDInsight での Apache Spark]** オプションを選択します。 **[Name]\(名前\)** と **[Main class name]\(メイン クラス名\)** の情報を入力して保存し、ローカル実行ボタンをクリックします。

### <a name="scenario-3-perform-local-debugging"></a>シナリオ 3:ローカル デバッグを実行する
1. **SparkCore_wasbloTest** スクリプトを開いてブレークポイントを設定します。
1. スクリプト エディターを右クリックし、 **[Debug '[Spark on HDInsight]XXX']\('[Spark on HDInsight]XXX' のデバッグ\)** オプションを選択してローカル デバッグを実行します。   



## <a name="learn-how-to-perform-remote-run-and-debugging"></a>リモートで実行してデバッグする方法
### <a name="scenario-1-perform-remote-run"></a>シナリオ 1:リモートで実行する

1. **[構成の編集]** メニューにアクセスするには、右上のアイコンを選択します。 このメニューでは、リモート デバッグの構成を作成または編集できます。

   ![構成の編集](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-edit-configurations.png) 

1. **[実行/デバッグ構成]** ダイアログ ボックスで、プラス記号 ( **+** ) を選択します。 次に **[HDInsight での Apache Spark]** オプションを選択します。

   ![新しい構成を追加する](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-add-new-Configuration.png)
1. **[Remotely Run in Cluster]\(クラスターでリモート実行\)** タブに切り替えます。 **[名前]** 、 **[Spark cluster]\(Spark クラスター\)** 、 **[Main class name]\(メイン クラス名\)** に情報を入力します。 **[詳細な構成 (リモート デバッグ)]** をクリックします。 ツールでは、**Executor** を使用したデバッグがサポートされています。 **numExectors** の既定値は 5 です。 3 より大きい値に設定することはお勧めできません。

   ![デバッグ構成の実行](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-run-debug-configurations.png)

1. **[詳細な構成 (リモート デバッグ)]** パートで、 **[Enable Spark remote debug]\(Spark のリモート デバッグを有効化\)** を選択します。 SSH ユーザー名を入力し、次にパスワードを入力するか、秘密キー ファイルを使用します。 リモート デバッグを実行する場合は、これを設定する必要があります。 リモート実行を使用する場合は、設定する必要はありません。

   ![[Enable Spark remote debug]\(Spark のリモート デバッグを有効化\)](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-enable-spark-remote-debug.png)

1. 指定した名前で構成が保存されます。 構成の詳細を表示するには、構成名を選択します。 変更するには、 **[構成の編集]** を選択します。 

1. 構成の設定が完了したら、リモート クラスターでプロジェクトを実行したり、リモート デバッグを実行したりすることができます。
   
   ![リモート実行ボタン](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/perform-remote-run.png)

1. **[切断]** ボタンをクリックします。送信ログは左側のパネルに表示されません。 ただし、バックエンドで実行が続けられています。

   ![リモート実行ボタン](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/remote-run-result.png)



### <a name="scenario-2-perform-remote-debugging"></a>シナリオ 2:リモート デバッグを実行する
1. ブレークポイントを設定し、 **[Remote debug]\(リモート デバッグ\)** アイコンをクリックします。 リモート送信との違いは、SSH ユーザー名/パスワードの構成が必要である点です。

   ![デバッグ アイコンを選択する](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-icon.png)

1. プログラムの実行がブレークポイントに達すると、 **[ドライバー]** タブと 2 つの **[Executor]** タブが **[デバッガー]** ウィンドウに表示されます。 **[Resume Program]\(プログラムの再開\)** アイコンを選択してコードの実行を続けます。その後、次のブレークポイントに到達します。 デバッグの対象となる Executor を見つけるには、正しい **[Executor]** タブに切り替える必要があります。 対応する **[Console]\(コンソール\)** タブで実行ログを確認できます。

   ![[デバッグ] タブ](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debugger-tab.png)

### <a name="scenario-3-perform-remote-debugging-and-bug-fixing"></a>シナリオ 3:リモート デバッグを実行し、バグを修正するには
1. 2 つのブレークポイントを設定し、 **[デバッグ]** アイコンを選択して、リモート デバッグ プロセスを開始します。

1. 最初のブレークポイントでコードが停止し、 **[変数]** ウィンドウにパラメーターと変数の情報が表示されます。 

1. **[Resume Program]\(プログラムの再開\)** アイコンを選択して続行します。 2 番めのブレーク ポイントでコードが停止します。 例外が想定どおりにキャッチされます。

   ![エラーのスロー](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-throw-error.png) 

1. もう一度 **[Resume Program]\(プログラムの再開\)** アイコンを選択します。 **[HDInsight Spark Submission]\(HDInsight Spark の送信\)** ウィンドウに、"ジョブ実行失敗" エラーが表示されます。

   ![エラーの送信](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-error-submission.png) 

1. IntelliJ のデバッグ機能を使って変数の値を動的に更新するには、もう一度 **[デバッグ]** を選択します。 **[変数]** ウィンドウが再度表示されます。 

1. **[デバッグ]** タブでターゲットを右クリックし、 **[値の設定]** を選択します。 次に、変数に新しい値を入力します。 **[Enter]\(確定\)** を選択して値を保存します。 

   ![値の設定](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-set-value.png) 

1. **[Resume Program]\(プログラムの再開\)** アイコンを選択して、プログラムの実行を続けます。 今回は例外はキャッチされません。 プロジェクトは例外なしに正常に実行されていることが表示されます。

   ![デバッグで例外が発生しない場合](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-without-exception.png)

## <a name="seealso"></a>次のステップ
* [概要:Azure HDInsight での Apache Spark](apache-spark-overview.md)

### <a name="demo"></a>デモ
* Scala プロジェクトを作成する (ビデオ):[Apache Spark Scala アプリケーションを作成する](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* リモート デバッグ (ビデオ):[Azure Toolkit for IntelliJ を使用して HDInsight クラスター上で Apache Spark アプリケーションをリモートでデバッグする](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>シナリオ
* [Apache Spark と BI:HDInsight と BI ツールで Spark を使用して対話型データ分析を実行する](apache-spark-use-bi-tools.md)
* [Apache Spark と Machine Learning:HDInsight で Spark を使用して、HVAC データを使用して建物の温度を分析する](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark と Machine Learning:HDInsight で Spark を使用して食品の検査結果を予測する](apache-spark-machine-learning-mllib-ipython.md)
* [HDInsight 上での Apache Spark を使用した Web サイト ログ分析](../hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>アプリケーションの作成と実行
* [Scala を使用してスタンドアロン アプリケーションを作成する](../hdinsight-apache-spark-create-standalone-application.md)
* [Apache Livy を使用して Apache Spark クラスターでジョブをリモートから実行する](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>ツールと拡張機能
* [Azure Toolkit for IntelliJ を使用して HDInsight クラスター向けの Apache Spark アプリケーションを作成する](apache-spark-intellij-tool-plugin.md)
* [Azure Toolkit for IntelliJ を使用して VPN 経由で Apache Spark アプリケーションをリモートでデバッグする](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Azure Toolkit for Eclipse の HDInsight Tools を使用して Apache Spark アプリケーションを作成する](../hdinsight-apache-spark-eclipse-tool-plugin.md)
* [HDInsight 上の Apache Spark クラスターで Apache Zeppelin Notebook を使用する](apache-spark-zeppelin-notebook.md)
* [HDInsight 用の Apache Spark クラスター内の Jupyter Notebook で使用可能なカーネル](apache-spark-jupyter-notebook-kernels.md)
* [Jupyter Notebook で外部のパッケージを使用する](apache-spark-jupyter-notebook-use-external-packages.md)
* [Jupyter をコンピューターにインストールして HDInsight Spark クラスターに接続する](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>リソースの管理
* [Azure HDInsight での Apache Spark クラスターのリソースの管理](apache-spark-resource-manager.md)
* [HDInsight の Apache Spark クラスターで実行されるジョブの追跡とデバッグ](apache-spark-job-debugging.md)
