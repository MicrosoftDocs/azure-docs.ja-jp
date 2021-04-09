---
title: チュートリアル - Spark & Hive Tools for VSCode (Spark アプリケーション)
description: チュートリアル - Spark & Hive Tools for VSCode を使用して、Python で記述された Spark アプリケーションを開発し、サーバーレス Apache Spark プールに送信します。
services: synapse-analytics
author: jejiang
ms.author: jejiang
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: spark
ms.date: 09/03/2020
ms.openlocfilehash: e5d335ee14709ec330405419f5be8ac5fbd6ce75
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98943776"
---
# <a name="tutorial-create-an-apache-spark-applications-with-vscode-using-a-synapse-workspace"></a>チュートリアル:VSCode で Synapse ワークスペースを使用して Apache Spark アプリケーションを作成する

Apache Spark & Hive Tools for Visual Studio Code を使用する方法について説明します。 これらのツールを使用して、Apache Hive バッチ ジョブ、対話型 Hive クエリ、Apache Spark 用の PySpark スクリプトを作成して送信します。 最初に、Visual Studio Code で Spark & Hive Tools をインストールする方法について説明します。 次に、Spark & Hive Tools にジョブを送信する方法について説明します。

Spark & Hive Tools は、Visual Studio Code でサポートされているプラットフォームにインストールできます。 プラットフォームごとに以下の前提条件に注意してください。

## <a name="prerequisites"></a>前提条件

この記事の手順を完了するには、次の項目が必要です。

