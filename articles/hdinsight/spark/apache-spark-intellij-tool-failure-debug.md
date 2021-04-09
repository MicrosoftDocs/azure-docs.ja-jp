---
title: Azure Toolkit for IntelliJ (プレビュー) を使用して Spark ジョブをデバッグする - HDInsight
description: Azure Toolkit for IntelliJ の HDInsight ツールを使用したアプリケーションのデバッグのガイダンス
keywords: デバッグ、intellij のリモート デバッグ、ssh、intellij、hdinsight、intellij のデバッグ、デバッグ
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 07/12/2019
ms.openlocfilehash: 5422fe324ca1f3ef5bb2d14fb04664c8fb03fe3c
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104866235"
---
# <a name="failure-spark-job-debugging-with-azure-toolkit-for-intellij-preview"></a>Azure Toolkit for IntelliJ を使用した失敗した Spark ジョブのデバッグ (プレビュー)

この記事では、[Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij) の HDInsight Tools を使用して **Spark Failure Debug** アプリケーションを実行する方法に関するステップ バイ ステップ ガイダンスを提供します。

## <a name="prerequisites"></a>前提条件

* [Oracle Java Development Kit](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)。 このチュートリアルでは、Java バージョン 8.0.202 を使用します。
  
* IntelliJ IDEA。 この記事では、[IntelliJ IDEA Community Version 2019.1.3](https://www.jetbrains.com/idea/download/#section=windows) を使用します。
  
* Azure Toolkit for IntelliJ。 「[Azure Toolkit for IntelliJ のインストール](/azure/developer/java/toolkit-for-intellij/installation)」を参照してください。

* HDInsight クラスターに接続します。 [HDInsight クラスターへの接続](apache-spark-intellij-tool-plugin.md)に関するページを参照してください。

* Microsoft Azure Storage Explorer。 [Microsoft Azure Storage Explorer のダウンロード](https://azure.microsoft.com/features/storage-explorer/)に関するページを参照してください。

## <a name="create-a-project-with-debugging-template"></a>デバッグ テンプレートを含むプロジェクトを作成する

このドキュメントでは、失敗したデバッグを続行し、失敗したタスク デバッグのサンプル ファイルを取得するための Spark 2.3.2 プロジェクトを作成します。

1. IntelliJ IDEA を開きます。 **[新しいプロジェクト]** ウィンドウを開きます。

   a. 左側のウィンドウの **[Azure Spark/HDInsight]** を選択します。

   b. メイン ウィンドウから **[Spark Project with Failure Task Debugging Sample(Preview)(Scala)] (失敗したタスク デバッグのサンプルを含む Spark プロジェクト (プレビュー) (Scala))** を選択します。

     :::image type="content" source="./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-projectfor-failure-debug.png" alt-text="Intellij のデバッグ プロジェクトの作成" border="true":::

   c. **[次へ]** を選択します。

2. **[New Project]\(新しいプロジェクト\)** ウィンドウで、次の手順を実行します。

   :::image type="content" source="./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-project.png" alt-text="Intellij の新しいプロジェクト、Spark バージョンの選択" border="true":::

   a. プロジェクト名とプロジェクトの場所を入力します。

   b. **[Project SDK] (プロジェクト SDK)** ドロップダウン リストで、**Spark 2.3.2** クラスター用に **[Java 1.8]** を選択します。

   c. **[Spark バージョン]** ドロップダウン リストで、 **[Spark 2.3.2(Scala 2.11.8)]** を選択します。

   d. **[完了]** を選択します。

3. **[src]**  >  **[main]**  >  **[scala]** を選択してプロジェクトのコードを開きます。 この例では、**AgeMean_Div()** スクリプトを使用します。

## <a name="run-a-spark-scalajava-application-on-an-hdinsight-cluster"></a>HDInsight クラスター上で Spark Scala/Java アプリケーションを実行する

Spark Scala/Java アプリケーション作成した後、次の手順を実行して、そのアプリケーションを Spark クラスター上で実行します。

1. **[構成の追加]** をクリックして **[実行/デバッグ構成]** ウィンドウを開きます。

   :::image type="content" source="./media/apache-spark-intellij-tool-failure-debug/hdinsight-add-new-configuration.png" alt-text="HDI Intellij の構成の追加" border="true":::

2. **[実行/デバッグ構成]** ダイアログ ボックスで、プラス記号 ( **+** ) を選択します。 次に **[HDInsight での Apache Spark]** オプションを選択します。

   :::image type="content" source="./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-configuraion-01.png" alt-text="Intellij の新規構成の追加" border="true":::

3. **[Remotely Run in Cluster]\(クラスターでリモート実行\)** タブに切り替えます。 **[名前]** 、 **[Spark cluster]\(Spark クラスター\)** 、 **[Main class name]\(メイン クラス名\)** に情報を入力します。 ツールでは、**Executor** を使用したデバッグがサポートされています。 **[numExectors]** は既定値が 5 ですが、3 より大きい値に設定することはお勧めできません。 実行時間を短縮するために、 **[job Configurations] (ジョブ構成)** に **[spark.yarn.maxAppAttempts]** を追加し、その値を 1 に設定できます。 **[OK]** ボタンをクリックして構成を保存します。

   :::image type="content" source="./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-configuraion-002.png" alt-text="Intellij の新規構成の実行/デバッグ" border="true":::

4. 指定した名前で構成が保存されます。 構成の詳細を表示するには、構成名を選択します。 変更するには、 **[構成の編集]** を選択します。

5. 構成の設定を完了したら、リモート クラスターに対してプロジェクトを実行できます。

   :::image type="content" source="./media/apache-spark-intellij-tool-failure-debug/hdinsight-local-run-configuration.png" alt-text="Intellij のリモート Spark ジョブのデバッグ (リモート実行ボタン)" border="true":::

6. 出力ウィンドウからアプリケーション ID を確認できます。

   :::image type="content" source="./media/apache-spark-intellij-tool-failure-debug/hdinsight-remotely-run-result.png" alt-text="Intellij のリモート Spark ジョブのデバッグ (リモート実行結果)" border="true":::

## <a name="download-failed-job-profile"></a>失敗したジョブのプロファイルをダウンロードする

ジョブの送信に失敗した場合は、さらにデバッグするために、失敗したジョブのプロファイルをローカル コンピューターにダウンロードできます。

1. **Microsoft Azure Storage Explorer** を開き、失敗したジョブに対するクラスターの HDInsight アカウントを見つけて、失敗したジョブのリソースを、対応する場所である **\hdp\spark2-events\\.spark-failures\\\<application ID>** からローカル フォルダーにダウンロードします。 ダウンロードの進行状況が **[アクティビティ]** ウィンドウに表示されます。

   :::image type="content" source="./media/apache-spark-intellij-tool-failure-debug/hdinsight-find-spark-file-001.png" alt-text="Azure Storage Explorer での失敗のダウンロード" border="true":::

   :::image type="content" source="./media/apache-spark-intellij-tool-failure-debug/spark-on-cosmos-doenload-file-2.png" alt-text="Azure Storage Explorer でのダウンロードの成功" border="true":::

## <a name="configure-local-debugging-environment-and-debug-on-failure"></a>ローカルのデバッグ環境を構成して失敗をデバッグする

1. 元のプロジェクトを開くか、または新しいプロジェクトを作成し、それを元のソース コードに関連付けます。 現在、失敗したデバッグでは Spark 2.3.2 バージョンのみがサポートされています。

1. IntelliJ IDEA で、**Spark Failure Debug** 構成ファイルを作成し、 **[Spark Job Failure Context location] (Spark ジョブの失敗したコンテキストの場所)** フィールドで前にダウンロードされた失敗したジョブのリソースから FTD ファイルを選択します。

   :::image type="content" source="./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-failure-configuration-01.png" alt-text="失敗構成の作成" border="true":::

1. ツールバーのローカル実行ボタンをクリックすると、エラーが [実行] ウィンドウに表示されます。

   :::image type="content" source="./media/apache-spark-intellij-tool-failure-debug/local-run-failure-configuraion-01.png" alt-text="run-failure-configuration1" border="true":::

   :::image type="content" source="./media/apache-spark-intellij-tool-failure-debug/local-run-failure-configuration.png" alt-text="run-failure-configuration2" border="true":::

1. ログに示されているようにブレークポイントを設定した後、ローカル デバッグ ボタンをクリックして、IntelliJ の通常の Scala/Java プロジェクトと同様にローカル デバッグを実行します。

1. デバッグの後、プロジェクトが正常に完了した場合は、失敗したジョブを HDInsight クラスター上の Spark に再送信できます。

## <a name="next-steps"></a><a name="seealso"></a>次のステップ

* [概要:Apache Spark アプリケーションをデバッグする](apache-spark-intellij-tool-debug-remotely-through-ssh.md)

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
* [Hortonworks Sandbox と IntelliJ 用 HDInsight ツールを使用する](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)
* [Azure Toolkit for Eclipse 上の HDInsight Tools を使用して Apache Spark アプリケーションを作成する](./apache-spark-eclipse-tool-plugin.md)
* [HDInsight 上の Apache Spark クラスターで Apache Zeppelin Notebook を使用する](apache-spark-zeppelin-notebook.md)
* [HDInsight 用の Apache Spark クラスター内の Jupyter Notebook で使用可能なカーネル](apache-spark-jupyter-notebook-kernels.md)
* [Jupyter Notebook で外部のパッケージを使用する](apache-spark-jupyter-notebook-use-external-packages.md)
* [Jupyter をコンピューターにインストールして HDInsight Spark クラスターに接続する](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>リソースの管理

* [Azure HDInsight での Apache Spark クラスターのリソースの管理](apache-spark-resource-manager.md)
* [HDInsight の Apache Spark クラスターで実行されるジョブの追跡とデバッグ](apache-spark-job-debugging.md)