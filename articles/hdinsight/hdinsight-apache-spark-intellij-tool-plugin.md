---
title: "Azure Toolkit for IntelliJ: HDInsight クラスター向けの Spark アプリケーションを作成する | Microsoft Docs"
description: "Azure Toolkit for IntelliJ を使用して Scala で記述された Spark アプリケーションを開発し、HDInsight Spark クラスターに送信します。"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 73304272-6c8b-482e-af7c-cd25d95dab4d
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/24/2017
ms.author: nitinme
ms.openlocfilehash: 19cb8f436fa4d86f323013a5d4b3b50bf6c80a1a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-toolkit-for-intellij-to-create-spark-applications-for-an-hdinsight-cluster"></a>Azure Toolkit for IntelliJ を使用して HDInsight クラスター向けの Spark アプリケーションを作成する

Azure Toolkit for IntelliJ プラグインを使用して Scala で記述された Spark アプリケーションを開発し、IntelliJ 統合開発環境 (IDE) から直接、HDInsight Spark クラスターに送信します。 このプラグインには、次のような使い方があります。

* Scala Spark アプリケーションを開発して HDInsight Spark クラスターに送信する。
* Azure HDInsight Spark クラスター リソースにアクセスする。
* Scala Spark アプリケーションをローカルで開発して実行する。

プロジェクトの作成方法については、[Azure Toolkit for IntelliJ を使用した Spark アプリケーションの作成](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)に関するビデオをご覧ください。

> [!IMPORTANT]
> このプラグインを使用して、Linux 上で HDInsight Spark クラスター専用のアプリケーションを作成し、送信することができます。
> 

## <a name="prerequisites"></a>前提条件

