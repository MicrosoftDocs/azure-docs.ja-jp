---
title: Azure HDInsight Tools - Hive、LLAP、PySpark に Visual Studio Code を使用する | Microsoft Docs
description: Azure HDInsight Tools for Visual Studio Code を使用して、クエリとスクリプトを作成および送信する方法について説明します。
Keywords: Visual Studio Code, Azure HDInsight Tools, Hive, Python, PySpark, Spark, HDInsight, Hadoop, LLAP, インタラクティブ Hive, インタラクティブ クエリ
services: HDInsight
documentationcenter: ''
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/04/2019
ms.openlocfilehash: 71e12e661c704af028ac4dc48f255bdee980619c
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58100328"
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

2. メニュー バーから、**[表示]** > **[拡張機能]** に移動します。

3. 検索ボックスに「**HDInsight**」と入力します。

4. 検索結果から **Azure HDInsight Tools** を選択し、**[インストール]** を選択します。  

   ![HDInsight for Visual Studio Code の Python のインストール](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

5. インストールが完了したら、**[再読み込み]** をクリックして、**Azure HDInsight Tools** 拡張機能をアクティブにします。


## <a name="open-hdinsight-work-folder"></a>HDInsight 作業フォルダーを開く

次の手順を実行して作業フォルダーを開き、Visual Studio Code でファイルを作成します。

1. メニュー バーから、**[ファイル]** > **[フォルダーを開く]** > **[C:\HD\HDexample]** に移動し、**[フォルダーの選択]** ボタンをクリックします。 左側の **[エクスプローラー]** ビューに、フォルダーが表示されます。

2. **[エクスプローラー]** ビューからフォルダー (**HDexample**) を選択し、作業フォルダーの横にある **[新しいファイル]** アイコンをクリックします。

   ![[新しいファイル]](./media/hdinsight-for-vscode/new-file.png)

3. ファイル拡張子 .hql (Hive クエリ) または .py (Spark スクリプト) のいずれかを使って、新しいファイルに名前を付けます。  この例では **HelloWorld.hql** を使用します。

## <a name="connect-to-hdinsight-cluster"></a>HDInsight クラスターへの接続

Visual Studio Code から HDInsight クラスターにスクリプトを送信するには、(Ambari のユーザー名/パスワードかドメイン参加アカウントを利用し) 先に Azure アカウントに接続するか、クラスターをリンクする必要があります。  次の手順を完了して Azure に接続します。

1. メニュー バーから、**[表示]** > **[...コマンド パレット]** に移動し、「**HDInsight:Login**」と入力することもできます。

    ![HDInsight Tools for Visual Studio Code のログイン](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)

2. **[出力]** ウィンドウのサインインの指示に従います。
    + Azure グローバル環境では、**HDInsight: Login** コマンドによって、HDInsight エクスプ ローラーで **Azure へのサインイン** アクションがトリガーされます。その逆も同様です。

        ![Azure でのサインイン手順](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-signin.png)

    + その他の環境では、サインイン手順に従います。

        ![その他の環境でのサインイン手順](./media/hdinsight-for-vscode/hdi-azure-hdinsight-hdinsight-signin.png)

   接続すると、Visual Studio Code ウィンドウの左下のステータス バーに Azure アカウント名が表示されます。  
  

<h2 id="linkcluster">リンクを作成する:Azure HDInsight</h2>

[Apache Ambari](https://ambari.apache.org/) マネージド ユーザー名を使用して通常のクラスターをリンクするか、またはドメイン ユーザー名 (user1@contoso.com など) を使用して Enterprise Security Pack のセキュリティ保護された Hadoop クラスターをリンクできます。

1. メニュー バーから、**[表示]** > **[...コマンド パレット]** に移動し、「**HDInsight:Link a cluster**」と入力します。

   ![リンク クラスターのコマンド](./media/hdinsight-for-vscode/link-cluster-command.png)

2. リンクされるクラスターの種類として **[Azure HDInsight]** を選択します。

3. HDInsight クラスターの URL を入力します。

4. Ambari のユーザー名を入力します (既定値は **admin** です)。

5. Ambari のパスワードを入力します。

6. クラスターの種類を選択します。

7. 検証のため、**[出力]** ビューを確認します。

   > [!NOTE]  
   > リンクされたユーザー名とパスワードは、クラスターが Azure サブスクリプションにログインし、かつクラスターにリンクしていた場合に使用されます。  


## <a name="create-link-generic-livy-endpoint"></a>リンクを作成する:ジェネリック Livy エンドポイント

1. メニュー バーから、**[表示]** > **[...コマンド パレット]** に移動し、「**HDInsight:Link a cluster**」と入力します。

2. リンクされるクラスターの種類として、**[Generic Livy Endpoint]\(ジェネリック Livy エンドポイント\)** を選択します。

3. ジェネリック Livy エンドポイントを入力します (例: http\://10.172.41.42:18080)。

4. 承認の種類として、**[基本]** または **[なし]** を選択します。  **[基本]** の場合は、次の操作を行います。  
    &emsp;a. Ambari のユーザー名を入力します (既定値は **admin** です)。  
    &emsp;b. Ambari のパスワードを入力します。

5. 検証のため、**[出力]** ビューを確認します。

## <a name="list-hdinsight-clusters"></a>List HDInsight clusters

1. メニュー バーから、**[表示]** > **[...コマンド パレット]** に移動し、「**HDInsight:List Cluster**」と入力します。

2. 目的のサブスクリプションを選択します。

3. **[出力]** ビューを確認します。  ビューには、リンクされたクラスターと、Azure サブスクリプションのすべてのクラスターが表示されます。

    ![既定のクラスター構成を設定する](./media/hdinsight-for-vscode/list-cluster-result.png)

## <a name="set-default-cluster"></a>Set default cluster

1. [以前に](#open-hdinsight-work-folder)作成した **HDexample** フォルダーを再び開きます (閉じている場合)。  

2. [以前に](#open-hdinsight-work-folder)作成した **HelloWorld.hql** ファイルを選択すると、ファイルがスクリプト エディターで開きます。

3. Azure アカウントに[接続](#connect-to-hdinsight-cluster)します (まだ接続していない場合)。

4. スクリプト エディターを右クリックして、**[HDInsight:Set Default Cluster]** を選択します。  

5. 現在のスクリプト ファイルの既定のクラスターとしてクラスターを選択します。 構成ファイル (**.VSCode\settings.json**) が自動的に更新されます。 

   ![既定のクラスター構成を設定する](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)

## <a name="set-the-azure-environment"></a>Azure 環境を設定する

1. Azure アカウントに[接続](#connect-to-hdinsight-cluster)します (まだ接続していない場合)。

2. メニュー バーから、**[表示]** > **[...コマンド パレット]** に移動し、「**HDInsight:Set Azure Environment**」と入力します。

3. 既定のログイン エントリとして、環境を選択します。

4. 一方、ツールによって既定のログイン エントリが **.VSCode\settings.json** に既に保存されています。 また、この構成ファイル内で直接、更新することもできます。 

   ![既定のログイン エントリの構成を設定する](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)


## <a name="submit-interactive-hive-queries-hive-batch-scripts"></a>対話型 Hive クエリ、Hive バッチ スクリプトを送信する

HDInsight Tools for Visual Studio Code を使用すると、対話型 Hive クエリ、および Hive バッチ スクリプトを HDInsight クラスターに送信できます。

1. [以前に](#open-hdinsight-work-folder)作成した **HDexample** フォルダーを再び開きます (閉じている場合)。  

2. [以前に](#open-hdinsight-work-folder)作成した **HelloWorld.hql** ファイルを選択すると、ファイルがスクリプト エディターで開きます。

3. Azure アカウントに[接続](#connect-to-hdinsight-cluster)します (まだ接続していない場合)。

4. 次のコードをコピーし、Hive ファイルに貼り付けて保存します。

    ```hiveql
    SELECT * FROM hivesampletable;
    ```

5. スクリプト エディターを右クリックし、**[HDInsight:Hive Interactive]** を選択してクエリを送信するか、ショートカット **Ctrl + Alt + I** を使用します。**[HDInsight:Hive Batch]** を選択してスクリプトを送信するか、ショートカット**Ctrl + Alt + H** を使用します。  

6. 既定のクラスターを指定していない場合は、クラスターを選択します。 また、このツールでは、コンテキスト メニューを使用して、スクリプト ファイル全体ではなく、コードのブロックを送信することもできます。 しばらくすると、新しいタブにクエリの結果が表示されます。

   ![対話型 Hive の結果](./media/hdinsight-for-vscode/interactive-hive-result.png)

    - **[結果]** パネル:結果全体を CSV、JSON、または Excel ファイルとしてローカル パスに保存したり、複数の行だけを選択したりできます。

    - **[メッセージ]** パネル:**行**の番号を選択すると、実行中のスクリプトの最初の行にジャンプします。

## <a name="submit-interactive-pyspark-queries"></a>対話型 PySpark クエリの送信

1. [以前に](#open-hdinsight-work-folder)作成した **HDexample** フォルダーを再び開きます (閉じている場合)。  

2. [以前の](#open-hdinsight-work-folder)手順に従って、新しい **HelloWorld.py** ファイルを作成します。

3. 前提条件の Python をインストールしなかった場合は、Python 拡張機能の推奨事項ダイアログが表示されます。  Visual Studio Code インストールして再読み込みし、インストールを完了します。

    >![HDInsight for Visual Studio Code の Python のインストール](./media/hdinsight-for-vscode/hdinsight-vscode-install-python.png)

4. Azure アカウントに[接続](#connect-to-hdinsight-cluster)します (まだ接続していない場合)。

5. 以下のコードをコピーして、スクリプト ファイルに貼り付けます。
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

6. スクリプト エディターを右クリックし、**[HDInsight:PySpark Interactive]** を選択してクエリを送信するか、ショートカット **Ctrl + Alt + I** を使用します。  

7. 既定のクラスターを指定していない場合は、クラスターを選択します。 また、このツールでは、コンテキスト メニューを使用して、スクリプト ファイル全体ではなく、コードのブロックを送信することもできます。 しばらくすると、新しいタブにクエリの結果が表示されます。

   ![Python ジョブの送信の結果](./media/hdinsight-for-vscode/pyspark-interactive-result.png) 

8. ツールでは **SQL 句**のクエリもサポートされています。

   ![Python ジョブの送信の結果](./media/hdinsight-for-vscode/pyspark-ineteractive-select-result.png) 送信の状態は、クエリの実行時に下部のステータス バーの左側に表示されます。 **[PySpark Kernel (busy)]\(PySpark カーネル (ビジー)\)** 状態のときに他のクエリを送信しないでください。  

>[!NOTE]  
>クラスターはセッション情報を保持できます。 定義済みの変数、関数、対応する値がセッションで保持されるので、同じクラスターの複数のサービス呼び出しで参照できます。 

### <a name="pyspark3-is-not-supported-with-spark2223"></a>Spark2.2/2.3 では PySpark3 はサポートされていません

Spark 2.2 クラスターと Spark2.3 クラスターでは PySpark3 はサポートされなくなりました。"PySpark" のみが Python 向けにサポートされています。 Python3 で Spark 2.2/2.3 への送信が失敗するのは既知の問題です。

   ![python3 への送信でエラーが発生する](./media/hdinsight-for-vscode/hdi-azure-hdinsight-py3-error.png)

Python2.x を使用する手順に従います: 

1. ローカル コンピューターに Python 2.7 をインストールし、システム パスに追加します。

2. Visual Studio Code を再起動します。

3. ステータス バーにある **[Python XXX]** をクリックして Python 2 に切り替えてから、ターゲットの Python を選択します。

   ![Python のバージョンを選択する](./media/hdinsight-for-vscode/hdi-azure-hdinsight-select-python.png)


## <a name="submit-pyspark-batch-job"></a>PySpark バッチ ジョブの送信

1. [以前に](#open-hdinsight-work-folder)作成した **HDexample** フォルダーを再び開きます (閉じている場合)。  

2. [以前の](#open-hdinsight-work-folder)手順に従って、新しい **BatchFile.py** ファイルを作成します。

3. Azure アカウントに[接続](#connect-to-hdinsight-cluster)します (まだ接続していない場合)。

4. 以下のコードをコピーして、スクリプト ファイルに貼り付けます。

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

4. スクリプト エディターを右クリックして、**[HDInsight:PySpark Batch]** を選択するか、ショートカット **Ctrl + Alt + H** を使用します。 

5. PySpark ジョブの送信先のクラスターを選択します。 

   ![Python ジョブの送信の結果](./media/hdinsight-for-vscode/submit-pythonjob-result.png) 

Python ジョブを送信したら、Visual Studio Code の **[出力]** ウィンドウに送信ログが表示されます。 **Spark UI URL** と **Yarn UI URL** も表示されます。 URL を Web ブラウザーで開くと、ジョブの状態を追跡できます。

## <a name="apache-livy-configuration"></a>Apache Livy の構成

[Apache Livy](https://livy.incubator.apache.org/) の構成がサポートされており、ワークスペース フォルダーの **.VSCode\settings.json** で設定できます。 現在、Livy の構成では Python スクリプトのみをサポートします。 詳しくは、[Livy の README](https://github.com/cloudera/livy/blob/master/README.rst ) をご覧ください。

<a id="triggerlivyconf"></a>**Livy の構成をトリガーする方法**

方法 1  
1. メニュー バーから、**[ファイル]** > **[ユーザー設定]** > **[設定]** に移動します。  
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
    | proxyUser | ジョブを実行するときに偽装するユーザー | 文字列 | 
    | className | アプリケーションの Java/Spark のメイン クラス | 文字列 |
    | args | アプリケーションのコマンド ライン引数 | string のリスト | 
    | jars | このセッションで使用される Jar | 文字列のリスト | 
    | pyFiles | このセッションで使用される Python ファイル | 文字列のリスト |
    | ファイルのアップロード | このセッションで使用されるファイル | 文字列のリスト |
    | driverMemory | ドライバーのプロセスに使用するメモリの量 | 文字列 |
    | driverCores | ドライバーのプロセスに使用するコアの数 | int |
    | executorMemory | Executor プロセスごとに使用するメモリの量 | 文字列 |
    | executorCores | Executor ごとに使用するコアの数 | int |
    | numExecutors | このセッションに対して起動する Executor の数 | int |
    | archives | このセッションで使用されるアーカイブ | 文字列のリスト |
    | キュー | 送信対象の YARN キューの名前 | 文字列 |
    | name | このセッションの名前 | 文字列 |
    | conf | Spark の構成のプロパティ | キーと値のマップ |

    応答本文   
    作成された Batch オブジェクト

    | name | description | type | 
    | :- | :- | :- | 
    | id | セッション ID | int | 
    | appId | このセッションのアプリケーション ID |  String |
    | appInfo | アプリケーションの詳細情報 | キーと値のマップ |
    | log | ログの行 | string のリスト |
    | state |   バッチの状態 | 文字列 |

>[!NOTE]
>スクリプトを送信すると、割り当てられている Livy の構成が出力ウィンドウに表示されます。

## <a name="integrate-with-azure-hdinsight-from-explorer"></a>エクスプローラーから Azure HDInsight と統合する

**Azure HDInsight** がエクスプローラー ビューに追加されました。 クラスターは、**Azure HDInsight** を通じて直接参照し、管理できます。

1. Azure アカウントに[接続](#connect-to-hdinsight-cluster)します (まだ接続していない場合)。

2. メニュー バーから、**[表示]** > **[エクスプローラー]** に移動します。

3. 左側のウィンドウから、**AZURE HDINSIGHT** を展開します。  利用可能なサブスクリプションとクラスター (Spark、Hadoop、および HBase がサポートされています) が一覧表示されます。 

   ![Azure HDInsight サブスクリプション](./media/hdinsight-for-vscode/hdi-azure-hdinsight-subscription.png)

4. クラスターを展開して、Hive メタデータのデータベースとテーブルのスキーマを表示します。

   ![Azure HDInsight クラスター](./media/hdinsight-for-vscode/hdi-azure-hdinsight-cluster.png)


## <a name="additional-features"></a>その他の機能

HDInsight for Visual Studio Code では、次の機能をサポートしています。

- **IntelliSense オートコンプリート**。 キーワード、メソッド、変数などの候補がポップアップ表示されます。 オブジェクトの種類ごとに異なるアイコンで表されます。

    ![HDInsight Tools for Visual Studio Code の IntelliSense オブジェクト型](./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png)
- **IntelliSense エラー マーカー**。 言語サービスでは、Hive スクリプトの編集エラーに下線が引かれます。     
- **構文の強調表示**。 言語サービスでは、変数、キーワード、データ型、関数などを区別するために、異なる色が使用されます。 

    ![HDInsight Tools for Visual Studio Code の構文の強調表示](./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png)


## <a name="unlink-cluster"></a>クラスターのリンクを解除する

1. メニュー バーから、**[表示]** > **[...コマンド パレット]** に移動し、「**HDInsight:Unlink a Cluster**」と入力します。  

2. リンクを解除するクラスターを種類します。  

3. 検証のため、**[出力]** ビューを確認します。  


## <a name="logout"></a>Logout  

メニュー バーから、**[表示]** > **[...コマンド パレット]** に移動し、「**HDInsight:Logout** コマンドを入力します。  **正常にログアウトした**ことを示すポップアップ メッセージが右下隅に表示されます。


## <a name="next-steps"></a>次の手順
HDInsight for Visual Studio Code の使用については、[HDInsight for Visual Studio Code](https://go.microsoft.com/fwlink/?linkid=858706) のデモ ビデオをご覧ください
