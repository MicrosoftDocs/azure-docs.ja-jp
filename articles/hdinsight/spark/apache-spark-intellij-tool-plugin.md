---
title: Azure Toolkit for IntelliJ:Spark アプリ - HDInsight
description: Azure Toolkit for IntelliJ を使用して Scala で記述された Spark アプリケーションを開発し、HDInsight Spark クラスターに送信します。
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 04/14/2020
ms.openlocfilehash: ced066623d23c6894745374ace812454c52f3109
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/13/2021
ms.locfileid: "103420019"
---
# <a name="use-azure-toolkit-for-intellij-to-create-apache-spark-applications-for-hdinsight-cluster"></a>Azure Toolkit for IntelliJ を使用して HDInsight クラスター向けの Apache Spark アプリケーションを作成する

この記事では、IntelliJ IDE 用の **Azure Toolkit** プラグインを使用して Azure HDInsight 上の Apache Spark アプリケーションを開発する方法について説明します。 [Azure HDInsight](../hdinsight-overview.md) は、クラウドでのオープンソースのマネージド分析サービスです。 このサービスでは、Hadoop、Apache Spark、Apache Hive、Apache Kafka などのオープンソース フレームワークを使用できます。

**Azure Toolkit** プラグインには、次のような使い方があります。

* Scala Spark アプリケーションを開発して HDInsight Spark クラスターに送信する。
* Azure HDInsight Spark クラスター リソースにアクセスする。
* Scala Spark アプリケーションをローカルで開発して実行する。

この記事では、次のことについて説明します。
> [!div class="checklist"]
> * Azure Toolkit for IntelliJ プラグインを使用する
> * Apache Spark アプリケーションを開発する
> * Azure HDInsight クラスターにアプリケーションを送信する

## <a name="prerequisites"></a>前提条件

* HDInsight での Apache Spark クラスター。 手順については、「 [Create Apache Spark clusters in Azure HDInsight (Azure HDInsight での Apache Spark クラスターの作成)](apache-spark-jupyter-spark-sql.md)」を参照してください。

* [Oracle Java Development Kit](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)。  この記事では、Java バージョン 8.0.202 を使用します。

