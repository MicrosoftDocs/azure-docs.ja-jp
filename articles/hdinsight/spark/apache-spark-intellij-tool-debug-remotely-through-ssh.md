---
title: Azure Toolkit for IntelliJ:SSH を使用して Spark アプリをデバッグする - HDInsight
description: Azure Toolkit for IntelliJ のHDInsight ツールを使用し、HDInsight クラスターで SSH によりアプリケーションをリモート デバッグする方法の手順を示すガイダンス
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 12/23/2019
ms.openlocfilehash: de838e094c8a37d375aa6c7649ee5717705ad33c
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104866354"
---
# <a name="debug-apache-spark-applications-on-an-hdinsight-cluster-with-azure-toolkit-for-intellij-through-ssh"></a>Azure Toolkit for IntelliJ を使用して HDInsight クラスター上で SSH により Apache Spark アプリケーションをデバッグする

この記事では、[Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij) の HDInsight Tools を使用して HDInsight クラスターでアプリケーションをリモートでデバッグする方法に関する詳細な手順について説明します。 プロジェクトをデバッグするために、「[Debug HDInsight Spark applications with Azure Toolkit for IntelliJ (Azure Toolkit for IntelliJ を使用して HDInsight Spark アプリケーションをデバッグする)](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)」ビデオを参照することもできます。

## <a name="prerequisites"></a>前提条件

* HDInsight での Apache Spark クラスター。 [Apache Spark クラスターの作成](../spark/apache-spark-jupyter-spark-sql-use-portal.md)に関するページを参照してください。

