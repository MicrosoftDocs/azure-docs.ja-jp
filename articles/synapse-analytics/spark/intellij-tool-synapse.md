---
title: チュートリアル - Azure Toolkit for IntelliJ (Spark アプリケーション)
description: チュートリアル - Azure Toolkit for IntelliJ を使用して Scala で記述された Spark アプリケーションを開発し、サーバーレス Apache Spark プールに送信します。
services: synapse-analytics
author: jejiang
ms.author: jejiang
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: spark
ms.date: 04/15/2020
ms.openlocfilehash: a4512a8f75d59f617058a50846d7578a90b9d062
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101675815"
---
# <a name="tutorial-create-an-apache-spark-application-with-intellij-using-a-synapse-workspace"></a>チュートリアル:IntelliJ で Synapse ワークスペースを使用して Apache Spark アプリケーションを作成する

このチュートリアルでは、Azure Toolkit for IntelliJ プラグインを使用して、[Scala](https://www.scala-lang.org/) で記述された Apache Spark アプリケーションを開発し、それを IntelliJ 統合開発環境 (IDE) からサーバーレス Apache Spark プールに直接送信する方法について説明します。 このプラグインには、次のような使い方があります。

- Scala Spark アプリケーションを開発して Spark プール に送信する。
- Spark プール リソースにアクセスする。
- Scala Spark アプリケーションをローカルで開発して実行する。

このチュートリアルでは、以下の内容を学習します。
> [!div class="checklist"]
>
> - Azure Toolkit for IntelliJ プラグインを使用する
> - Apache Spark アプリケーションを開発する
> - アプリケーションを Spark プールに送信する

## <a name="prerequisites"></a>前提条件

- [IntelliJ IDEA コミュニティ バージョン](https://www.jetbrains.com/idea/download/download-thanks.html?platform=windows&code=IIC)。
- Azure ツールキット プラグイン 3.27.0-2019.2 - [IntelliJ プラグイン リポジトリ](/java/azure/intellij/azure-toolkit-for-intellij-installation)からインストール
- [JDK (バージョン 1.8)](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)。
- Scala プラグイン - [IntelliJ プラグイン リポジトリ](../../hdinsight/spark/apache-spark-intellij-tool-plugin.md#install-scala-plugin-for-intellij-idea)からインストール。
- 次の前提条件は、Windows ユーザーのみを対象としています。

  Windows コンピューターでローカルの Spark Scala アプリケーションを実行中に、[SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356) で説明されている例外が発生する場合があります。 この例外は、Windows 上に WinUtils.exe がないことが原因で発生します。
  このエラーを回避するには、[WinUtils 実行可能ファイル](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe)をダウンロードして、**C:\WinUtils\bin** などの場所に保存します。 次に、環境変数 **HADOOP_HOME** を追加し、この変数の値を **C:\WinUtils** に設定します。

## <a name="create-a-spark-scala-application-for-a-spark-pool"></a>Spark プールの Spark Scala アプリケーションを作成する

1. IntelliJ IDEA を起動し、 **[Create New Project]\(新しいプロジェクトの作成\)** を選択して、 **[New Project]\(新しいプロジェクト\)** ウィンドウを開きます。
2. 左側のウィンドウから **[Apache Spark/HDInsight]** を選択します。
3. メイン ウィンドウから **[サンプルありの Spark プロジェクト (Scala)]** を選択します。
4. **[Build tool]\(ビルド ツール\)** ドロップダウン ボックスの一覧で、次のいずれかの種類を選択します。

   - Scala プロジェクト作成ウィザードをサポートする場合は **Maven**。
   - 依存関係を管理し、Scala プロジェクトをビルドする場合は **SBT**。

    ![IntelliJ IDEA の [New Project]\(新しいプロジェクト\) ダイアログ ボックス](./media/intellij-tool-synapse/create-synapse-application01.png)

5. **[次へ]** を選択します。
6. **[New Project]\(新しいプロジェクト\)** ウィンドウで、次の情報を指定します。

    | プロパティ | 説明 |
    | ----- | ----- |
    |プロジェクト名| 名前を入力します。 このチュートリアルでは `myApp` を使用します。|
    |Project&nbsp;location (プロジェクトの場所)| プロジェクトを保存する場所を入力します。|
    |Project SDK (プロジェクト SDK)| IDEA を初めて使用するとき、これは空白の場合があります。 **[New]\(新規作成\)** を選択し、自分の JDK に移動します。|
    |Spark バージョン|作成ウィザードにより、Spark SDK と Scala SDK の適切なバージョンが統合されます。 Synapse でサポートされるのは **Spark 2.4.0** のみです。|
    |||

    ![Apache Spark SDK を選択する](./media/intellij-tool-synapse/create-synapse-application02.png)

7. **[完了]** を選択します。 プロジェクトが使用可能になるまで数分かかる場合があります。
8. Spark プロジェクトによって自動的に成果物が作成されます。 成果物を表示するには、次の操作を実行します。

   a. メニュー バーから、 **[ファイル]**  >  **[プロジェクトの構造...]** に移動します。

   b. **[プロジェクトの構造]** ウィンドウで、 **[アーティファクト]** を選択します。

   c. 成果物の表示後、 **[キャンセル]** をクリックします。

    ![ダイアログ ボックスの成果物情報](./media/intellij-tool-synapse/default-artifact-dialog.png)

9. **LogQuery** を **myApp** > **src** > **main** > **scala**> **sample**> **LogQuery** から見つけます。 このチュートリアルでは、実行に **LogQuery** を使用します。

   ![プロジェクトから Scala クラスを作成するためのコマンド](./media/intellij-tool-synapse/select-run-file.png)

## <a name="connect-to-your-spark-pools"></a>Spark プールに接続する

Azure サブスクリプションにサインインして、Spark プールに接続します。

### <a name="sign-in-to-your-azure-subscription"></a>Azure サブスクリプションにサインインします。

1. メニュー バーから、 **[表示]**  >  **[ツール ウィンドウ]**  >  **[Azure Explorer]** に移動します。

   ![IntelliJ IDEA に表示される Azure Explorer](./media/intellij-tool-synapse/show-azure-explorer1.png)

2. Azure Explorer から、 **[Azure]** ノードを右クリックし、 **[サインイン]** を選択します。

   ![IntelliJ IDEA エクスプローラーで Azure を右クリックする](./media/intellij-tool-synapse/explorer-rightclick-azure.png)

3. **[Azure Sign In]\(Azure サインイン\)** ダイアログ ボックスで、 **[デバイスのログイン]** を選択してから、 **[サインイン]** を選択します。

    ![IntelliJ IDEA の [Azure Sign In]\(Azure サインイン\)](./media/intellij-tool-synapse/intellij-view-explorer2.png)

4. **[Azure Device Login]\(Azure デバイスのログイン\)** ダイアログ ボックスで **[Copy&Open]\(コピーして開く\)** を選択します。

   ![IntelliJ IDEA の [Azure Device Login]\(Azure デバイスのログイン\)](./media/intellij-tool-synapse/intellij-view-explorer5.png)

5. ブラウザー インターフェイスで、コードを貼り付けて **[次へ]** を選択します。

   ![Microsoft の HDI の [コードの入力] ダイアログ](./media/intellij-tool-synapse/intellij-view-explorer6.png)

6. 自分の Azure 資格情報を入力して、ブラウザーを閉じます。

   ![Microsoft の HDI のメールの入力ダイアログ](./media/intellij-tool-synapse/intellij-view-explorer7.png)

7. サインイン後、 **[Select Subscriptions]\(サブスクリプションの選択\)** ダイアログ ボックスに、その資格情報に関連付けられているすべての Azure サブスクリプションの一覧が表示されます。 該当するサブスクリプションを選択して、 **[選択]** を選択します。

    ![[サブスクリプションの選択] ダイアログ ボックス](./media/intellij-tool-synapse/Select-Subscriptions.png)

8. **Azure Explorer** から、 **[Apache Spark on Synapse]** を展開し、自分のサブスクリプションにあるワークスペースを表示します。

    ![IntelliJ IDEA Azure Explorer のメイン ビュー](./media/intellij-tool-synapse/azure-explorer-workspace.png)

9. Spark プールを表示するには、ワークスペースをさらに展開します。

    ![Azure Explorer のストレージ アカウント](./media/intellij-tool-synapse/azure-explorer-pool.png)

## <a name="remote-run-a-spark-scala-application-on-a-spark-pool"></a>Spark プールの Spark Scala アプリケーションをリモート実行する

Scala アプリケーションを作成した後、これをリモートから実行できます。

1. アイコンをクリックして、 **[Run/Debug Configurations]\(実行/デバッグ構成\)** ウィンドウを開きます。

    ![[Submit Spark Application to HDInsight]\(HDInsight への Spark アプリケーションの送信\) コマンド 1](./media/intellij-tool-synapse/open-configuration-window.png)

2. **[Run/Debug Configurations]\(実行/デバッグ構成\)** ダイアログ ウィンドウで、 **[+]** を選択してから、 **[Apache Spark on Synapse]** を選択します。

    ![[Submit Spark Application to HDInsight]\(HDInsight への Spark アプリケーションの送信\) コマンド 2](./media/intellij-tool-synapse/create-synapse-configuration02.png)

3. **[Run/Debug Configurations]\(実行/デバッグ構成\)** ウィンドウで、次の値を指定して **[OK]** を選択します。

    |プロパティ |値 |
    |----|----|
    |Spark プール|アプリケーションを実行する Spark プールを選択します。|
    |送信するアーティファクトの選択|既定の設定のままにします。|
    |メイン クラス名|既定値は、選択したファイルのメイン クラスです。 クラスを変更するには、省略記号 ( **...** ) をクリックし、別のクラスを選択します。|
    |ジョブの構成|既定のキーと値を変更できます。 詳細については、[Apache Livy REST API](http://livy.incubator.apache.org./docs/latest/rest-api.html) に関するページを参照してください。|
    |コマンド ライン引数|必要に応じて、main クラスの引数をスペースで区切って入力できます。|
    |参照される JAR と参照されるファイル|参照されている Jar およびファイルのパスを入力できます (存在する場合)。 現在 ADLS Gen2 クラスターのみをサポートする Azure 仮想ファイル システム内のファイルを参照することもできます。 詳細については、[Apache Spark 構成]https://spark.apache.org/docs/2.4.5/configuration.html#runtime-environment) および[リソースをクラスターにアップロードする方法](../../storage/blobs/storage-quickstart-blobs-storage-explorer.md)に関するページをご覧ください。|
    |ジョブ アップロード ストレージ|展開して追加のオプションを表示します。|
    |ストレージ型|ドロップダウンリストから **[Use Azure Blob to upload]\(Azure BLOB を使用してアップロード\)** または **[Use cluster default storage account to upload]\(クラスターの既定のストレージ アカウントを使用してアップロード\)** を選択します。|
    |ストレージ アカウント|ストレージ アカウントを入力します。|
    |ストレージ キー|ストレージ キーを入力します。|
    |ストレージ コンテナー|**[ストレージ アカウント]** と **[ストレージ キー]** の入力後、ドロップダウン リストからストレージ コンテナーを選択します。|

    ![[Spark Submission]\(Spark 送信\) ダイアログ ボックス 1](./media/intellij-tool-synapse/create-synapse-configuration03.png)

4. **[SparkJobRun]** アイコンを選択して、プロジェクトを選択した Spark プールに送信します。 **[Remote Spark Job in Cluster]\(クラスターのリモート Spark ジョブ\)** タブの下部には、ジョブの実行の進行状況が表示されます。 赤いボタンを選択すると、アプリケーションを停止できます。

    ![[Apache Spark Submission]\(Apache Spark 送信\) ウィンドウ](./media/intellij-tool-synapse/remotely-run-synapse.png)

    ![[Spark Submission]\(Spark 送信\) ダイアログ ボックス 2](./media/intellij-tool-synapse/remotely-run-result.png)

## <a name="local-rundebug-apache-spark-applications"></a>Apache Spark アプリケーションをローカル実行およびデバッグする

以下の手順に従って、Apache Spark ジョブのローカル実行とローカル デバッグを設定できます。

### <a name="scenario-1-do-local-run"></a>シナリオ 1:ローカル実行する

1. **[Run/Debug Configurations]\(実行/デバッグ構成\)** ダイアログを開き、プラス記号 ( **+** ) を選択します。 次に **[Apache Spark on Synapse]** オプションを選択します。 **[名前]** 、 **[Main class name]\(メイン クラス名\)** に保存する情報を入力します。

    ![Intellij の実行/デバッグ構成 (ローカル実行 1)](./media/intellij-tool-synapse/local-run-synapse.png)

    - [環境変数] と [WinUtils.exe Location]\(WinUtils.exe の場所\) は、Windows ユーザーのみを対象としています。
    - 環境変数:システム環境変数は、以前設定されている場合は自動検出され、手動で追加する必要はありません。
    - [[WinUtils.exe Location]\(WinUtils.exe の場所\)](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe):WinUtils の場所を指定するには、右側にあるフォルダー アイコンを選択します。

2. 次に、ローカル再生ボタンを選択します。

    ![Intellij の実行/デバッグ構成 (ローカル実行 2)](./media/intellij-tool-synapse/local-run-synapse01.png)

3. ローカル実行が完了したら、スクリプトに出力が含まれている場合は、 **[data]**  >  **[__default__]** から出力ファイルを確認できます。

    ![Intellij プロジェクトのローカル実行の結果 1](./media/intellij-tool-synapse/spark-local-run-result.png)

### <a name="scenario-2-do-local-debugging"></a>シナリオ 2: ローカル デバッグを実行する

1. **LogQuery** スクリプトを開き、ブレークポイントを設定します。
2. **[ローカル デバッグ]** アイコンを選択してローカル デバッグを実行します。

    ![Intellij プロジェクトのローカル実行の結果 2](./media/intellij-tool-synapse/local-debug-synapse.png)

## <a name="access-and-manage-synapse-workspace"></a>Synapse ワークスペースへアクセスして管理する

Azure Toolkit for IntelliJ 内の Azure Explorer では、さまざまな操作を実行できます。 メニュー バーから、 **[表示]**  >  **[ツール ウィンドウ]**  >  **[Azure Explorer]** に移動します。

### <a name="launch-workspace"></a>ワークスペースを起動する

1. Azure Explorer から、 **[Apache Spark on Synapse]** に移動して展開します。

    ![IntelliJ IDEA Azure Explorer のメイン ビュー](./media/intellij-tool-synapse/azure-explorer-workspace.png)

2. ワークスペースを右クリックし、 **[ワークスペースの起動]** を選択すると、Web サイトが開きます。

    ![Spark ジョブ ビューのアプリケーションの詳細 1](./media/intellij-tool-synapse/launch-workspace-synapse.png)

    ![Spark ジョブ ビューのアプリケーションの詳細 2](./media/intellij-tool-synapse/launch-workspace-result.png)

## <a name="spark-console"></a>Spark コンソール

Spark Local Console(Scala) を実行するか、Spark Livy Interactive Session Console(Scala) を実行することができます。

### <a name="spark-local-console-scala"></a>Spark ローカル コンソール (Scala)

WINUTILS.EXE の前提条件を満たしていることを確認します。

1. メニュー バーから、 **[Run]\(実行\)**  >  **[Edit Configurations]\(構成の編集\)** に移動します。
2. **[Run/Debug Configurations]\(実行/デバッグ構成\)** ウィンドウから、左側のペインで、 **[Apache Spark on Synapse]**  >  **[[Spark on Synapse] myApp]** を選択します。
3. メイン ウィンドウで、 **[Locally Run]\(ローカル実行\)** タブを選択します。
4. 次の値を指定し、 **[OK]** を選択します。

    |プロパティ |値 |
    |----|----|
    |環境変数|HADOOP_HOME の値が正しいことを確認します。|
    |WINUTILS.exe の場所|パスが正しいことを確認します。|

    ![Local Console の構成の設定](./media/intellij-tool-synapse/local-console-synapse01.png)

5. [Project]\(プロジェクト\) から **myApp** > **src** > **main** > **scala** > **myApp** に移動します。
6. メニュー バーから、 **[Tools]\(ツール\)**  >  **[Spark console]\(Spark コンソール\)**  >  **[Run Spark Local Console(Scala)]\(Spark Local Console(Scala) の実行\)** に移動します。
7. 次に、依存関係を自動修正するかどうかを確認する 2 つのダイアログ ボックスが表示されます。 そうする場合は、 **[Auto Fix]\(自動修正\)** を選択します。

    ![IntelliJ IDEA Spark の自動修正ダイアログ 1](./media/intellij-tool-synapse/intellij-console-autofix1.png)

    ![IntelliJ IDEA Spark の自動修正ダイアログ 2](./media/intellij-tool-synapse/intellij-console-autofix2.png)

8. コンソールは次の図のようになります。 コンソール ウィンドウに「`sc.appName`」と入力し、Ctrl + Enter キーを押します。 結果が表示されます。 赤いボタンを選択すると、ローカル コンソールを終了できます。

    ![IntelliJ IDEA のローカル コンソールの結果](./media/intellij-tool-synapse/local-console-result.png)

### <a name="spark-livy-interactive-session-console-scala"></a>Spark Livy Interactive Session Console (Scala)

これは、IntelliJ 2018.2 および 2018.3 でのみサポートされます。

1. メニュー バーから、 **[Run]\(実行\)**  >  **[Edit Configurations]\(構成の編集\)** に移動します。

2. **[実行/デバッグ構成]** ウィンドウから、左側のペインで、 **[Apache Spark on Synapse]**  >  **[[Spark on Synapse] myApp]** を選択します。

3. メイン ウィンドウから **[Remotely Run in Cluster]\(クラスターでリモート実行\)** タブを選択します。

4. 次の値を指定し、 **[OK]** を選択します。

    |プロパティ |値 |
    |----|----|
    |メイン クラス名| Main クラス名を選択します。| 
    |Spark プール|アプリケーションを実行する Spark プールを選択します。|
    ||

    ![対話型コンソールセットの構成](./media/intellij-tool-synapse/interactive-console-configuration.png)

5. [Project]\(プロジェクト\) から **myApp** > **src** > **main** > **scala** > **myApp** に移動します。

6. メニュー バーで、 **[Tools]\(ツール\)**  >  **[Spark console]\(Spark コンソール\)**  >  **[Run Spark Livy Interactive Session Console(Scala)]\(Spark Livy Interactive Session Console(Scala) の実行\)** に移動します。
7. コンソールは次の図のようになります。 コンソール ウィンドウに「`sc.appName`」と入力し、Ctrl + Enter キーを押します。 結果が表示されます。 赤いボタンを選択すると、ローカル コンソールを終了できます。

    ![IntelliJ IDEA の対話型コンソールの結果](./media/intellij-tool-synapse/interactive-console-result.png)

### <a name="send-selection-to-spark-console"></a>選択内容を Spark コンソールに送信する

ローカル コンソールまたは Livy Interactive Session Console(Scala) に何らかのコードを送信することで、スクリプトの結果を確認することをお勧めします。 それを行うには、Scala ファイル内の一部のコードを強調表示し、 **[選択内容を Spark コンソールに送信する]** を右クリックします。 選択したコードがコンソールに送信され、実行されます。 結果は、コンソールのコードの後に表示されます。 コンソールでは、既存のエラーが確認されます。

   ![選択内容を Spark コンソールに送信する](./media/intellij-tool-synapse/send-selection-to-console.png)

## <a name="next-steps"></a>次のステップ

- [Azure Synapse Analytics](../overview-what-is.md)
- [Azure Synapse Analytics ワークスペース用の新しい Apache Spark プールを作成する](../../synapse-analytics/quickstart-create-apache-spark-pool-studio.md)