* IntelliJ IDEA。 この記事では、[IntelliJ IDEA Community Version 2018.3.4](https://www.jetbrains.com/idea/download/) を使用します。

* Azure Toolkit for IntelliJ。  「[Azure Toolkit for IntelliJ のインストール](/azure/developer/java/toolkit-for-intellij/)」を参照してください。

## <a name="install-scala-plugin-for-intellij-idea"></a>IntelliJ IDEA 用の Scala プラグインをインストールする

Scala プラグインをインストールする手順:

1. IntelliJ IDEA を開きます。

2. ようこそ画面で **[構成]**  >  **[プラグイン]** の順に移動し、 **[プラグイン]** ウィンドウを開きます。

    ![IntelliJ IDEA で Scala プラグインを有効にする](./media/apache-spark-intellij-tool-plugin/enable-scala-plugin1.png)

3. 新しいウィンドウに表示される Scala プラグインの **[インストール]** を選択します。  

    ![IntelliJ IDEA で Scala プラグインをインストールする](./media/apache-spark-intellij-tool-plugin/install-scala-plugin.png)

4. プラグインが正常にインストールされたら、IDE を再起動する必要があります。

## <a name="create-a-spark-scala-application-for-an-hdinsight-spark-cluster"></a>HDInsight Spark クラスター向けの Spark Scala アプリケーションの作成

1. IntelliJ IDEA を起動し、 **[Create New Project]\(新しいプロジェクトの作成\)** を選択して、 **[New Project]\(新しいプロジェクト\)** ウィンドウを開きます。

2. 左側のウィンドウの **[Azure Spark/HDInsight]** を選択します。

3. メイン ウィンドウで **[Spark Project (Scala)]\(Spark プロジェクト (Scala)\)** を選択します。

4. **[Build tool]\(ビルド ツール\)** ドロップダウン リストで、次のいずれかのオプションを選択します。
   * Scala プロジェクト作成ウィザードをサポートする場合は **Maven**。
   * 依存関係を管理し、Scala プロジェクトをビルドする場合は **SBT**。

     ![IntelliJ IDEA の [New Project]\(新しいプロジェクト\) ダイアログ ボックス](./media/apache-spark-intellij-tool-plugin/create-hdi-scala-app.png)

5. **[次へ]** を選択します。

6. **[New Project]\(新しいプロジェクト\)** ウィンドウで、次の情報を指定します。  

    |  プロパティ   | 説明   |  
    | ----- | ----- |  
    |プロジェクト名| 名前を入力します。  この記事では、`myApp` を使用します。|  
    |Project&nbsp;location (プロジェクトの場所)| プロジェクトを保存する場所を入力します。|
    |Project SDK (プロジェクト SDK)| IDEA を初めて使用するとき、このフィールドは空白の場合があります。  **[New]\(新規作成\)** を選択し、自分の JDK に移動します。|
    |Spark バージョン|作成ウィザードにより、Spark SDK と Scala SDK の適切なバージョンが統合されます。 Spark クラスターのバージョンが 2.0 より前の場合は、 **[Spark 1.x]** を選択します。 それ以外の場合は、 **[Spark2.x]** を選択します。 この例では、**Spark 2.3.0 (Scala 2.11.8)** を使用します。|

    ![Apache Spark SDK を選択する](./media/apache-spark-intellij-tool-plugin/intellij-new-project.png)

7. **[完了]** を選択します。  プロジェクトが使用可能になるまで数分かかる場合があります。

8. Spark プロジェクトによって自動的に成果物が作成されます。 成果物を表示するには、次の手順を実行します。

   a. メニュー バーから、 **[ファイル]**  >  **[プロジェクトの構造...]** に移動します。

   b. **[プロジェクトの構造]** ウィンドウで、 **[アーティファクト]** を選択します。  

   c. アーティファクトの表示後、 **[キャンセル]** をクリックします。

      ![ダイアログ ボックスの成果物情報](./media/apache-spark-intellij-tool-plugin/default-artifact-dialog.png)

9. 次の手順を行って、アプリケーション ソース コードを追加します。

    a. [プロジェクト] から、 **[myApp]**  >  **[src]**  >  **[main]**  >  **[scala]** に移動します。  

    b. **[scala]** を右クリックし、 **[新規作成]**  >  **[Scala Class]\(Scala クラス\)** に移動します。

   ![プロジェクトから Scala クラスを作成するためのコマンド](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code.png)

   c. **[Create New Scala Class]\(新規 Scala クラスの作成\)** ダイアログ ボックスで、名前を指定し、 **[Kind]\(種類\)** ドロップダウン リストで **[Object]\(オブジェクト\)** を選択して、 **[OK]** をクリックします。

     ![[Create New Scala Class]\(新規 Scala クラスの作成\) ダイアログ ボックス](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code-object.png)

   d. **myApp.scala** ファイルがメイン ビューで開きます。 既定のコードを次のコードに置き換えます。  

    ```scala
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
    ```

    このコードは HVAC.csv (すべての HDInsight Spark クラスターで使用可能) からデータを読み取り、CSV ファイルの 7 番目の列に 1 桁の数字のみが含まれる行を取得し、出力をクラスター用の既定のストレージ コンテナーの下にある `/HVACOut` に書き込みます。

## <a name="connect-to-your-hdinsight-cluster"></a>HDInsight クラスターに接続する

ユーザーは、[Azure サブスクリプションにサインインする](#sign-in-to-your-azure-subscription)か、[HDInsight クラスターをリンクする](#link-a-cluster)ことができます。 Ambari のユーザー名とパスワードまたはドメイン参加済みの資格情報を使用して、HDInsight クラスターに接続します。

### <a name="sign-in-to-your-azure-subscription"></a>Azure サブスクリプションにサインインします。

1. メニュー バーから、 **[表示]**  >  **[ツール ウィンドウ]**  >  **[Azure Explorer]** に移動します。

   ![IntelliJ IDEA で Azure Explorer を表示する](./media/apache-spark-intellij-tool-plugin/show-azure-explorer1.png)

2. Azure Explorer から、 **[Azure]** ノードを右クリックし、 **[サインイン]** を選択します。

   ![IntelliJ IDEA エクスプローラーで Azure を右クリックする](./media/apache-spark-intellij-tool-plugin/explorer-rightclick-azure.png)

3. **[Azure Sign In]\(Azure サインイン\)** ダイアログ ボックスで、 **[デバイスのログイン]** を選択してから、 **[サインイン]** を選択します。

    ![IntelliJ IDEA の [Azure Sign In]\(Azure サインイン\) の [デバイスのログイン]](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer2.png)

4. **[Azure Device Login]\(Azure デバイスのログイン\)** ダイアログ ボックスで **[Copy&Open]\(コピーして開く\)** をクリックします。

   ![IntelliJ IDEA の [Azure Device Login]\(Azure デバイスのログイン\)](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer5.png)

5. ブラウザー インターフェイスで、コードを貼り付けて **[次へ]** をクリックします。

   ![Microsoft の HDI の [コードの入力] ダイアログ](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer6.png)

6. 自分の Azure 資格情報を入力して、ブラウザーを閉じます。

   ![Microsoft の HDI のメールの入力ダイアログ](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer7.png)

7. サインイン後、 **[Select Subscriptions]\(サブスクリプションの選択\)** ダイアログ ボックスに、その資格情報に関連付けられているすべての Azure サブスクリプションの一覧が表示されます。 サブスクリプションを選択してから **[選択]** ボタンを選択します。

    ![[サブスクリプションの選択] ダイアログ ボックス](./media/apache-spark-intellij-tool-plugin/Select-Subscriptions.png)

8. **Azure Explorer** から、 **[HDInsight]** を展開し、自分のサブスクリプションにある HDInsight Spark クラスターを表示します。

    ![IntelliJ IDEA Azure Explorer のメイン ビュー](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer3.png)

9. クラスターに関連付けられているリソース (ストレージ アカウントなど) を表示するには、クラスター名ノードをさらに展開します。

    ![Azure Explorer のストレージ アカウント](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer4.png)

### <a name="link-a-cluster"></a>クラスターのリンク

Apache Ambari マネージド ユーザー名を使用して、HDInsight クラスターをリンクできます。 同様に、ドメイン参加済み HDInsight クラスターでは、`user1@contoso.com` などのドメインとユーザー名を使用して、リンクできます。 Livy Service クラスターをリンクすることもできます。

1. メニュー バーから、 **[表示]**  >  **[ツール ウィンドウ]**  >  **[Azure Explorer]** に移動します。

1. Azure Explorer から、 **[HDInsight]** ノードを右クリックし、 **[Link A Cluster]\(クラスターのリンク\)** を選択します。

   ![Azure Explorer の [Link A Cluster]\(クラスターのリンク\) コンテキスト メニュー](./media/apache-spark-intellij-tool-plugin/link-a-cluster-context-menu.png)

1. **[Link A Cluster]\(クラスターのリンク\)** ウィンドウで使用可能なオプションは、 **[Link Resource Type]\(リンクのリソースの種類\)** ドロップダウン リストから選択する値によって異なります。  値を入力して **[OK]** を選択します。

    * **HDInsight クラスター**  
  
        |プロパティ |値 |
        |----|----|
        |リンクのリソースの種類|ドロップダウン リストから **[HDInsight クラスター]** を選択します。|
        |クラスター名/URL| クラスター名を入力します。|
        |認証の種類| **[基本認証]** のままにします|
        |[ユーザー名]| クラスターのユーザー名を入力します。既定値は admin です。|
        |Password| ユーザー名のパスワードを入力します。|

        ![IntelliJ IDEA の [Link A Cluster]\(クラスターのリンク\) ダイアログ](./media/apache-spark-intellij-tool-plugin/link-hdinsight-cluster-dialog.png)

    * **Livy サービス**  
  
        |プロパティ |値 |
        |----|----|
        |リンクのリソースの種類|ドロップダウン リストから **[Livy Service]\(Livy サービス\)** を選択します。|
        |Livy エンドポイント| Livy エンドポイントを入力します|
        |クラスター名| クラスター名を入力します。|
        |Yarn エンドポイント|省略可能。|
        |認証の種類| **[基本認証]** のままにします|
        |[ユーザー名]| クラスターのユーザー名を入力します。既定値は admin です。|
        |Password| ユーザー名のパスワードを入力します。|

        ![IntelliJ IDEA の Livy クラスターのリンク ダイアログ](./media/apache-spark-intellij-tool-plugin/link-livy-cluster-dialog.png)

1. **HDInsight** ノードからリンクされたクラスターを確認できます。

   ![Azure Explorer のリンクされたクラスター 1](./media/apache-spark-intellij-tool-plugin/hdinsight-linked-cluster.png)

1. また、**Azure 用エクスプローラー** からクラスターのリンクを解除することもできます。

   ![Azure Explorer からクラスターのリンクを解除する](./media/apache-spark-intellij-tool-plugin/hdi-unlinked-cluster.png)

## <a name="run-a-spark-scala-application-on-an-hdinsight-spark-cluster"></a>HDInsight Spark クラスターでの Spark Scala アプリケーションの実行

Scala アプリケーションを作成した後に、クラスターに送信できます。

1. [Project]\(プロジェクト\) から **myApp** > **src** > **main** > **scala** > **myApp** に移動します。  **[myApp]** を右クリックし、 **[Submit Spark Application]\(Spark アプリケーションの送信\)** を選択します (多くの場合、リストの最下部にあります)。

      ![[Submit Spark Application to HDInsight]\(HDInsight への Spark アプリケーションの送信\) コマンド](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-1.png)

2. **[Submit Spark Application]\(Spark アプリケーションの送信\)** ダイアログ ウィンドウで、 **[1.Spark on HDInsight]\(HDInsight 上の Spark\)** を選択します。

3. **[構成の編集]** ウィンドウで、次の値を指定して **[OK]** を選択します。

    |プロパティ |値 |
    |----|----|
    |Spark クラスター (Linux のみ)|アプリケーションを実行する HDInsight Spark クラスターを選択します。|
    |送信するアーティファクトの選択|既定の設定のままにします。|
    |メイン クラス名|既定値は、選択したファイルのメイン クラスです。 クラスを変更するには、省略記号 ( **...** ) をクリックし、別のクラスを選択します。|
    |ジョブの構成|既定のキーと値のどちらかまたは両方を変更できます。 詳細については、[Apache Livy REST API](https://livy.incubator.apache.org/docs/latest/rest-api.html) に関するページを参照してください。|
    |コマンド ライン引数|必要に応じて、main クラスの引数をスペースで区切って入力できます。|
    |参照される JAR と参照されるファイル|参照されている Jar およびファイルのパスを入力できます (存在する場合)。 現在 ADLS Gen 2 クラスターのみをサポートする Azure 仮想ファイル システム内のファイルを参照することもできます。 詳細情報:[Apache Spark 構成](https://spark.apache.org/docs/latest/configuration.html#runtime-environment)。  [リソースをクラスターにアップロードする方法](../../storage/blobs/storage-quickstart-blobs-storage-explorer.md)に関するページも参照してください。|
    |ジョブ アップロード ストレージ|展開して追加のオプションを表示します。|
    |ストレージ型|ドロップダウン リストから **[Use Azure Blob to upload]\(Azure BLOB を使用してアップロード\)** を選択します。|
    |ストレージ アカウント|ストレージ アカウントを入力します。|
    |ストレージ キー|ストレージ キーを入力します。|
    |ストレージ コンテナー|**[ストレージ アカウント]** と **[ストレージ キー]** の入力後、ドロップダウン リストからストレージ コンテナーを選択します。|

    ![[Spark Submission]\(Spark 送信\) ダイアログ ボックス](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-02.png)

4. **[SparkJobRun]** を選択して、選択したクラスターにプロジェクトを送信します。 **[Remote Spark Job in Cluster]\(クラスターのリモート Spark ジョブ\)** タブの下部には、ジョブの実行の進行状況が表示されます。 赤いボタンをクリックすると、アプリケーションを停止できます。

    ![[Apache Spark Submission]\(Apache Spark 送信\) ウィンドウ](./media/apache-spark-intellij-tool-plugin/hdi-spark-app-result.png)

## <a name="debug-apache-spark-applications-locally-or-remotely-on-an-hdinsight-cluster"></a>HDInsight クラスターで Apache Spark アプリケーションをローカルまたはリモートでデバッグする

クラスターに Spark アプリケーションを送信するお勧めの方法はほかにもあります。 それは、**実行/デバッグ構成** の IDE でパラメーターを設定する方法です。 [Azure Toolkit for IntelliJ を使用した HDInsight クラスター上での SSH による Apache Spark アプリケーションのローカルまたはリモートでのデバッグ](apache-spark-intellij-tool-debug-remotely-through-ssh.md)に関するページを参照してください。

## <a name="access-and-manage-hdinsight-spark-clusters-by-using-azure-toolkit-for-intellij"></a>Azure Toolkit for IntelliJ を使用して HDInsight Spark クラスターにアクセスして管理する

Azure Toolkit for IntelliJ を使用してさまざまな操作を行うことができます。  ほとんどの操作は **Azure Explorer** から開始されます。  メニュー バーから、 **[表示]**  >  **[ツール ウィンドウ]**  >  **[Azure Explorer]** に移動します。

### <a name="access-the-job-view"></a>ジョブ ビューにアクセスする

1. Azure Explorer で **[HDInsight]**  > \<Your Cluster> >  **[ジョブ]** の順に移動します。

    ![IntelliJ Azure Explorer のジョブ ビュー ノード](./media/apache-spark-intellij-tool-plugin/intellij-job-view-node.png)

2. 右側のウィンドウの **[Spark Job View (Spark ジョブ ビュー)]** タブに、クラスター上で実行されていたすべてのアプリケーションが表示されます。 詳細情報を確認したいアプリケーションの名前を選択します。

    ![Spark ジョブ ビューのアプリケーションの詳細](./media/apache-spark-intellij-tool-plugin/intellij-view-job-logs.png)

3. 実行中のジョブの基本情報が表示するには、ジョブ グラフにマウス ポインターを合わせます。 各ジョブについて生成されるステージのグラフと情報を確認するには、ジョブ グラフ上のノードを選択します。

    ![Spark ジョブ ビューのジョブ ステージの詳細](./media/apache-spark-intellij-tool-plugin/Job-graph-stage-info.png)

4. *Driver Stderr*、*Driver Stdout*、*Directory Info* などの頻繁に使用されるログを表示するには、 **[Log]\(ログ\)** タブを選択します。

    ![Spark ジョブ ビューのログの詳細](./media/apache-spark-intellij-tool-plugin/intellij-job-log-info.png)

5. Spark 履歴 UI と YARN UI を (アプリケーション レベルで) 表示できます。 ウィンドウ上部のリンクを選択します。

### <a name="access-the-spark-history-server"></a>Spark 履歴サーバーにアクセスする

1. Azure Explorer から、 **[HDInsight]** を展開します。Spark クラスター名を右クリックし、 **[Open Spark History UI]\(Spark 履歴 UI を開く\)** を選択します。  
2. 入力を求められたら、クラスターの管理者資格情報 (クラスターの設定時に指定したもの) を入力します。

3. Spark 履歴サーバーのダッシュボードでは、実行が終了したばかりのアプリケーションをアプリケーション名を使って探すことができます。 上記のコードでは、`val conf = new SparkConf().setAppName("myApp")` を使用してアプリケーション名を設定しました。 ご利用の Spark アプリケーション名は **myApp** です。

### <a name="start-the-ambari-portal"></a>Ambari ポータルを起動する

1. Azure Explorer から、 **[HDInsight]** を展開します。Spark クラスター名を右クリックし、 **[Open Cluster Management Portal (Ambari)]\(クラスター管理ポータルを開く (Ambari)\)** を選択します。  

2. 入力を求められたら、クラスターの管理者資格情報を入力します。 これらの資格情報は、クラスターの設定プロセス中に指定したものです。

### <a name="manage-azure-subscriptions"></a>Azure サブスクリプションの管理

Azure Toolkit for IntelliJ の既定では、すべての Azure サブスクリプションからの Spark クラスターが一覧表示されます。 必要に応じて、アクセスするサブスクリプションを指定できます。  

1. Azure Explorer から、 **[Azure]** ルート ノードを右クリックし、 **[サブスクリプションの選択]** を選択します。  

2. **[サブスクリプションの選択]** ウィンドウから、アクセスしないサブスクリプションのチェック ボックスをオフにして **[閉じる]** を選択します。

## <a name="spark-console"></a>Spark コンソール

Spark Local Console(Scala) を実行するか、Spark Livy Interactive Session Console(Scala) を実行することができます。

### <a name="spark-local-consolescala"></a>Spark Local Console(Scala)

WINUTILS.EXE の前提条件を満たしていることを確実にします。

1. メニュー バーから、 **[Run]\(実行\)**  >  **[Edit Configurations]\(構成の編集\)** に移動します。

2. **[Run/Debug Configurations]\(実行/デバッグ構成\)** ウィンドウから、左側のウィンドウで、 **[Apache Spark on HDInsight]\(HDInsight 上の Apache Spark\)**  >  **[Spark on HDInsight] myApp** を選択します。

3. メイン ウィンドウで、 **[`Locally Run`]** タブを選択します。

4. 次の値を指定し、 **[OK]** を選択します。

    |プロパティ |値 |
    |----|----|
    |ジョブのメイン クラス|既定値は、選択したファイルのメイン クラスです。 クラスを変更するには、省略記号 ( **...** ) をクリックし、別のクラスを選択します。|
    |環境変数|HADOOP_HOME の値が正しいことを確認します。|
    |WINUTILS.exe の場所|パスが正しいことを確認します。|

    ![Local Console の構成の設定](./media/apache-spark-intellij-tool-plugin/console-set-configuration.png)

5. [Project]\(プロジェクト\) から **myApp** > **src** > **main** > **scala** > **myApp** に移動します。  

6. メニュー バーから、 **[Tools]\(ツール\)**  >  **[Spark Console]\(Spark コンソール\)**  >  **[Run Spark Local Console(Scala)]\(Spark Local Console(Scala) の実行\)** に移動します。

7. 次に、依存関係を自動修正するかどうかを確認する 2 つのダイアログ ボックスが表示されます。 そうする場合は、 **[Auto Fix]\(自動修正\)** を選択します。

    ![IntelliJ IDEA Spark の自動修正ダイアログ 1](./media/apache-spark-intellij-tool-plugin/intellij-console-autofix1.png)

    ![IntelliJ IDEA Spark の自動修正ダイアログ 2](./media/apache-spark-intellij-tool-plugin/intellij-console-autofix2.png)

8. コンソールは次の図のようになります。 コンソール ウィンドウに「`sc.appName`」と入力し、Ctrl + Enter キーを押します。  結果が表示されます。 赤いボタンをクリックすると、ローカル コンソールを終了できます。

    ![IntelliJ IDEA のローカル コンソールの結果](./media/apache-spark-intellij-tool-plugin/local-console-result.png)

### <a name="spark-livy-interactive-session-consolescala"></a>Spark Livy Interactive Session Console(Scala)

1. メニュー バーから、 **[Run]\(実行\)**  >  **[Edit Configurations]\(構成の編集\)** に移動します。

2. **[Run/Debug Configurations]\(実行/デバッグ構成\)** ウィンドウから、左側のウィンドウで、 **[Apache Spark on HDInsight]\(HDInsight 上の Apache Spark\)**  >  **[Spark on HDInsight] myApp** を選択します。

3. メイン ウィンドウで、 **[`Remotely Run in Cluster`]** タブを選択します。

4. 次の値を指定し、 **[OK]** を選択します。

    |プロパティ |値 |
    |----|----|
    |Spark クラスター (Linux のみ)|アプリケーションを実行する HDInsight Spark クラスターを選択します。|
    |メイン クラス名|既定値は、選択したファイルのメイン クラスです。 クラスを変更するには、省略記号 ( **...** ) をクリックし、別のクラスを選択します。|

    ![対話型コンソールセットの構成](./media/apache-spark-intellij-tool-plugin/interactive-console-configuration.png)

5. [Project]\(プロジェクト\) から **myApp** > **src** > **main** > **scala** > **myApp** に移動します。  

6. メニュー バーで、 **[Tools]\(ツール\)**  >  **[Spark Console]\(Spark コンソール\)**  >  **[Run Spark Livy Interactive Session Console(Scala)]\(Spark Livy Interactive Session Console(Scala) の実行\)** に移動します。

7. コンソールは次の図のようになります。 コンソール ウィンドウに「`sc.appName`」と入力し、Ctrl + Enter キーを押します。  結果が表示されます。 赤いボタンをクリックすると、ローカル コンソールを終了できます。

    ![IntelliJ IDEA の対話型コンソールの結果](./media/apache-spark-intellij-tool-plugin/interactive-console-result.png)

### <a name="send-selection-to-spark-console"></a>選択項目の Spark コンソールへの送信

一部のコードをローカルのコンソールまたは Livy Interactive Session Console(Scala) に送信して、スクリプトの結果を事前に確認すると便利です。 Scala ファイル内の一部のコードを強調表示し、 **[Send Selection To Spark Console]\(選択内容を Spark コンソールに送信\)** を右クリックします。 選択したコードがコンソールに送信されます。 結果は、コンソールのコードの後に表示されます。 コンソールでは、エラーが存在するかどうかがチェックされます。  

   ![選択項目の Spark コンソールへの送信](./media/apache-spark-intellij-tool-plugin/send-selection-to-console.png)

## <a name="integrate-with-hdinsight-identity-broker-hib"></a>HDInsight Identity Broker (HIB) との統合

### <a name="connect-to-your-hdinsight-esp-cluster-with-id-broker-hib"></a>HDInsight ESP cluster with ID Broker (HIB) に接続する

HDInsight ESP cluster with ID Broker (HIB) に接続するには、通常の手順に従って Azure サブスクリプションにサインインします。 サインインすると、Azure Explorer にクラスターの一覧が表示されます。 詳細な手順については、「[HDInsight クラスターに接続する](#connect-to-your-hdinsight-cluster)」を参照してください。

### <a name="run-a-spark-scala-application-on-an-hdinsight-esp-cluster-with-id-broker-hib"></a>HDInsight ESP cluster with ID Broker (HIB) 上での Spark Scala アプリケーションを実行する

HDInsight ESP cluster with ID Broker (HIB) にジョブを送信するには、通常の手順に従います。 詳細な手順については、「[HDInsight Spark クラスターでの Spark Scala アプリケーションの実行](#run-a-spark-scala-application-on-an-hdinsight-spark-cluster)」を参照してください。

必要なファイルは、ご利用のサインイン アカウントが名前になっているフォルダーにアップロードしてあります。アップロード パスは、構成ファイル内で確認できます。

   ![構成内のアップロード パス](./media/apache-spark-intellij-tool-plugin/upload-path-in-the-configuration.png)

### <a name="spark-console-on-an-hdinsight-esp-cluster-with-id-broker-hib"></a>HDInsight ESP cluster with ID Broker (HIB) 上の Spark コンソール

HDInsight ESP cluster with ID Broker (HIB) 上で、Spark Local Console(Scala) を実行したり、Spark Livy Interactive Session Console(Scala) を実行したりすることができます。 詳細な手順については、「[Spark コンソール](#spark-console)」を参照してください。

   > [!NOTE]  
   > HDInsight ESP cluster with Id Broker (HIB) では、現在、[クラスターのリンク](#link-a-cluster)と[リモートでの Apache Spark アプリケーションのデバッグ](#debug-apache-spark-applications-locally-or-remotely-on-an-hdinsight-cluster)はサポートされていません。

## <a name="reader-only-role"></a>読み取り専用ロール

ユーザーが読み取り専用ロールのアクセス許可を使用してジョブをクラスターに送信する場合、Ambari の資格情報が必要です。

### <a name="link-cluster-from-context-menu"></a>コンテキスト メニューからクラスターをリンクする

1. 読み取り専用ロールのアカウントでサインインします。

2. **Azure Explorer** で **[HDInsight]** を展開し、自分のサブスクリプションにある HDInsight クラスターを表示します。 **"Role:Reader"** とマークされているクラスターには、読み取り専用ロールのアクセス許可しかありません。

    ![IntelliJ Azure Explorer の Role:Reader](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer15.png)

3. 読み取り専用ロールのアクセス許可があるクラスターを右クリックします。 コンテキスト メニューで **[Link this cluster]\(このクラスターをリンク\)** を選択して、クラスターをリンクします。 Ambari のユーザー名とパスワードを入力します。

    ![IntelliJ Azure Explorer の [Link this cluster]\(このクラスターをリンク\)](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer11.png)

4. クラスターが正常にリンクされると、HDInsight が更新されます。
   クラスターのステージはリンク状態になります。
  
    ![IntelliJ Azure Explorer のリンク状態ダイアログ](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer8.png)

### <a name="link-cluster-by-expanding-jobs-node"></a>ジョブ ノードを展開してクラスターをリンクする

1. **Jobs** ノードをクリックします。 **[Cluster Job Access Denied]\(拒否されたクラスター ジョブ アクセス\)** ウィンドウが表示されます。

2. **[Link this cluster]\(このクラスターをリンク\)** をクリックして、クラスターをリンクします。

    ![[Cluster Job Access Denied]\(拒否されたクラスター ジョブ アクセス\) ダイアログ](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer9.png)

### <a name="link-cluster-from-rundebug-configurations-window"></a>[Run/Debug Configurations]\(実行/デバッグ構成\) ウィンドウからクラスターをリンクする

1. HDInsight 構成を作成します。 次に、 **[Remotely Run in Cluster]\(クラスターでリモート実行\)** を選択します。

2. **[Spark clusters(Linux only)]\(Spark クラスター (Linux のみ)\)** で、読み取り専用ロールのアクセス許可があるクラスターを選択します。 警告メッセージが表示されます。 **[Link this cluster]\(このクラスターをリンク\)** をクリックして、クラスターをリンクできます。

   ![IntelliJ IDEA の実行/デバッグ構成の作成](./media/apache-spark-intellij-tool-plugin/create-configuration.png)

### <a name="view-storage-accounts"></a>ストレージ アカウントを表示する

* 読み取り専用ロールのアクセス許可があるクラスターで、**Storage Accounts** ノードをクリックします。 **[Storage Access Denied]\(拒否されたストレージ アクセス\)** ウィンドウが表示されます。 **[Azure Storage Explorer を開く]** をクリックして Storage Explorer を開くことができます。

   ![IntelliJ IDEA の拒否されたストレージ アクセス](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer14.png)

   ![IntelliJ IDEA の拒否されたストレージ アクセス ボタン](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer10.png)

* リンクされたクラスターで、**Storage Accounts** ノードをクリックします。 **[Storage Access Denied]\(拒否されたストレージ アクセス\)** ウィンドウが表示されます。 **[Azure Storage Explorer を開く]** をクリックして Storage Explorer を開くことができます。

   ![IntelliJ IDEA の拒否されたストレージ アクセス 2](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer13.png)

   ![IntelliJ IDEA の拒否されたストレージ アクセス 2 ボタン](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer12.png)

## <a name="convert-existing-intellij-idea-applications-to-use-azure-toolkit-for-intellij"></a>Azure Toolkit for IntelliJ を使用できるように既存の IntelliJ IDEA アプリケーションを変換する

IntelliJ IDEA で作成した既存の Spark Scala アプリケーションを、Azure Toolkit for IntelliJ に対応するように変換することができます。 その後、プラグインを使用して、そのアプリケーションを HDInsight Spark クラスターに送信できます。

1. IntelliJ IDEA で作成した既存の Spark Scala アプリケーションの、関連付けられている `.iml` ファイルを開きます。

2. ルート レベルに、次のテキストのような **module** 要素があります。

    ```xml
    <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4">
    ```

   **module** 要素が次のテキストのようになるように、この要素を編集して `UniqueKey="HDInsightTool"` を追加します。

    ```xml
    <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4" UniqueKey="HDInsightTool">
    ```

3. 変更を保存します。 これで、アプリケーションは Azure Toolkit for IntelliJ との互換性を持つようになります。 これをテストするには、[プロジェクト] でプロジェクト名を右クリックします。 これで、ポップアップ メニューで、 **[Submit Spark Application to HDInsight]\(HDInsight への Spark アプリケーションの送信\)** を選択できるようになります。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このアプリケーションを引き続き使用しない場合は、次の手順で作成したクラスターを削除します。

1. [Azure portal](https://portal.azure.com/) にサインインします。

1. 上部の **検索** ボックスに「**HDInsight**」と入力します。

1. **[サービス]** の下の **[HDInsight クラスター]** を選択します。

1. 表示される HDInsight クラスターの一覧で、この記事用に作成したクラスターの横にある **[...]** を選択します。

1. **[削除]** を選択します。 **[はい]** を選択します。

![Azure portal で HDInsight クラスターを削除する](./media/apache-spark-intellij-tool-plugin/hdinsight-azure-portal-delete-cluster.png "HDInsight クラスターの削除")

## <a name="next-steps"></a>次のステップ

この記事では、Azure Toolkit for IntelliJ プラグインを使用して、[Scala](https://www.scala-lang.org/) で記述された Apache Spark アプリケーションを開発する方法について学習しました。 その後、IntelliJ 統合開発環境 (IDE) から直接 HDInsight Spark クラスターに送信しました。 次の記事に進んで、Apache Spark に登録したデータを Power BI などの BI 分析ツールに取り込む方法を確認してください。

> [!div class="nextstepaction"]
> [Power BI を使用して Apache Spark データを分析する](apache-spark-use-bi-tools.md)