- HDInsight Linux での Apache Spark クラスター。 手順については、 [Azure HDInsight での Apache Spark クラスターの作成](hdinsight-apache-spark-jupyter-spark-sql.md)に関するページを参照してください。
- Oracle Java Development Kit。 [Oracle の Web サイト](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)からインストールできます。
- IntelliJ IDEA。 この記事では、バージョン 2017.1 を使用します。 [JetBrains の Web サイト](https://www.jetbrains.com/idea/download/)からインストールできます。

## <a name="install-azure-toolkit-for-intellij"></a>Azure Toolkit for IntelliJ をインストールする
インストール手順については、「[Azure Toolkit for IntelliJ のインストール](../azure-toolkit-for-intellij-installation.md)」を参照してください。

## <a name="sign-in-to-your-azure-subscription"></a>Azure サブスクリプションにサインインします。

1. IntelliJ IDE を起動し、Azure Explorer を開きます。 **[View]\(表示\)** メニューの **[Tools Windows]\(ツール ウィンドウ\)** を選択し、**[Azure Explorer]** を選択します。
       
   ![Azure Explorer のリンク](./media/hdinsight-apache-spark-intellij-tool-plugin/show-azure-explorer.png)

2. **[Azure]** ノードを右クリックし、**[Sign in]\(サインイン\)** を選択します。

3. **[Azure Sign In]\(Azure サインイン\)** ダイアログ ボックスの **[Sign in]\(サインイン\)** を選択し、Azure 資格情報を入力します。

    ![[Azure Sign In]\(Azure サインイン\) ダイアログ ボックス](./media/hdinsight-apache-spark-intellij-tool-plugin/view-explorer-2.png)

4. サインイン後、**[Select Subscriptions]\(サブスクリプションの選択\)** ダイアログ ボックスに、その資格情報に関連付けられているすべての Azure サブスクリプションの一覧が表示されます。 **[Select]\(選択\)** ボタンをクリックします。

    ![[サブスクリプションの選択] ダイアログ ボックス](./media/hdinsight-apache-spark-intellij-tool-plugin/Select-Subscriptions.png)

5. **[Azure Explorer]** タブで **[HDInsight]** を展開し、自分のサブスクリプションにある HDInsight Spark クラスターを表示します。
   
    ![Azure Explorer での HDInsight Spark クラスター](./media/hdinsight-apache-spark-intellij-tool-plugin/view-explorer-3.png)

6. クラスターに関連付けられているリソース (ストレージ アカウントなど) を表示するには、クラスター名ノードをさらに展開します。
   
    ![展開されたクラスター名ノード](./media/hdinsight-apache-spark-intellij-tool-plugin/view-explorer-4.png)

## <a name="run-a-spark-scala-application-on-an-hdinsight-spark-cluster"></a>HDInsight Spark クラスターでの Spark Scala アプリケーションの実行

1. IntelliJ IDEA を起動し、プロジェクトを作成します。 **[新しいプロジェクト]** ダイアログ ボックスで、次の操作を行います。 

   a. **[HDInsight]** > **[Spark on HDInsight (Scala)]\(HDInsight の Spark (Scala)\)** を選択します。

   b. **[Build tool]\(ビルド ツール\)** ボックスの一覧で、ニーズに応じて次のいずれかを選択します。

      * **Maven**: Scala プロジェクト作成ウィザードをサポートする場合
      * **SBT**: 依存関係を管理し、Scala プロジェクトをビルドする場合

    ![[New Project]\(新しいプロジェクト\) ダイアログ ボックス](./media/hdinsight-apache-spark-intellij-tool-plugin/create-hdi-scala-app.png)

2. **[次へ]**を選択します。

3. Scala プロジェクト作成ウィザードは Scala プラグインをインストールしたかどうかを自動的に検出します。 **[インストール]** を選択します。

   ![Scala プラグインの確認](./media/hdinsight-apache-spark-intellij-tool-plugin/Scala-Plugin-check-Reminder.PNG) 

4. Scala プラグインをダウンロードするには **[OK]** をクリックします。 手順に従って IntelliJ を再起動します。 

   ![Scala プラグインのインストールのダイアログ ボックス](./media/hdinsight-apache-spark-intellij-tool-plugin/Choose-Scala-Plugin.PNG)

5. **[New Project]\(新しいプロジェクト\)** ウィンドウで、次の操作を行います。  

    ![Spark SDK の選択](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-new-project.png)

   a. プロジェクト名とプロジェクトの場所を入力します。

   b. **[Project SDK]\(プロジェクトのSDK\)** ボックスの一覧で、Spark 2.x クラスターの場合は **Java 1.8** を選択し、Spark 1.x クラスターの場合は **Java 1.7** を選択します。

   c. **[Spark version]\(Spark のバージョン\)** ボックスの一覧には、Spark SDK と Scala SDK の適切なバージョンが組み合わされて表示されます。 Spark クラスターのバージョンが 2.0 より前の場合は、**Spark 1.x** を選択します。 それ以外の場合は、**Spark 2.x** を選択します。 この例では、**Spark 2.0.2 (Scala 2.11.8)** を使用します。

6. **[完了]** を選択します。

7. Spark プロジェクトでは、アーティファクトが自動的に作成されます。 次の操作を実行して、アーティファクトを表示します。

   a. **[File]\(ファイル\)** メニューの **[Project Structure]\(プロジェクトの構造\)** をクリックします。

   b. **[Project Structure]\(プロジェクトの構造)** ダイアログ ボックスで、**[Artifacts]\(アーティファクト)** を選択すると、作成された既定のアーティファクトが表示されます。 プラス記号 (**+**) をクリックして、独自のアーティファクトを作成することもできます。

      ![アーティファクト情報を示すダイアログ ボックス](./media/hdinsight-apache-spark-intellij-tool-plugin/default-artifact.png)
      
8. 次の操作を実行して、アプリケーション ソース コードを追加します。

   a. Project Explorer で、**[src]** を右クリックし、**[New]\(新規\)** をポイントして、**[Scala Class]\(Scala クラス\)** を選択します。
      
      ![Project Explorer から Scala クラスを作成するためのコマンド](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-scala-code.png)

   b. **[Create New Scala Class]\(新規 Scala クラスの作成\)** ダイアログ ボックスで、名前を指定し、**[Kind]\(種類\)** ボックスの一覧で **[Object]\(オブジェクト\)** を選択して、**[OK]** をクリックします。
      
      ![[Create New Scala Class]\(新規 Scala クラスの作成\) ダイアログ ボックス](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-scala-code-object.png)

   c. **MyClusterApp.scala** ファイルで、次のコードを貼り付けます。 このコードは HVAC.csv (すべての HDInsight Spark クラスターで使用可能) からデータを読み取り、CSV ファイルの 7 番目の列に 1 桁の数字のみが含まれる行を取得し、出力をクラスター用の既定のストレージ コンテナーの下にある **/HVACOut** に書き込みます。

        import org.apache.spark.SparkConf
        import org.apache.spark.SparkContext
    
        object MyClusterApp{
            def main (arg: Array[String]): Unit = {
            val conf = new SparkConf().setAppName("MyClusterApp")
            val sc = new SparkContext(conf)
    
            val rdd = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
    
            //find the rows that have only one digit in the seventh column in the CSV file
            val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)
    
            rdd1.saveAsTextFile("wasb:///HVACOut")
            }
    
        }

