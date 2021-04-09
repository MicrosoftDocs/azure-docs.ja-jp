---
title: Azure Toolkit for Eclipse:HDInsight Spark 向けの Scala アプリを作成する
description: Azure Toolkit for Eclipse の HDInsight ツールを使用して Scala で記述された Spark アプリケーションを開発し、Eclipse IDE から直接、HDInsight Spark クラスターに送信します。
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/13/2019
ms.openlocfilehash: e4f19afc0edf0f43b64548ae1c93ed5ff8dfa8a1
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104866575"
---
# <a name="use-azure-toolkit-for-eclipse-to-create-apache-spark-applications-for-an-hdinsight-cluster"></a>Azure Toolkit for Eclipse を使用して HDInsight クラスター向けの Apache Spark アプリケーションを作成する

Azure Toolkit for [Eclipse](https://www.eclipse.org/) の HDInsight Tools を使用して [Scala](https://www.scala-lang.org/) で記述された [Apache Spark](https://spark.apache.org/) アプリケーションを開発し、Eclipse IDE から直接、Azure HDInsight Spark クラスターに送信します。 HDInsight Tools プラグインには、次のような複数の使い方があります。

* Scala Spark アプリケーションを開発して HDInsight Spark クラスターに送信する。
* Azure HDInsight Spark クラスター リソースにアクセスする。
* Scala Spark アプリケーションをローカルで開発して実行する。

## <a name="prerequisites"></a>前提条件

* HDInsight 上の Apache Spark クラスター。 手順については、「 [Create Apache Spark clusters in Azure HDInsight (Azure HDInsight での Apache Spark クラスターの作成)](apache-spark-jupyter-spark-sql.md)」を参照してください。

* [Java Developer キット](/azure/developer/java/fundamentals/java-jdk-long-term-support) (JDK) バージョン 8

* [Eclipse IDE](https://www.eclipse.org/downloads/)。 この記事では、Java 開発者向けの Eclipse IDE を使用します。

## <a name="install-required-plug-ins"></a>必要なプラグインをインストールする

### <a name="install-azure-toolkit-for-eclipse"></a>Azure Toolkit for Eclipse をインストールする

インストール手順については、「[Azure Toolkit for Eclipse のインストール](/azure/developer/java/toolkit-for-eclipse/installation)」を参照してください。

### <a name="install-the-scala-plug-in"></a>Scala プラグインをインストールする

Eclipse の起動時に、Scala プラグインがインストールされているかどうかを HDInsight Tools が自動的に検出します。 **[OK]** を選択して続行し、指示に従って Eclipse Marketplace からプラグインをインストールします。 インストールが完了したら、IDE を再起動します。

:::image type="content" source="./media/apache-spark-eclipse-tool-plugin/auto-installation-scala1.png" alt-text="Scala プラグインの自動インストール" border="true":::

### <a name="confirm-plug-ins"></a>プラグインの確認

1. **ヘルプ** の > **Eclipse Marketplace...** に移動します。

1. **[インストール]** タブを選択します。

1. 少なくとも次のように表示する必要があります:
    * Azure Toolkit for Eclipse \<version>。
    * Scala IDE \<version>。

## <a name="sign-in-to-your-azure-subscription"></a>Azure サブスクリプションにサインインします。

1. Eclipse IDE を起動します。

1. **[ウィンドウ]**  >   **[表示ビュー]**  >  **[その他...]**  >  **[サインイン...] に移動します**。

1. **[表示ビュー]** ダイアログで、 **[Azure]**  >  **[Azure Explorer]** に移動し、 **[開く]** を選択します。

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer1.png" alt-text="Apache Spark Eclipse 表示ビュー" border="true":::

1. **Azure Explorer** で、 **[Azure]** ノードを右クリックし、 **[サインイン]** を選択します。

1. **[Azure サインイン]** ダイアログボックスで、[認証方法] を選択し、 **[サインイン]** を選択して、サインインプロセスを完了します。

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer2.png" alt-text="Apache Spark Eclipse の Azure サインイン" border="true":::

1. サインインすると、 **[サブスクリプション]** ダイアログボックスに、資格情報に関連付けられたすべての Azure サブスクリプションが一覧表示されます。 **[選択]** をクリックし、ダイアログボックスを閉じます。

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/Select-Subscriptions.png" alt-text="[Select Subscriptions]\(サブスクリプションの選択\) ダイアログ ボックス" border="true":::

1. **Azure Explorer** から、**Azure** >  **HDInsight** に移動し、サブスクリプションの下にある HDInsight Spark クラスターを表示します。

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer3.png" alt-text="Azure Explorer での HDInsight Spark クラスター 3" border="true":::

1. クラスター名のノードをさらに展開すると、そのクラスターに関連付けられているリソース (ストレージ アカウントなど) を表示できます。

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer4.png" alt-text="クラスター名を展開してリソースを表示する" border="true":::

## <a name="link-a-cluster"></a>クラスターのリンク

Ambari マネージド ユーザー名を使用して、通常のクラスターをリンクすることができます。 同様に、ドメイン参加済み HDInsight クラスターでは、`user1@contoso.com` などのドメインとユーザー名を使用して、リンクできます。

1. **Azure Explorer** で、 **[HDInsight]** を右クリックし、 **[クラスターのリンク]** を選択します。

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/link-a-cluster-context-menu.png" alt-text="Azure Explorer のリンク クラスター メニュー" border="true":::

1. **クラスター名**、**ユーザー名**、および **パスワード** を入力し、 **[OK]** を選択します。 任意で、[ストレージ アカウント] と [ストレージ キー] を入力し、左のツリー ビューでストレージ エクスプローラーに [ストレージ コンテナー] を選択します。

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/link-cluster-dialog1.png" alt-text="[Link New HDInsight cluster]\(新しい HDInsight クラスターのリンク\) ダイアログ" border="true":::

   > [!NOTE]  
   > クラスターが Azure サブスクリプションにログインし、かつクラスターにリンクしていた場合、リンクされたストレージ キー、ユーザー名、パスワードを使用します。
   > :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/storage-explorer-in-Eclipse.png" alt-text="Azure Explorer のストレージ アカウント" border="true":::
   >
   > キーボードのみを使用しているユーザーの場合、現在のフォーカスが **ストレージ キー** にあるときは、**Ctrl+TAB** を使用して、ダイアログの次のフィールドにフォーカスを移動する必要があります。

1. リンクされたクラスターは **HDInsight** の下に表示されます。 これでリンクされたクラスターにアプリケーションを送信できるようになりました。

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/hdinsight-linked-cluster.png" alt-text="Azure Explorer の hdi リンク済みクラスター" border="true":::

1. また、**Azure 用エクスプローラー** からクラスターのリンクを解除することもできます。

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/hdi-unlinked-cluster.png" alt-text="Azure Explorer からクラスターのリンクを解除する" border="true":::

## <a name="set-up-a-spark-scala-project-for-an-hdinsight-spark-cluster"></a>HDInsight Spark クラスター用の Spark Scala プロジェクトを設定する

1. Eclipse IDE ワークスペースで、 **[ファイル]**  >  **[新しい** > **プロジェクト...]** を選択します。

1. **[新しいプロジェクト]** ウィザードで、 **[hdinsight プロジェクト]**  > **Spark on HDInsight (Scala)** を選択します。 **[次へ]** を選択します。

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/create-hdi-scala-app-2.png" alt-text="[Spark on HDInsight (Scala)]\(HDInsight の Spark (Scala)\) プロジェクトの選択" border="true":::

1. **[New HDInsight Scala Project]\(新しい HDInsight Scala プロジェクト\)** ダイアログ ボックスで、次の値を指定し、 **[Next]\(次へ\)** を選択します。
   * プロジェクトの名前を入力します。
   * **[JRE]** 領域で、 **[Use an execution environment JRE]\(実行環境 JRE を使用する\)** が **JavaSE-1.7** 以降に設定されていることを確認します。
   * **[Spark Library]\(Spark ライブラリ\)** 領域では、 **[Use Maven to configure Spark SDK]\(Maven を使用して Spark SDK を構成する\)** オプションを選択できます。  ツールにより Spark SDK と Scala SDK の適切なバージョンが統合されます。 また、**手動で Spark SDK を手動で追加する** オプションを選択して、ダウンロードし、Spark SDK を手動で追加することもできます。

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/create-hdi-scala-app-3.png" alt-text="[New HDInsight Scala Project]\(新しい HDInsight Scala プロジェクト\) ダイアログ ボックス" border="true":::

1. 次のダイアログボックスで、詳細を確認し、 **[完了]** を選択します。

## <a name="create-a-scala-application-for-an-hdinsight-spark-cluster"></a>HDInsight Spark クラスター向けの Scala アプリケーションを作成する

1. **Package Explorer** から、前に作成したプロジェクトを展開します。 **src** を右クリックし、 **[新規]**  >  **[その他...** ] を選択します。

1. **[ウィザードの選択]** ダイアログボックスで、 **[Scala ウィザード]**  >  **[Scala オブジェクト]** を選択します。 **[次へ]** を選択します。

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/create-scala-project1.png" alt-text="[Select a wizard]\(ウィザードの選択\) の [Create a Scala Object]\(Scala オブジェクトの作成\)" border="true":::

1. **[Create New File] \(新しいファイルの作成)** ダイアログ ボックスでオブジェクトの名前を入力し、 **[Finish] \(完了)** を選択します。 テキストエディターが開きます。

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/create-scala-project2.png" alt-text="[New File Wizard]\(新規ファイル ウィザード\) の [Create New File]\(新しいファイルの作成\)" border="true":::

1. テキストエディターで、現在の内容を次のコードに置き換えます:

    ```scala
    import org.apache.spark.SparkConf
    import org.apache.spark.SparkContext

    object MyClusterApp{
        def main (arg: Array[String]): Unit = {
        val conf = new SparkConf().setAppName("MyClusterApp")
        val sc = new SparkContext(conf)

        val rdd = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

        //find the rows that have only one digit in the seventh column in the CSV
        val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)

        rdd1.saveAsTextFile("wasbs:///HVACOut")
        }
    }
    ```

1. HDInsight Spark クラスターでアプリケーションを実行します。

   a. Package Explorer で、プロジェクト名を右クリックし、 **[Submit Spark Application to HDInsight]\(HDInsight への Spark アプリケーションの送信\)** を選択します。

   b. **[Spark Submission]\(Spark 送信\)** ダイアログ ボックスで、次の値を入力し、 **[Submit]\(送信\)** を選択します。

   * **[Cluster Name (クラスター名)]** で、アプリケーションを実行する HDInsight Spark クラスターを選択します。
   * Eclipse プロジェクトまたはハード ドライブからアーティファクトを選択します。 既定値は、Package Explorer から右クリックした項目によって異なります。
   * 送信ウィザードの **[Main class name]\(メイン クラス名\)** ボックスの一覧に、プロジェクトのすべてのオブジェクト名が表示されます。 実行するオブジェクトを選択または入力します。 ハード ドライブからアーティファクトを選択した場合は、メイン クラス名を手動で入力する必要があります。 
   * この例のアプリケーションコードでは、コマンドライン引数を必要とせず、Jar またはファイルを参照することもないため、残りのテキストボックスは空のままにすることができます。

     :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/create-scala-project3.png" alt-text="[Apache Spark Submission]\(Apache Spark 送信\) ダイアログ ボックス" border="true":::

1. **[Spark Submission (Spark 送信)]** タブで、進行状況の表示が開始されます。 **[Spark Submission]\(Spark 送信\)** ウィンドウにある赤いボタンを選択して、アプリケーションを停止することができます。 地球アイコン (図では青のボックスで示されています) を選択して、この特定のアプリケーションの実行に関するログを表示することもできます。

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/create-scala-project4.png" alt-text="[Apache Spark Submission]\(Apache Spark 送信\) ウィンドウ" border="true":::

## <a name="access-and-manage-hdinsight-spark-clusters-by-using-hdinsight-tools-in-azure-toolkit-for-eclipse"></a>Azure Toolkit for Eclipse の HDInsight Tools を使用して HDInsight Spark クラスターにアクセスして管理する

HDInsight Tools を使用すると、ジョブの出力へのアクセスなど、さまざまな操作を実行できます。

### <a name="access-the-job-view"></a>ジョブ ビューにアクセスする

1. **Azure Explorer** で、 **[HDInsight]** 、[Spark クラスター名] の順に展開し、 **[ジョブ]** を選択します。

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-job-view-node.png" alt-text="Azure Explorer Eclipse のジョブ ビュー ノード" border="true":::

1. **[ジョブ]** ノードを選択します。 Java のバージョンが **1.8** より前である場合、HDInsight ツールは **E(fx)clipse** プラグインのインストールを求めるアラームを自動的に表示します。 **[OK]** を選択して続行し、ウィザードに従って Eclipse Marketplace からプラグインをインストールし、Eclipse を再起動します。

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/auto-install-efxclipse.png" alt-text="不足プラグイン インストールの E(fx)clipse" border="true":::

1. **[ジョブ]** ノードからジョブ ビューを開きます。 右側のウィンドウの **[Spark Job View (Spark ジョブ ビュー)]** タブに、クラスター上で実行されていたすべてのアプリケーションが表示されます。 詳細情報を確認したいアプリケーションの名前を選択します。

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-job-logs.png" alt-text="Apache Eclipse ビュー ジョブ ログの詳細" border="true":::

   これで、以下のアクションを実行できます。

   * ジョブ グラフをポイントします。 すると、実行中のジョブに関する基本情報が表示されます。 ジョブ グラフを選択すると、各ジョブについて生成されるステージおよび情報を確認できます。

     :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/Job-graph-stage-info.png" alt-text="Apache Spark ジョブ グラフのステージ情報" border="true":::

   * **[Log]\(ログ\)** タブを選択して、**Driver Stderr**、**Driver Stdout**、**Directory Info** などの頻繁に使用されるログを表示します。

     :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-job-log-info.png" alt-text="Apache Spark Eclipse のジョブ ログ情報" border="true":::

   * ウィンドウの上部にあるハイパーリンクを選択して、Spark 履歴 UI と Apache Hadoop YARN UI を (アプリケーション レベルで) 開きます。

### <a name="access-the-storage-container-for-the-cluster"></a>クラスターのストレージ コンテナーにアクセスする

1. Azure Explorer で、 **[HDInsight]** ルート ノードを展開して、使用できる HDInsight Spark クラスターの一覧を表示します。

1. クラスター名を展開して、ストレージ アカウントと、クラスターの既定のストレージ コンテナーを表示します。

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer5.png" alt-text="ストレージ アカウントと既定のストレージ コンテナー" border="true":::

1. クラスターに関連付けられているストレージ コンテナー名を選択します。 右側のウィンドウで、**HVACOut** フォルダーをダブルクリックします。 **part-** ファイルのいずれかを開いて、アプリケーションの出力を確認します。

### <a name="access-the-spark-history-server"></a>Spark 履歴サーバーにアクセスする

1. Azure Explorer で、Spark クラスター名を右クリックし、 **[Open Spark History UI]\(Spark 履歴 UI を開く\)** を選択します。 入力を求められたら、クラスターの管理者資格情報を入力します。 これらは、クラスターのプロビジョニング時に指定したものです。

1. Spark 履歴サーバーのダッシュボードで、実行が終了したばかりのアプリケーションをアプリケーション名を使って探します。 上記のコードでは、`val conf = new SparkConf().setAppName("MyClusterApp")` を使用してアプリケーション名を設定しました。 したがって、Spark アプリケーション名は **MyClusterApp** です。

### <a name="start-the-apache-ambari-portal"></a>Apache Ambari ポータルを起動する

1. Azure Explorer で、Spark クラスター名を右クリックし、 **[Open Cluster Management Portal (Ambari)]\(クラスター管理ポータルを開く (Ambari)\)** を選択します。

1. 入力を求められたら、クラスターの管理者資格情報を入力します。 これらは、クラスターのプロビジョニング時に指定したものです。

### <a name="manage-azure-subscriptions"></a>Azure サブスクリプションの管理

Azure Toolkit for Eclipse の HDInsight ツールの既定では、すべての Azure サブスクリプションからの Spark クラスターが一覧表示されます。 必要に応じて、クラスターにアクセスするサブスクリプションを指定できます。

1. Azure Explorer で、 **[Azure]** ルート ノードを右クリックし、 **[Manage Subscriptions]\(サブスクリプションの管理\)** を選択します。

1. ダイアログ ボックスで、アクセスしないサブスクリプションのチェック ボックスをオフにし、 **[Close]\(閉じる\)** を選択します。 Azure サブスクリプションからサインアウトする場合は、 **[Sign Out]\(サインアウト\)** を選択することもできます。

## <a name="run-a-spark-scala-application-locally"></a>Spark Scala アプリケーションのローカルでの実行

Azure Toolkit for Eclipse の HDInsight Tools を使用すると、ワークステーション上で Spark Scala アプリケーションをローカルに実行することができます。 通常、そのようなアプリケーションは、ストレージ コンテナーなどのクラスター リソースにアクセスする必要がなく、ローカルで実行しテストすることができます。

### <a name="prerequisite"></a>前提条件

Windows コンピューターでローカルの Spark Scala アプリケーションを実行中に、[SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356) で説明されている例外が発生する場合があります。 この例外は、Windows 上に **WinUtils.exe** がないことが原因で発生します。

このエラーを解決するには、**C:\WinUtils\bin** などの場所に [Winutils.exe](https://github.com/steveloughran/winutils) が必要です。次に、環境変数 **HADOOP_HOME** を追加し、この変数の値を **C:\WinUtils** に設定します。

### <a name="run-a-local-spark-scala-application"></a>ローカル Spark Scala アプリケーションの実行

1. Eclipse を起動し、プロジェクトを作成します。 **[新しいプロジェクト]** ダイアログ ボックスで、次の選択を行い、 **[Next]\(次へ\)** を選択します。

1. **[新しいプロジェクト]** ウィザードで、 **[HDInsight プロジェクト]**  >  **[Spark on HDInsight Local Run Sample (Scala)]** を選択します。 **[次へ]** を選択します。

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run.png" alt-text="[新しいプロジェクト] のウィザード ダイアログを選択します" border="true":::

1. プロジェクトの詳細を指定するために、前のセクション「[HDInsight Spark クラスター用の Spark Scala プロジェクトを設定する](#set-up-a-spark-scala-project-for-an-hdinsight-spark-cluster)」の手順 3. から 6. に従います。

1. テンプレートは、コンピューターでローカルに実行することができるサンプル コード (**LogQuery**) を **src** フォルダーの下に追加します。

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/local-scala-application.png" alt-text="LogQuery ローカル Scala アプリケーションの場所" border="true":::

1. **LogQuery.scala** を右クリックし、 **[実行]**  > **1 つのアプリケーション**　を選択します。 次のような出力が **[コンソール]** タブに表示されます。

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run-result.png" alt-text="Spark アプリケーションをローカルに実行した結果" border="true":::

## <a name="reader-only-role"></a>読み取り専用ロール

ユーザーが読み取り専用ロールのアクセス許可を使用してジョブをクラスターに送信する場合、Ambari の資格情報が必要です。

### <a name="link-cluster-from-context-menu"></a>コンテキスト メニューからクラスターをリンクする

1. 読み取り専用ロールのアカウントでサインインします。

2. **Azure Explorer** で **[HDInsight]** を展開し、自分のサブスクリプションにある HDInsight クラスターを表示します。 **"Role:Reader"** とマークされているクラスターには、読み取り専用ロールのアクセス許可しかありません。

    :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer6.png" alt-text="Azure Explorer での HDInsight Spark クラスター Role Reader" border="true":::

3. 読み取り専用ロールのアクセス許可があるクラスターを右クリックします。 コンテキスト メニューで **[Link this cluster]\(このクラスターをリンク\)** を選択して、クラスターをリンクします。 Ambari のユーザー名とパスワードを入力します。

    :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer7.png" alt-text="Azure Explorer での HDInsight Spark クラスター リンク" border="true":::

4. クラスターが正常にリンクされると、HDInsight が更新されます。
   クラスターのステージはリンク状態になります。
  
    :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer8.png" alt-text="Azure Explorer での HDInsight Spark クラスター リンク済み" border="true":::

### <a name="link-cluster-by-expanding-jobs-node"></a>ジョブ ノードを展開してクラスターをリンクする

1. **Jobs** ノードをクリックします。 **[Cluster Job Access Denied]\(拒否されたクラスター ジョブ アクセス\)** ウィンドウが表示されます。

2. **[Link this cluster]\(このクラスターをリンク\)** をクリックして、クラスターをリンクします。

    :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer9.png" alt-text="Azure Explorer での HDInsight Spark クラスター 9" border="true":::

### <a name="link-cluster-from-spark-submission-window"></a>[Spark Submission]\(Spark 送信\) ウィンドウからクラスターをリンクする

1. HDInsight プロジェクトを作成します。

2. パッケージを右クリックします。 次に、 **[Submit Spark Application to HDInsight]\(HDInsight への Spark アプリケーションの送信\)** を選択します。

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer11.png" alt-text="Azure Explorer での HDInsight Spark クラスター 送信" border="true":::

3. **クラスター名** に対する閲覧者専用のロールアクセス許可を持つクラスターを選択します。 警告メッセージが表示されます。 **[Link this cluster]\(このクラスターをリンク\)** をクリックして、クラスターをリンクできます。

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer15.png" alt-text="Azure Explorer での HDInsight Spark クラスター これをリンク" border="true":::

### <a name="view-storage-accounts"></a>ストレージ アカウントを表示する

* 読み取り専用ロールのアクセス許可があるクラスターで、**Storage Accounts** ノードをクリックします。 **[Storage Access Denied]\(拒否されたストレージ アクセス\)** ウィンドウが表示されます。

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer13.png" alt-text="Azure Explorer での HDInsight Spark クラスター ストレージ" border="true":::

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer12.png" alt-text="Azure Explorer での HDInsight Spark クラスター 拒否" border="true":::

* リンクされたクラスターで、**Storage Accounts** ノードをクリックします。 **[Storage Access Denied]\(拒否されたストレージ アクセス\)** ウィンドウが表示されます。

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer14.png" alt-text="Azure Explorer での HDInsight Spark クラスター 拒否 2" border="true":::

## <a name="known-problems"></a>既知の問題

**クラスターをリンクする** を使用する場合は、ストレージの資格情報を入力することをお勧めします。

:::image type="content" source="./media/apache-spark-eclipse-tool-plugin/link-cluster-with-storage-credential-eclipse.png" alt-text="クラスターをストレージ資格情報とリンクする (Eclipse)" border="true":::

ジョブの送信には、2 つのモードがあります。 ストレージ資格情報が入力されると、バッチ モードが使用され、ジョブが送信されます。 入力されない場合、対話モードが使用されます。 クラスターがビジー状態の場合、以下のエラーが表示されることがあります。

:::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-interactive-cluster-busy-upload.png " alt-text="クラスターがビジー状態のときの Eclipse エラー" border="true":::

:::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-interactive-cluster-busy-submit.png " alt-text="クラスターがビジー状態のときの Eclipse エラー YARN" border="true":::

## <a name="see-also"></a>関連項目

* [概要:Azure HDInsight での Apache Spark](apache-spark-overview.md)

### <a name="scenarios"></a>シナリオ

* [Apache Spark と BI:HDInsight と BI ツールで Spark を使用した対話型データ分析の実行](apache-spark-use-bi-tools.md)
* [Apache Spark と Machine Learning: HDInsight で Spark を使用して、HVAC データを使用して建物の温度を分析する](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark と Machine Learning:HDInsight で Spark を使用して食品の検査結果を予測する](apache-spark-machine-learning-mllib-ipython.md)
* [HDInsight 上での Apache Spark を使用した Web サイト ログ分析](apache-spark-custom-library-website-log-analysis.md)

### <a name="creating-and-running-applications"></a>アプリケーションの作成と実行

* [Scala を使用してスタンドアロン アプリケーションを作成する](apache-spark-create-standalone-application.md)
* [Apache Livy を使用して Apache Spark クラスターでジョブをリモートから実行する](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>ツールと拡張機能

* [Azure Toolkit for IntelliJ を使用して Spark Scala アプリケーションを作成して送信する](apache-spark-intellij-tool-plugin.md)
* [Azure Toolkit for IntelliJ を使用して VPN 経由で Apache Spark アプリケーションをリモートでデバッグする](./apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Azure Toolkit for IntelliJ を使用して SSH 経由で Apache Spark アプリケーションをリモートでデバッグする](./apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [HDInsight 上の Apache Spark クラスターで Apache Zeppelin Notebook を使用する](apache-spark-zeppelin-notebook.md)
* [HDInsight 用の Apache Spark クラスター内の Jupyter Notebook で使用可能なカーネル](apache-spark-jupyter-notebook-kernels.md)
* [Jupyter Notebook で外部のパッケージを使用する](apache-spark-jupyter-notebook-use-external-packages.md)
* [Jupyter をコンピューターにインストールして HDInsight Spark クラスターに接続する](apache-spark-jupyter-notebook-install-locally.md)

### <a name="managing-resources"></a>リソースの管理

* [Azure HDInsight での Apache Spark クラスターのリソースの管理](apache-spark-resource-manager.md)
* [HDInsight の Apache Spark クラスターで実行されるジョブの追跡とデバッグ](apache-spark-job-debugging.md)