* Windows ユーザーの場合:Windows コンピューターでローカルの Spark Scala アプリケーションを実行中に、[SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356) で説明されている例外が発生する場合があります。 この例外は、Windows 上に WinUtils.exe がないことが原因で発生します。

    このエラーを回避するには、[Winutils.exe](https://github.com/steveloughran/winutils) をダウンロードして、**C:\WinUtils\bin** などの場所に保存します。 次に、環境変数 **HADOOP_HOME** を追加し、この変数の値を **C:\WinUtils** に設定します。

* [IntelliJ IDEA](https://www.jetbrains.com/idea/download/#section=windows) (Community エディションは無料です。)

* [Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/installation)。

* [IntelliJ 用の Scala プラグイン](../spark/apache-spark-intellij-tool-plugin.md#install-scala-plugin-for-intellij-idea)。

* SSH クライアント 詳細については、[SSH を使用して HDInsight (Apache Hadoop) に接続する方法](../hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

## <a name="create-a-spark-scala-application"></a>Spark Scala アプリケーションを作成する

1. IntelliJ IDEA を起動し、 **[Create New Project]\(新しいプロジェクトの作成\)** を選択して、 **[New Project]\(新しいプロジェクト\)** ウィンドウを開きます。

1. 左側のウィンドウから **[Apache Spark/HDInsight]** を選択します。

1. メイン ウィンドウから **[サンプルありの Spark プロジェクト (Scala)]** を選択します。

1. **[Build tool]\(ビルド ツール\)** ドロップダウン ボックスの一覧で、次のいずれかを選択します。

    * Scala プロジェクト作成ウィザードをサポートする場合は **Maven**。
    * 依存関係を管理し、Scala プロジェクトをビルドする場合は **SBT**。

     :::image type="content" source="./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-create-projectfor-debug-remotely.png" alt-text="Intellij の新規プロジェクト作成 (Spark)" border="true":::

1. **[次へ]** を選択します。

1. 次の **[新しいプロジェクト]** ウィンドウで、次の情報を指定します。

    |プロパティ |説明 |
    |---|---|
    |プロジェクト名|名前を入力します。 このチュートリアルは `myApp` を使用します。|
    |プロジェクトの場所|プロジェクトを保存する任意の場所を入力します。|
    |Project SDK (プロジェクト SDK)|空白の場合は、 **[新規作成]** を選択し、JDK に移動します。|
    |Spark バージョン|作成ウィザードにより、Spark SDK と Scala SDK の適切なバージョンが統合されます。 Spark クラスターのバージョンが 2.0 より前の場合は、 **[Spark 1.x]** を選択します。 それ以外の場合は、 **[Spark 2.x]** を選択します。 この例では、**Spark 2.3.0 (Scala 2.11.8)** を使用します。|

   :::image type="content" source="./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-new-project.png" alt-text="Intellij の新しいプロジェクト、Spark バージョンの選択" border="true":::

1. **[完了]** を選択します。 プロジェクトが使用可能になるまで数分かかる場合があります。 右下隅の進行状況を監視します。

1. プロジェクトを展開し、**src** > **main** > **scala** > **sample** に移動します。 **[SparkCore_WasbIOTest]** をダブルクリックします。

## <a name="perform-local-run"></a>ローカルで実行する

1. **SparkCore_WasbIOTest** スクリプトから、スクリプト エディターを右クリックし、 **[Run 'SparkCore_WasbIOTest'] ('SparkCore_WasbIOTest' の実行)** オプションを選択してローカルで実行します。

1. ローカル実行が完了すると、現在のプロジェクト エクスプローラーの **[data]**  >  **__[default]__** に出力ファイルが保存されていることを確認できます。

    :::image type="content" source="./media/apache-spark-intellij-tool-debug-remotely-through-ssh/spark-local-run-result.png" alt-text="Intellij プロジェクトのローカル実行の結果" border="true":::

1. ローカル実行とローカル デバッグを行うと、既定のローカル実行構成が自動的に設定されます。 右上済の **[Spark on HDInsight] XXX** という構成を開くと、 **[HDInsight の Apache Spark]** の下に **[Spark on HDInsight]XXX** が既に作成されていることがわかります。 **[Locally Run]\(ローカル実行\)** タブに切り替えます。

    :::image type="content" source="./media/apache-spark-intellij-tool-debug-remotely-through-ssh/local-run-configuration.png" alt-text="Intellij の実行/デバッグ構成 (ローカル実行)" border="true":::

    - [環境変数](#prerequisites):システム環境変数 **HADOOP_HOME** を **C:\WinUtils** に設定した場合は、自動的に検出されます。手動で追加する必要はありません。
    - [[WinUtils.exe Location]\(WinUtils.exe の場所\)](#prerequisites):システム環境変数が未設定である場合、対応するボタンをクリックして場所を探すことができます。
    - 2 つのオプションのどちらかを選択するだけです。これらは MacOS と Linux では必要ありません。

1. ローカル実行とローカル デバッグの前に構成を手動で設定することもできます。 先ほどのスクリーンショットの正符号 ( **+** ) を選択します。 次に **[HDInsight での Apache Spark]** オプションを選択します。 **[Name]\(名前\)** と **[Main class name]\(メイン クラス名\)** の情報を入力して保存し、ローカル実行ボタンをクリックします。

## <a name="perform-local-debugging"></a>ローカル デバッグを実行する

1. **SparkCore_wasbloTest** スクリプトを開いてブレークポイントを設定します。

1. スクリプト エディターを右クリックし、 **[Debug '[Spark on HDInsight]XXX']\('[Spark on HDInsight]XXX' のデバッグ\)** オプションを選択してローカル デバッグを実行します。

## <a name="perform-remote-run"></a>リモートで実行する

1. **[実行]**  >  **[Edit Configurations...] (構成の編集...)** の順に移動します。このメニューでは、リモート デバッグの構成を作成または編集できます。

1. **[実行/デバッグ構成]** ダイアログ ボックスで、プラス記号 ( **+** ) を選択します。 次に **[HDInsight での Apache Spark]** オプションを選択します。

   :::image type="content" source="./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-add-new-Configuration.png" alt-text="Intellij の新規構成の追加" border="true":::

1. **[Remotely Run in Cluster]\(クラスターでリモート実行\)** タブに切り替えます。 **[名前]** 、 **[Spark cluster]\(Spark クラスター\)** 、 **[Main class name]\(メイン クラス名\)** に情報を入力します。 **[詳細な構成 (リモート デバッグ)]** をクリックします。 ツールでは、**Executor** を使用したデバッグがサポートされています。 **numExectors** の既定値は 5 です。 3 より大きい値に設定することはお勧めできません。

   :::image type="content" source="./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-run-debug-configurations.png" alt-text="Intellij の実行/デバッグ構成" border="true":::

1. **[詳細な構成 (リモート デバッグ)]** パートで、 **[Enable Spark remote debug]\(Spark のリモート デバッグを有効化\)** を選択します。 SSH ユーザー名を入力し、次にパスワードを入力するか、秘密キー ファイルを使用します。 リモート デバッグを実行する場合は、これを設定する必要があります。 リモート実行を使用する場合は、設定する必要はありません。

   :::image type="content" source="./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-enable-spark-remote-debug.png" alt-text="Intellij の詳細構成 (Spark のリモート デバッグの有効化)" border="true":::

1. 指定した名前で構成が保存されます。 構成の詳細を表示するには、構成名を選択します。 変更するには、 **[構成の編集]** を選択します。

1. 構成の設定が完了したら、リモート クラスターでプロジェクトを実行したり、リモート デバッグを実行したりすることができます。

   :::image type="content" source="./media/apache-spark-intellij-tool-debug-remotely-through-ssh/perform-remote-run-button.png" alt-text="Intellij のリモート Spark ジョブのデバッグ (リモート実行ボタン)" border="true":::

1. **[切断]** ボタンをクリックします。送信ログは左側のパネルに表示されません。 ただし、バックエンドで実行が続けられています。

   :::image type="content" source="./media/apache-spark-intellij-tool-debug-remotely-through-ssh/spark-remote-run-result.png" alt-text="Intellij のリモート Spark ジョブのデバッグ (リモート実行結果)" border="true":::

## <a name="perform-remote-debugging"></a>リモート デバッグを実行する

1. ブレークポイントを設定し、 **[Remote debug]\(リモート デバッグ\)** アイコンをクリックします。 リモート送信との違いは、SSH ユーザー名/パスワードの構成が必要である点です。

   :::image type="content" source="./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-icon.png" alt-text="Intellij のリモート Spark ジョブのデバッグ (デバッグ アイコン)" border="true":::

1. プログラムの実行がブレークポイントに達すると、 **[ドライバー]** タブと 2 つの **[Executor]** タブが **[デバッガー]** ウィンドウに表示されます。 **[Resume Program]\(プログラムの再開\)** アイコンを選択してコードの実行を続けます。その後、次のブレークポイントに到達します。 デバッグの対象となる Executor を見つけるには、正しい **[Executor]** タブに切り替える必要があります。 対応する **[Console]\(コンソール\)** タブで実行ログを確認できます。

   :::image type="content" source="./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debugger-tab.png" alt-text="Intellij のリモート Spark ジョブのデバッグ (デバッグ タグ)" border="true":::

### <a name="perform-remote-debugging-and-bug-fixing"></a>リモート デバッグを実行し、バグを修正するには

1. 2 つのブレークポイントを設定し、 **[デバッグ]** アイコンを選択して、リモート デバッグ プロセスを開始します。

1. 最初のブレークポイントでコードが停止し、 **[変数]** ウィンドウにパラメーターと変数の情報が表示されます。

1. **[Resume Program]\(プログラムの再開\)** アイコンを選択して続行します。 2 番めのブレーク ポイントでコードが停止します。 例外が想定どおりにキャッチされます。

   :::image type="content" source="./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-throw-error.png" alt-text="Intellij のリモート Spark ジョブのデバッグ (エラーのスロー)" border="true":::

1. もう一度 **[Resume Program]\(プログラムの再開\)** アイコンを選択します。 **[HDInsight Spark Submission]\(HDInsight Spark の送信\)** ウィンドウに、"ジョブ実行失敗" エラーが表示されます。

   :::image type="content" source="./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-error-submission.png" alt-text="Intellij のリモート Spark ジョブのデバッグ (エラーの送信)" border="true":::

1. IntelliJ のデバッグ機能を使って変数の値を動的に更新するには、もう一度 **[デバッグ]** を選択します。 **[変数]** ウィンドウが再度表示されます。

1. **[デバッグ]** タブでターゲットを右クリックし、 **[値の設定]** を選択します。 次に、変数に新しい値を入力します。 **[Enter]\(確定\)** を選択して値を保存します。

   :::image type="content" source="./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-set-value1.png" alt-text="Intellij のリモート Spark ジョブのデバッグ (値の設定)" border="true":::

1. **[Resume Program]\(プログラムの再開\)** アイコンを選択して、プログラムの実行を続けます。 今回は例外はキャッチされません。 プロジェクトは例外なしに正常に実行されていることが表示されます。

   :::image type="content" source="./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-without-exception.png" alt-text="Intellij のリモート Spark ジョブのデバッグ (例外なし)" border="true":::

## <a name="next-steps"></a>次のステップ

* [概要:Azure HDInsight での Apache Spark](apache-spark-overview.md)

### <a name="demo"></a>デモ

* Scala プロジェクトを作成する (ビデオ):[Apache Spark Scala アプリケーションを作成する](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* リモート デバッグ (ビデオ):[Azure Toolkit for IntelliJ を使用して HDInsight クラスター上で Apache Spark アプリケーションをリモートでデバッグする](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>シナリオ

* [Apache Spark と BI:HDInsight と BI ツールで Spark を使用して対話型データ分析を実行する](apache-spark-use-bi-tools.md)
* [Apache Spark と Machine Learning:HDInsight で Spark を使用して、HVAC データを使用して建物の温度を分析する](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark と Machine Learning:HDInsight で Spark を使用して食品の検査結果を予測する](apache-spark-machine-learning-mllib-ipython.md)
* [HDInsight 上での Apache Spark を使用した Web サイト ログ分析](./apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>アプリケーションの作成と実行

* [Scala を使用してスタンドアロン アプリケーションを作成する](./apache-spark-create-standalone-application.md)
* [Apache Livy を使用して Apache Spark クラスターでジョブをリモートから実行する](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>ツールと拡張機能

* [Azure Toolkit for IntelliJ を使用して HDInsight クラスター向けの Apache Spark アプリケーションを作成する](apache-spark-intellij-tool-plugin.md)
* [Azure Toolkit for IntelliJ を使用して VPN 経由で Apache Spark アプリケーションをリモートでデバッグする](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Azure Toolkit for Eclipse 上の HDInsight Tools を使用して Apache Spark アプリケーションを作成する](./apache-spark-eclipse-tool-plugin.md)
* [HDInsight 上の Apache Spark クラスターで Apache Zeppelin Notebook を使用する](apache-spark-zeppelin-notebook.md)
* [HDInsight 用の Apache Spark クラスター内の Jupyter Notebook で使用可能なカーネル](apache-spark-jupyter-notebook-kernels.md)
* [Jupyter Notebook で外部のパッケージを使用する](apache-spark-jupyter-notebook-use-external-packages.md)
* [Jupyter をコンピューターにインストールして HDInsight Spark クラスターに接続する](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>リソースの管理

* [Azure HDInsight での Apache Spark クラスターのリソースの管理](apache-spark-resource-manager.md)
* [HDInsight の Apache Spark クラスターで実行されるジョブの追跡とデバッグ](apache-spark-job-debugging.md)