9. 次の操作に従って、HDInsight Spark クラスターでアプリケーションを実行します。

   a. Project Explorer で、プロジェクト名を右クリックし、**[Submit Spark Application to HDInsight]\(HDInsight への Spark アプリケーションの送信\)** を選択します。
      
      ![[Submit Spark Application to HDInsight]\(HDInsight への Spark アプリケーションの送信\) コマンド](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-submit-spark-app-1.png)

   b. Azure サブスクリプションの資格情報を入力するよう求められます。 **[Spark Submission]\(Spark 送信\)** ダイアログ ボックスで、次の値を入力し、**[Submit]\(送信\)** を選択します。
      
      * **[Spark clusters (Linux only) (Spark クラスター (Linux のみ))]**では、アプリケーションを実行する HDInsight Spark クラスターを選択します。

      * IntelliJ プロジェクトまたはハード ドライブからアーティファクトを選択します。

      * **[Main class name]\(メイン クラス名\)** ボックスで、省略記号 (**...**) を選択し、アプリケーションのソース コード内のメイン クラスを選択して、**[OK]** を選択します。

        ![[Select Main Class]\(メイン クラスの選択\) ダイアログ ボックス](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-submit-spark-app-3.png)

      * この例のアプリケーション コードでは、コマンドライン引数を必要とせず、JAR またはファイルを参照することもないので、残りのボックスは空のままでかまいません。 すべての情報を指定すると、ダイアログ ボックスは次の図のようになります。
        
        ![[Spark Submission]\(Spark 送信\) ダイアログ ボックス](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-submit-spark-app-2.png)

   c. ウィンドウの下部にある **[Spark Submission (Spark 送信)]** タブで、進行状況の表示が開始されます。 **[Spark Submission]\(Spark 送信\)** ウィンドウにある赤いボタンをクリックして、アプリケーションを停止することもできます。
      
      ![[Spark Submission]\(Spark 送信\) ウィンドウ](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-app-result.png)
      
      ジョブ出力にアクセスする方法については、この記事の後の「Azure Toolkit for IntelliJ を使用して HDInsight Spark クラスターにアクセスして管理する」セクションを参照してください。

## <a name="run-or-debug-a-spark-scala-application-on-an-hdinsight-spark-cluster"></a>HDInsight Spark クラスターでの Spark Scala アプリケーションの実行またはデバッグ
クラスターに Spark アプリケーションを送信するお勧めの方法はほかにもあります。 それは、**実行/デバッグ構成**の IDE でパラメーターを設定する方法です。 詳細については、「[Azure Toolkit for IntelliJ を使用して HDInsight クラスター上で SSH により Spark アプリケーションをリモートでデバッグする](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh)」を参照してください。

## <a name="access-and-manage-hdinsight-spark-clusters-by-using-azure-toolkit-for-intellij"></a>Azure Toolkit for IntelliJ を使用して HDInsight Spark クラスターにアクセスして管理する
Azure Toolkit for IntelliJ を使用してさまざまな操作を実行できます。

### <a name="access-the-job-view"></a>ジョブ ビューにアクセスする
1. Azure Explorer で **[HDInsight]** を展開します。Spark クラスター名を展開した後、**[Jobs]\(ジョブ\)** を選択します。  

    ![ジョブ ビューのノード](./media/hdinsight-apache-spark-intellij-tool-plugin/job-view-node.png)

2. 右側のウィンドウの **[Spark Job View (Spark ジョブ ビュー)]** タブに、クラスター上で実行されていたすべてのアプリケーションが表示されます。 詳細情報を確認したいアプリケーションの名前を選択します。

    ![Application details](./media/hdinsight-apache-spark-intellij-tool-plugin/view-job-logs.png)

