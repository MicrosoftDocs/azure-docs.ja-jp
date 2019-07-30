---
title: チュートリアル - Azure Toolkit for IntelliJ:HDInsight クラスター向けの Spark アプリケーションを作成する
description: チュートリアル - Azure Toolkit for IntelliJ を使用して Scala で記述された Spark アプリケーションを開発し、HDInsight Spark クラスターに送信します。
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 06/26/2019
ms.author: hrasheed
ms.openlocfilehash: 32f5ff2ebc9d938b1936d7f2929af83d552a543d
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/25/2019
ms.locfileid: "68489855"
---
# <a name="tutorial-use-azure-toolkit-for-intellij-to-create-apache-spark-applications-for-an-hdinsight-cluster"></a>チュートリアル:Azure Toolkit for IntelliJ を使用して HDInsight クラスター向けの Apache Spark アプリケーションを作成する

このチュートリアルでは、Azure Toolkit for IntelliJ プラグインを使用して [Scala](https://www.scala-lang.org/) で記述された Apache Spark アプリケーションを開発してから、それを IntelliJ 統合開発環境 (IDE) から直接 HDInsight Spark クラスターに送信する方法を説明します。 このプラグインには、次のような使い方があります。

* Scala Spark アプリケーションを開発して HDInsight Spark クラスターに送信する。
* Azure HDInsight Spark クラスター リソースにアクセスする。
* Scala Spark アプリケーションをローカルで開発して実行する。

このチュートリアルでは、以下の内容を学習します。
> [!div class="checklist"]
> * Azure Toolkit for IntelliJ プラグインを使用する
> * Apache Spark アプリケーションを開発する
> * Azure HDInsight クラスターにアプリケーションを送信する

## <a name="prerequisites"></a>前提条件

* HDInsight での Apache Spark クラスター。 手順については、「 [Create Apache Spark clusters in Azure HDInsight (Azure HDInsight での Apache Spark クラスターの作成)](apache-spark-jupyter-spark-sql.md)」を参照してください。

* [Oracle Java Development Kit](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)。  このチュートリアルでは、Java バージョン 8.0.202 を使用します。

* IntelliJ IDEA。 この記事では、[IntelliJ IDEA Community Version 2018.3.4](https://www.jetbrains.com/idea/download/) を使用します。

* Azure Toolkit for IntelliJ。  「[Azure Toolkit for IntelliJ のインストール](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-installation?view=azure-java-stable)」を参照してください。

## <a name="install-scala-plugin-for-intellij-idea"></a>IntelliJ IDEA 用の Scala プラグインをインストールする

Scala プラグインをインストールするには、次の手順を実行します。

1. IntelliJ IDEA を開きます。

2. ようこそ画面で **[構成]**  >  **[プラグイン]** の順に移動し、 **[プラグイン]** ウィンドウを開きます。
   
    ![Scala プラグインの有効化](./media/apache-spark-intellij-tool-plugin/enable-scala-plugin.png)

3. 新しいウィンドウに表示される Scala プラグインの **[インストール]** を選択します。  

    ![Scala プラグインのインストール](./media/apache-spark-intellij-tool-plugin/install-scala-plugin.png)

4. プラグインが正常にインストールされたら、IDE を再起動する必要があります。

## <a name="create-a-spark-scala-application-for-an-hdinsight-spark-cluster"></a>HDInsight Spark クラスター向けの Spark Scala アプリケーションの作成

1. IntelliJ IDEA を起動し、 **[Create New Project]\(新しいプロジェクトの作成\)** を選択して、 **[New Project]\(新しいプロジェクト\)** ウィンドウを開きます。

2. 左側のウィンドウの **[Azure Spark/HDInsight]** を選択します。

3. メイン ウィンドウで **[Spark Project (Scala)]\(Spark プロジェクト (Scala)\)** を選択します。

4. **[Build tool]\(ビルド ツール\)** ドロップダウン ボックスの一覧で、次のいずれかを選択します。
   * Scala プロジェクト作成ウィザードをサポートする場合は **Maven**。
   * 依存関係を管理し、Scala プロジェクトをビルドする場合は **SBT**。

     ![[New Project]\(新しいプロジェクト\) ダイアログ ボックス](./media/apache-spark-intellij-tool-plugin/create-hdi-scala-app.png)

5. **[次へ]** を選択します。

6. **[New Project]\(新しいプロジェクト\)** ウィンドウで、次の情報を指定します。  

    |  プロパティ   | 説明   |  
    | ----- | ----- |  
    |プロジェクト名| 名前を入力します。  このチュートリアルでは `myApp` を使用します。|  
    |Project&nbsp;location (プロジェクトの場所)| プロジェクトを保存する任意の場所を入力します。|
    |Project SDK (プロジェクト SDK)| IDEA を初めて使用するとき、これは空白の場合があります。  **[New]\(新規作成\)** を選択し、自分の JDK に移動します。|
    |Spark バージョン|作成ウィザードにより、Spark SDK と Scala SDK の適切なバージョンが統合されます。 Spark クラスターのバージョンが 2.0 より前の場合は、**Spark 1.x** を選択します。 それ以外の場合は、**Spark 2.x** を選択します。 この例では、**Spark 2.3.0 (Scala 2.11.8)** を使用します。|

    ![Spark SDK の選択](./media/apache-spark-intellij-tool-plugin/hdi-new-project.png)

7. **[完了]** を選択します。  プロジェクトが使用可能になるまで数分かかる場合があります。

8. Spark プロジェクトでは、アーティファクトが自動的に作成されます。 次の操作を実行して、アーティファクトを表示します。

   a. メニュー バーから、 **[ファイル]**  >  **[プロジェクトの構造...]** に移動します。

   b. **[プロジェクトの構造]** ウィンドウで、 **[アーティファクト]** を選択します。  

   c. アーティファクトの表示後、 **[キャンセル]** をクリックします。

      ![アーティファクト情報を示すダイアログ ボックス](./media/apache-spark-intellij-tool-plugin/default-artifact.png)

9. 次の操作を実行して、アプリケーション ソース コードを追加します。

    a. [プロジェクト] から、 **[myApp]**  >  **[src]**  >  **[main]**  >  **[scala]** に移動します。  

    b. **[scala]** を右クリックし、 **[新規作成]**  >  **[Scala Class]\(Scala クラス\)** に移動します。

   ![プロジェクトから Scala クラスを作成するためのコマンド](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code.png)

   c. **[Create New Scala Class]\(新規 Scala クラスの作成\)** ダイアログ ボックスで、名前を指定し、 **[Kind]\(種類\)** ドロップダウン リストで **[Object]\(オブジェクト\)** を選択して、 **[OK]** をクリックします。

     ![[Create New Scala Class]\(新規 Scala クラスの作成\) ダイアログ ボックス](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code-object.png)

   d. **myApp.scala** ファイルがメイン ビューで開きます。 既定のコードを次のコードに置き換えます。  

        import org.apache.spark.SparkConf
        import org.apache.spark.SparkContext
    
        object myApp{
            def main (arg: Array[String]): Unit = {
            val conf = new SparkConf().setAppName("myApp")
            val sc = new SparkContext(conf)
    
            val rdd = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
    
            //find the rows that have only one digit in the seventh column in the CSV file
            val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)
    
            rdd1.saveAsTextFile("wasbs:///HVACOut")
            }
    
        }

    このコードは HVAC.csv (すべての HDInsight Spark クラスターで使用可能) からデータを読み取り、CSV ファイルの 7 番目の列に 1 桁の数字のみが含まれる行を取得し、出力をクラスター用の既定のストレージ コンテナーの下にある `/HVACOut` に書き込みます。

## <a name="connect-to-your-hdinsight-cluster"></a>HDInsight クラスターに接続する
[Azure サブスクリプションにサインイン](#sign-in-to-your-azure-subscription)するか、[HDInsight クラスターをリンク](#link-a-cluster)して開始できます。Ambari のユーザー名/パスワードまたはドメイン参加の資格情報を使用して HDInsight クラスターに接続してください。

### <a name="sign-in-to-your-azure-subscription"></a>Azure サブスクリプションにサインインします。

1. メニュー バーから、 **[表示]**  >  **[ツール ウィンドウ]**  >  **[Azure Explorer]** に移動します。
       
   ![Azure Explorer のリンク](./media/apache-spark-intellij-tool-plugin/show-azure-explorer.png)

2. Azure Explorer から、 **[Azure]** ノードを右クリックし、 **[サインイン]** を選択します。
   
   ![Azure Explorer のリンク](./media/apache-spark-intellij-tool-plugin/explorer-rightclick-azure.png)

3. **[Azure Sign In]\(Azure サインイン\)** ダイアログ ボックスで、 **[デバイスのログイン]** を選択してから、 **[サインイン]** を選択します。

    ![[Azure Sign In]\(Azure サインイン\) ダイアログ ボックス](./media/apache-spark-intellij-tool-plugin/view-explorer-2.png)

4. **[Azure Device Login]\(Azure デバイスのログイン\)** ダイアログ ボックスで **[Copy&Open]\(コピーして開く\)** をクリックします。
   
   ![[Azure Sign In]\(Azure サインイン\) ダイアログ ボックス](./media/apache-spark-intellij-tool-plugin/view-explorer-5.png)

5. ブラウザー インターフェイスで、コードを貼り付けて **[次へ]** をクリックします。
   
   ![[Azure Sign In]\(Azure サインイン\) ダイアログ ボックス](./media/apache-spark-intellij-tool-plugin/view-explorer-6.png)

6. 自分の Azure 資格情報を入力して、ブラウザーを閉じます。
   
   ![[Azure Sign In]\(Azure サインイン\) ダイアログ ボックス](./media/apache-spark-intellij-tool-plugin/view-explorer-7.png)

7. サインイン後、 **[Select Subscriptions]\(サブスクリプションの選択\)** ダイアログ ボックスに、その資格情報に関連付けられているすべての Azure サブスクリプションの一覧が表示されます。 サブスクリプションを選択してから **[選択]** ボタンを選択します。

    ![[サブスクリプションの選択] ダイアログ ボックス](./media/apache-spark-intellij-tool-plugin/Select-Subscriptions.png)

8. **Azure Explorer** から、 **[HDInsight]** を展開し、自分のサブスクリプションにある HDInsight Spark クラスターを表示します。

    ![Azure Explorer での HDInsight Spark クラスター](./media/apache-spark-intellij-tool-plugin/view-explorer-3.png)

9.  クラスターに関連付けられているリソース (ストレージ アカウントなど) を表示するには、クラスター名ノードをさらに展開します。

    ![展開されたクラスター名ノード](./media/apache-spark-intellij-tool-plugin/view-explorer-4.png)

### <a name="link-a-cluster"></a>クラスターのリンク

Apache Ambari マネージド ユーザー名を使用して、HDInsight クラスターをリンクできます。 同様に、ドメイン参加済み HDInsight クラスターでは、`user1@contoso.com` などのドメインとユーザー名を使用して、リンクできます。 Livy Service クラスターをリンクすることもできます。

1. メニュー バーから、 **[表示]**  >  **[ツール ウィンドウ]**  >  **[Azure Explorer]** に移動します。

2. Azure Explorer から、 **[HDInsight]** ノードを右クリックし、 **[Link A Cluster]\(クラスターのリンク\)** を選択します。

   ![リンク クラスターのコンテキスト メニュー](./media/apache-spark-intellij-tool-plugin/link-a-cluster-context-menu.png)

3. **[Link A Cluster]\(クラスターのリンク\)** ウィンドウで使用可能なオプションは、 **[Link Resource Type]\(リンクのリソースの種類\)** ドロップダウン リストから選択する値によって異なります。  値を入力して **[OK]** を選択します。

    * **HDInsight クラスター**  
  
        |プロパティ |値 |
        |----|----|
        |リンクのリソースの種類|ドロップダウン リストから **[HDInsight クラスター]** を選択します。|
        |クラスター名/URL| クラスター名を入力します。|
        |認証の種類| **[基本認証]** のままにします|
        |ユーザー名| クラスターのユーザー名を入力します。既定値は admin です。|
        |パスワード| ユーザー名のパスワードを入力します。|
    
        ![HDInsight クラスターのリンク ダイアログ](./media/apache-spark-intellij-tool-plugin/link-hdinsight-cluster-dialog.png)

    * **Livy サービス**  
  
        |プロパティ |値 |
        |----|----|
        |リンクのリソースの種類|ドロップダウン リストから **[Livy Service]\(Livy サービス\)** を選択します。|
        |Livy エンドポイント| Livy エンドポイントを入力します|
        |クラスター名| クラスター名を入力します。|
        |Yarn エンドポイント|省略可能。|
        |認証の種類| **[基本認証]** のままにします|
        |ユーザー名| クラスターのユーザー名を入力します。既定値は admin です。|
        |パスワード| ユーザー名のパスワードを入力します。|

        ![Livy クラスターのリンク ダイアログ](./media/apache-spark-intellij-tool-plugin/link-livy-cluster-dialog.png)

1. **HDInsight** ノードからリンクされたクラスターを確認できます。

   ![リンクされたクラスター](./media/apache-spark-intellij-tool-plugin/linked-cluster.png)

2. また、**Azure 用エクスプローラー**からクラスターのリンクを解除することもできます。

   ![リンク解除されたクラスター](./media/apache-spark-intellij-tool-plugin/unlink.png)

## <a name="run-a-spark-scala-application-on-an-hdinsight-spark-cluster"></a>HDInsight Spark クラスターでの Spark Scala アプリケーションの実行

Scala アプリケーションを作成した後に、クラスターに送信できます。

1. [プロジェクト] から、 **[myApp]**  >  **[src]**  >  **[main]**  >  **[scala]**  >  **[myApp]** に移動します。  **[myApp]** を右クリックし、 **[Submit Spark Application]\(Spark アプリケーションの送信\)** を選択します (多くの場合、リストの最下部にあります)。
    
      ![[Submit Spark Application to HDInsight]\(HDInsight への Spark アプリケーションの送信\) コマンド](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-1.png)

2. **[Submit Spark Application]\(Spark アプリケーションの送信\)** ダイアログ ウィンドウで、 **[1.Spark on HDInsight]\(HDInsight 上の Spark\)** を選択します。

3. **[構成の編集]** ウィンドウで、次の値を指定して **[OK]** を選択します。

    |プロパティ |値 |
    |----|----|
    |Spark クラスター (Linux のみ)|アプリケーションを実行する HDInsight Spark クラスターを選択します。|
    |送信するアーティファクトの選択|既定の設定のままにします。|
    |Main クラス名|既定値は、選択されたファイルのメイン クラスです。 省略記号 ( **...** ) を選択して別のクラスを選ぶことにより、クラスを変更できます。|
    |ジョブの構成|既定のキーと値のどちらかまたは両方を変更できます。 詳細については、[Apache Livy REST API](https://livy.incubator.apache.org./docs/latest/rest-api.html) に関するページを参照してください。|
    |Command line arguments|必要に応じて、main クラスの引数をスペースで区切って入力できます。|
    |参照される JAR と参照されるファイル|参照される JAR およびファイルのパスを入力できます (存在する場合)。 現在 ADLS Gen 2 クラスターのみをサポートする Azure 仮想ファイル システム内のファイルを参照することもできます。 詳細:[Apache Spark 構成](https://spark.apache.org/docs/latest/configuration.html#runtime-environment)。  [リソースをクラスターにアップロードする方法](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-storage-explorer)に関するページも参照してください。|
    |ジョブ アップロード ストレージ|展開して追加のオプションを表示します。|
    |ストレージの種類|ドロップダウン リストから **[Use Azure Blob to upload]\(Azure BLOB を使用してアップロード\)** を選択します。|
    |ストレージ アカウント|ストレージ アカウントを入力します。|
    |ストレージ キー|ストレージ キーを入力します。|
    |ストレージ コンテナー|**[ストレージ アカウント]** と **[ストレージ キー]** の入力後、ドロップダウン リストからストレージ コンテナーを選択します。|

    ![[Spark Submission]\(Spark 送信\) ダイアログ ボックス](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-02.png)

4. **[SparkJobRun]** を選択して、選択したクラスターにプロジェクトを送信します。 **[Remote Spark Job in Cluster]\(クラスターのリモート Spark ジョブ\)** タブの下部にジョブ実行の進行状況が表示されます。 赤いボタンをクリックして、アプリケーションを停止できます。 ジョブ出力にアクセスする方法については、この記事の後の「Azure Toolkit for IntelliJ を使用して HDInsight Spark クラスターにアクセスして管理する」セクションを参照してください。  
      
    ![[Spark Submission]\(Spark 送信\) ウィンドウ](./media/apache-spark-intellij-tool-plugin/hdi-spark-app-result.png)

## <a name="debug-apache-spark-applications-locally-or-remotely-on-an-hdinsight-cluster"></a>HDInsight クラスターで Apache Spark アプリケーションをローカルまたはリモートでデバッグする 

クラスターに Spark アプリケーションを送信するお勧めの方法はほかにもあります。 それは、**実行/デバッグ構成**の IDE でパラメーターを設定する方法です。 詳細については、[Azure Toolkit for IntelliJ を使用して SSH 経由で HDInsight クラスターで Apache Spark アプリケーションをローカルまたはリモートでデバッグする方法](apache-spark-intellij-tool-debug-remotely-through-ssh.md)に関するページを参照してください。

## <a name="access-and-manage-hdinsight-spark-clusters-by-using-azure-toolkit-for-intellij"></a>Azure Toolkit for IntelliJ を使用して HDInsight Spark クラスターにアクセスして管理する

Azure Toolkit for IntelliJ を使用してさまざまな操作を実行できます。  ほとんどの操作は**Azure Explorer** から開始されます。  メニュー バーから、 **[表示]**  >  **[ツール ウィンドウ]**  >  **[Azure Explorer]** に移動します。

### <a name="access-the-job-view"></a>ジョブ ビューにアクセスする

1. Azure Explorer から、 **[HDInsight]**  > \<対象のクラスター > **[ジョブ]** に移動します。

    ![ジョブ ビューのノード](./media/apache-spark-intellij-tool-plugin/job-view-node.png)

2. 右側のウィンドウの **[Spark Job View (Spark ジョブ ビュー)]** タブに、クラスター上で実行されていたすべてのアプリケーションが表示されます。 詳細情報を確認したいアプリケーションの名前を選択します。

    ![Application details](./media/apache-spark-intellij-tool-plugin/view-job-logs.png)

3. 実行中のジョブの基本情報が表示するには、ジョブ グラフにマウス ポインターを合わせます。 各ジョブについて生成されるステージのグラフと情報を確認するには、ジョブ グラフ上のノードを選択します。

    ![ジョブのステージの詳細](./media/apache-spark-intellij-tool-plugin/Job-graph-stage-info.png)

4. *Driver Stderr*、*Driver Stdout*、*Directory Info* などの頻繁に使用されるログを表示するには、 **[Log]\(ログ\)** タブを選択します。

    ![ログの詳細](./media/apache-spark-intellij-tool-plugin/Job-log-info.png)

5. ウィンドウの上部にあるリンクを選択して、Spark 履歴 UI と YARN UI を (アプリケーション レベルで) 表示することもできます。

### <a name="access-the-spark-history-server"></a>Spark 履歴サーバーにアクセスする

1. Azure Explorer から、 **[HDInsight]** を展開します。Spark クラスター名を右クリックし、 **[Open Spark History UI]\(Spark 履歴 UI を開く\)** を選択します。  
2. 入力を求められたら、クラスターの管理者資格情報 (クラスターの設定時に指定したもの) を入力します。

3. Spark 履歴サーバーのダッシュボードでは、実行が終了したばかりのアプリケーションをアプリケーション名を使って探すことができます。 上記のコードでは、`val conf = new SparkConf().setAppName("myApp")` を使用してアプリケーション名を設定しました。 そのため、Spark アプリケーション名は **myApp** です。

### <a name="start-the-ambari-portal"></a>Ambari ポータルを起動する

1. Azure Explorer から、 **[HDInsight]** を展開します。Spark クラスター名を右クリックし、 **[Open Cluster Management Portal (Ambari)]\(クラスター管理ポータルを開く (Ambari)\)** を選択します。  

2. 入力を求められたら、クラスターの管理者資格情報を入力します。 これらの資格情報は、クラスターの設定プロセス中に指定したものです。

### <a name="manage-azure-subscriptions"></a>Azure サブスクリプションの管理

Azure Toolkit for IntelliJ の既定では、すべての Azure サブスクリプションからの Spark クラスターが一覧表示されます。 必要に応じて、アクセスするサブスクリプションを指定できます。  

1. Azure Explorer から、 **[Azure]** ルート ノードを右クリックし、 **[サブスクリプションの選択]** を選択します。  

2. **[サブスクリプションの選択]** ウィンドウから、アクセスしないサブスクリプションのチェック ボックスをオフにして **[閉じる]** を選択します。

## <a name="spark-console"></a>Spark コンソール

Spark Local Console(Scala) を実行したり、Spark Livy Interactive Session Console(Scala) を実行したりすることができます。

### <a name="spark-local-consolescala"></a>Spark Local Console(Scala)

WINUTILS.EXE の前提条件が満たされていることを確認します。

1. メニュー バーから、 **[実行]**  >  **[構成の編集...]** に移動します。

2. **[Run/Debug Configurations]\(実行/デバッグ構成\)** ウィンドウから、左側のウィンドウで、 **[Apache Spark on HDInsight]\(HDInsight 上の Apache Spark\)**  >  **[Spark on HDInsight] myApp** を選択します。

3. メイン ウィンドウから、 **[ローカルで実行]** タブを選択します。

4. 次の値を指定してから **[OK]** を選択します。

    |プロパティ |値 |
    |----|----|
    |ジョブの main クラス|既定値は、選択されたファイルのメイン クラスです。 省略記号 ( **...** ) を選択して別のクラスを選ぶことにより、クラスを変更できます。|
    |環境変数|HADOOP_HOME の値が正しいことを確認します。|
    |WINUTILS.exe の場所|パスが正しいことを確認します。|

    ![ローカル コンソールの構成の設定](./media/apache-spark-intellij-tool-plugin/console-set-configuration.png)

5. [プロジェクト] から、 **[myApp]**  >  **[src]**  >  **[main]**  >  **[scala]**  >  **[myApp]** に移動します。  

6. メニュー バーから、 **[ツール]**  >  **[Spark Console]\(Spark コンソール\)**  >  **[Run Spark Local Console(Scala)]\(Spark ローカル コンソール (Scala) の実行\)** に移動します。

7. 依存関係を自動修正するかどうかを尋ねる 2 つのダイアログ ボックスが表示される場合があります。 その場合、 **[自動修正]** を選択します。

    ![Spark 自動修正 1](./media/apache-spark-intellij-tool-plugin/console-auto-fix1.png)

    ![Spark 自動修正 2](./media/apache-spark-intellij-tool-plugin/console-auto-fix2.png)

8. コンソールは次の画像のように表示されます。 コンソール ウィンドウで `sc.appName` を入力してから、Ctrl + Enter キーを押します。  結果が表示されます。 ローカルのコンソールを終了するには、赤いボタンをクリックします。

    ![ローカル コンソールの結果](./media/apache-spark-intellij-tool-plugin/local-console-result.png)

### <a name="spark-livy-interactive-session-consolescala"></a>Spark Livy Interactive Session Console(Scala)

これは、IntelliJ 2018.2 および 2018.3 上でのみサポートされます。

1. メニュー バーから、 **[実行]**  >  **[構成の編集...]** に移動します。

2. **[Run/Debug Configurations]\(実行/デバッグ構成\)** ウィンドウから、左側のウィンドウで、 **[Apache Spark on HDInsight]\(HDInsight 上の Apache Spark\)**  >  **[Spark on HDInsight] myApp** を選択します。

3. メイン ウィンドウから、 **[Remotely Run in Cluster]\(クラスターでリモート実行\)** タブを選択します。

4. 次の値を指定してから **[OK]** を選択します。

    |プロパティ |値 |
    |----|----|
    |Spark クラスター (Linux のみ)|アプリケーションを実行する HDInsight Spark クラスターを選択します。|
    |Main クラス名|既定値は、選択されたファイルのメイン クラスです。 省略記号 ( **...** ) を選択して別のクラスを選ぶことにより、クラスを変更できます。|

    ![対話型コンソールの構成の設定](./media/apache-spark-intellij-tool-plugin/interactive-console-configuration.png)

5. [プロジェクト] から、 **[myApp]**  >  **[src]**  >  **[main]**  >  **[scala]**  >  **[myApp]** に移動します。  

6. メニュー バーから、 **[ツール]**  >  **[Spark Console]\(Spark コンソール\)**  >  **[Run Spark Livy Interactive Session Console(Scala)]\(Spark Livy インタラクティブ セッション コンソール (Scala) の実行\)** に移動します。

7. コンソールは次の画像のように表示されます。 コンソール ウィンドウで `sc.appName` を入力してから、Ctrl + Enter キーを押します。  結果が表示されます。 ローカルのコンソールを終了するには、赤いボタンをクリックします。

    ![Interactive Console の結果](./media/apache-spark-intellij-tool-plugin/interactive-console-result.png)

### <a name="send-selection-to-spark-console"></a>選択項目の Spark コンソールへの送信

一部のコードをローカルのコンソールまたは Livy 対話式セッション コンソール (Scala) に送信して、スクリプトの結果を事前に確認すると便利です。 Scala ファイル内の一部のコードを強調表示し、右クリックして **[Send Selection To Spark Console]\(選択項目を Spark コンソールに送信する\)** を選択します。 選択したコードがコンソールに送信され、実行されます。 結果は、コンソール内のコードの後に表示されます。 コンソールは、エラーが存在する場合は、エラーをチェックします。  

   ![選択項目の Spark コンソールへの送信](./media/apache-spark-intellij-tool-plugin/send-selection-to-console.png)

## <a name="reader-only-role"></a>読み取り専用ロール

ユーザーが読み取り専用ロールのアクセス許可を使用してジョブをクラスターに送信する場合、Ambari の資格情報が必要です。

### <a name="link-cluster-from-context-menu"></a>コンテキスト メニューからクラスターをリンクする

1. 読み取り専用ロールのアカウントでサインインします。
       
2. **Azure Explorer** で **[HDInsight]** を展開し、自分のサブスクリプションにある HDInsight クラスターを表示します。 **"Role:Reader"** とマークされているクラスターには、読み取り専用ロールのアクセス許可しかありません。

    ![Azure Explorer での HDInsight Spark クラスター](./media/apache-spark-intellij-tool-plugin/view-explorer-15.png)

3. 読み取り専用ロールのアクセス許可があるクラスターを右クリックします。 コンテキスト メニューで **[Link this cluster]\(このクラスターをリンク\)** を選択して、クラスターをリンクします。 Ambari のユーザー名とパスワードを入力します。

  
    ![Azure Explorer での HDInsight Spark クラスター](./media/apache-spark-intellij-tool-plugin/view-explorer-11.png)

4. クラスターが正常にリンクされると、HDInsight が更新されます。
   クラスターのステージはリンク状態になります。
  
    ![Azure Explorer での HDInsight Spark クラスター](./media/apache-spark-intellij-tool-plugin/view-explorer-8.png)

### <a name="link-cluster-by-expanding-jobs-node"></a>ジョブ ノードを展開してクラスターをリンクする

1. **Jobs** ノードをクリックします。 **[Cluster Job Access Denied]\(拒否されたクラスター ジョブ アクセス\)** ウィンドウが表示されます。
   
2. **[Link this cluster]\(このクラスターをリンク\)** をクリックして、クラスターをリンクします。
   
    ![Azure Explorer での HDInsight Spark クラスター](./media/apache-spark-intellij-tool-plugin/view-explorer-9.png)

### <a name="link-cluster-from-rundebug-configurations-window"></a>[Run/Debug Configurations]\(実行/デバッグ構成\) ウィンドウからクラスターをリンクする

1. HDInsight 構成を作成します。 次に、 **[Remotely Run in Cluster]\(クラスターでリモート実行\)** を選択します。
   
2. **[Spark clusters(Linux only)]\(Spark クラスター (Linux のみ)\)** で、読み取り専用ロールのアクセス許可があるクラスターを選択します。 警告メッセージが表示されます。 **[Link this cluster]\(このクラスターをリンク\)** をクリックして、クラスターをリンクできます。
   
   ![Azure Explorer での HDInsight Spark クラスター](./media/apache-spark-intellij-tool-plugin/create-config-1.png)
   
### <a name="view-storage-accounts"></a>ストレージ アカウントを表示する

* 読み取り専用ロールのアクセス許可があるクラスターで、**Storage Accounts** ノードをクリックします。 **[Storage Access Denied]\(拒否されたストレージ アクセス\)** ウィンドウが表示されます。 **[Azure Storage Explorer を開く]** をクリックして Storage Explorer を開くことができます。
     
   ![Azure Explorer での HDInsight Spark クラスター](./media/apache-spark-intellij-tool-plugin/view-explorer-14.png)

   ![Azure Explorer での HDInsight Spark クラスター](./media/apache-spark-intellij-tool-plugin/view-explorer-10.png)

* リンクされたクラスターで、**Storage Accounts** ノードをクリックします。 **[Storage Access Denied]\(拒否されたストレージ アクセス\)** ウィンドウが表示されます。 **[Azure Storage Explorer を開く]** をクリックして Storage Explorer を開くことができます。
     
   ![Azure Explorer での HDInsight Spark クラスター](./media/apache-spark-intellij-tool-plugin/view-explorer-13.png)

   ![Azure Explorer での HDInsight Spark クラスター](./media/apache-spark-intellij-tool-plugin/view-explorer-12.png)

## <a name="convert-existing-intellij-idea-applications-to-use-azure-toolkit-for-intellij"></a>Azure Toolkit for IntelliJ を使用できるように既存の IntelliJ IDEA アプリケーションを変換する

IntelliJ IDEA で作成した既存の Spark Scala アプリケーションを、Azure Toolkit for IntelliJ に対応するように変換することができます。 その後、プラグインを使用して、そのアプリケーションを HDInsight Spark クラスターに送信できます。

1. IntelliJ IDEA で作成した既存の Spark Scala アプリケーションの、関連付けられている .iml ファイルを開きます。

2. ルート レベルに、次のような **module** 要素があります。
   
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4">

   **module** 要素が次のようになるように、この要素を編集して `UniqueKey="HDInsightTool"` を追加します。
   
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4" UniqueKey="HDInsightTool">

3. 変更を保存します。 これで、アプリケーションは Azure Toolkit for IntelliJ との互換性を持つようになります。 これをテストするには、[プロジェクト] でプロジェクト名を右クリックします。 これで、ポップアップ メニューで、 **[Submit Spark Application to HDInsight]\(HDInsight への Spark アプリケーションの送信\)** を選択できるようになります。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このアプリケーションを引き続き使用しない場合は、次の手順で作成したクラスターを削除します。

1. [Azure Portal](https://portal.azure.com/) にサインインします。

1. 上部の**検索**ボックスに「**HDInsight**」と入力します。

1. **[サービス]** の下の **[HDInsight クラスター]** を選択します。

1. 表示される HDInsight クラスターの一覧で、このチュートリアル用に作成したクラスターの横にある **[...]** を選択します。

1. **[削除]** を選択します。 **[はい]** を選択します。

![HDInsight クラスターの削除](./media/apache-spark-intellij-tool-plugin/hdinsight-azure-portal-delete-cluster.png "HDInsight クラスターの削除")

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure Toolkit for IntelliJ プラグインを使用して [Scala](https://www.scala-lang.org/) で記述された Apache Spark アプリケーションを開発してから、それを IntelliJ 統合開発環境 (IDE) から直接 HDInsight Spark クラスターに送信する方法を学習しました。 次の記事に進んで、Apache Spark に登録したデータを Power BI などの BI 分析ツールに取り込む方法を確認してください。

> [!div class="nextstepaction"]
> [BI ツールを使用したデータの分析](apache-spark-use-bi-tools.md)
