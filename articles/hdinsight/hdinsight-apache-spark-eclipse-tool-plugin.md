---
title: "Azure Toolkit for Eclipse: HDInsight Spark 向けの Scala アプリケーションの作成 | Microsoft Docs"
description: "Azure Toolkit for Eclipse の HDInsight ツールを使用して Scala で記述された Spark アプリケーションを開発し、Eclipse IDE から直接、HDInsight Spark クラスターに送信します。"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: f6c79550-5803-4e13-b541-e86c4abb420b
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: nitinme
ms.openlocfilehash: 79b3183171e3c28276c8e4e6d4fe3998e0109643
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-toolkit-for-eclipse-to-create-spark-applications-for-an-hdinsight-cluster"></a>Azure Toolkit for Eclipse を使用して HDInsight クラスター向けの Spark アプリケーションを作成する

Azure Toolkit for Eclipse の HDInsight Tools を使用して Scala で記述された Spark アプリケーションを開発し、Eclipse IDE から直接、Azure HDInsight Spark クラスターに送信します。 HDInsight Tools プラグインには、次のような複数の使い方があります。

* Scala Spark アプリケーションを開発して HDInsight Spark クラスターに送信する
* Azure HDInsight Spark クラスター リソースにアクセスする
* Scala Spark アプリケーションをローカルで開発して実行する

> [!IMPORTANT]
> このツールを使用して、Linux 上で HDInsight Spark クラスター専用のアプリケーションを作成し、送信することができます。
> 
> 

## <a name="prerequisites"></a>前提条件