3. 実行中のジョブの基本情報が表示するには、ジョブ グラフにマウス ポインターを合わせます。 各ジョブについて生成されるステージのグラフと情報を確認するには、ジョブ グラフ上のノードを選択します。

    ![ジョブのステージの詳細](./media/hdinsight-apache-spark-intellij-tool-plugin/Job-graph-stage-info.png)

4. *Driver Stderr*、*Driver Stdout*、*Directory Info* などの頻繁に使用されるログを表示するには、**[Log]\(ログ\)** タブを選択します。

    ![ログの詳細](./media/hdinsight-apache-spark-intellij-tool-plugin/Job-log-info.png)

5. ウィンドウの上部にあるリンクを選択して、Spark 履歴 UI と YARN UI を (アプリケーション レベルで) 表示することもできます。

### <a name="access-the-spark-history-server"></a>Spark 履歴サーバーにアクセスする
1. Azure Explorer で、**[HDInsight]** を展開します。Spark クラスター名を右クリックし、**[Open Spark History UI]\(Spark 履歴 UI を開く\)** を選択します。 

2. 入力を求められたら、クラスターの管理者資格情報 (クラスターの設定時に指定したもの) を入力します。

3. Spark 履歴サーバーのダッシュボードでは、実行が終了したばかりのアプリケーションをアプリケーション名を使って探すことができます。 上記のコードでは、`val conf = new SparkConf().setAppName("MyClusterApp")` を使用してアプリケーション名を設定しました。 そのため、Spark アプリケーション名は **MyClusterApp** です。

### <a name="start-the-ambari-portal"></a>Ambari ポータルを起動する
1. Azure Explorer で、**[HDInsight]** を展開します。Spark クラスター名を右クリックし、**[Open Cluster Management Portal (Ambari)]\(クラスター管理ポータルを開く (Ambari)\)** を選択します。 

2. 入力を求められたら、クラスターの管理者資格情報を入力します。 これらの資格情報は、クラスターの設定プロセス中に指定したものです。

### <a name="manage-azure-subscriptions"></a>Azure サブスクリプションの管理
Azure Toolkit for IntelliJ の既定では、すべての Azure サブスクリプションからの Spark クラスターが一覧表示されます。 必要に応じて、アクセスするサブスクリプションを指定できます。 

1. Azure Explorer で、**[Azure]** ルート ノードを右クリックし、**[Manage Subscriptions]\(サブスクリプションの管理\)** を選択します。 

2. ダイアログ ボックスで、アクセスしないサブスクリプションのチェック ボックスをオフにし、**[Close]\(閉じる\)** を選択します。 Azure サブスクリプションからサインアウトする場合は、**[Sign Out]\(サインアウト\)** を選択することもできます。

## <a name="run-a-spark-scala-application-locally"></a>Spark Scala アプリケーションのローカルでの実行
Azure Toolkit for IntelliJ を使用すると、ワークステーション上で Spark Scala アプリケーションをローカルに実行することができます。 そのようなアプリケーションは、通常、ストレージ コンテナーなどのクラスター リソースにアクセスする必要がなく、ローカルで実行しテストすることができます。

### <a name="prerequisite"></a>前提条件
Windows コンピューターでローカルの Spark Scala アプリケーションを実行中に、[SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356) で説明されている例外が発生する場合があります。 この例外は、Windows 上に WinUtils.exe がないことが原因で発生します。 

このエラーを回避するには、[実行可能ファイルをダウンロード](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe)して、**C:\WinUtils\bin** などの場所に保存します。 次に、環境変数 **HADOOP_HOME** を追加し、この変数の値を **C\WinUtils** に設定します。

### <a name="run-a-local-spark-scala-application"></a>ローカル Spark Scala アプリケーションの実行
1. IntelliJ IDEA を起動し、プロジェクトを作成します。 

