---
title: Azure HDInsight for Visual Studio Code
description: Spark & Hive Tools (Azure HDInsight) for Visual Studio Code を使用する方法について説明します。 これらのツールを使用して、クエリとスクリプトを作成して送信します。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 04/07/2020
ms.custom: devx-track-python
ms.openlocfilehash: bf31ad3311d6cbfd82ad1071d28bb7fee1bb9d2b
ms.sourcegitcommit: dea88d5e28bd4bbd55f5303d7d58785fad5a341d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2020
ms.locfileid: "87876786"
---
# <a name="use-spark--hive-tools-for-visual-studio-code"></a>Spark & Hive Tools for Visual Studio Code を使用する

Apache Spark & Hive Tools for Visual Studio Code を使用する方法について説明します。 これらのツールを使用して、Apache Hive バッチ ジョブ、対話型 Hive クエリ、Apache Spark 用の PySpark スクリプトを作成して送信します。 最初に、Visual Studio Code で Spark & Hive Tools をインストールする方法について説明します。 次に、Spark & Hive Tools にジョブを送信する方法について説明します。  

Spark & Hive Tools は、Visual Studio Code でサポートされているプラットフォームにインストールできます。 プラットフォームごとに以下の前提条件に注意してください。

## <a name="prerequisites"></a>前提条件

この記事の手順を完了するには、次の項目が必要です。

