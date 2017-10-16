---
title: "Azure HDInsight Tools - Hive、LLAP、pySpark に Visual Studio Code を使用する | Microsoft Docs"
description: "Azure HDInsight Tools for Visual Studio Code を使用して、クエリとスクリプトを作成および送信する方法について説明します。"
Keywords: "VScode,Azure HDInsight Tools,Hive,Python,PySpark,Spark,HDInsight,Hadoop,LLAP,対話型 Hive,対話型クエリ"
services: HDInsight
documentationcenter: 
author: jejiang
manager: 
editor: jgao
tags: azure-portal
ms.assetid: 
ms.service: HDInsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/26/2017
ms.author: jejiang
ms.openlocfilehash: fdf1911f4f5b515592b6197ea738d808104cb43e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-hdinsight-tool-for-visual-studio-code"></a>Azure HDInsight Tool for Visual Studio Code を使用する

Azure HDInsight Tools for Visual Studio Code (VSCode) を使用して、Hive バッチ ジョブ、対話型 Hive クエリ、および pySpark スクリプトを作成および送信する方法について説明します。 Azure HDInsight Tools は、VSCode でサポートされている Windows、Linux、MacOS などのプラットフォームにインストールできます。 プラットフォームごとの前提条件については、以下を参照してください。


## <a name="prerequisites"></a>前提条件

この記事を完了するには、次の項目が必要です。

