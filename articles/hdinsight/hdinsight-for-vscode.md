---
title: Azure HDInsight for Visual Studio Code
description: Spark & Hive Tools (Azure HDInsight) for Visual Studio Code を使用し、クエリとスクリプトを作成して送信する方法について説明します。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: 2b4d588799d72be85030e70aed58b2c8d6b0a9a3
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091744"
---
# <a name="use-spark--hive-tools-for-visual-studio-code"></a>Spark & Hive Tools for Visual Studio Code を使用する

Spark & Hive Tools for Visual Studio Code を使用して Apache Hive バッチ ジョブ、インタラクティブ Hive クエリ、および Apache Spark 用 PySpark スクリプトを作成および送信する方法について説明します。 最初に Visual Studio Code に Spark & Hive Tools をインストールする方法を説明した後、Spark & Hive Tools にジョブを送信する方法を説明します。  

Spark & Hive Tools は、Visual Studio Code でサポートされている、Windows、Linux、macOS などのプラットフォームにインストールできます。 プラットフォームごとに以下の前提条件に注意してください。

## <a name="prerequisites"></a>前提条件

この記事の手順を実行するには、次のものが必要です。

- Azure HDInsight クラスター。 クラスターを作成するには、[HDInsight での Hadoop の使用](hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md)に関するページをご覧ください。 または、Apache Livy エンドポイントをサポートする Spark および Hive クラスターを使用します。
- [Visual Studio Code](https://code.visualstudio.com/)。
- [Mono](https://www.mono-project.com/docs/getting-started/install/)。 Mono は Linux と macOS でのみ必要です。
- [Visual Studio Code 用の PySpark 対話型環境](set-up-pyspark-interactive-environment.md)。
- **HDexample** という名前のローカル ディレクトリ。  この記事では、**C:\HD\HDexample** を使用します。

## <a name="install-spark--hive-tools"></a>Spark & Hive Tools をインストールする

前提条件を満たしたら、これらの手順に従って、Spark & Hive Tools for Visual Studio Code をインストールできます。

1. Visual Studio Code を開きます。

2. メニュー バーから、 **[ビュー]**  >  **[拡張機能]** の順に移動します。

3. 検索ボックスに、「**Spark & Hive**」と入力します。

4. 検索結果から **Spark & Hive Tools** を選び、 **[インストール]** を選択します。

   ![Spark & Hive for Visual Studio Code の Python のインストール](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

5. 必要に応じて **[再読み込み]** を選択します。

## <a name="open-a-work-folder"></a>作業フォルダーを開く

作業フォルダーを開いて Visual Studio Code でファイルを作成するには、これらの手順を実行します。

1. メニュー バーから、 **[ファイル]**  >  **[フォルダーを開く]**  > **C:\HD\HDexample** の順に移動し、 **[フォルダーの選択]** ボタンをクリックします。 左側の **[エクスプローラー]** ビューに、フォルダーが表示されます。

2. **[エクスプローラー]** ビューで **HDexample** フォルダーを選択し、作業フォルダーの横にある **[新しいファイル]** アイコンを選択します。

   ![Visual Studio Code の [新しいファイル] アイコン](./media/hdinsight-for-vscode/visual-studio-code-new-file.png)

3. `.hql` (Hive クエリ) または `.py` (Spark スクリプト) のいずれかのファイル拡張子を使用して、新しいファイルに名前を付けます。 この例では **HelloWorld.hql** を使用します。

## <a name="set-the-azure-environment"></a>Azure 環境を設定する

国内のクラウド ユーザーの場合は、まずこれらの手順に従って Azure 環境を設定してから、**Azure: Sign In** コマンドを使用して Azure にサインインします。

1. **File\Preferences\Settings** を選択します。
2. 次の文字列を検索します: **Azure:Cloud** を検索します
3. 一覧から国内のクラウドを選択します。

   ![既定のログイン エントリの構成を設定する](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)

## <a name="connect-to-an-azure-account"></a>Azure アカウントに接続する

Visual Studio Code からクラスターにスクリプトを送信する前に、自分の Azure アカウントに接続するか、クラスターをリンクする必要があります (Apache Ambari のユーザー名とパスワード資格情報、またはドメイン参加アカウントを使用)。 Azure に接続するには、これらの手順に従います。

1. メニュー バーから、 **[ビュー]**  >  **[コマンド パレット]** の順に移動し、「**Azure: Sign In**」と入力します。

    ![Spark & Hive Tools for Visual Studio Code のログイン](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)

2. サインインの手順に従って Azure にサインインします。 接続すると、Visual Studio Code ウィンドウの下部にあるステータス バーに Azure アカウント名が表示されます。  

## <a name="link-a-cluster"></a>クラスターのリンク

### <a name="link-azure-hdinsight"></a>リンク: Azure HDInsight

[Apache Ambari](https://ambari.apache.org/) マネージド ユーザー名を使用して通常のクラスターをリンクするか、またはドメイン ユーザー名 (`user1@contoso.com` など) を使用して Enterprise Security Pack のセキュリティ保護された Hadoop クラスターをリンクできます。

1. メニュー バーから、 **[ビュー]**  >  **[コマンド パレット]** の順に移動し、「**Spark / Hive: Link a cluster**」と入力します。

   ![コマンド パレットの link cluster コマンド](./media/hdinsight-for-vscode/link-cluster-command.png)

2. リンクされるクラスターの種類として **[Azure HDInsight]** を選択します。

3. HDInsight クラスターの URL を入力します。

4. 自分の Ambari ユーザー名を入力します。既定値は「**admin**」です。

5. 自分の Ambari パスワードを入力します。

6. クラスターの種類を選択します。

7. クラスターの表示名を設定します (省略可能)。

8. 検証のため、 **[出力]** ビューを確認します。

   > [!NOTE]  
   > リンクされたユーザー名とパスワードは、クラスターが Azure サブスクリプションにログインし、かつクラスターにリンクしていた場合に使用されます。  

### <a name="link-generic-livy-endpoint"></a>リンク: ジェネリック Livy エンドポイント

1. メニュー バーから、 **[ビュー]**  >  **[コマンド パレット]** の順に移動し、「**Spark / Hive: Link a cluster**」と入力します。

2. リンクされるクラスターの種類として、 **[Generic Livy Endpoint]\(ジェネリック Livy エンドポイント\)** を選択します。

3. ジェネリック Livy エンドポイントを入力します。 例: http\://10.172.41.42:18080。

4. 承認の種類として、 **[基本]** または **[なし]** を選択します。  **[基本]** を選択する場合:  
    &emsp;a. 自分の Ambari ユーザー名を入力します。既定値は「**admin**」です。  
    &emsp;b. 自分の Ambari パスワードを入力します。

5. 検証のため、 **[出力]** ビューを確認します。

## <a name="list-clusters"></a>クラスターの一覧表示

1. メニュー バーから、 **[ビュー]**  >  **[コマンド パレット]** の順に移動し、「**Spark / Hive: List Cluster**」と入力します。

2. 目的のサブスクリプションを選択します。

3. **[出力]** ビューを確認します。 このビューには、リンクされたクラスターと、お使いの Azure サブスクリプションのすべてのクラスターが表示されます。

    ![既定のクラスター構成を設定する](./media/hdinsight-for-vscode/list-cluster-result1.png)

## <a name="set-the-default-cluster"></a>既定のクラスターを設定する

1. [前に](#open-a-work-folder)説明した **HDexample** フォルダーを閉じていた場合、もう一度開きます。  

2. [前に](#open-a-work-folder)作成した **HelloWorld.hql** ファイルを選択します。 それがスクリプト エディターで開かれます。

3. スクリプト エディターを右クリックして、 **[Spark / Hive: Set Default Cluster]** を選択します。  

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

## <a name="submit-interactive-pyspark-queries"></a>対話型 PySpark クエリの送信

対話型 PySpark クエリを送信するには、これらの手順に従います。

1. [前に](#open-a-work-folder)説明した **HDexample** フォルダーを閉じていた場合、もう一度開きます。  

2. [前の](#open-a-work-folder)手順に従って、新しい **HelloWorld.py** ファイルを作成します。

3. 以下のコードをコピーして、スクリプト ファイルに貼り付けます。

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

4. お使いの Azure アカウントに[接続](#connect-to-an-azure-account)するか、クラスターをリンクします (まだ行っていない場合)。

5. すべてのコードを選択し、スクリプト エディターを右クリックして、 **[Spark: PySpark Interactive]** を選択してクエリを送信します。 または、Ctrl + Alt + I ショートカットを使用します。

   ![PySpark Interactive のコンテキスト メニュー](./media/hdinsight-for-vscode/pyspark-interactive-right-click.png)

6. 既定のクラスターを指定していない場合は、クラスターを選択します。 しばらくすると、新しいタブに **Python Interactive** の結果が表示されます。また、このツールでは、コンテキスト メニューを使用して、スクリプト ファイル全体ではなく、コードのブロックを送信することもできます。

   ![PySpark Interactive: PySpark Interactive ウィンドウ](./media/hdinsight-for-vscode/pyspark-interactive-python-interactive-window.png) 

7. 「 **%%info**」と入力し、Shift + Enter キーを押してジョブ情報を表示します (省略可能)。

   ![PySpark Interactive: ジョブ情報の表示](./media/hdinsight-for-vscode/pyspark-interactive-view-job-information.png)

8. ツールでは **Spark SQL** クエリもサポートされています。

   ![PySpark Interactive: 結果の表示](./media/hdinsight-for-vscode/pyspark-ineteractive-select-result.png)

   送信の状態は、クエリの実行時に下部のステータス バーの左側に表示されます。 **[PySpark Kernel (busy)]\(PySpark カーネル (ビジー)\)** 状態のときに他のクエリを送信しないでください。  

   > [!NOTE]
   >
   > 設定で **[Python Extension Enabled]\(Python 拡張機能が有効\)** チェック ボックスがオフ (既定の設定ではオン) の場合、送信される PySpark 対話結果には古いウィンドウが使用されます。
   >
   > ![PySpark Interactive: Python 拡張機能が無効](./media/hdinsight-for-vscode/pyspark-interactive-python-extension-disabled.png)

## <a name="submit-pyspark-batch-job"></a>PySpark バッチ ジョブの送信

1. [前に](#open-a-work-folder)説明した **HDexample** フォルダーを閉じていた場合、もう一度開きます。  

2. [前の](#open-a-work-folder)手順に従って、新しい **BatchFile.py** ファイルを作成します。

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

4. お使いの Azure アカウントに[接続](#connect-to-an-azure-account)するか、クラスターをリンクします (まだ行っていない場合)。

5. スクリプト エディターを右クリックして、 **[Spark: PySpark Batch]** を選択するか、キーボード ショートカットの Ctrl + Alt + H を使用します。

6. PySpark ジョブの送信先となるクラスターを選択します。

   ![Python ジョブの送信の結果の出力](./media/hdinsight-for-vscode/submit-pythonjob-result.png)

Python ジョブを送信したら、Visual Studio Code の **[出力]** ウィンドウに送信ログが表示されます。 Spark UI URL と Yarn UI URL も表示されます。 URL を Web ブラウザーで開くと、ジョブの状態を追跡できます。

## <a name="apache-livy-configuration"></a>Apache Livy の構成

[Apache Livy](https://livy.incubator.apache.org/) の構成がサポートされています。 これは、ワークスペース フォルダーにある **.VSCode\settings.json** ファイルで構成できます。 現在、Livy の構成では Python スクリプトのみがサポートされています。 詳しくは、[Livy の README](https://github.com/cloudera/livy/blob/master/README.rst ) を参照してください。

<a id="triggerlivyconf"></a>**Livy の構成をトリガーする方法**

方法 1  
1. メニュー バーから、 **[ファイル]**  >  **[基本設定]**  >  **[設定]** の順に移動します。
2. **[検索設定]** ボックスに「**HDInsight Job Submission: Livy Conf**」と入力します。  
3. 関連する検索結果の **[settings.json で編集]** を選択します。

方法 2: ファイルを送信すると、.vscode フォルダーが自動的に作業フォルダーに追加されます。 Livy の構成は **.vscode\settings.json** を選択することで確認できます。

+ プロジェクトの設定:

    ![HDInsight Apache Livy の構成](./media/hdinsight-for-vscode/hdi-apache-livy-config.png)

    >[!NOTE]
    >**driverMemory** と **executorMemory** の設定では、値と単位を設定します。 例: 1 g、1,024 m。

+ サポートされている Livy の構成:

    **POST/バッチ**要求本文

    | 名前 | description | type |
    | :- | :- | :- |
    | file | 実行するアプリケーションを含むファイル | パス (必須) |
    | proxyUser | ジョブを実行するときに偽装するユーザー | string |
    | className | アプリケーションの Java/Spark のメイン クラス | string |
    | args | アプリケーションのコマンドライン引数 | 文字列のリスト |
    | jars | このセッションで使用される Jar | 文字列のリスト | 
    | pyFiles | このセッションで使用される Python ファイル | 文字列のリスト |
    | ファイルのアップロード | このセッションで使用されるファイル | 文字列のリスト |
    | driverMemory | ドライバーのプロセスに使用するメモリの量 | string |
    | driverCores | ドライバーのプロセスに使用するコアの数 | int |
    | executorMemory | Executor プロセスごとに使用するメモリの量 | string |
    | executorCores | Executor ごとに使用するコアの数 | int |
    | numExecutors | このセッションに対して起動する Executor の数 | int |
    | archives | このセッションで使用されるアーカイブ | 文字列のリスト |
    | queue | 送信先の YARN キューの名前| string |
    | name | このセッションの名前 | string |
    | conf | Spark の構成のプロパティ | キーと値のマップ |

    応答本文   
    作成された Batch オブジェクト

    | name | description | type |
    | :- | :- | :- | 
    | id | セッション ID | int | 
    | appId | このセッションのアプリケーション ID | string |
    | appInfo | アプリケーションの詳細情報 | キーと値のマップ |
    | log | ログの行 | 文字列のリスト |
    | state |バッチの状態 | string |

    >[!NOTE]
    >割り当てられた Livy の構成は、スクリプトの送信時に出力ウィンドウに表示されます。

## <a name="integrate-with-azure-hdinsight-from-explorer"></a>エクスプローラーから Azure HDInsight と統合する

**Azure HDInsight** が [エクスプローラー] ビューに追加されました。 お使いのクラスターを、**Azure HDInsight** を通じて直接参照し、管理できます。

1. お使いの Azure アカウントに[接続](#connect-to-an-azure-account)するか、クラスターをリンクします (まだ行っていない場合)。

2. メニュー バーから、 **[ビュー]**  >  **[エクスプローラー]** の順に移動します。

3. 左側のウィンドウから、**AZURE HDINSIGHT** を展開します。  利用可能なサブスクリプションとクラスター (Spark、Hadoop、および HBase がサポートされています) が一覧表示されます。

   ![Azure HDInsight サブスクリプションの表示](./media/hdinsight-for-vscode/hdi-azure-hdinsight-subscription.png)

4. クラスターを展開して、Hive メタデータのデータベースとテーブルのスキーマを表示します。

   ![Azure HDInsight でのクラスターの削除](./media/hdinsight-for-vscode/hdi-azure-hdinsight-cluster.png)

## <a name="preview-hive-table"></a>Hive テーブルのプレビュー
**Azure HDInsight** のエクスプローラーを通じて直接、クラスター内の Hive テーブルをプレビューできます。
1. Azure アカウントに[接続](#connect-to-an-azure-account)します (まだ接続していない場合)。

2. 左端の列の **Azure** アイコンを選択します。

3. 左側のウィンドウから、**AZURE HDINSIGHT** を展開します。 利用可能なサブスクリプションとクラスターが一覧表示されます。

4. クラスターを展開して、Hive メタデータのデータベースとテーブルのスキーマを表示します。

5. Hive テーブルを右クリックします。 例: **hivesampletable**。 **[Preview]\(プレビュー\)** を選択します。 

   ![Spark & Hive for Visual Studio Code の Hive テーブルのプレビュー](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-hive-table.png)

6. **[結果のプレビュー]** ウィンドウが開きます。

   ![Spark & Hive for Visual Studio Code の結果ウィンドウのプレビュー](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-results-window.png)

- [結果] パネル

   結果全体を CSV、JSON、または Excel ファイルとしてローカル パスに保存したり、複数の行だけを選択したりできます。

- [メッセージ] パネル
   1. テーブルの行数が 100 行を超えると、次のメッセージが表示されます: "The first 100 rows are displayed for Hive table (Hive テーブルの先頭から 100 行を表示しています)"。
   2. テーブルの行数が 100 行以下の場合、次のようなメッセージが表示されます: "60 rows are displayed for Hive table (Hive テーブルの 60 行を表示しています)"。
   3. テーブルの内容がない場合は、次のメッセージが表示されます: "0 rows are displayed for Hive table (Hive テーブルの 0 行を表示しています)"。

        >[!NOTE]
        >
        >Linux でテーブル データをコピーするには、xclip をインストールします。
        >
        >![Linux の Spark & Hive for Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-linux-install-xclip.png)
    ## <a name="additional-features"></a>その他の機能

Spark & Hive for Visual Studio Code では、次の機能もサポートされています。

- **IntelliSense オートコンプリート**。 キーワード、メソッド、変数、その他のプログラミング要素の候補がポップアップ表示されます。 オブジェクトの種類ごとに異なるアイコンで表されます：

    ![Spark & Hive for Visual Studio Code の IntelliSense オブジェクト](./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png)

- **IntelliSense エラー マーカー**。 言語サービスにより、Hive スクリプトの編集エラーに下線が引かれます。     
- **構文の強調表示**。 言語サービスにより、異なる色を使用して、変数、キーワード、データ型、関数、その他のプログラミング要素が区別されます。

    ![Spark & Hive for Visual Studio Code の構文の強調表示](./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png)

## <a name="reader-only-role"></a>読み取り専用ロール

クラスターに対する読み取り専用ロールを割り当てられたユーザーは、HDInsight クラスターにジョブを送信することも、Hive データベースを表示することもできなくなります。 クラスター管理者に連絡し、[Azure portal](https://ms.portal.azure.com/) で [**HDInsight クラスター オペレーター**](https://docs.microsoft.com/azure/hdinsight/hdinsight-migrate-granular-access-cluster-configurations#add-the-hdinsight-cluster-operator-role-assignment-to-a-user)へとロールをアップグレードしてもらいます。 有効な Ambari 資格情報を持っている場合は、次のガイダンスに従って、クラスターを手動でリンクすることができます。

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

Azure HDInsight のエクスプローラー上で選択して Data Lake Storage Gen2 アカウントを展開したときに、お使いの Azure アカウントに Gen2 ストレージへのアクセス権がない場合は、ストレージ アクセス キーの入力を求められます。 アクセス キーが検証されると、Data Lake Storage Gen2 アカウントは自動的に展開されます。

### <a name="submit-jobs-to-an-hdinsight-cluster-with-data-lake-storage-gen2"></a>Data Lake Storage Gen2 を使用して HDInsight クラスターにジョブを送信する

Data Lake Storage Gen2 を使用して HDInsight クラスターにジョブを送信するときに、お使いの Azure アカウントに Gen2 ストレージへの書き込みアクセス権がない場合は、ストレージ アクセス キーの入力を求められます。 アクセス キーが検証されると、ジョブは正常に送信されます。

![Spark & Hive Tools for Visual Studio Code のアクセス キー](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-accesskey.png)

> [!NOTE]
>
> ストレージ アカウントのアクセス キーは Azure portal から取得できます。 詳細については、[アクセス キーの表示とコピー](https://docs.microsoft.com/azure/storage/common/storage-account-manage#access-keys)に関する記事を参照してください。

## <a name="unlink-cluster"></a>クラスターのリンクを解除する

1. メニュー バーから、 **[ビュー]**  >  **[コマンド パレット]** の順に移動し、「**Spark / Hive: Unlink a Cluster**」と入力します。  

2. リンクを解除するクラスターを選択します。  

3. 検証のため、 **[出力]** ビューを確認します。  

## <a name="sign-out"></a>サインアウトする  

メニュー バーから、 **[ビュー]**  >  **[コマンド パレット]** の順に移動し、「**Azure: Sign Out**」と入力します。

## <a name="next-steps"></a>次の手順

Spark & Hive for Visual Studio Code の使用については、[Spark & Hive for Visual Studio Code](https://go.microsoft.com/fwlink/?linkid=858706) のデモ ビデオをご覧ください。