* HDInsight 上の Apache Spark クラスター。 手順については、 [Azure HDInsight での Apache Spark クラスターの作成](hdinsight-apache-spark-jupyter-spark-sql.md)に関するページを参照してください。
* Eclipse IDE ランタイムで使用される Oracle Java Development Kit Version 8。 [Oracle の Web サイト](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)からダウンロードできます。
* Eclipse IDE。 この記事では、Eclipse Neon を使用します。 [Eclipse の Web サイト](https://www.eclipse.org/downloads/)からインストールできます。
* Spark SDK。 [GitHub](http://go.microsoft.com/fwlink/?LinkID=723585&clcid=0x409) からダウンロードできます。


## <a name="install-hdinsight-tools-in-azure-toolkit-for-eclipse-and-the-scala-plug-in"></a>Azure Toolkit for Eclipse の HDInsight ツールおよび Scala プラグインをインストールする
### <a name="install-hdinsight-tools"></a>HDInsight ツールをインストールする
Eclipse 用の HDInsight Tools は、Azure Toolkit for Eclipse に付属しています。 インストール手順については、「[Azure Toolkit for Eclipse のインストール](../azure-toolkit-for-eclipse-installation.md)」を参照してください。
### <a name="install-the-scala-plug-in"></a>Scala プラグインをインストールする
Eclipse の起動時に、Scala プラグインがインストールされているかどうかを HDInsight Tools が自動的に検出します。 **[OK]** を選択して続行し、指示に従って Eclipse Marketplace からプラグインをインストールします。

![Scala プラグインの自動インストール](./media/hdinsight-apache-spark-eclipse-tool-plugin/auto-install-scala.png)

## <a name="sign-in-to-your-azure-subscription"></a>Azure サブスクリプションにサインインします。
1. Eclipse IDE を起動し、Azure Explorer を開きます。 **[Window]\(ウィンドウ\)** メニューの **[Show View]\(ビューの表示\)** を選択し、**[Other]\(その他\)** を選択します。 表示されたダイアログ ボックスで **[Azure]** を展開し、**[Azure Explorer]** を選択して、**[OK]** を選択します。

   ![[Show View]\(ビューの表示\) ダイアログ ボックス](./media/hdinsight-apache-spark-eclipse-tool-plugin/view-explorer-1.png)
2. **[Azure]** ノードを右クリックし、**[Sign in]\(サインイン\)** を選択します。
3. **[Azure Sign In]\(Azure サインイン\)** ダイアログ ボックスで、認証モードを選択します。**[Sign in]\(サインイン\)** を選択し、Azure 資格情報を入力します。
   
   ![Azure サインイン ダイアログ ボックス](./media/hdinsight-apache-spark-eclipse-tool-plugin/view-explorer-2.png)
4. サインイン後、**[Select Subscriptions]\(サブスクリプションの選択\)** ダイアログ ボックスに、その資格情報に関連付けられているすべての Azure サブスクリプションの一覧が表示されます。 **[Select]\(選択\)** をクリックしてダイアログ ボックスを閉じます。

   ![[Select Subscriptions]\(サブスクリプションの選択\) ダイアログ ボックス](./media/hdinsight-apache-spark-eclipse-tool-plugin/Select-Subscriptions.png)
5. **[Azure Explorer]** タブで **[HDInsight]** を展開し、自分のサブスクリプションの下にある HDInsight Spark クラスターを表示します。
   
   ![Azure Explorer での HDInsight Spark クラスター](./media/hdinsight-apache-spark-eclipse-tool-plugin/view-explorer-3.png)
6. クラスター名のノードをさらに展開すると、そのクラスターに関連付けられているリソース (ストレージ アカウントなど) を表示できます。
   
   ![クラスター名を展開してリソースを表示する](./media/hdinsight-apache-spark-eclipse-tool-plugin/view-explorer-4.png)



## <a name="set-up-a-spark-scala-project-for-an-hdinsight-spark-cluster"></a>HDInsight Spark クラスター用の Spark Scala プロジェクトを設定する

1. Eclipse IDE のワークスペースで、**[File]\(ファイル\)**、**[New]\(新規\)**、**[Project]\(プロジェクト\)** の順に選択します。 
2. New Project \(新しいプロジェクト\) ウィザードで、**[HDInsight]** を展開して、**[Spark on HDInsight (Scala)]\(HDInsight の Spark (Scala)\)** を選択し、**[Next]\(次へ\)** を選択します。

   ![[Spark on HDInsight (Scala)]\(HDInsight の Spark (Scala)\) プロジェクトの選択](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-2.png)
3. Scala プロジェクト作成ウィザードは Scala プラグインをインストールしたかどうかを自動的に検出します。 **[OK]** を選択して Scala プラグインのダウンロードを続行し、指示に従って Eclipse を再起動します。

   ![Scala チェック](./media/hdinsight-apache-spark-eclipse-tool-plugin/auto-install-scala-2.png)
4. **[New HDInsight Scala Project]\(新しい HDInsight Scala プロジェクト\)** ダイアログ ボックスで、次の値を指定し、**[Next]\(次へ\)** を選択します。
   * プロジェクトの名前を入力します。
   * **[JRE]** 領域で、**[Use an execution environment JRE]\(実行環境 JRE を使用する\)** が **JavaSE-1.7** 以降に設定されていることを確認します。
   * Spark SDK が、SDK をダウンロードした場所に設定されていることを確認します。 ダウンロード場所へのリンクは、この記事の前の「[前提条件](#prerequisites)」にあります。 このダイアログ ボックスのリンクから SDK をダウンロードすることもできます。

   ![[New HDInsight Scala Project]\(新しい HDInsight Scala プロジェクト\) ダイアログ ボックス](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-3.png)
5. 次のダイアログ ボックスで **[Libraries]\(ライブラリ\)** タブを選択し、既定値をそのまま受け入れて **[Finish]\(完了\)** を選択します。 
   
   ![[Libraries]\(ライブラリ\) タブ](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-4.png)
  
## <a name="create-a-scala-application-for-an-hdinsight-spark-cluster"></a>HDInsight Spark クラスター向けの Scala アプリケーションを作成する

1. Eclipse IDE の Package Explorer で、前に作成したプロジェクトを展開し、**[src]** を右クリックして **[New]\(新規\)** をポイントして、**[Other]\(その他\)** を選択します。
2. **[Select a wizard] \(ウィザードの選択)** ダイアログ ボックスで **[Scala Wizards] \(Scala ウィザード)** を展開し、**[Scala Object] \(Scala オブジェクト)**、**[Next] \(次へ)** の順に選択します。
   
   ![[Select a wizard]\(ウィザードの選択\) ダイアログ ボックス](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-scala-proj-1.png)
3. **[Create New File] \(新しいファイルの作成)** ダイアログ ボックスでオブジェクトの名前を入力し、**[Finish] \(完了)** を選択します。
   
   ![[Create New File]\(新しいファイルの作成\) ダイアログ ボックス](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-scala-proj-2.png)
4. テキスト エディターで次のコードを貼り付けます。
   
        import org.apache.spark.SparkConf
        import org.apache.spark.SparkContext
   
        object MyClusterApp{
          def main (arg: Array[String]): Unit = {
            val conf = new SparkConf().setAppName("MyClusterApp")
            val sc = new SparkContext(conf)
   
            val rdd = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
   
            //find the rows that have only one digit in the seventh column in the CSV
            val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)
   
            rdd1.saveAsTextFile("wasb:///HVACOut")
          }        
        }
5. HDInsight Spark クラスターでアプリケーションを実行します。
   
   a. Package Explorer で、プロジェクト名を右クリックし、**[Submit Spark Application to HDInsight]\(HDInsight への Spark アプリケーションの送信\)** を選択します。        
   b. **[Spark Submission]\(Spark 送信\)** ダイアログ ボックスで、次の値を入力し、**[Submit]\(送信\)** を選択します。
      
      * **[Cluster Name (クラスター名)]**で、アプリケーションを実行する HDInsight Spark クラスターを選択します。
      * Eclipse プロジェクトまたはハード ドライブからアーティファクトを選択します。 既定値は、Package Explorer から右クリックした項目によって異なります。
      * 送信ウィザードの **[Main class name]\(メイン クラス名\)** ボックスの一覧に、プロジェクトのすべてのオブジェクト名が表示されます。 実行するオブジェクトを選択または入力します。 ハード ドライブからアーティファクトを選択した場合は、メイン クラス名を手動で入力する必要があります。 
      * この例のアプリケーション コードでは、コマンドライン引数を必要とせず、JAR またはファイルを参照することもないので、残りのテキスト ボックスは空のままでかまいません。
        
      ![[Spark Submission]\(Spark 送信\) ダイアログ ボックス](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-scala-proj-3.png)
6. **[Spark Submission (Spark 送信)]** タブで、進行状況の表示が開始されます。 **[Spark Submission]\(Spark 送信\)** ウィンドウにある赤いボタンを選択して、アプリケーションを停止することができます。 地球アイコン (図では青のボックスで示されています) を選択して、この特定のアプリケーションの実行に関するログを表示することもできます。
      
   ![[Spark Submission]\(Spark 送信\) ウィンドウ](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-scala-proj-4.png)

## <a name="access-and-manage-hdinsight-spark-clusters-by-using-hdinsight-tools-in-azure-toolkit-for-eclipse"></a>Azure Toolkit for Eclipse の HDInsight Tools を使用して HDInsight Spark クラスターにアクセスして管理する
HDInsight Tools を使用すると、ジョブの出力へのアクセスなど、さまざまな操作を実行できます。

### <a name="access-the-job-view"></a>ジョブ ビューにアクセスする
1. Azure Explorer で **[HDInsight]** を展開します。Spark クラスター名を展開した後、**[Jobs]\(ジョブ\)** を選択します。 

   ![ジョブ ビューのノード](./media/hdinsight-apache-spark-intellij-tool-plugin/job-view-node.png)

2. **[ジョブ]** ノードを選択します。 E(fx)clipse プラグインがインストールされているかどうかを HDInsight Tools が自動的に検出します。 **[OK]** を選択して続行し、指示に従って Eclipse Marketplace をインストールし、Eclipse を再起動します。

   ![E(fx)clipse をインストールする](./media/hdinsight-apache-spark-eclipse-tool-plugin/auto-install-efxclipse.png)

3. **[ジョブ]** ノードからジョブ ビューを開きます。 右側のウィンドウの **[Spark Job View (Spark ジョブ ビュー)]** タブに、クラスター上で実行されていたすべてのアプリケーションが表示されます。 詳細情報を確認したいアプリケーションの名前を選択します。

   ![Application details](./media/hdinsight-apache-spark-intellij-tool-plugin/view-job-logs.png)

   これで、以下のアクションを実行できます。

   * ジョブ グラフをポイントします。 すると、実行中のジョブに関する基本情報が表示されます。 ジョブ グラフを選択すると、各ジョブについて生成されるステージおよび情報を確認できます。

     ![ジョブのステージの詳細](./media/hdinsight-apache-spark-intellij-tool-plugin/Job-graph-stage-info.png)

   * **[Log]\(ログ\)** タブを選択して、**Driver Stderr**、**Driver Stdout**、**Directory Info** などの頻繁に使用されるログを表示します。

     ![ログの詳細](./media/hdinsight-apache-spark-intellij-tool-plugin/Job-log-info.png)

   * ウィンドウの上部にあるハイパーリンクを選択して、Spark 履歴 UI と YARN UI を (アプリケーション レベルで) 開きます。

### <a name="access-the-storage-container-for-the-cluster"></a>クラスターのストレージ コンテナーにアクセスする
1. Azure Explorer で、**[HDInsight]** ルート ノードを展開して、使用できる HDInsight Spark クラスターの一覧を表示します。
2. クラスター名を展開して、ストレージ アカウントと、クラスターの既定のストレージ コンテナーを表示します。
   
   ![ストレージ アカウントと既定のストレージ コンテナー](./media/hdinsight-apache-spark-eclipse-tool-plugin/view-explorer-5.png)
3. クラスターに関連付けられているストレージ コンテナー名を選択します。 右側のウィンドウで、**HVACOut** フォルダーをダブルクリックします。 **part-** ファイルのいずれかを開いて、アプリケーションの出力を確認します。

### <a name="access-the-spark-history-server"></a>Spark 履歴サーバーにアクセスする
1. Azure Explorer で、Spark クラスター名を右クリックし、**[Open Spark History UI]\(Spark 履歴 UI を開く\)** を選択します。 入力を求められたら、クラスターの管理者資格情報を入力します。 これらは、クラスターのプロビジョニング時に指定したものです。
2. Spark 履歴サーバーのダッシュボードで、実行が終了したばかりのアプリケーションをアプリケーション名を使って探します。 上記のコードでは、`val conf = new SparkConf().setAppName("MyClusterApp")` を使用してアプリケーション名を設定しました。 したがって、Spark アプリケーション名は **MyClusterApp** です。

### <a name="start-the-ambari-portal"></a>Ambari ポータルを起動する
1. Azure Explorer で、Spark クラスター名を右クリックし、**[Open Cluster Management Portal (Ambari)]\(クラスター管理ポータルを開く (Ambari)\)** を選択します。 
2. 入力を求められたら、クラスターの管理者資格情報を入力します。 これらは、クラスターのプロビジョニング時に指定したものです。

### <a name="manage-azure-subscriptions"></a>Azure サブスクリプションの管理
Azure Toolkit for Eclipse の HDInsight Tools の既定では、すべての Azure サブスクリプションからの Spark クラスターが一覧表示されます。 必要に応じて、クラスターにアクセスするサブスクリプションを指定できます。 

1. Azure Explorer で、**[Azure]** ルート ノードを右クリックし、**[Manage Subscriptions]\(サブスクリプションの管理\)** を選択します。 
2. ダイアログ ボックスで、アクセスしないサブスクリプションのチェック ボックスをオフにし、**[Close]\(閉じる\)** を選択します。 Azure サブスクリプションからサインアウトする場合は、**[Sign Out]\(サインアウト\)** を選択することもできます。

## <a name="run-a-spark-scala-application-locally"></a>Spark Scala アプリケーションのローカルでの実行
Azure Toolkit for Eclipse の HDInsight Tools を使用すると、ワークステーション上で Spark Scala アプリケーションをローカルに実行することができます。 通常、そのようなアプリケーションは、ストレージ コンテナーなどのクラスター リソースにアクセスする必要がなく、ローカルで実行しテストすることができます。

### <a name="prerequisite"></a>前提条件
Windows コンピューターでローカルの Spark Scala アプリケーションを実行中に、[SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356) で説明されている例外が発生する場合があります。 この例外は、Windows 上に **WinUtils.exe** がないことが原因で発生します。 

このエラーを回避するには、[実行可能ファイルをダウンロード](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe)して、**C:\WinUtils\bin** のような場所に保存する必要があります。 次に、環境変数 **HADOOP_HOME** を追加し、この変数の値を **C\WinUtils** に設定します。

### <a name="run-a-local-spark-scala-application"></a>ローカル Spark Scala アプリケーションの実行
1. Eclipse を起動し、プロジェクトを作成します。 **[新しいプロジェクト]** ダイアログ ボックスで、次の選択を行い、**[Next]\(次へ\)** を選択します。
   
   * 左側のウィンドウで、**[HDInsight]** を選択します。
   * 右側のウィンドウで、**[Spark on HDInsight Local Run Sample (Scala)]\(HDInsight の Spark のローカル実行サンプル (Scala)\)** を選択します。

   ![New Project dialog box](./media/hdinsight-apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run.png)
   
2. プロジェクトの詳細を指定するために、前のセクション「[HDInsight Spark クラスター用の Spark Scala プロジェクトを設定する](#set-up-a-spark-scala-project-for-an-hdinsight-spark-cluster)」の手順 3. から 6. に従います。

3. テンプレートは、コンピューターでローカルに実行することができるサンプル コード (**LogQuery**) を **src** フォルダーの下に追加します。
   
   ![LogQuery の場所](./media/hdinsight-apache-spark-eclipse-tool-plugin/local-app.png)
   
4. **LogQuery** アプリケーションを右クリックし、**[Run As]\(実行者\)** をポイントして、**[1 Scala Application]\(1 Scala アプリケーション\)** を選択します。 次のような出力が **[コンソール]** タブに表示されます。
   
   ![Spark アプリケーションをローカルに実行した結果](./media/hdinsight-apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run-result.png)

## <a name="known-problems"></a>既知の問題
アプリケーションを Azure Data Lake Store に送信するには、Azure サインイン プロセスで **[Interactive]\(インタラクティブ\)** モードを選択します。 **[Automated]\(自動\)** モードを選択した場合、エラーが発生することがあります。

![対話型のサインイン](./media/hdinsight-apache-spark-eclipse-tool-plugin/interactive-authentication.png)

Azure Data Lake クラスターを選択して、任意のサインイン方法でアプリケーションを送信することができます。

現在、Spark の出力を直接表示する機能はサポートされていません。

## <a name="feedback"></a>フィードバック
ご意見がございましたら、またはこのツールを使用していてその他の問題が発生した場合は、hdivstool@microsoft.com に電子メールでお問い合わせください。

## <a name="seealso"></a>関連項目
* [概要: Azure HDInsight での Apache Spark](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>シナリオ
* [Spark と BI: HDInsight と BI ツールで Spark を使用した対話型データ分析の実行](hdinsight-apache-spark-use-bi-tools.md)
* [Spark と Machine Learning: HDInsight で Spark を使用して HVAC データを基に建物の温度を分析する](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Spark と Machine Learning: HDInsight で Spark を使用して食品の検査結果を予測する](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Spark ストリーミング: リアルタイム ストリーミング アプリケーションを作成するための HDInsight での Spark の使用](hdinsight-apache-spark-eventhub-streaming.md)
* [Website log analysis using Spark in HDInsight (HDInsight での Spark を使用した Web サイト ログ分析)](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="creating-and-running-applications"></a>アプリケーションの作成と実行
* [Scala を使用してスタンドアロン アプリケーションを作成する](hdinsight-apache-spark-create-standalone-application.md)
* [Livy を使用して Spark クラスターでジョブをリモートで実行する](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>ツールと拡張機能
* [Azure Toolkit for IntelliJ を使用して Spark Scala アプリケーションを作成して送信する](hdinsight-apache-spark-intellij-tool-plugin.md)
* [Azure Toolkit for IntelliJ を使用して VPN を介して Spark アプリケーションをリモートでデバッグする](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Azure Toolkit for IntelliJ を使用して SSH を介して Spark アプリケーションをリモートでデバッグする](hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Hortonworks Sandbox と IntelliJ 用 HDInsight ツールを使用する](hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [HDInsight の Spark クラスターで Zeppelin Notebook を使用する](hdinsight-apache-spark-zeppelin-notebook.md)
* [HDInsight 用の Spark クラスターの Jupyter Notebook で使用可能なカーネル](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Jupyter Notebook で外部のパッケージを使用する](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Jupyter をコンピューターにインストールして HDInsight Spark クラスターに接続する](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="managing-resources"></a>リソースの管理
* [Azure HDInsight での Apache Spark クラスターのリソースの管理](hdinsight-apache-spark-resource-manager.md)
* [HDInsight の Apache Spark クラスターで実行されるジョブの追跡とデバッグ](hdinsight-apache-spark-job-debugging.md)