- HDInsight クラスター。  クラスターを作成するには、[HDInsight での Hadoop の使用]( hdinsight-hadoop-linux-tutorial-get-started.md)に関するページをご覧ください。
- [Visual Studio Code](https://www.visualstudio.com/products/code-vs.aspx)。
- [Mono](http://www.mono-project.com/docs/getting-started/install/)。 Mono は Linux と MacOS のみに必要です。

## <a name="install-the-hdinsight-tools"></a>HDInsight Tools をインストールする
   
前提条件の各項目のインストールが完了したら、Azure HDInsight Tools for VSCode をインストールできます。 

**Azure HDInsight Tools をインストールするには**

1. **Visual Studio Code** を開きます。
2. 左側のウィンドウで **[拡張機能]** をクリックします。 検索ボックスに「**HDInsight**」と入力します。
3. **Azure HDInsight Tools** の横にある **[インストール]** をクリックします。 数秒後、**[インストール]** ボタンが **[再度読み込む]** に変わります。
4. **[再度読み込む]** をクリックして、**Azure HDInsight Tools** の拡張機能をアクティブにします。
5. **[ウィンドウの再読み込み]** をクリックして確認します。 拡張機能ウィンドウに **Azure HDInsight Tools** が表示されます。

   ![HDInsight for Visual Studio Code の Python のインストール](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

Spark ユーザーの場合は、言語サービス エクスペリエンスを向上させるために、Python 拡張機能が推奨されます。 Python 拡張機能は、Hive ユーザーには必要ありません。 

**Python をインストールするには**

1. **Visual Studio Code** を開きます。
2. 左側のウィンドウで **[拡張機能]** をクリックします。 検索ボックスに「**python**」と入力します。 Python 拡張機能の一覧が表示されます。 その中に **Python** があります。
3. **Python** の横にある **[インストール]** をクリックします。 数秒後、**[インストール]** ボタンが **[再度読み込む]** に変わります。
4. **[再度読み込む]** をクリックして、**Python** 拡張機能をアクティブにします。
5. **[ウィンドウの再読み込み]** をクリックして確認します。 拡張機能ウィンドウに **Python** が表示されます。

     ![HDInsight for Visual Studio Code の Python のインストール](./media/hdinsight-for-vscode/hdinsight-vscode-install-python.png)

## <a name="open-hdinsight-workspace"></a>HDInsight ワークスペースを開く

Azure に接続するには、VSCode にワークスペースを作成する必要があります。

**ワークスペースを開くには**

1. [**ファイル**] メニューから [**フォルダーを開く**] をクリックし、既存のフォルダーを指定するか、作業フォルダーとして新しいフォルダーを作成します。 フォルダーが左側のウィンドウに表示されます。
2. 左側のウィンドウで、作業フォルダーの横にある **新しいファイル**をクリックします。

   ![新しいファイル](./media/hdinsight-for-vscode/new-file.png)
3. .hql (Hive クエリ) または .py (Spark スクリプト) のファイル拡張子のいずれかを使って、新しいファイルに名前を付けます。 **XXXX_hdi_settings.json** 構成ファイルが、作業フォルダーに自動的に追加されることに注意してください。
4. **[エクスプローラー]** から **XXXX_hdi_settings.json** を開くか、スクリプト エディターを右クリックして **[構成の設定]** を選択します。 ファイルのサンプルに示すように、ログイン エントリ、既定のクラスター、およびジョブの送信パラメーターを設定できます。 残りのパラメーターを空のままにすることもできます。

## <a name="connect-to-azure"></a>Azure への接続

VSCode から HDInsight クラスターにスクリプトを送信するには、Azure アカウントに接続する必要があります。

**Azure に接続するには**

1. 新しい作業フォルダーと新しいスクリプト ファイルが存在しない場合は、作成します。
2. スクリプト エディターを右クリックして、コンテキスト メニューから **[HDInsight: Login]** を選択します。 また、**Ctrl + Shift + P キー**を押して、「**HDInsight: Login**」と入力することもできます。

    ![HDInsight Tools for Visual Studio Code のログイン](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)
3. **[出力]** ウィンドウのログインの指示に従って、ログインします。

    **Azure:** ![HDInsight Tools for Visual Studio Code のログイン情報](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-Azurelogin-info.png)

    接続すると、VSCode ウィンドウの左下にあるステータス バーに Azure アカウント名が表示されます。 

    > [!NOTE] 
    > Azure アカウントで 2 要素認証が有効になっている場合は、PIN ではなく、電話認証を使用することをお勧めします。
    > Google Chrome を使用した Azure のログインには、既知の問題があります。

4. スクリプト エディターを右クリックして、コンテキスト メニューを開きます。

    ![HDInsight Tools for Visual Studio Code スクリプト エディターのコンテキスト メニュー](./media/hdinsight-for-vscode/hdinsight-for-vscode-context-menu.png)

    コンテキスト メニューから次のタスクを実行できます。

    - logout
    - list clusters
    - Set default cluster
    - Submit interactive Hive queries
    - Submit Hive batch script
    - Set configuration

## <a name="list-hdinsight-clusters"></a>List HDInsight clusters

接続をテストするために、HDInsight クラスターを一覧表示できます。

**Azure サブスクリプションの HDInsight クラスターを一覧表示するには**
1. ワークスペースを開いて、Azure に接続します。 「[HDInsight ワークスペースを開く](#open-hdinsight-workspace)」および「[Azure への接続](#connect-to-azure)」をご覧ください。
2. スクリプト エディターを右クリックして、コンテキスト メニューから **[HDInsight: List Cluster]** を選択します。 
3. Hive および Spark クラスターが、**[出力]** ウィンドウに表示されます。

    ![既定のクラスター構成の設定](./media/hdinsight-for-vscode/list-cluster-result.png)

## <a name="set-default-cluster"></a>Set default cluster
1. ワークスペースを開いて、Azure に接続します。 「[HDInsight ワークスペースを開く](#open-hdinsight-workspace)」および「[Azure への接続](#connect-to-azure)」をご覧ください。
2. スクリプト エディターを右クリックし、**[HDInsight: Set Default Cluster]** をクリックします。 
3. 現在のスクリプト ファイルに、既定のクラスターとしてクラスターを選択します。 ツールによって、構成ファイル (**XXXX_hdi_settings.json**) が自動的に更新されます。 

   ![既定のクラスター構成の設定](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)

## <a name="set-azure-environment"></a>Azure 環境を設定する 
1. **Ctrl + Shift + P** キーを押してコマンド パレットを開きます。
2. 「**HDInsight: Set Azure Environment**」と入力します。
3. 既定のログイン エントリとして、Azure または AzureChina のいずれかを選択します。
4. その間、Microsoft のツールによって、**XXXX_hdi_settings.json** に選択した既定のログイン エントリが保存されています。 また、この構成ファイル内で直接、更新することもできます。 

   ![既定のログイン エントリの構成を設定する](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)

## <a name="submit-interactive-hive-queries"></a>Submit interactive Hive queries

HDInsight Tools for VSCode を使用すると、対話型 Hive クエリを HDInsight 対話型クエリ クラスターに送信することができます。

1. 新しい作業フォルダーと新しい Hive スクリプト ファイルが存在しない場合は、作成します。
2. Azure アカウントに接続して、既定のクラスターを構成していない場合は、構成します。
3. 以下のコードをコピーし、Hive ファイルに貼り付けて保存します。

    ```hiveql
    SELECT * FROM hivesampletable;
    ```
3. スクリプト エディターを右クリックし、**[HDInsight: Interactive Hive]** をクリックしてクエリを送信します。 また、このツールでは、コンテキスト メニューを使用して、スクリプト ファイル全体ではなく、コードのブロックを送信することもできます。 その後すぐに、クエリの結果が新しいタブに示されます。

   ![対話型 Hive の結果](./media/hdinsight-for-vscode/interactive-hive-result.png)

    - **[結果]** パネル: 結果全体を CSV、JSON、EXCEL 形式でローカル パスに保存したり、複数の行だけを選択したりすることができます。
    - **[メッセージ]** パネル: **行**の番号をクリックすると、実行中のスクリプトの最初の行にジャンプします。

[Hive バッチ ジョブの実行](#submit-hive-batch-scripts)と比較すると、対話型クエリは非常に短時間しか必要としません。

## <a name="submit-hive-batch-scripts"></a>Hive バッチ スクリプトの送信

1. 新しい作業フォルダーと新しい Hive スクリプト ファイルが存在しない場合は、作成します。
2. Azure アカウントに接続して、既定のクラスターを構成していない場合は、構成します。
3. 以下のコードをコピーし、Hive ファイルに貼り付けて保存します。

    ```hiveql
    SELECT * FROM hivesampletable;
    ```
3. スクリプト エディターを右クリックし、**[HDInsight: Submit Hive Batch Script]** をクリックして Hive ジョブを送信します。 
4. 送信するクラスターを選択します。  

    Hive ジョブを送信したら、送信成功に関する情報とジョブ ID が、**[出力]** パネルに表示されます。 **Web ブラウザー**が開き、ジョブのリアルタイムのログと状態が表示されます。

   ![Hive ジョブの結果の送信](./media/hdinsight-for-vscode/submit-Hivejob-result.png)

[対話型 Hive クエリの送信](#submit-interactive-hive-queries)と比較すると、バッチ ジョブの時間はかなり長くなります。


## <a name="submit-pyspark-job"></a>PySpark ジョブの送信

1. 新しい作業フォルダーと .py 拡張子の新しいスクリプト ファイルが存在しない場合は作成します。
2. Azure アカウントに接続して、既定のクラスターを構成していない場合は、構成します。
3. 以下のコードをコピーして、スクリプト ファイルに貼り付けます。

    ```python
    from __future__ import print_function
    import sys
    from operator import add
    from pyspark.sql import SparkSession
    if __name__ == "__main__":
        spark = SparkSession\
            .builder\
            .appName("PythonWordCount")\
            .getOrCreate()
    
        lines = spark.read.text('/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv').rdd.map(lambda r: r[0])
        counts = lines.flatMap(lambda x: x.split(' '))\
                    .map(lambda x: (x, 1))\
                    .reduceByKey(add)
        output = counts.collect()
        for (word, count) in output:
            print("%s: %i" % (word, count))
        spark.stop()
    ```
4. スクリプト エディターを右クリックし、**[HDInsight: Submit PySpark Job]** をクリックします。 
5. クラスターを選択して、PySpark ジョブを送信します。 

   ![python ジョブの送信の結果](./media/hdinsight-for-vscode/submit-pythonjob-result.png) 

python ジョブを送信したら、送信ログが VSCode の **[出力]** ウィンドウに表示されます。 **Spark UI URL** と **Yarn UI URL** も表示されます。 URL を Web ブラウザーで開くと、ジョブの状態を追跡できます。


## <a name="additional-features"></a>その他の機能

HDInsight for VSCode では、以下の機能がサポートされています。

- **IntelliSense オートコンプリート**。 キーワード、メソッド、変数などの候補がポップアップ表示されます。以下に示すさまざまなオブジェクト型が、異なるアイコンで表されます。

    ![HDInsight Tools for Visual Studio Code の IntelliSense オブジェクト型](./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png)
- **IntelliSense エラー マーカー**。 言語サービスでは、Hive スクリプトの編集エラーに下線が引かれます。     
- **構文の強調表示**。 言語サービスでは、変数、キーワード、データ型、関数などを区別するために、異なる色が使用されます。 

    ![HDInsight Tools for Visual Studio Code の構文の強調表示](./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png)

## <a name="next-steps"></a>次のステップ

### <a name="demo"></a>デモ
* HDInsight for VScode: [ビデオ](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>ツールと拡張機能
* [Azure Toolkit for IntelliJ を使用して VPN を介して Spark アプリケーションをリモートでデバッグする](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Azure Toolkit for IntelliJ を使用して SSH を介して Spark アプリケーションをリモートでデバッグする](hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Hortonworks Sandbox と IntelliJ 用 HDInsight ツールを使用する](hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Azure Toolkit for Eclipse の HDInsight ツールを使用して Spark アプリケーションを作成する](hdinsight-apache-spark-eclipse-tool-plugin.md)
* [HDInsight の Spark クラスターで Zeppelin Notebook を使用する](hdinsight-apache-spark-zeppelin-notebook.md)
* [HDInsight 用の Spark クラスターの Jupyter Notebook で使用可能なカーネル](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Jupyter Notebook で外部のパッケージを使用する](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Jupyter をコンピューターにインストールして HDInsight Spark クラスターに接続する](hdinsight-apache-spark-jupyter-notebook-install-locally.md)
* [Azure HDInsight の Microsoft Power BI で Hive データを視覚化する](./hdinsight-connect-hive-power-bi.md)。
* [Zeppelin を使用して Azure HDInsight で Hive クエリを実行する](./hdinsight-connect-hive-zeppelin.md)。

### <a name="scenarios"></a>シナリオ
* [Spark と BI: HDInsight と BI ツールで Spark を使用した対話型データ分析の実行](hdinsight-apache-spark-use-bi-tools.md)
* [Spark と Machine Learning: HDInsight で Spark を使用して HVAC データを基に建物の温度を分析する](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Spark with Machine Learning: Use Spark in HDInsight to predict food inspection results (Spark と Machine Learning: HDInsight で Spark を使用して食品の検査結果を予測する)](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Spark ストリーミング: リアルタイム ストリーミング アプリケーションを作成するための HDInsight での Spark の使用](hdinsight-apache-spark-eventhub-streaming.md)
* [Website log analysis using Spark in HDInsight (HDInsight での Spark を使用した Web サイト ログ分析)](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="creating-and-running-applications"></a>アプリケーションの作成と実行
* [Scala を使用してスタンドアロン アプリケーションを作成する](hdinsight-apache-spark-create-standalone-application.md)
* [Livy を使用して Spark クラスターでジョブをリモートで実行する](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="managing-resources"></a>リソースの管理
* [Azure HDInsight での Apache Spark クラスターのリソースの管理](hdinsight-apache-spark-resource-manager.md)
* [HDInsight の Apache Spark クラスターで実行されるジョブの追跡とデバッグ](hdinsight-apache-spark-job-debugging.md)