2. **[新しいプロジェクト]** ダイアログ ボックスで、次の操作を行います。
   
    a. **[HDInsight]** > **[Spark on HDInsight Local Run Sample (Scala)]\(HDInsight の Spark のローカル実行サンプル (Scala)\)** を選択します。

    b. **[Build tool]\(ビルド ツール\)** ボックスの一覧で、ニーズに応じて次のいずれかを選択します。

      * **Maven**: Scala プロジェクト作成ウィザードをサポートする場合
      * **SBT**: 依存関係を管理し、Scala プロジェクトをビルドする場合

    ![[New Project]\(新しいプロジェクト\) ダイアログ ボックス](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-app-local-run.png)

3. **[次へ]**を選択します。
 
4. 表示されたウィンドウで、次の操作を行います。
   
    a. プロジェクト名とプロジェクトの場所を入力します。

    b. **[Project SDK]\(プロジェクト SDK\)** ボックスの一覧で、バージョン 1.7 より後の Java バージョンを選択します。

    c. **[Spark Version]\(Spark のバージョン\)** ボックスの一覧で、使用する Scala のバージョン (Spark 2.0 の場合は Scala 2.11.x、Spark 1.6 の場合は Scala 2.10.x) を選択します。

    ![[New Project]\(新しいプロジェクト\) ダイアログ ボックス](./media/hdinsight-apache-spark-intellij-tool-plugin/Create-local-project.PNG)

5. **[完了]** を選択します。

6. テンプレートは、コンピューターでローカルに実行することができるサンプル コード (**LogQuery**) を **src** フォルダーの下に追加します。
   
    ![LogQuery の場所](./media/hdinsight-apache-spark-intellij-tool-plugin/local-app.png)

7. **LogQuery** アプリケーションを右クリックし、**[Run 'LogQuery']\('LogQuery' の実行\)** を選択します。 下部の **[Run]\(実行\)** タブに、次のような出力が表示されます。
   
   ![Spark アプリケーションをローカルに実行した結果](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-app-local-run-result.png)

## <a name="convert-existing-intellij-idea-applications-to-use-azure-toolkit-for-intellij"></a>Azure Toolkit for IntelliJ を使用できるように既存の IntelliJ IDEA アプリケーションを変換する
IntelliJ IDEA で作成した既存の Spark Scala アプリケーションを、Azure Toolkit for IntelliJ に対応するように変換することができます。 その後、プラグインを使用して、そのアプリケーションを HDInsight Spark クラスターに送信できます。

1. IntelliJ IDEA で作成した既存の Spark Scala アプリケーションの、関連付けられている .iml ファイルを開きます。

2. ルート レベルに、次のような **module** 要素があります。
   
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4">

   **module** 要素が次のようになるように、この要素を編集して `UniqueKey="HDInsightTool"` を追加します。
   
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4" UniqueKey="HDInsightTool">

3. 変更を保存します。 これで、アプリケーションは Azure Toolkit for IntelliJ との互換性を持つようになります。 これをテストするには、Project Explorer でプロジェクト名を右クリックします。 これで、ポップアップ メニューで、**[Submit Spark Application to HDInsight]\(HDInsight への Spark アプリケーションの送信\)** を選択できるようになります。

## <a name="troubleshooting"></a>トラブルシューティング

### <a name="error-in-local-run-please-use-a-larger-heap-size"></a>ローカル実行で、"*Please use a larger heap size*" というエラーが発生した場合
Spark 1.6 で 32 ビットの Java SDK を使用している場合、ローカル実行時に次のようなエラーが発生することがあります。

    Exception in thread "main" java.lang.IllegalArgumentException: System memory 259522560 must be at least 4.718592E8. Please use a larger heap size.
        at org.apache.spark.memory.UnifiedMemoryManager$.getMaxMemory(UnifiedMemoryManager.scala:193)
        at org.apache.spark.memory.UnifiedMemoryManager$.apply(UnifiedMemoryManager.scala:175)
        at org.apache.spark.SparkEnv$.create(SparkEnv.scala:354)
        at org.apache.spark.SparkEnv$.createDriverEnv(SparkEnv.scala:193)
        at org.apache.spark.SparkContext.createSparkEnv(SparkContext.scala:288)
        at org.apache.spark.SparkContext.<init>(SparkContext.scala:457)
        at LogQuery$.main(LogQuery.scala:53)
        at LogQuery.main(LogQuery.scala)
        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
        at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:57)
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
        at java.lang.reflect.Method.invoke(Method.java:606)
        at com.intellij.rt.execution.application.AppMain.main(AppMain.java:144)