- サーバーレス Apache Spark プール。 サーバーレス Apache Spark プールを作成するには、[Azure portal を使用した Apache Spark プールの作成](../../synapse-analytics/quickstart-create-apache-spark-pool-portal.md)に関する記事をご覧ください。
- [Visual Studio Code](https://code.visualstudio.com/)。
- [Mono](https://www.mono-project.com/docs/getting-started/install/)。 Mono は Linux と macOS でのみ必要です。
- [Visual Studio Code 用の PySpark 対話型環境](../../hdinsight/set-up-pyspark-interactive-environment.md)。
- ローカル ディレクトリ。 この記事では、**C:\HD\Synaseexample** を使用します。

## <a name="install-spark--hive-tools"></a>Spark & Hive Tools をインストールする

前提条件を満たしたら、これらの手順に従って、Spark & Hive Tools for Visual Studio Code をインストールできます。

1. Visual Studio Code を開きます。

2. メニュー バーから **[View]\(表示\)**  >  **[Extensions]\(拡張機能\)** に移動します。

3. 検索ボックスに、「**Spark & Hive**」と入力します。

4. 検索結果から **Spark & Hive Tools** を選び、 **[インストール]** を選択します。

     ![Spark & Hive for Visual Studio Code の Python のインストール](./media/vscode-tool-synapse/install-hdInsight-plugin.png)

5. 必要に応じて **[再読み込み]** を選択します。

## <a name="open-a-work-folder"></a>作業フォルダーを開く

作業フォルダーを開いて Visual Studio Code でファイルを作成するには、これらの手順を実行します。

1. メニュー バーから、 **[ファイル]**  >  **[フォルダーを開く]**  > **C:\HD\Synaseexample** に移動し、 **[フォルダーの選択]** ボタンを選択します。 左側の **[Explorer]\(エクスプローラー\)** ビューにフォルダーが表示されます。

2. **[エクスプローラー]** ビューで **Synaseexample** フォルダーを選択し、作業フォルダーの横にある **[新しいファイル]** アイコンを選択します。

     ![Visual Studio Code の [新しいファイル] アイコン](./media/vscode-tool-synapse/visual-studio-code-new-file.png)

3. `.py` (Spark スクリプト) ファイル拡張子を使用して新しいファイルに名前を付けます。 この例では、**HelloWorld.py** を使用します。

## <a name="connect-to-your-spark-pools"></a>Spark プールに接続する

Azure サブスクリプションにサインインして、Spark プールに接続します。

### <a name="sign-in-to-your-azure-subscription"></a>Azure サブスクリプションにサインインします。

Azure に接続するには、これらの手順に従います。

1. メニュー バーから、 **[表示]**  >  **[コマンド パレット...]** に移動し、「**Azure:Sign In**」と入力します。

     ![Spark & Hive Tools for Visual Studio Code のログイン](./media/vscode-tool-synapse/hdinsight-for-vscode-extension-login.png)

2. サインインの手順に従って Azure にサインインします。 接続されると、Visual Studio Code ウィンドウの下部にあるステータス バーに Azure アカウント名が表示されます。

## <a name="set-the-default-spark-pool"></a>既定の Spark プールを設定する

1. [前に](#open-a-work-folder)説明した **Synaseexample** フォルダーを閉じていた場合、もう一度開きます。  

2. [前に](#open-a-work-folder)作成した **HelloWorld.py** ファイルを選択します。 それがスクリプト エディターで開かれます。

3. スクリプト エディターを右クリックし、 **[Synapse: Set default Spark pool]\(既定の Spark プールを設定\)** を選択します。  

4. Azure アカウントに[接続](#connect-to-your-spark-pools)します (まだ接続していない場合)。

5. 現在のスクリプト ファイルの既定の Spark プールにする Spark プールを選択します。 ツールによって、構成ファイルである **.VSCode\settings.json** が自動的に更新されます。

     ![既定のクラスター構成を設定する](./media/vscode-tool-synapse/set-default-cluster-configuration.png)

## <a name="submit-interactive-synapse-pyspark-queries-to-spark-pool"></a>対話型 Synapse PySpark クエリを Spark プールに送信する

ユーザーは、次の方法で Spark プール上の Synapse PySpark Interactive を実行できます。

### <a name="using-the-synapse-pyspark-interactive-command-in-py-file"></a>PY ファイルで Synapse PySpark Interactive コマンドを使用する
PySpark Interactive コマンドを使用してクエリを送信するには、次の手順を実行します。

1. [前に](#open-a-work-folder)説明した **Synaseexample** フォルダーを閉じていた場合、もう一度開きます。  

2. [前の](#open-a-work-folder)手順に従って、新しい **HelloWorld.py** ファイルを作成します。

3. 次のコードをコピーしてスクリプト ファイルに貼り付けます。

```python
import sys
from operator import add
from pyspark.sql import SparkSession, Row
 
spark = SparkSession\
 .builder\
 .appName("PythonWordCount")\
 .getOrCreate()
 
data = [Row(col1='pyspark and spark', col2=1), Row(col1='pyspark', col2=2), Row(col1='spark vs hadoop', col2=2), Row(col1='spark', col2=2), Row(col1='hadoop', col2=2)]
df = spark.createDataFrame(data)
lines = df.rdd.map(lambda r: r[0])
 
counters = lines.flatMap(lambda x: x.split(' ')) \
 .map(lambda x: (x, 1)) \
 .reduceByKey(add)
 
output = counters.collect()
sortedCollection = sorted(output, key = lambda r: r[1], reverse = True)
 
for (word, count) in sortedCollection:
 print("%s: %i" % (word, count))
```

4. PySpark/Synapse Pyspark カーネルをインストールするためのプロンプトが、ウィンドウの右下隅に表示されます。 PySpark/Synapse Pyspark のインストールを続行するには **[インストール]** ボタンをクリックします。この手順をスキップするには、 **[スキップ]** をクリックします。

     ![PySpark カーネルをインストールする](./media/vscode-tool-synapse/install-the-pyspark-kernel.png)

5. 後でインストールする必要がある場合は、 **[ファイル]**  >  **[基本設定]**  >  **[設定]** に移動し、 **[Hdinsight:Pyspark のインストールをスキップする]** をオフにします。 
    
     ![Pyspark のインストールのスキップを有効にする](./media/vscode-tool-synapse/enable-skip-pyspark-installation.png)

6. 手順 4 でインストールが正常に完了した場合、ウィンドウの右下隅に "PySpark/Synapse Pyspark が正常にインストールされました" というメッセージ ボックスが表示されます。 **[再読み込み]** ボタンをクリックして、ウィンドウを再度読み込みます。

     ![正常にインストールされた PySpark](./media/vscode-tool-synapse/pyspark-kernel-installed-successfully.png)

7. メニュー バーから、 **[表示]**  >  **[コマンド パレット]** に移動するか、**Shift + Ctrl + P** キーボード ショートカットを使用して、「**Python: Select Interpreter to start Jupyter Server**」と入力します。

     ![インタープリターを選択して Jupyter サーバーを起動する](./media/vscode-tool-synapse/select-interpreter-to-start-jupyter-server.png)

8. 下の Python オプションを選択します。

     ![下のオプションを選択する](./media/vscode-tool-synapse/choose-the-below-option.png)
    
9. メニュー バーから、 **[表示]**  >  **[コマンド パレット]** に移動するか、**Shift + Ctrl + P** キーボード ショートカットを使用して、「**Developer: Reload Window**」と入力します。

     ![ウィンドウを再度読み込む](./media/vscode-tool-synapse/reload-window.png)

10. Azure アカウントに[接続](#connect-to-your-spark-pools)します (まだ接続していない場合)。

11. すべてのコードを選択し、スクリプト エディターを右クリックして、 **[Synapse: Pyspark Interactive]** を選択してクエリを送信します。 

     ![PySpark Interactive のコンテキスト メニュー](./media/vscode-tool-synapse/pyspark-interactive-right-click.png)

12. 既定の Spark プールを指定していない場合は、Spark プールを選択します。 しばらくすると、**Python Interactive** の結果が新しいタブに表示されます。PySpark をクリックし、カーネルを **Synapse PySpark** に切り替えてから、選択したコードをもう一度送信します。コードは正常に実行されます。 また、このツールでは、コンテキスト メニューを使用して、スクリプト ファイル全体ではなく、コードのブロックを送信することもできます。

     ![対話型](./media/vscode-tool-synapse/pyspark-interactive-python-interactive-window.png)

### <a name="perform-interactive-query-in-py-file-using-a--comment"></a>#%% コメントを使用して PY ファイルで対話型クエリを実行する

1. ノートブックのエクスペリエンスを得るには、コードの前に **#%%** を追加します。

     ![#%% を追加する](./media/vscode-tool-synapse/run-cell.png)

2. **[セルの実行]** をクリックします。 しばらくすると、新しいタブに Python Interactive の結果が表示されます。PySpark をクリックしてカーネルを **Synapse PySpark** に切り替えてから、 **[セルの実行]** を再度クリックすると、コードが正常に実行されます。 

     ![セルの実行結果](./media/vscode-tool-synapse/run-cell-get-results.png)

## <a name="leverage-ipynb-support-from-python-extension"></a>Python 拡張機能の IPYNB サポートを活用する

1. コマンド パレットからコマンドを使用するか、ワークスペースで新しい .ipynb ファイルを作成することで、Jupyter Notebook を作成できます。 詳細については、「[Visual Studio Code での Jupyter Notebook の使用](https://code.visualstudio.com/docs/python/jupyter-support)」を参照してください。

2. **[セルの実行]** ボタンをクリックし、画面の指示に従って **既定の Spark プールを設定** します (ノートブックを開く前に毎回既定のクラスターまたはプールを設定することを強くお勧めします)。その後、ウィンドウを **再度読み込みます**。

     ![既定の Spark プールを設定して再度読み込む](./media/vscode-tool-synapse/set-the-default-spark-pool-and-reload.png)

3. [PySpark] をクリックしてカーネルを **Synapse Pyspark** に切り替え、 **[セルの実行]** をクリックすると、しばらくしてから結果が表示されます。

     ![ipynb の実行結果](./media/vscode-tool-synapse/run-ipynb-file-results.png)


> [!NOTE]
>
>1. この拡張では 2020.5.78807 バージョン以降の ms-python はサポートされていません。これは[既知の問題](#known-issues)です。
>  
>2. Synapse Pyspark カーネルに切り替えて、Azure Portal で自動設定を無効にすることをお勧めします。 そうしないと、クラスターをウェイクアップし、初めて使用するために Synapse カーネルを設定するのに時間がかかることがあります。 
>
>    ![自動設定](./media/vscode-tool-synapse/auto-settings.png)

## <a name="submit-pyspark-batch-job-to-spark-pool"></a>PySpark バッチ ジョブを Spark プールに送信する

1. [前に](#open-a-work-folder)説明した **Synaseexample** フォルダーを閉じていた場合、もう一度開きます。  

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

4. Azure アカウントに[接続](#connect-to-your-spark-pools)します (まだ接続していない場合)。

5. スクリプト エディターを右クリックし、 **[Synapse: PySpark Batch]** を選択します。

6. PySpark ジョブの送信先となる Spark プールを選択します。

     ![Python ジョブの送信の結果の出力](./media/vscode-tool-synapse/submit-pythonjob-result.png)

Spark プールにバッチ ジョブを送信すると、Visual Studio Code の **[出力]** ウィンドウに送信ログが表示されます。 **Spark UI** の URL と **Spark ジョブ アプリケーション UI** の URL も表示されます。 Web ブラウザーで URL を開いて、ジョブの状態を追跡することができます。

## <a name="access-and-manage-synapse-workspace"></a>Synapse ワークスペースへアクセスして管理する

Spark & Hive tools for VSCode 内の Azure Explorer では、さまざまな操作を実行できます。 Azure Explorer から。

![Azure のイメージ](./media/vscode-tool-synapse/azure.png)

### <a name="launch-workspace"></a>ワークスペースを起動する

1. Azure Explorer で **[SYNAPSE]** に移動し、それを展開して、Synapse サブスクリプションの一覧を表示します。

     ![Synapse エクスプローラー](./media/vscode-tool-synapse/synapse-explorer.png)

2. Synapse ワークスペースの [サブスクリプション] をクリックし、それを展開して、ワークスペースの一覧を表示します。

3. ワークスペースを右クリックし、 **[View Apache Spark applications]\(Apache Spark アプリケーションの表示\)** を選択すると、Synapse Studio Web サイトの Apache Spark アプリケーション ページが開きます。

     ![ワークスペースを右クリックする](./media/vscode-tool-synapse/right-click-on-workspace.png)

     ![Synapse Studio アプリケーション](./media/vscode-tool-synapse/synapse-studio-application.png)

4. ワークスペースを展開すると、 **[既定のストレージ]** と **[Spark Pools]\(Spark プール\)** が表示されます。

5. **[既定のストレージ]** を右クリックすると、 **[完全パスのコピー]** と **[Open in Synapse Studio]\(Synapse Studio で開く\)** が表示されます。 

     ![既定のストレージを右クリックする](./media/vscode-tool-synapse/right-click-on-default-storage.png)

     - **[完全パスのコピー]** をクリックすると、プライマリ ADLS Gen2 アカウントの URL がコピーされ、必要な場所に貼り付けることができます。

     - **[Open in Synapse Studio]\(Synapse Studio で開く\)** をクリックすると、プライマリ ストレージ アカウントが Synapse Studio で開かれます。

     ![Synapse Studio の既定のストレージ](./media/vscode-tool-synapse/default-storage-in-synapse-studio.png)

6. **[既定のストレージ]** を展開すると、プライマリ ストレージ アカウントが表示されます。

7. **[Spark Pools]\(Spark プール\)** を展開すると、ワークスペース内のすべての Spark プールが表示されます。

     ![ストレージ プールを展開する](./media/vscode-tool-synapse/expand-storage-pool.png)


## <a name="known-issues"></a>既知の問題

### <a name="ms-python-2020578807-version-is-not-supported-on-this-extention"></a>この拡張では 2020.5.78807 バージョン以降の ms-python はサポートされていません 

"Jupyter Notebook に接続できませんでした。" は、Python バージョン 2020.5.78807 以降の既知の問題です。 この問題を回避するには、 **[2020.4.76186](https://github.com/microsoft/vscode-python/releases/download/2020.4.76186/ms-python-release.vsix)** バージョンの ms-python を使用することをお勧めします。

![既知の問題](./media/vscode-tool-synapse/known-issue.png)


## <a name="next-steps"></a>次のステップ

- [Azure Synapse Analytics](../overview-what-is.md)
- [Azure Synapse Analytics ワークスペース用の新しい Apache Spark プールを作成する](../../synapse-analytics/quickstart-create-apache-spark-pool-studio.md)
