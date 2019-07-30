---
title: Azure HDInsight Tools - Hive、LLAP、または PySpark に Visual Studio Code を使用する
description: Azure HDInsight Tools for Visual Studio Code を使用して、クエリとスクリプトを作成および送信する方法について説明します。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: aadfae9a7b74986fd0ac8857669dd3ccaf62af1f
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/17/2019
ms.locfileid: "67166059"
---
# <a name="use-azure-hdinsight-tools-for-visual-studio-code"></a>Azure HDInsight Tool for Visual Studio Code の使用

Azure HDInsight Tools for Visual Studio Code を使用して Apache Hive バッチ ジョブ、対話型 Hive クエリ、および Apache Spark 用 PySpark スクリプトを作成および送信する方法について説明します。 最初に Visual Studio Code に HDInsight Tools をインストールする方法を説明した後、Hive および Spark にジョブを送信する方法を説明します。  

Azure HDInsight Tools は、Visual Studio Code でサポートされている Windows、Linux、macOS などのプラットフォームにインストールできます。 以下では、さまざまなプラットフォームに対する前提条件について説明します。


## <a name="prerequisites"></a>前提条件

この記事の手順を実行するには、次のものが必要です。

- HDInsight クラスター。 クラスターを作成するには、[HDInsight での Hadoop の使用](hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md)に関するページをご覧ください。
- [Visual Studio Code](https://code.visualstudio.com/)。
- [Mono](https://www.mono-project.com/docs/getting-started/install/)。 Mono は Linux と macOS にのみ必要です。
- Visual Studio Code 用の [Azure Account 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)。
- [Visual Studio Code 用の PySpark 対話型環境を設定する](set-up-pyspark-interactive-environment.md)。
- **HDexample** という名前のローカル ディレクトリ。  この記事では、**C:\HD\HDexample** を使用します。

## <a name="install-azure-hdinsight-tools"></a>Azure HDInsight Tools をインストールする

前提条件の各項目が完了したら、Azure HDInsight Tools for Visual Studio Code をインストールできます。  Azure HDInsight Tools をインストールするには、次の手順を完了します。

1. Visual Studio Code を開きます。

2. メニュー バーから、 **[表示]**  >  **[拡張機能]** に移動します。

3. 検索ボックスに「**HDInsight**」と入力します。

4. 検索結果から **Azure HDInsight Tools** を選択し、 **[インストール]** を選択します。  

   ![HDInsight for Visual Studio Code の Python のインストール](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

5. インストールが完了したら、 **[再読み込み]** をクリックして、**Azure HDInsight Tools** 拡張機能をアクティブにします。


## <a name="open-hdinsight-work-folder"></a>HDInsight 作業フォルダーを開く

次の手順を実行して作業フォルダーを開き、Visual Studio Code でファイルを作成します。

1. メニュー バーから、 **[ファイル]**  >  **[フォルダーを開く]**  >  **[C:\HD\HDexample]** に移動し、 **[フォルダーの選択]** ボタンをクリックします。 左側の **[エクスプローラー]** ビューに、フォルダーが表示されます。

2. **[エクスプローラー]** ビューからフォルダー (**HDexample**) を選択し、作業フォルダーの横にある **[新しいファイル]** アイコンをクリックします。

   ![[新しいファイル]](./media/hdinsight-for-vscode/new-file.png)

3. ファイル拡張子 `.hql` (Hive クエリ) または `.py` (Spark スクリプト) のいずれかを使って、新しいファイルに名前を付けます。  この例では **HelloWorld.hql** を使用します。

## <a name="set-the-azure-environment"></a>Azure 環境を設定する

1. Azure アカウントに[接続](#connect-to-azure-account)するか、クラスターをリンクします (まだ行っていない場合)。

2. メニュー バーから、 **[表示]**  >  **[...コマンド パレット]** に移動し、「**HDInsight:Set Azure Environment**」と入力します。

3. 既定のログイン エントリとして、環境を選択します。

4. 一方、ツールによって既定のログイン エントリが **.VSCode\settings.json** に既に保存されています。 また、この構成ファイル内で直接、更新することもできます。 

   ![既定のログイン エントリの構成を設定する](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)

## <a name="connect-to-azure-account"></a>Azure アカウントへの接続

Visual Studio Code から HDInsight クラスターにスクリプトを送信するには、(Ambari のユーザー名/パスワードかドメイン参加アカウントを利用し) 先に Azure アカウントに接続するか、クラスターをリンクする必要があります。  次の手順を完了して Azure に接続します。

1. メニュー バーから、 **[表示]**  >  **[...コマンド パレット]** に移動し、「**HDInsight:Login**」と入力することもできます。

    ![HDInsight Tools for Visual Studio Code のログイン](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)

2. **[出力]** ウィンドウのサインイン手順に従います。
    + Azure グローバル環境では、**HDInsight: Login** コマンドによって、HDInsight エクスプ ローラーで **Azure へのサインイン** アクションがトリガーされます。その逆も同様です。

        ![Azure でのサインイン手順](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-signin.png)

    + その他の環境では、サインイン手順に従います。

        ![その他の環境でのサインイン手順](./media/hdinsight-for-vscode/hdi-azure-hdinsight-hdinsight-signin.png)

   接続すると、Visual Studio Code ウィンドウの左下のステータス バーに Azure アカウント名が表示されます。  
  

## <a name="link-a-cluster"></a>クラスターのリンク

### <a name="link-azure-hdinsight"></a>リンク: Azure HDInsight

[Apache Ambari](https://ambari.apache.org/) マネージド ユーザー名を使用して通常のクラスターをリンクするか、またはドメイン ユーザー名 (user1@contoso.com など) を使用して Enterprise Security Pack のセキュリティ保護された Hadoop クラスターをリンクできます。

1. メニュー バーから、 **[表示]**  >  **[...コマンド パレット]** に移動し、「**HDInsight:Link a cluster**」と入力します。

   ![リンク クラスターのコマンド](./media/hdinsight-for-vscode/link-cluster-command.png)

2. リンクされるクラスターの種類として **[Azure HDInsight]** を選択します。

3. HDInsight クラスターの URL を入力します。

4. Ambari のユーザー名を入力します (既定値は **admin** です)。

5. Ambari のパスワードを入力します。

6. クラスターの種類を選択します。

7. 検証のため、 **[出力]** ビューを確認します。

   > [!NOTE]  
   > リンクされたユーザー名とパスワードは、クラスターが Azure サブスクリプションにログインし、かつクラスターにリンクしていた場合に使用されます。  



### <a name="link-generic-livy-endpoint"></a>リンク: ジェネリック Livy エンドポイント

1. メニュー バーから、 **[表示]**  >  **[...コマンド パレット]** に移動し、「**HDInsight:Link a cluster**」と入力します。

2. リンクされるクラスターの種類として、 **[Generic Livy Endpoint]\(ジェネリック Livy エンドポイント\)** を選択します。

3. ジェネリック Livy エンドポイントを入力します (例: http\://10.172.41.42:18080)。

4. 承認の種類として、 **[基本]** または **[なし]** を選択します。  **[基本]** の場合は、次の操作を行います。  
    &emsp;a. Ambari のユーザー名を入力します (既定値は **admin** です)。  
    &emsp;b. Ambari のパスワードを入力します。

5. 検証のため、 **[出力]** ビューを確認します。

## <a name="list-hdinsight-clusters"></a>List HDInsight clusters

1. メニュー バーから、 **[表示]**  >  **[...コマンド パレット]** に移動し、「**HDInsight:List Cluster**」と入力します。

2. 目的のサブスクリプションを選択します。

3. **[出力]** ビューを確認します。  ビューには、リンクされたクラスターと、Azure サブスクリプションのすべてのクラスターが表示されます。

    ![既定のクラスター構成を設定する](./media/hdinsight-for-vscode/list-cluster-result.png)

## <a name="set-default-cluster"></a>Set default cluster

1. [以前に](#open-hdinsight-work-folder)作成した **HDexample** フォルダーを再び開きます (閉じている場合)。  

2. [以前に](#open-hdinsight-work-folder)作成した **HelloWorld.hql** ファイルを選択すると、ファイルがスクリプト エディターで開きます。

3. スクリプト エディターを右クリックして、 **[HDInsight:Set Default Cluster]** を選択します。  

4. Azure アカウントに[接続](#connect-to-azure-account)するか、クラスターをリンクします (まだ行っていない場合)。

5. 現在のスクリプト ファイルの既定のクラスターとしてクラスターを選択します。 構成ファイル ( **.VSCode\settings.json**) が自動的に更新されます。 

   ![既定のクラスター構成を設定する](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)


## <a name="submit-interactive-hive-queries-hive-batch-scripts"></a>対話型 Hive クエリ、Hive バッチ スクリプトを送信する

HDInsight Tools for Visual Studio Code を使用すると、対話型 Hive クエリ、および Hive バッチ スクリプトを HDInsight クラスターに送信できます。

1. [以前に](#open-hdinsight-work-folder)作成した **HDexample** フォルダーを再び開きます (閉じている場合)。  

2. [以前に](#open-hdinsight-work-folder)作成した **HelloWorld.hql** ファイルを選択すると、ファイルがスクリプト エディターで開きます。


3. 次のコードをコピーし、Hive ファイルに貼り付けて保存します。

    ```hiveql
    SELECT * FROM hivesampletable;
    ```

4. Azure アカウントに[接続](#connect-to-azure-account)するか、クラスターをリンクします (まだ行っていない場合)。

5. スクリプト エディターを右クリックし、 **[HDInsight:Hive Interactive]** を選択してクエリを送信するか、ショートカット **Ctrl + Alt + I** を使用します。 **[HDInsight:Hive Batch]** を選択してスクリプトを送信するか、ショートカット**Ctrl + Alt + H** を使用します。  

6. 既定のクラスターを指定していない場合は、クラスターを選択します。 また、このツールでは、コンテキスト メニューを使用して、スクリプト ファイル全体ではなく、コードのブロックを送信することもできます。 しばらくすると、新しいタブにクエリの結果が表示されます。

   ![対話型 Hive の結果](./media/hdinsight-for-vscode/interactive-hive-result.png)

    - **[結果]** パネル:結果全体を CSV、JSON、または Excel ファイルとしてローカル パスに保存したり、複数の行だけを選択したりできます。

    - **[メッセージ]** パネル:**行**の番号を選択すると、実行中のスクリプトの最初の行にジャンプします。

## <a name="submit-interactive-pyspark-queries"></a>対話型 PySpark クエリの送信

次の手順に従って、対話型 PySpark クエリを送信することができます。

1. [以前に](#open-hdinsight-work-folder)作成した **HDexample** フォルダーを再び開きます (閉じている場合)。  

2. [以前の](#open-hdinsight-work-folder)手順に従って、新しい **HelloWorld.py** ファイルを作成します。

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

4. Azure アカウントに[接続](#connect-to-azure-account)するか、クラスターをリンクします (まだ行っていない場合)。

5. すべてのコードを選択し、スクリプト エディターを右クリックして、 **[HDInsight: PySpark Interactive]** を選択してクエリを送信するか、ショートカット **Ctrl + Alt + I** を使用します。

   ![PySpark Interactive: 右クリック](./media/hdinsight-for-vscode/pyspark-interactive-right-click.png)

6. 既定のクラスターを指定していない場合は、クラスターを選択します。 しばらくすると、新しいタブに **Python Interactive の結果**が表示されます。また、このツールでは、コンテキスト メニューを使用して、スクリプト ファイル全体ではなく、コードのブロックを送信することもできます。 

   ![PySpark Interactive: PySpark Interactive ウィンドウ](./media/hdinsight-for-vscode/pyspark-interactive-python-interactive-window.png) 

7. 「 **%%info**」と入力し、**Shift + Enter** キーを押してジョブ情報を表示します。 (省略可能)

   ![ジョブ情報の表示](./media/hdinsight-for-vscode/pyspark-interactive-view-job-information.png)

8. ツールでは **Spark SQL** クエリもサポートされています。

   ![PySpark Interactive: 結果の表示](./media/hdinsight-for-vscode/pyspark-ineteractive-select-result.png)

   送信の状態は、クエリの実行時に下部のステータス バーの左側に表示されます。 **[PySpark Kernel (busy)]\(PySpark カーネル (ビジー)\)** 状態のときに他のクエリを送信しないでください。  

   > [!NOTE] 
   >
   > 設定で **[Python Extension Enabled]\(Python 拡張機能が有効\)** チェック ボックスがオフ (既定の設定ではオン) の場合、送信される PySpark 対話結果は古いウィンドウを使用します。
   >
   > ![PySpark Interactive: Python 拡張機能が無効](./media/hdinsight-for-vscode/pyspark-interactive-python-extension-disabled.png)


## <a name="submit-pyspark-batch-job"></a>PySpark バッチ ジョブの送信

1. [以前に](#open-hdinsight-work-folder)作成した **HDexample** フォルダーを再び開きます (閉じている場合)。  

2. [以前の](#open-hdinsight-work-folder)手順に従って、新しい **BatchFile.py** ファイルを作成します。

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

4. Azure アカウントに[接続](#connect-to-azure-account)するか、クラスターをリンクします (まだ行っていない場合)。

5. スクリプト エディターを右クリックして、 **[HDInsight:PySpark Batch]** を選択するか、ショートカット **Ctrl + Alt + H** を使用します。 

6. PySpark ジョブの送信先のクラスターを選択します。 

   ![Python ジョブの送信の結果](./media/hdinsight-for-vscode/submit-pythonjob-result.png) 

Python ジョブを送信したら、Visual Studio Code の **[出力]** ウィンドウに送信ログが表示されます。 **Spark UI URL** と **Yarn UI URL** も表示されます。 URL を Web ブラウザーで開くと、ジョブの状態を追跡できます。

## <a name="apache-livy-configuration"></a>Apache Livy の構成

[Apache Livy](https://livy.incubator.apache.org/) の構成がサポートされており、ワークスペース フォルダーの **.VSCode\settings.json** で設定できます。 現在、Livy の構成では Python スクリプトのみをサポートします。 詳しくは、[Livy の README](https://github.com/cloudera/livy/blob/master/README.rst ) をご覧ください。

<a id="triggerlivyconf"></a>**Livy の構成をトリガーする方法**

方法 1  
1. メニュー バーから、 **[ファイル]**  >  **[ユーザー設定]**  >  **[設定]** に移動します。  
2. **[検索設定]** テキスト ボックスに「**HDInsight Job Sumission:Livy Conf**」と入力します。  
3. 関連する検索結果の **[settings.json で編集]** を選択します。

方法 2   
ファイルを送信します (.vscode フォルダーが自動的に作業フォルダーに追加されます)。 Livy の構成は **.vscode\settings.json** をクリックして確認できます。

+ プロジェクトの設定:

    ![Livy の構成](./media/hdinsight-for-vscode/hdi-livyconfig.png)

>[!NOTE]
>**driverMomory** および **executorMomry** の設定で、単位付きの値 (例: 1g または 1024m) を設定します。 

+ サポートされている Livy の構成:   

    **POST/バッチ**   
    要求本文

    | name | description | type | 
    | :- | :- | :- | 
    | file | 実行するアプリケーションを含むファイル | パス (必須) | 
    | proxyUser | ジョブを実行するときに偽装するユーザー | string | 
    | className | アプリケーションの Java/Spark のメイン クラス | string |
    | args | アプリケーションのコマンド ライン引数 | string のリスト | 
    | jars | このセッションで使用される Jar | 文字列のリスト | 
    | pyFiles | このセッションで使用される Python ファイル | 文字列のリスト |
    | files | このセッションで使用されるファイル | 文字列のリスト |
    | driverMemory | ドライバーのプロセスに使用するメモリの量 | string |
    | driverCores | ドライバーのプロセスに使用するコアの数 | int |
    | executorMemory | Executor プロセスごとに使用するメモリの量 | string |
    | executorCores | Executor ごとに使用するコアの数 | int |
    | numExecutors | このセッションに対して起動する Executor の数 | int |
    | archives | このセッションで使用されるアーカイブ | 文字列のリスト |
    | キュー | 送信対象の YARN キューの名前 | string |
    | name | このセッションの名前 | string |
    | conf | Spark の構成のプロパティ | キーと値のマップ |

    応答本文   
    作成された Batch オブジェクト

    | name | description | type | 
    | :- | :- | :- | 
    | id | セッション ID | int | 
    | appId | このセッションのアプリケーション ID |  string |
    | appInfo | アプリケーションの詳細情報 | キーと値のマップ |
    | log | ログの行 | string のリスト |
    | state |   バッチの状態 | string |

>[!NOTE]
>スクリプトを送信すると、割り当てられている Livy の構成が出力ウィンドウに表示されます。

## <a name="integrate-with-azure-hdinsight-from-explorer"></a>エクスプローラーから Azure HDInsight と統合する

**Azure HDInsight** がエクスプローラー ビューに追加されました。 クラスターは、**Azure HDInsight** を通じて直接参照し、管理できます。

1. Azure アカウントに[接続](#connect-to-azure-account)するか、クラスターをリンクします (まだ行っていない場合)。

2. メニュー バーから、 **[表示]**  >  **[エクスプローラー]** に移動します。

3. 左側のウィンドウから、**AZURE HDINSIGHT** を展開します。  利用可能なサブスクリプションとクラスター (Spark、Hadoop、および HBase がサポートされています) が一覧表示されます。 

   ![Azure HDInsight サブスクリプション](./media/hdinsight-for-vscode/hdi-azure-hdinsight-subscription.png)

4. クラスターを展開して、Hive メタデータのデータベースとテーブルのスキーマを表示します。

   ![Azure HDInsight クラスター](./media/hdinsight-for-vscode/hdi-azure-hdinsight-cluster.png)


## <a name="preview-hive-table"></a>Hive テーブルのプレビュー
**Azure HDInsight** のエクスプローラーから直接、クラスター内の Hive テーブルをプレビューできます。
1. Azure アカウントに[接続](#connect-to-azure-account)します (まだ接続していない場合)。

2. 左端の列の **[Azure]** アイコンをクリックします。

3. 左側のウィンドウから、[AZURE HDINSIGHT] を展開します。 利用可能なサブスクリプションとクラスターが一覧表示されます。

4. クラスターを展開して、Hive メタデータのデータベースとテーブルのスキーマを表示します。

5. Hive テーブル (例: hivesampletable) を右クリックします。 **[Preview]\(プレビュー\)** を選択します。 

   ![HDInsight for VSCode: Hive テーブルのプレビュー](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-hive-table.png)

6. **[結果のプレビュー]** ウィンドウが開きます。

   ![HDInsight for VSCode: [結果のプレビュー] ウィンドウ](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-results-window.png)
   
- **[RESULTS]\(結果\)** パネル

   結果全体を CSV、JSON、または Excel ファイルとしてローカル パスに保存したり、複数の行だけを選択したりできます。

- **[MESSAGES]\(メッセージ\)** パネル
   1. テーブルの行数が 100 行を超えると、次のメッセージが表示されます: **The first 100 rows are displayed for Hive table** (Hive テーブルの先頭から 100 行を表示しています)。
   2. テーブルの行数が 100 行以下の場合、次のメッセージが表示されます: **60 rows are displayed for Hive table** (Hive テーブルの 60 行を表示しています)。
   3. テーブルの中身がない場合、次のメッセージが表示されます: **0 row is displayed for Hive table** (Hive テーブルの 0 行を表示しています)。

>[!NOTE]
>
>Linux でテーブル データをコピーするには、xclip をインストールします。
>
>![Linux の HDInsight for VSCode](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-linux-install-xclip.png)
## <a name="additional-features"></a>その他の機能

HDInsight for Visual Studio Code では、次の機能をサポートしています。

- **IntelliSense オートコンプリート**。 キーワード、メソッド、変数などの候補がポップアップ表示されます。 オブジェクトの種類ごとに異なるアイコンで表されます。

    ![HDInsight Tools for Visual Studio Code の IntelliSense オブジェクト型](./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png)
- **IntelliSense エラー マーカー**。 言語サービスでは、Hive スクリプトの編集エラーに下線が引かれます。     
- **構文の強調表示**。 言語サービスでは、変数、キーワード、データ型、関数などを区別するために、異なる色が使用されます。 

    ![HDInsight Tools for Visual Studio Code の構文の強調表示](./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png)

## <a name="reader-only-role"></a>閲覧者限定ロール

クラスターの**閲覧者** **限定** **ロール**であるユーザーは、以後、HDInsight クラスターにジョブを送信することも、Hive データベースを表示することもできません。 クラスター管理者に連絡して、[Azure portal](https://ms.portal.azure.com/) で [**HDInsight** **Cluster** **Operator**](https://docs.microsoft.com/azure/hdinsight/hdinsight-migrate-granular-access-cluster-configurations#add-the-hdinsight-cluster-operator-role-assignment-to-a-user) にロールをアップグレードする必要があります。 Ambari の資格情報がわかっている場合、次の指示に従って手動でクラスターをリンクすることができます。

### <a name="browse-hdinsight-cluster"></a>HDInsight クラスターを参照する  

Azure HDInsight のエクスプローラー上でクリックして HDInsight クラスターを展開したときに、そのクラスターに対して閲覧者限定ロールである場合は、クラスターをリンクするよう求められます。 次の手順に従い、Ambari の資格情報を使ってクラスターにリンクします。 

### <a name="submit-job-to-hdinsight-cluster"></a>HDInsight クラスターにジョブを送信する

HDInsight クラスターにジョブを送信するときに、そのクラスターに対して閲覧者限定ロールである場合は、クラスターをリンクするように求められます。 次の手順に従い、Ambari の資格情報を使ってクラスターにリンクします。 

### <a name="link-to-cluster"></a>クラスターにリンクする

1.  Ambari のユーザー名を入力します 
2.  Ambari ユーザーのパスワードを入力します。

   ![HDInsight Tools for Visual Studio Code: ユーザー名](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-username.png)

   ![HDInsight Tools for Visual Studio Code: パスワード](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-password.png)

> [!NOTE]
>
>"HDInsight: List Cluster" を使用して、リンクされたクラスターを確認できます。
>
>![HDInsight Tools for Visual Studio Code: リンクされた閲覧者](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-reader-linked.png)

## <a name="azure-data-lake-storage-gen2-adls-gen2"></a>Azure Data Lake Storage Gen2 (ADLS Gen2)

### <a name="browse-an-adls-gen2-account"></a>ADLS Gen2 アカウントを参照する

Azure HDInsight のエクスプローラー上でクリックして ADLS Gen2 アカウントを展開したときに、お使いの Azure アカウントに Gen2 ストレージへのアクセス権がない場合、ストレージの**アクセス キー**の入力を求められます。 アクセスキーが正常に検証されると、ADLS Gen2 アカウントは自動的に拡張されます。 

### <a name="submit-jobs-to-hdinsight-cluster-with-adls-gen2"></a>ADLS Gen2 で HDInsight クラスターにジョブを送信する

ADLS Gen2 で HDInsight クラスターにジョブを送信するときに、お使いの Azure アカウントに Gen2 ストレージへのアクセス権がない場合、ストレージの**アクセス キー**の入力を求められます。  アクセス キーが正常に検証されると、ジョブは正常に送信されます。 

![HDInsight Tools for Visual Studio Code: アクセス キー](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-accesskey.png)   

> [!NOTE]
> 
>ストレージ アカウントのアクセス キーは Azure portal から入手できます。 詳細については、「[アクセス キーの表示とコピー](https://docs.microsoft.com/azure/storage/common/storage-account-manage#view-and-copy-access-keys)」を参照してください。

## <a name="unlink-cluster"></a>クラスターのリンクを解除する

1. メニュー バーから、 **[表示]**  >  **[...コマンド パレット]** に移動し、「**HDInsight:Unlink a Cluster**」と入力します。  

2. リンクを解除するクラスターを種類します。  

3. 検証のため、 **[出力]** ビューを確認します。  

## <a name="sign-out"></a>サインアウトする  

メニュー バーから、 **[表示]**  >  **[...コマンド パレット]** に移動し、「**HDInsight:Logout** コマンドを入力します。  **正常にログアウトした**ことを示すポップアップ メッセージが右下隅に表示されます。


## <a name="next-steps"></a>次の手順
HDInsight for Visual Studio Code の使用については、[HDInsight for Visual Studio Code](https://go.microsoft.com/fwlink/?linkid=858706) のデモ ビデオをご覧ください