これらのエラーは、Spark を実行するのに十分なヒープ サイズがないために発生します  Spark には少なくとも 471 MB のヒープが必要となります  (詳細については、[SPARK-12081](https://issues.apache.org/jira/browse/SPARK-12081) に関するページを参照してください)。簡単な解決方法としては、64 ビットの Java SDK を使用することが考えられます。 または、IntelliJ で JVM 設定に以下のオプションを追加します。

    -Xms128m -Xmx512m -XX:MaxPermSize=300m -ea

![IntelliJ の [VM options]\(VM オプション\) ボックスへのオプションの追加](./media/hdinsight-apache-spark-intellij-tool-plugin/change-heap-size.png)

## <a name="faq"></a>FAQ
アプリケーションを Azure Data Lake Store に送信するには、Azure サインイン プロセスで **[Interactive]\(インタラクティブ\)** モードを選択します。 **[Automated]\(自動\)** モードを選択した場合は、エラーが発生することがあります。

![対話型のサインイン](./media/hdinsight-apache-spark-intellij-tool-plugin/interative-signin.png)

これで解決されました。 Azure Data Lake クラスターを選択して、任意のサインイン方法でアプリケーションを送信することができます。

## <a name="feedback-and-known-issues"></a>フィードバックと既知の問題
現在、Spark の出力を直接表示する機能はサポートされていません。

ご提案やフィードバックがある場合、またはこのプラグインを使用していて問題が発生した場合は、hdivstool@microsoft.com に電子メールをお送りください。

## <a name="seealso"></a>次のステップ
* [概要: Azure HDInsight での Apache Spark](hdinsight-apache-spark-overview.md)

### <a name="demo"></a>デモ
* Scala プロジェクトの作成 (ビデオ): [Spark Scala アプリケーションの作成](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* リモート デバッグ (ビデオ): [Azure Toolkit for IntelliJ を使用して HDInsight クラスター上で Spark アプリケーションをリモートでデバッグする](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>シナリオ
* [Spark と BI: HDInsight で BI ツールと Spark を使用して対話型データ分析を実行する](hdinsight-apache-spark-use-bi-tools.md)
* [Spark と Machine Learning: HDInsight で Spark を使用して HVAC データを基に建物の温度を分析する](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Spark with Machine Learning: Use Spark in HDInsight to predict food inspection results (Spark と Machine Learning: HDInsight で Spark を使用して食品の検査結果を予測する)](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Spark ストリーミング: HDInsight で Spark を使用してリアルタイム ストリーミング アプリケーションを作成する](hdinsight-apache-spark-eventhub-streaming.md)
* [Website log analysis using Spark in HDInsight (HDInsight での Spark を使用した Web サイト ログ分析)](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="creating-and-running-applications"></a>アプリケーションの作成と実行
* [Scala を使用してスタンドアロン アプリケーションを作成する](hdinsight-apache-spark-create-standalone-application.md)
* [Livy を使用して Spark クラスターでジョブをリモートで実行する](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>ツールと拡張機能
* [Azure Toolkit for IntelliJ を使用して VPN を介して Spark アプリケーションをリモートでデバッグする](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Azure Toolkit for IntelliJ を使用して SSH を介して Spark アプリケーションをリモートでデバッグする](hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Hortonworks Sandbox と IntelliJ 用 HDInsight ツールを使用する](hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Azure Toolkit for Eclipse の HDInsight ツールを使用して Spark アプリケーションを作成する](hdinsight-apache-spark-eclipse-tool-plugin.md)
* [HDInsight の Spark クラスターで Zeppelin Notebook を使用する](hdinsight-apache-spark-zeppelin-notebook.md)
* [HDInsight 用の Spark クラスターの Jupyter Notebook で使用可能なカーネル](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Jupyter Notebook で外部のパッケージを使用する](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Jupyter をコンピューターにインストールして HDInsight Spark クラスターに接続する](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="managing-resources"></a>リソースの管理
* [Azure HDInsight での Apache Spark クラスターのリソースの管理](hdinsight-apache-spark-resource-manager.md)
* [HDInsight の Apache Spark クラスターで実行されるジョブの追跡とデバッグ](hdinsight-apache-spark-job-debugging.md)