- Azure HDInsight クラスター。 クラスターを作成するには、[HDInsight での Hadoop の使用](hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md)に関するページをご覧ください。 または、Apache Livy エンドポイントをサポートする Spark および Hive クラスターを使用します。
- [Visual Studio Code](https://code.visualstudio.com/)。
- [Mono](https://www.mono-project.com/docs/getting-started/install/)。 Mono は Linux と macOS でのみ必要です。
- [Visual Studio Code 用の PySpark 対話型環境](set-up-pyspark-interactive-environment.md)。
- ローカル ディレクトリ。 この記事では、**C:\HD\HDexample** を使用します。

## <a name="install-spark--hive-tools"></a>Spark & Hive Tools をインストールする

前提条件を満たしたら、これらの手順に従って、Spark & Hive Tools for Visual Studio Code をインストールできます。

1. Visual Studio Code を開きます。

2. メニュー バーから **[View]\(表示\)**  >  **[Extensions]\(拡張機能\)** に移動します。

3. 検索ボックスに、「**Spark & Hive**」と入力します。

4. 検索結果から **Spark & Hive Tools** を選び、 **[インストール]** を選択します。

   ![Spark & Hive for Visual Studio Code の Python のインストール](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

5. 必要に応じて **[再読み込み]** を選択します。

## <a name="open-a-work-folder"></a>作業フォルダーを開く

作業フォルダーを開いて Visual Studio Code でファイルを作成するには、これらの手順を実行します。

1. メニュー バーから、 **[ファイル]**  >  **[フォルダーを開く...]**  > **C:\HD\HDexample** に移動し、 **[フォルダーの選択]** ボタンを選択します。 左側の **[Explorer]\(エクスプローラー\)** ビューにフォルダーが表示されます。

2. **[エクスプローラー]** ビューで **HDexample** フォルダーを選択し、作業フォルダーの横にある **[新しいファイル]** アイコンを選択します。

   ![Visual Studio Code の [新しいファイル] アイコン](./media/hdinsight-for-vscode/visual-studio-code-new-file.png)

3. `.hql` (Hive クエリ) または `.py` (Spark スクリプト) のいずれかのファイル拡張子を使用して、新しいファイルに名前を付けます。 この例では **HelloWorld.hql** を使用します。

## <a name="set-the-azure-environment"></a>Azure 環境を設定する

国内のクラウド ユーザーの場合は、まずこれらの手順に従って Azure 環境を設定してから、**Azure: Sign In** コマンドを使用して Azure にサインインします。

1. **[ファイル]**  >  **[ユーザー設定]**  >  **[設定]** に移動します。
2. 次の文字列を検索します: **Azure:クラウド**。
3. 一覧から国内のクラウドを選択します。

   ![既定のログイン エントリの構成を設定する](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)

## <a name="connect-to-an-azure-account"></a>Azure アカウントに接続する

Visual Studio Code からクラスターにスクリプトを送信する前に、ユーザーは Azure サブスクリプションにサインインするか、[HDInsight クラスターをリンクする](#link-a-cluster)ことができます。 Ambari のユーザー名とパスワードまたは ESP クラスターのドメイン参加済みの資格情報を使用して、HDInsight クラスターに接続します。 Azure に接続するには、これらの手順に従います。

1. メニュー バーから、 **[表示]**  >  **[コマンド パレット...]** に移動し、「**Azure:Sign In**」と入力します。

    ![Spark & Hive Tools for Visual Studio Code のログイン](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)

2. サインインの手順に従って Azure にサインインします。 接続されると、Visual Studio Code ウィンドウの下部にあるステータス バーに Azure アカウント名が表示されます。  

## <a name="link-a-cluster"></a>クラスターのリンク

### <a name="link-azure-hdinsight"></a>リンク: Azure HDInsight

[Apache Ambari](https://ambari.apache.org/) マネージド ユーザー名を使用して通常のクラスターをリンクするか、またはドメイン ユーザー名 (`user1@contoso.com` など) を使用して Enterprise Security Pack のセキュリティ保護された Hadoop クラスターをリンクできます。

1. メニュー バーから、 **[表示]**  >  **[コマンド パレット...]** に移動し、「**Spark / Hive:Link a Cluster**」と入力します。

   ![コマンド パレットの link cluster コマンド](./media/hdinsight-for-vscode/link-cluster-command.png)

2. リンクされるクラスターの種類として **[Azure HDInsight]** を選択します。

3. HDInsight クラスターの URL を入力します。

4. 自分の Ambari ユーザー名を入力します。既定値は「**admin**」です。

5. 自分の Ambari パスワードを入力します。

6. クラスターの種類を選択します。

7. クラスターの表示名を設定します (省略可能)。

8. **[OUTPUT]\(出力\)** ビューを確認します。

   > [!NOTE]  
   > リンクされたユーザー名とパスワードは、クラスターが Azure サブスクリプションにログインし、かつクラスターにリンクしていた場合に使用されます。  

### <a name="link-generic-livy-endpoint"></a>リンク: ジェネリック Livy エンドポイント

1. メニュー バーから、 **[表示]**  >  **[コマンド パレット...]** に移動し、「**Spark / Hive:Link a Cluster**」と入力します。

2. リンクされるクラスターの種類として、 **[Generic Livy Endpoint]\(ジェネリック Livy エンドポイント\)** を選択します。

3. ジェネリック Livy エンドポイントを入力します。 例: http\://10.172.41.42:18080。

4. 承認の種類として、 **[基本]** または **[なし]** を選択します。  **[基本]** を選択する場合:  
    &emsp;a. 自分の Ambari ユーザー名を入力します。既定値は「**admin**」です。  
    &emsp;b. 自分の Ambari パスワードを入力します。

5. **[OUTPUT]\(出力\)** ビューを確認します。

## <a name="list-clusters"></a>クラスターを一覧表示する

1. メニュー バーから、 **[表示]**  >  **[コマンド パレット...]** に移動し、「**Spark / Hive:List Cluster**」と入力します。

2. 目的のサブスクリプションを選択します。

3. **[OUTPUT]\(出力\)** ビューを確認します。 このビューには、リンクされたクラスターと、お使いの Azure サブスクリプションのすべてのクラスターが表示されます。

    ![既定のクラスター構成を設定する](./media/hdinsight-for-vscode/list-cluster-result1.png)

## <a name="set-the-default-cluster"></a>既定のクラスターを設定する

1. [前に](#open-a-work-folder)説明した **HDexample** フォルダーを閉じていた場合、もう一度開きます。  

2. [前に](#open-a-work-folder)作成した **HelloWorld.hql** ファイルを選択します。 それがスクリプト エディターで開かれます。

3. スクリプト エディターを右クリックして、 **[Spark / Hive: Set Default Cluster]\(Spark / Hive: 既定のクラスターの設定\)** を選択します。  

4. お使いの Azure アカウントに[接続](#connect-to-an-azure-account)するか、クラスターをリンクします (まだ行っていない場合)。

5. 現在のスクリプト ファイルの既定のクラスターとしてクラスターを選択します。 ツールによって、構成ファイルである **.VSCode\settings.json** が自動的に更新されます。

   ![既定のクラスター構成を設定する](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)

## <a name="submit-interactive-hive-queries-and-hive-batch-scripts"></a>対話型 Hive クエリと Hive バッチ スクリプトを送信する

Spark & Hive Tools for Visual Studio Code を使用すると、対話型 Hive クエリと Hive バッチ スクリプトをクラスターに送信できます。

1. [前に](#open-a-work-folder)説明した **HDexample** フォルダーを閉じていた場合、もう一度開きます。  

2. [前に](#open-a-work-folder)作成した **HelloWorld.hql** ファイルを選択します。 それがスクリプト エディターで開かれます。

3. 次のコードをコピーし、Hive ファイルに貼り付けて保存します。

    ```hiveql
    SELECT * FROM hivesampletable;
    ```

4. お使いの Azure アカウントに[接続](#connect-to-an-azure-account)するか、クラスターをリンクします (まだ行っていない場合)。

5. スクリプト エディターを右クリックし、 **[Hive: Interactive]** を選択してクエリを送信するか、キーボード ショートカットの Ctrl + Alt + I を使用します。  **[Hive:Batch]** を選択してスクリプトを送信するか、キーボード ショートカットの Ctrl + Alt + H を使用します。  

6. 既定のクラスターを指定していない場合は、クラスターを選択します。 また、このツールでは、コンテキスト メニューを使用して、スクリプト ファイル全体ではなく、コードのブロックを送信することもできます。 しばらくすると、新しいタブにクエリの結果が表示されます。

   ![対話型 Apache Hive クエリの結果](./media/hdinsight-for-vscode/interactive-hive-result.png)

    - **[結果]** パネル:結果全体を CSV、JSON、または Excel ファイルとしてローカル パスに保存したり、複数の行だけを選択したりできます。

    - **[メッセージ]** パネル:**行**の番号を選択すると、実行されているスクリプトの最初の行にジャンプします。

## <a name="submit-interactive-pyspark-queries"></a>対話型の PySpark クエリを送信する

ユーザーは、次の方法で PySpark Interactive を実行できます。

### <a name="using-the-pyspark-interactive-command-in-py-file"></a>PY ファイルで PySpark Interactive コマンドを使用する
PySpark Interactive コマンドを使用してクエリを送信するには、次の手順を実行します。

1. [前に](#open-a-work-folder)説明した **HDexample** フォルダーを閉じていた場合、もう一度開きます。  

2. [前の](#open-a-work-folder)手順に従って、新しい **HelloWorld.py** ファイルを作成します。

3. 次のコードをコピーしてスクリプト ファイルに貼り付けます。

   ```python
   from operator import add
   lines = spark.read.text("/HdiSamples/HdiSamples/FoodInspectionData/README").rdd.map(lambda r: r[0])
   counters = lines.flatMap(lambda x: x.split(' ')) \
                .map(lambda x: (x, 1)) \
                .reduceByKey(add)

   coll = counters.collect()
   sortedCollection = sorted(coll, key = lambda r: r[1], reverse = True)

   for i in range(0, 5):
        print(sortedCollection[i])
   ```

4. PySpark カーネルをインストールするためのプロンプトが、ウィンドウの右下隅に表示されます。 PySpark のインストールを続行するには **[インストール]** ボタンをクリックします。この手順をスキップするには、 **[スキップ]** をクリックします。

   ![PySpark カーネルをインストールする](./media/hdinsight-for-vscode/install-the-pyspark-kernel.png)

5. 後でインストールする必要がある場合は、 **[ファイル]**  >  **[基本設定]**  >  **[設定]** に移動し、 **[Hdinsight:Pyspark のインストールをスキップする]** をオフにします。 
    
    ![PySpark カーネルをインストールする](./media/hdinsight-for-vscode/enable-skip-pyspark-installation.png)

6. 手順 4 でインストールが正常に完了した場合、ウィンドウの右下隅に "PySpark が正常にインストールされました" というメッセージ ボックスが表示されます。 **[再読み込み]** ボタンをクリックして、ウィンドウを再度読み込みます。
    ![正常にインストールされた PySpark](./media/hdinsight-for-vscode/pyspark-kernel-installed-successfully.png)

7. お使いの Azure アカウントに[接続](#connect-to-an-azure-account)するか、クラスターをリンクします (まだ行っていない場合)。

8. すべてのコードを選択し、スクリプト エディターを右クリックして、 **[Spark: PySpark Interactive]** を選択してクエリを送信します。 または、Ctrl + Alt + I ショートカットを使用します。

    ![PySpark Interactive のコンテキスト メニュー](./media/hdinsight-for-vscode/pyspark-interactive-right-click.png)

9. 既定のクラスターを指定していない場合は、クラスターを選択します。 しばらくすると、**Python Interactive** の結果が新しいタブに表示されます。PySpark をクリックすると、カーネルが **PySpark** に切り替わり、コードが正常に実行されます。 また、このツールでは、コンテキスト メニューを使用して、スクリプト ファイル全体ではなく、コードのブロックを送信することもできます。

   ![PySpark Interactive の Python Interactive ウィンドウ](./media/hdinsight-for-vscode/pyspark-interactive-python-interactive-window.png)

10. 「 **%%info**」と入力し、Shift + Enter キーを押してジョブ情報を表示します (省略可能)。

    ![PySpark Interactive: ジョブ情報の表示](./media/hdinsight-for-vscode/pyspark-interactive-view-job-information.png)

ツールでは **Spark SQL** クエリもサポートされています。

   ![PySpark Interactive: 結果の表示](./media/hdinsight-for-vscode/pyspark-ineteractive-select-result.png)


### <a name="perform-interactive-query-in-py-file-using-a--comment"></a>#%% コメントを使用して PY ファイルで対話型クエリを実行する

1. ノートブックのエクスペリエンスを得るには、PY コードの前に **#%%** を追加します。

    ![#%% を追加する](./media/hdinsight-for-vscode/run-cell.png)

2. **[セルの実行]** をクリックします。 しばらくすると、新しいタブに Python Interactive の結果が表示されます。

   ![セルの実行結果](./media/hdinsight-for-vscode/run-cell-get-results.png)

   > [!NOTE]  
   > カーネルまたは設定が混雑した場合は、**Python:インタープリターを選択して Jupyter サーバーを起動する** コマンドを使用し、**IPython カーネルを再起動**してから、VSCode を再読み込みすると、解決できます。

## <a name="leverage-ipynb-support-from-python-extension"></a>Python 拡張機能の IPYNB サポートを活用する

1. コマンド パレットからコマンドを使用するか、ワークスペースで新しい .ipynb ファイルを作成することで、Jupyter Notebook を作成できます。 詳細については、「[Visual Studio Code での Jupyter Notebook の使用](https://code.visualstudio.com/docs/python/jupyter-support)」を参照してください。

2. [PySpark] をクリックしてカーネルを **PySpark** に切り替え、 **[セルの実行]** をクリックすると、しばらくしてから結果が表示されます。

   ![ipynb の実行結果](./media/hdinsight-for-vscode/run-ipynb-file-results.png)


> [!NOTE]
>
>この拡張では 2020.5.78807 バージョン以降の ms-python はサポートされていません。これは[既知の問題](#known-issues)です。

## <a name="submit-pyspark-batch-job"></a>PySpark バッチ ジョブを送信する

1. [前に](#open-a-work-folder)説明した **HDexample** フォルダーを閉じていた場合、もう一度開きます。  

2. [前の](#open-a-work-folder)手順に従って、新しい **BatchFile.py** ファイルを作成します。

3. 次のコードをコピーしてスクリプト ファイルに貼り付けます。

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

4. お使いの Azure アカウントに[接続](#connect-to-an-azure-account)するか、クラスターをリンクします (まだ行っていない場合)。

5. スクリプト エディターを右クリックし、 **[Spark:PySpark Batch]** を選択するか、キーボード ショートカットの Ctrl + Alt + H を使用します。

6. PySpark ジョブの送信先となるクラスターを選択します。

   ![Python ジョブの送信の結果の出力](./media/hdinsight-for-vscode/submit-pythonjob-result.png)

Python ジョブを送信すると、Visual Studio Code の **[OUTPUT]\(出力\)** ウィンドウに送信ログが表示されます。 Spark UI URL と Yarn UI URL も表示されます。 Web ブラウザーで URL を開いて、ジョブの状態を追跡することができます。

## <a name="integrate-with-hdinsight-identity-broker-hib"></a>HDInsight Identity Broker (HIB) との統合

### <a name="connect-to-your-hdinsight-esp-cluster-with-id-broker-hib"></a>HDInsight ESP cluster with ID Broker (HIB) に接続する

HDInsight ESP cluster with ID Broker (HIB) に接続するには、通常の手順に従って Azure サブスクリプションにサインインします。 サインインすると、Azure Explorer にクラスターの一覧が表示されます。 詳細な手順については、「[HDInsight クラスターに接続する](#connect-to-an-azure-account)」を参照してください。

### <a name="run-a-hivepyspark-job-on-an-hdinsight-esp-cluster-with-id-broker-hib"></a>HDInsight ESP cluster with ID Broker (HIB) で Hive/PySpark ジョブを実行する

Hive ジョブを実行するには、通常の手順に従って、HDInsight ESP cluster with ID Broker (HIB) にジョブを送信します。 詳細な手順については、「[対話型 Hive クエリと Hive バッチ スクリプトを送信する](#submit-interactive-hive-queries-and-hive-batch-scripts)」を参照してください。

対話型 PySpark ジョブを実行するには、通常の手順に従って、HDInsight ESP cluster with ID Broker (HIB) にジョブを送信します。 詳細な手順については、「[「対話型の PySpark クエリを送信する](#submit-interactive-pyspark-queries)」を参照してください。

PySpark バッチ ジョブを実行するには、通常の手順に従って、HDInsight ESP cluster with ID Broker (HIB) にジョブを送信します。 詳細な手順については、「[PySpark バッチ ジョブを送信する](#submit-pyspark-batch-job)」を参照してください。


## <a name="apache-livy-configuration"></a>Apache Livy の構成

[Apache Livy](https://livy.incubator.apache.org/) の構成がサポートされています。 これは、ワークスペース フォルダーにある **.VSCode\settings.json** ファイルで構成できます。 現在、Livy の構成では Python スクリプトのみがサポートされています。 詳細については、[Livy の README](https://github.com/cloudera/livy/blob/master/README.rst ) を参照してください。

<a id="triggerlivyconf"></a>**Livy の構成をトリガーする方法**

方法 1  
1. メニュー バーから **[File]\(ファイル\)**  >  **[Preferences]\(基本設定\)**  >  **[Settings]\(設定\)** に移動します。
2. **[検索設定]** ボックスに「**HDInsight Job Submission: Livy Conf**」と入力します。  
3. 関連する検索結果に対して **[Edit in settings.json]\(settings.json で編集\)** を選択します。

方法 2: ファイルを送信し、`.vscode` フォルダーが作業フォルダーに自動的に追加されることに注意してください。 Livy の構成は **.vscode\settings.json** を選択することで確認できます。

+ プロジェクトの設定:

    ![HDInsight Apache Livy の構成](./media/hdinsight-for-vscode/hdi-apache-livy-config.png)

    >[!NOTE]
    >**driverMemory** と **executorMemory** の設定では、値と単位を設定します。 次に例を示します。1 g、1,024 m。

+ サポートされている Livy の構成:

    **POST/バッチ**要求本文

    | name | description | type |
    | --- | --- | --- |
    | file | 実行するアプリケーションを含むファイル | パス (必須) |
    | proxyUser | ジョブの実行時に権限を借用するユーザー | String |
    | className | アプリケーション Java/Spark のメイン クラス | String |
    | args | アプリケーションのコマンドライン引数 | 文字列のリスト |
    | jars | このセッションで使用される Jar | 文字列のリスト | 
    | pyFiles | このセッションで使用される Python ファイル | 文字列のリスト |
    | files | このセッションで使用されるファイル | 文字列のリスト |
    | driverMemory | ドライバー プロセスに使用するメモリの量 | String |
    | driverCores | ドライバー プロセスに使用するコアの数 | int |
    | executorMemory | 実行プログラム プロセスごとに使用するメモリの量 | String |
    | executorCores | 実行プログラムごとに使用するコアの数 | int |
    | numExecutors | このセッションで起動する実行プログラムの数 | int |
    | archives | このセッションで使用するアーカイブ | 文字列のリスト |
    | queue | 送信先の YARN キューの名前| String |
    | name | このセッションの名前 | String |
    | conf | Spark の構成プロパティ | キーのマップ = val |

    応答本文   作成された Batch オブジェクト。

    | name | description | type |
    | --- | ---| --- |
    | id | セッション ID | int |
    | appId | このセッションのアプリケーション ID | String |
    | appInfo | アプリケーションの詳細情報 | キーのマップ = val |
    | log | ログの行 | 文字列のリスト |
    | state |バッチの状態 | String |

    > [!NOTE]
    > 割り当てられた Livy の構成は、スクリプトの送信時に出力ウィンドウに表示されます。

## <a name="integrate-with-azure-hdinsight-from-explorer"></a>エクスプローラーから Azure HDInsight と統合する

**Azure HDInsight** のエクスプローラーを通じて直接、クラスター内の Hive テーブルをプレビューできます。

1. Azure アカウントに[接続](#connect-to-an-azure-account)します (まだ接続していない場合)。

2. 左端の列の **Azure** アイコンを選択します。

3. 左側のウィンドウから、 **[AZURE:HDINSIGHT]** を展開します。 利用可能なサブスクリプションとクラスターが一覧表示されます。

4. クラスターを展開して、Hive メタデータのデータベースとテーブルのスキーマを表示します。

5. Hive テーブルを右クリックします。 例: **hivesampletable**。 **[Preview]\(プレビュー\)** を選択します。

   ![Spark & Hive for Visual Studio Code の Hive テーブルのプレビュー](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-hive-table.png)

6. **[結果のプレビュー]** ウィンドウが開きます。

   ![Spark & Hive for Visual Studio Code の結果ウィンドウのプレビュー](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-results-window.png)

- [結果] パネル

   結果全体を CSV、JSON、または Excel ファイルとしてローカル パスに保存したり、複数の行だけを選択したりできます。

- [メッセージ] パネル
   1. テーブルの行数が 100 行を超えると、次のメッセージが表示されます: "The first 100 rows are displayed for Hive table (Hive テーブルの先頭から 100 行を表示しています)"。
   2. テーブル内の行数が 100 以下の場合は、次のメッセージが表示されます: "60 rows are displayed for Hive table (Hive テーブルの 60 行を表示しています)"。
   3. テーブルの内容がない場合は、次のメッセージが表示されます: "`0 rows are displayed for Hive table.`"

        >[!NOTE]
        >
        >Linux でテーブル データをコピーするには、xclip をインストールします。
        >
        >![Linux の Spark & Hive for Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-linux-install-xclip.png)

## <a name="additional-features"></a>その他の機能

Spark & Hive for Visual Studio Code では、次の機能もサポートされています。

- **IntelliSense のオートコンプリート**。 キーワード、メソッド、変数、その他のプログラミング要素の候補がポップアップ表示されます。 オブジェクトの種類ごとに異なるアイコンで表されます：

    ![Spark & Hive for Visual Studio Code の IntelliSense オブジェクト](./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png)

- **IntelliSense エラー マーカー**。 言語サービスにより、Hive スクリプトの編集エラーに下線が引かれます。     
- **構文の強調表示**。 言語サービスにより、異なる色を使用して、変数、キーワード、データ型、関数、その他のプログラミング要素が区別されます。

    ![Spark & Hive Tools for Visual Studio Code の構文の強調表示](./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png)

## <a name="reader-only-role"></a>読み取り専用ロール

クラスターに対する読み取り専用ロールが割り当てられているユーザーは、HDInsight クラスターにジョブを送信することも、Hive データベースを表示することもできません。 クラスター管理者に連絡し、[Azure portal](https://ms.portal.azure.com/) で [**HDInsight クラスター オペレーター**](https://docs.microsoft.com/azure/hdinsight/hdinsight-migrate-granular-access-cluster-configurations#add-the-hdinsight-cluster-operator-role-assignment-to-a-user)へとロールをアップグレードしてもらいます。 有効な Ambari 資格情報を持っている場合は、次のガイダンスに従って、クラスターを手動でリンクすることができます。

### <a name="browse-the-hdinsight-cluster"></a>HDInsight クラスターを参照する  

Azure HDInsight のエクスプローラー上で選択して HDInsight クラスターを展開したときに、そのクラスターに対して読み取り専用ロールを持っている場合は、クラスターをリンクするよう求められます。 自分の Ambari 資格情報を使用してクラスターにリンクするには、次の方法を使用します。

### <a name="submit-the-job-to-the-hdinsight-cluster"></a>HDInsight クラスターにジョブを送信する

HDInsight クラスターにジョブを送信するときに、そのクラスターに対して読み取り専用ロールを持っている場合は、クラスターをリンクするよう求められます。 Ambari 資格情報を使用してクラスターにリンクするには、次の手順を使用します。

### <a name="link-to-the-cluster"></a>クラスターにリンクする

1. 有効な Ambari ユーザー名を入力します。
2. 有効なパスワードを入力します。

   ![Spark & Hive Tools for Visual Studio Code のユーザー名](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-username.png)

   ![Spark & Hive Tools for Visual Studio Code のパスワード](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-password.png)

  > [!NOTE]
  >
  >`Spark / Hive: List Cluster` を使用して、リンクされたクラスターを確認できます。
  >
  >![Spark & Hive Tools for Visual Studio Code のリンクされた閲覧者](./media/hdinsight-for-vscode/list-cluster-result1.png)

## <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

### <a name="browse-a-data-lake-storage-gen2-account"></a>Data Lake Storage Gen2 アカウントを参照する

Azure HDInsight エクスプローラーを選択して Data Lake Storage Gen2 アカウントを展開します。 Azure アカウントに Gen2 ストレージへのアクセス権がない場合は、ストレージ アクセス キーを入力するよう求められます。 アクセス キーが検証されると、Data Lake Storage Gen2 アカウントは自動的に展開されます。

### <a name="submit-jobs-to-an-hdinsight-cluster-with-data-lake-storage-gen2"></a>Data Lake Storage Gen2 を使用して HDInsight クラスターにジョブを送信する

Data Lake Storage Gen2 を使用して HDInsight クラスターにジョブを送信します。 Azure アカウントに Gen2 ストレージへの書き込みアクセス権がない場合は、ストレージ アクセス キーを入力するよう求められます。 アクセス キーが検証されると、ジョブは正常に送信されます。

![Spark & Hive Tools for Visual Studio Code のアクセス キー](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-accesskey.png)

> [!NOTE]
>
> ストレージ アカウントのアクセス キーは Azure portal から取得できます。 詳細については、「[ストレージ アカウント アクセス キーを管理する](../storage/common/storage-account-keys-manage.md)」を参照してください。

## <a name="unlink-cluster"></a>クラスターのリンク解除

1. メニュー バーから、 **[表示]**  >  **[コマンド パレット]** の順に移動し、「**Spark / Hive: Unlink a Cluster**」と入力します。  

2. リンクを解除するクラスターを選択します。  

3. 検証のため、 **[出力]** ビューを確認します。  

## <a name="sign-out"></a>サインアウトする  

メニュー バーから、 **[表示]**  >  **[コマンド パレット]** の順に移動し、「**Azure: Sign Out**」と入力します。

## <a name="known-issues"></a>既知の問題
### <a name="ms-python-2020578807-version-is-not-supported-on-this-extention"></a>この拡張では 2020.5.78807 バージョン以降の ms-python はサポートされていません 

"Jupyter Notebook に接続できませんでした。" は、Python バージョン 2020.5.78807 以降の既知の問題です。 この問題を回避するには、 **[2020.4.76186](https://github.com/microsoft/vscode-python/releases/download/2020.4.76186/ms-python-release.vsix)** バージョンの ms-python を使用することをお勧めします。

![既知の問題](./media/hdinsight-for-vscode/known-issue.png)

## <a name="next-steps"></a>次のステップ

Spark & Hive for Visual Studio Code の使用については、[Spark & Hive for Visual Studio Code](https://go.microsoft.com/fwlink/?linkid=858706) のデモ ビデオをご覧ください。
