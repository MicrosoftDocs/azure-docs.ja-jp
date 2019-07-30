---
title: 'Azure Toolkit for Eclipse:HDInsight Spark 向けの Scala アプリケーションの作成 '
description: Azure Toolkit for Eclipse の HDInsight ツールを使用して Scala で記述された Spark アプリケーションを開発し、Eclipse IDE から直接、HDInsight Spark クラスターに送信します。
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/30/2017
ms.author: hrasheed
ms.openlocfilehash: 6e8f8e7d8324f23a81cd6ae3284bd6d7cff24117
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/25/2019
ms.locfileid: "68489852"
---
# <a name="use-azure-toolkit-for-eclipse-to-create-apache-spark-applications-for-an-hdinsight-cluster"></a>Azure Toolkit for Eclipse を使用して HDInsight クラスター向けの Apache Spark アプリケーションを作成する

Azure Toolkit for [Eclipse](https://www.eclipse.org/) の HDInsight Tools を使用して [Scala](https://www.scala-lang.org/) で記述された [Apache Spark](https://spark.apache.org/) アプリケーションを開発し、Eclipse IDE から直接、Azure HDInsight Spark クラスターに送信します。 HDInsight Tools プラグインには、次のような複数の使い方があります。

* Scala Spark アプリケーションを開発して HDInsight Spark クラスターに送信する。
* Azure HDInsight Spark クラスター リソースにアクセスする。
* Scala Spark アプリケーションをローカルで開発して実行する。

> [!IMPORTANT]  
> このツールを使用して、Linux 上で HDInsight Spark クラスター専用のアプリケーションを作成し、送信することができます。
> 
> 

## <a name="prerequisites"></a>前提条件

* HDInsight 上の Apache Spark クラスター。 手順については、「 [Create Apache Spark clusters in Azure HDInsight (Azure HDInsight での Apache Spark クラスターの作成)](apache-spark-jupyter-spark-sql.md)」を参照してください。
* Eclipse IDE ランタイムで使用される Oracle Java Development Kit Version 8。 [Oracle の Web サイト](https://aka.ms/azure-jdks)からダウンロードできます。
* Eclipse IDE。 この記事では、Eclipse Neon を使用します。 [Eclipse の Web サイト](https://www.eclipse.org/downloads/)からインストールできます。



## <a name="install-hdinsight-tools-in-azure-toolkit-for-eclipse-and-the-scala-plug-in"></a>Azure Toolkit for Eclipse の HDInsight ツールおよび Scala プラグインをインストールする

### <a name="install-azure-toolkit-for-eclipse"></a>Azure Toolkit for Eclipse をインストールする
Eclipse 用の HDInsight Tools は、Azure Toolkit for Eclipse に付属しています。 インストール手順については、「[Azure Toolkit for Eclipse のインストール](https://docs.microsoft.com/java/azure/eclipse/azure-toolkit-for-eclipse-installation)」を参照してください。

### <a name="install-the-scala-plug-in"></a>Scala プラグインをインストールする
Eclipse の起動時に、Scala プラグインがインストールされているかどうかを HDInsight ツールが自動的に検出します。 **[OK]** を選択して続行し、指示に従って Eclipse Marketplace からプラグインをインストールします。

![Scala プラグインの自動インストール](./media/apache-spark-eclipse-tool-plugin/auto-install-scala.png)

[Azure サブスクリプションにサインイン](#sign-in-to-your-azure-subscription)するか、[HDInsight クラスターをリンク](#link-a-cluster)して開始できます。Ambari のユーザー名/パスワードまたはドメイン参加の資格情報を利用してください。 

## <a name="sign-in-to-your-azure-subscription"></a>Azure サブスクリプションにサインインします。
1. Eclipse IDE を起動し、Azure Explorer を開きます。 **[Window]\(ウィンドウ\)** メニューの **[Show View]\(ビューの表示\)** を選択し、 **[Other]\(その他\)** を選択します。 表示されたダイアログ ボックスで **[Azure]** を展開し、 **[Azure Explorer]** を選択して、 **[OK]** を選択します。

   ![[Show View]\(ビューの表示\) ダイアログ ボックス](./media/apache-spark-eclipse-tool-plugin/view-explorer-1.png)
1. **[Azure]** ノードを右クリックし、 **[Sign in]\(サインイン\)** を選択します。
1. **[Azure Sign In]\(Azure サインイン\)** ダイアログ ボックスで、認証モードを選択します。 **[Sign in]\(サインイン\)** を選択し、Azure 資格情報を入力します。
   
   ![Azure サインイン ダイアログ ボックス](./media/apache-spark-eclipse-tool-plugin/view-explorer-2.png)
1. サインイン後、 **[Select Subscriptions]\(サブスクリプションの選択\)** ダイアログ ボックスに、その資格情報に関連付けられているすべての Azure サブスクリプションの一覧が表示されます。 **[Select]\(選択\)** をクリックしてダイアログ ボックスを閉じます。

   ![[Select Subscriptions]\(サブスクリプションの選択\) ダイアログ ボックス](./media/apache-spark-eclipse-tool-plugin/Select-Subscriptions.png)
1. **[Azure Explorer]** タブで **[HDInsight]** を展開し、自分のサブスクリプションの下にある HDInsight Spark クラスターを表示します。
   
   ![Azure Explorer での HDInsight Spark クラスター](./media/apache-spark-eclipse-tool-plugin/view-explorer-3.png)
1. クラスター名のノードをさらに展開すると、そのクラスターに関連付けられているリソース (ストレージ アカウントなど) を表示できます。
   
   ![クラスター名を展開してリソースを表示する](./media/apache-spark-eclipse-tool-plugin/view-explorer-4.png)

## <a name="link-a-cluster"></a>クラスターのリンク
Ambari マネージド ユーザー名を使用して、通常のクラスターをリンクすることができます。 同様に、ドメイン参加済み HDInsight クラスターでは、`user1@contoso.com` などのドメインとユーザー名を使用して、リンクできます。

1. **Azure Explorer** の **[Link a cluster]\(クラスターのリンク\)** を選択します。

   ![リンク クラスターのコンテキスト メニュー](./media/apache-spark-intellij-tool-plugin/link-a-cluster-context-menu.png)

1. **[クラスター名]** 、 **[ユーザー名]** 、 **[パスワード]** を入力し、[OK] ボタンをクリックしてクラスターをリンクします。 任意で、[ストレージ アカウント] と [ストレージ キー] を入力し、左のツリー ビューでストレージ エクスプローラーに [ストレージ コンテナー] を選択します。
   
   ![リンク クラスターのダイアログ](./media/apache-spark-eclipse-tool-plugin/link-cluster-dialog.png)
   
   > [!NOTE]  
   > クラスターが Azure サブスクリプションにログインし、かつクラスターにリンクしていた場合、リンクされたストレージ キー、ユーザー名、パスワードを使用します。
   > ![Eclipse のストレージ エクスプローラー](./media/apache-spark-eclipse-tool-plugin/storage-explorer-in-Eclipse.png)

1. 入力した情報が正しい場合、[OK] をクリックすると **[HDInsight]** ノードに [リンク済み] クラスターが表示されます。 これでリンクされたクラスターにアプリケーションを送信できるようになりました。

   ![リンクされたクラスター](./media/apache-spark-intellij-tool-plugin/linked-cluster.png)

1. また、**Azure 用エクスプローラー**からクラスターのリンクを解除することもできます。
   
   ![リンク解除されたクラスター](./media/apache-spark-intellij-tool-plugin/unlink.png)


## <a name="set-up-a-spark-scala-project-for-an-hdinsight-spark-cluster"></a>HDInsight Spark クラスター用の Spark Scala プロジェクトを設定する

1. Eclipse IDE のワークスペースで、 **[File]\(ファイル\)** 、 **[New]\(新規\)** 、 **[Project]\(プロジェクト\)** の順に選択します。 
1. New Project \(新しいプロジェクト\) ウィザードで、 **[HDInsight]** を展開して、 **[Spark on HDInsight (Scala)]\(HDInsight の Spark (Scala)\)** を選択し、 **[Next]\(次へ\)** を選択します。

   ![[Spark on HDInsight (Scala)]\(HDInsight の Spark (Scala)\) プロジェクトの選択](./media/apache-spark-eclipse-tool-plugin/create-hdi-scala-app-2.png)
1. Scala プロジェクト作成ウィザードは Scala プラグインをインストールしたかどうかを自動的に検出します。 **[OK]** を選択して Scala プラグインのダウンロードを続行し、指示に従って Eclipse を再起動します。

   ![Scala チェック](./media/apache-spark-eclipse-tool-plugin/auto-install-scala-2.png)
1. **[New HDInsight Scala Project]\(新しい HDInsight Scala プロジェクト\)** ダイアログ ボックスで、次の値を指定し、 **[Next]\(次へ\)** を選択します。
   * プロジェクトの名前を入力します。
   * **[JRE]** 領域で、 **[Use an execution environment JRE]\(実行環境 JRE を使用する\)** が **JavaSE-1.7** 以降に設定されていることを確認します。
   * **[Spark Library]\(Spark ライブラリ\)** 領域では、 **[Use Maven to configure Spark SDK]\(Maven を使用して Spark SDK を構成する\)** オプションを選択できます。  ツールにより Spark SDK と Scala SDK の適切なバージョンが統合されます。 **[Add Spark SDK manually]\(Spark SDK を手動で追加する\)** オプションを選択して、Spark SDK を手動でダウンロードして追加することもできます。

   ![[New HDInsight Scala Project]\(新しい HDInsight Scala プロジェクト\) ダイアログ ボックス](./media/apache-spark-eclipse-tool-plugin/create-hdi-scala-app-3.png)
1. 次のダイアログ ボックスで、 **[完了]** を選択します。 
   
  
## <a name="create-a-scala-application-for-an-hdinsight-spark-cluster"></a>HDInsight Spark クラスター向けの Scala アプリケーションを作成する

1. Eclipse IDE の Package Explorer で、前に作成したプロジェクトを展開し、 **[src]** を右クリックして **[New]\(新規\)** をポイントして、 **[Other]\(その他\)** を選択します。
1. **[Select a wizard] \(ウィザードの選択)** ダイアログ ボックスで **[Scala Wizards] \(Scala ウィザード)** を展開し、 **[Scala Object] \(Scala オブジェクト)** 、 **[Next] \(次へ)** の順に選択します。
   
   ![[Select a wizard]\(ウィザードの選択\) ダイアログ ボックス](./media/apache-spark-eclipse-tool-plugin/create-scala-proj-1.png)
1. **[Create New File] \(新しいファイルの作成)** ダイアログ ボックスでオブジェクトの名前を入力し、 **[Finish] \(完了)** を選択します。
   
   ![[Create New File]\(新しいファイルの作成\) ダイアログ ボックス](./media/apache-spark-eclipse-tool-plugin/create-scala-proj-2.png)
1. テキスト エディターで次のコードを貼り付けます。
   
        import org.apache.spark.SparkConf
        import org.apache.spark.SparkContext
   
        object MyClusterApp{
          def main (arg: Array[String]): Unit = {
            val conf = new SparkConf().setAppName("MyClusterApp")
            val sc = new SparkContext(conf)
   
            val rdd = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
   
            //find the rows that have only one digit in the seventh column in the CSV
            val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)
   
            rdd1.saveAsTextFile("wasb:///HVACOut")
          }        
        }
1. HDInsight Spark クラスターでアプリケーションを実行します。
   
   a. Package Explorer で、プロジェクト名を右クリックし、 **[Submit Spark Application to HDInsight]\(HDInsight への Spark アプリケーションの送信\)** を選択します。        
   b. **[Spark Submission]\(Spark 送信\)** ダイアログ ボックスで、次の値を入力し、 **[Submit]\(送信\)** を選択します。
      
   * **[Cluster Name (クラスター名)]** で、アプリケーションを実行する HDInsight Spark クラスターを選択します。
   * Eclipse プロジェクトまたはハード ドライブからアーティファクトを選択します。 既定値は、Package Explorer から右クリックした項目によって異なります。
   * 送信ウィザードの **[Main class name]\(メイン クラス名\)** ボックスの一覧に、プロジェクトのすべてのオブジェクト名が表示されます。 実行するオブジェクトを選択または入力します。 ハード ドライブからアーティファクトを選択した場合は、メイン クラス名を手動で入力する必要があります。 
   * この例のアプリケーション コードでは、コマンドライン引数を必要とせず、JAR またはファイルを参照することもないので、残りのテキスト ボックスは空のままでかまいません。
        
     ![[Spark Submission]\(Spark 送信\) ダイアログ ボックス](./media/apache-spark-eclipse-tool-plugin/create-scala-proj-3.png)
1. **[Spark Submission (Spark 送信)]** タブで、進行状況の表示が開始されます。 **[Spark Submission]\(Spark 送信\)** ウィンドウにある赤いボタンを選択して、アプリケーションを停止することができます。 地球アイコン (図では青のボックスで示されています) を選択して、この特定のアプリケーションの実行に関するログを表示することもできます。
      
   ![[Spark Submission]\(Spark 送信\) ウィンドウ](./media/apache-spark-eclipse-tool-plugin/create-scala-proj-4.png)


## <a name="access-and-manage-hdinsight-spark-clusters-by-using-hdinsight-tools-in-azure-toolkit-for-eclipse"></a>Azure Toolkit for Eclipse の HDInsight Tools を使用して HDInsight Spark クラスターにアクセスして管理する
HDInsight Tools を使用すると、ジョブの出力へのアクセスなど、さまざまな操作を実行できます。

### <a name="access-the-job-view"></a>ジョブ ビューにアクセスする
1. Azure Explorer で **[HDInsight]** を展開します。Spark クラスター名を展開した後、 **[Jobs]\(ジョブ\)** を選択します。 

   ![ジョブ ビューのノード](./media/apache-spark-eclipse-tool-plugin/job-view-node.png)

1. **[ジョブ]** ノードを選択します。 Java のバージョンが **1.8** より前である場合、HDInsight ツールは **E(fx)clipse** プラグインのインストールを求めるアラームを自動的に表示します。 **[OK]** を選択して続行し、ウィザードに従って Eclipse Marketplace からプラグインをインストールし、Eclipse を再起動します。 

   ![E(fx)clipse をインストールする](./media/apache-spark-eclipse-tool-plugin/auto-install-efxclipse.png)

1. **[ジョブ]** ノードからジョブ ビューを開きます。 右側のウィンドウの **[Spark Job View (Spark ジョブ ビュー)]** タブに、クラスター上で実行されていたすべてのアプリケーションが表示されます。 詳細情報を確認したいアプリケーションの名前を選択します。

   ![Application details](./media/apache-spark-eclipse-tool-plugin/view-job-logs.png)

   これで、以下のアクションを実行できます。

   * ジョブ グラフをポイントします。 すると、実行中のジョブに関する基本情報が表示されます。 ジョブ グラフを選択すると、各ジョブについて生成されるステージおよび情報を確認できます。

     ![ジョブのステージの詳細](./media/apache-spark-eclipse-tool-plugin/Job-graph-stage-info.png)

   * **[Log]\(ログ\)** タブを選択して、**Driver Stderr**、**Driver Stdout**、**Directory Info** などの頻繁に使用されるログを表示します。

     ![ログの詳細](./media/apache-spark-eclipse-tool-plugin/Job-log-info.png)

   * ウィンドウの上部にあるハイパーリンクを選択して、Spark 履歴 UI と Apache Hadoop YARN UI を (アプリケーション レベルで) 開きます。

### <a name="access-the-storage-container-for-the-cluster"></a>クラスターのストレージ コンテナーにアクセスする
1. Azure Explorer で、 **[HDInsight]** ルート ノードを展開して、使用できる HDInsight Spark クラスターの一覧を表示します。
1. クラスター名を展開して、ストレージ アカウントと、クラスターの既定のストレージ コンテナーを表示します。
   
   ![ストレージ アカウントと既定のストレージ コンテナー](./media/apache-spark-eclipse-tool-plugin/view-explorer-5.png)
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

このエラーを解決するには、[実行可能ファイルをダウンロード](https://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe)して、**C:\WinUtils\bin** などの場所に保存する必要があります。次に、環境変数 **HADOOP_HOME** を追加し、この変数の値を **C:\WinUtils** に設定します。

### <a name="run-a-local-spark-scala-application"></a>ローカル Spark Scala アプリケーションの実行
1. Eclipse を起動し、プロジェクトを作成します。 **[新しいプロジェクト]** ダイアログ ボックスで、次の選択を行い、 **[Next]\(次へ\)** を選択します。
   
   * 左側のウィンドウで、 **[HDInsight]** を選択します。
   * 右側のウィンドウで、 **[Spark on HDInsight Local Run Sample (Scala)]\(HDInsight の Spark のローカル実行サンプル (Scala)\)** を選択します。

   ![New Project dialog box](./media/apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run.png)
   
1. プロジェクトの詳細を指定するために、前のセクション「[HDInsight Spark クラスター用の Spark Scala プロジェクトを設定する](#set-up-a-spark-scala-project-for-an-hdinsight-spark-cluster)」の手順 3. から 6. に従います。

1. テンプレートは、コンピューターでローカルに実行することができるサンプル コード (**LogQuery**) を **src** フォルダーの下に追加します。
   
   ![LogQuery の場所](./media/apache-spark-eclipse-tool-plugin/local-app.png)
   
1. **LogQuery** アプリケーションを右クリックし、 **[Run As]\(実行者\)** をポイントして、 **[1 Scala Application]\(1 Scala アプリケーション\)** を選択します。 次のような出力が **[コンソール]** タブに表示されます。
   
   ![Spark アプリケーションをローカルに実行した結果](./media/apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run-result.png)

## <a name="reader-only-role"></a>読み取り専用ロール
ユーザーが読み取り専用ロールのアクセス許可を使用してジョブをクラスターに送信する場合、Ambari の資格情報が必要です。

### <a name="link-cluster-from-context-menu"></a>コンテキスト メニューからクラスターをリンクする

1. 読み取り専用ロールのアカウントでサインインします。
       
2. **Azure Explorer** で **[HDInsight]** を展開し、自分のサブスクリプションにある HDInsight クラスターを表示します。 **"Role:Reader"** とマークされているクラスターには、読み取り専用ロールのアクセス許可しかありません。

    ![Azure Explorer での HDInsight Spark クラスター](./media/apache-spark-eclipse-tool-plugin/view-explorer-6.png)

3. 読み取り専用ロールのアクセス許可があるクラスターを右クリックします。 コンテキスト メニューで **[Link this cluster]\(このクラスターをリンク\)** を選択して、クラスターをリンクします。 Ambari のユーザー名とパスワードを入力します。

    ![Azure Explorer での HDInsight Spark クラスター](./media/apache-spark-eclipse-tool-plugin/view-explorer-7.png)

4. クラスターが正常にリンクされると、HDInsight が更新されます。
   クラスターのステージはリンク状態になります。
  
    ![Azure Explorer での HDInsight Spark クラスター](./media/apache-spark-eclipse-tool-plugin/view-explorer-8.png)



### <a name="link-cluster-by-expanding-jobs-node"></a>ジョブ ノードを展開してクラスターをリンクする

1. **Jobs** ノードをクリックします。 **[Cluster Job Access Denied]\(拒否されたクラスター ジョブ アクセス\)** ウィンドウが表示されます。
   
2. **[Link this cluster]\(このクラスターをリンク\)** をクリックして、クラスターをリンクします。
   
    ![Azure Explorer での HDInsight Spark クラスター](./media/apache-spark-eclipse-tool-plugin/view-explorer-9.png)

### <a name="link-cluster-from-spark-submission-window"></a>[Spark Submission]\(Spark 送信\) ウィンドウからクラスターをリンクする

1. HDInsight プロジェクトを作成します。

2. パッケージを右クリックします。 次に、 **[Submit Spark Application to HDInsight]\(HDInsight への Spark アプリケーションの送信\)** を選択します。
   
   ![Azure Explorer での HDInsight Spark クラスター](./media/apache-spark-eclipse-tool-plugin/view-explorer-11.png)

3. **[Cluster Name]\(クラスター名\)** に対して読み取り専用ロール アクセス許可があるクラスターを選択します。 警告メッセージが表示されます。 **[Link this cluster]\(このクラスターをリンク\)** をクリックして、クラスターをリンクできます。
   
   ![Azure Explorer での HDInsight Spark クラスター](./media/apache-spark-eclipse-tool-plugin/view-explorer-15.png)
   
### <a name="view-storage-accounts"></a>ストレージ アカウントを表示する

* 読み取り専用ロールのアクセス許可があるクラスターで、**Storage Accounts** ノードをクリックします。 **[Storage Access Denied]\(拒否されたストレージ アクセス\)** ウィンドウが表示されます。 
     
   ![Azure Explorer での HDInsight Spark クラスター](./media/apache-spark-eclipse-tool-plugin/view-explorer-13.png)

   ![Azure Explorer での HDInsight Spark クラスター](./media/apache-spark-eclipse-tool-plugin/view-explorer-12.png)

* リンクされたクラスターで、**Storage Accounts** ノードをクリックします。 **[Storage Access Denied]\(拒否されたストレージ アクセス\)** ウィンドウが表示されます。 
     
   ![Azure Explorer での HDInsight Spark クラスター](./media/apache-spark-eclipse-tool-plugin/view-explorer-14.png)

## <a name="known-problems"></a>既知の問題
クラスターをリンクするときに、ストレージの資格情報を入力することをお勧めします。

![対話型のサインイン](./media/apache-spark-eclipse-tool-plugin/link-cluster-with-storage-credential-eclipse.png)

ジョブの送信には、2 つのモードがあります。 ストレージ資格情報が入力されると、バッチ モードが使用され、ジョブが送信されます。 入力されない場合、対話モードが使用されます。 クラスターがビジー状態の場合、下のエラーが表示されることがあります。

![クラスターがビジー状態のときの Eclipse エラー](./media/apache-spark-eclipse-tool-plugin/eclipse-interactive-cluster-busy-upload.png)

![クラスターがビジー状態のときの Eclipse エラー](./media/apache-spark-eclipse-tool-plugin/eclipse-interactive-cluster-busy-submit.png)

## <a name="seealso"></a>関連項目
* [概要: Azure HDInsight での Apache Spark](apache-spark-overview.md)

### <a name="scenarios"></a>シナリオ
* [Apache Spark と BI:HDInsight と BI ツールで Spark を使用した対話型データ分析の実行](apache-spark-use-bi-tools.md)
* [Apache Spark と Machine Learning:HDInsight で Spark を使用して、HVAC データを使用して建物の温度を分析する](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark と Machine Learning:HDInsight で Spark を使用して食品の検査結果を予測する](apache-spark-machine-learning-mllib-ipython.md)
* [HDInsight 上での Apache Spark を使用した Web サイト ログ分析](apache-spark-custom-library-website-log-analysis.md)

### <a name="creating-and-running-applications"></a>アプリケーションの作成と実行
* [Scala を使用してスタンドアロン アプリケーションを作成する](apache-spark-create-standalone-application.md)
* [Apache Livy を使用して Apache Spark クラスターでジョブをリモートから実行する](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>ツールと拡張機能
* [Azure Toolkit for IntelliJ を使用して Spark Scala アプリケーションを作成して送信する](apache-spark-intellij-tool-plugin.md)
* [Azure Toolkit for IntelliJ を使用して VPN 経由で Apache Spark アプリケーションをリモートでデバッグする](../hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Azure Toolkit for IntelliJ を使用して SSH 経由で Apache Spark アプリケーションをリモートでデバッグする](../hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [HDInsight 上の Apache Spark クラスターで Apache Zeppelin Notebook を使用する](apache-spark-zeppelin-notebook.md)
* [HDInsight 用の Apache Spark クラスター内の Jupyter Notebook で使用可能なカーネル](apache-spark-jupyter-notebook-kernels.md)
* [Jupyter Notebook で外部のパッケージを使用する](apache-spark-jupyter-notebook-use-external-packages.md)
* [Jupyter をコンピューターにインストールして HDInsight Spark クラスターに接続する](apache-spark-jupyter-notebook-install-locally.md)

### <a name="managing-resources"></a>リソースの管理
* [Azure HDInsight での Apache Spark クラスターのリソースの管理](apache-spark-resource-manager.md)
* [HDInsight の Apache Spark クラスターで実行されるジョブの追跡とデバッグ](apache-spark-job-debugging.md)

