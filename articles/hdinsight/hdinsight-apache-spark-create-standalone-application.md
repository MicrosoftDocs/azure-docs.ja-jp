---
title: "スタンドアロン Scala アプリケーションを作成して Azure Spark クラスターで実行する | Microsoft Docs"
description: "スタンドアロン Scala アプリケーションを作成して HDInsight Spark クラスターで実行する方法を説明します。"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: b2467a40-a340-4b80-bb00-f2c3339db57b
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: a939a0845d7577185ff32edd542bcb2082543a26
ms.openlocfilehash: 153b1ea4ec3d326fb533817cdb74d3489135f7d9
ms.lasthandoff: 01/24/2017


---
# <a name="create-a-standalone-scala-application-to-run-on-apache-spark-cluster-on-hdinsight"></a>スタンドアロン Scala アプリケーションを作成して、HDInsight の Apache Spark クラスターで実行する

この記事では、Maven と IntelliJ IDEA を使用して、スタンドアロン Spark アプリケーションを Scala で開発する手順を説明します。 この記事では、ビルド システムとして Apache Maven を使用し、IntelliJ IDEA で提供されている Scala 用の既存の Maven アーキタイプから始めます。  大まかには、IntelliJ IDEA で Scala アプリケーションを作成するには、次の手順が必要です。

* ビルド システムとして Maven を使用します。
* プロジェクト オブジェクト モデル (POM) ファイルを更新して、Spark モジュールの依存関係を解決します。
* Scala でアプリケーションを作成します。
* HDInsight Spark クラスターに送信できる jar ファイルを生成します。
* Livy を使用して Spark クラスターでアプリケーションを実行します。

> [!NOTE]
> HDInsight には、アプリケーションを作成して Linux の HDInsight Spark クラスターに送信するプロセスを容易にする IntelliJ IDEA プラグイン ツールも用意されています。 詳細については、 [IntelliJ IDEA 用の HDInsight Tools プラグインを使用した Spark Scala アプリケーションの作成と送信](hdinsight-apache-spark-intellij-tool-plugin.md)に関するページをご覧ください。
> 
> 

**前提条件**

* Azure サブスクリプション。 [Azure 無料試用版の取得](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。
* HDInsight での Apache Spark クラスター。 手順については、 [Azure HDInsight での Apache Spark クラスターの作成](hdinsight-apache-spark-jupyter-spark-sql.md)に関するページを参照してください。
* Oracle Java Development kit。 [ここ](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)からインストールできます。
* Java IDE。 この記事では、IntelliJ IDEA 15.0.1 を使用します。 [ここ](https://www.jetbrains.com/idea/download/)からインストールできます。

## <a name="install-scala-plugin-for-intellij-idea"></a>IntelliJ IDEA 用の Scala プラグインをインストールする
IntelliJ IDEA のインストールで、Scala プラグインを有効にするように求めるメッセージが表示されなかった場合は、IntelliJ IDEA を起動し、次の手順を実行してプラグインをインストールします。

1. IntelliJ IDEA を起動し、[ようこそ] 画面で、**[Configure (構成)]** をクリックし、**[Plugins (プラグイン)]** をクリックします。
   
    ![Scala プラグインの有効化](./media/hdinsight-apache-spark-create-standalone-application/enable-scala-plugin.png)
2. 次の画面の左下隅にある **[Install JetBrains plugin (JetBrains プラグインのインストール)]** をクリックします。 **[Browse JetBrains Plugins (JetBrains プラグインを参照)]** ダイアログ ボックスが開いたら、Scala を検索して、**[Install (インストール)]** をクリックします。
   
    ![Scala プラグインのインストール](./media/hdinsight-apache-spark-create-standalone-application/install-scala-plugin.png)
3. プラグインが正常にインストールされたら、 **[Restart IntelliJ IDEA (IntelliJ IDEA の再起動)]** ボタンをクリックして、IDE を再起動します。

## <a name="create-a-standalone-scala-project"></a>スタンドアロンの Scala プロジェクトを作成する
1. IntelliJ IDEA を起動し、新しいプロジェクトを作成します。 [New Project (新規プロジェクト)] ダイアログ ボックスで、次の選択を行い、 **[Next (次へ)]**をクリックします。
   
    ![Maven プロジェクトの作成](./media/hdinsight-apache-spark-create-standalone-application/create-maven-project.png)
   
   * プロジェクトの種類として、 **[Maven]** を選択します。
   * **[Project SDK (プロジェクトのSDK)]** を指定します。 [New (新規)] をクリックし、Java のインストール ディレクトリに移動します。通常は、`C:\Program Files\Java\jdk1.8.0_66` です。
   * **[Create from archetype (アーキタイプからの作成)]** オプションを選択します。
   * アーキタイプの一覧から、 **org.scala-tools.archetypes:scala-archetype-simple**を選択します。 これによって、正しいディレクトリ構造が作成され、Scala プログラムを作成するのに必要な既定の依存関係がダウンロードされます。
2. **[GroupId]**、**[ArtifactId]**、および **[Version]** に関連する値を指定します。 **[次へ]**をクリックします。
3. 次のダイアログ ボックスでは (ここでは、Maven ホーム ディレクトリとその他のユーザー設定を指定)、既定の設定をそのまま使用し、 **[Next (次へ)]**をクリックします。
4. 最後のダイアログ ボックスで、プロジェクト名と場所を指定し、 **[Finish (完了)]**をクリックします。
5. **src\test\scala\com\microsoft\spark\example** にある **MySpec.Scala** ファイルを削除します。 これはアプリケーションに必要ありません。
6. 必要な場合は、既定のソースおよびテスト ファイルの名前を変更します。 IntelliJ IDEA の左側のウィンドウで、**src\main\scala\com.microsoft.spark.example** に移動します。 **App.scala** を右クリックし、**[Refactor (リファクター)]** をクリックし、[Rename file (ファイル名の変更)] をクリックし、ダイアログ ボックスで、アプリケーションの新しい名前を指定して、**[Refactor (リファクター)]** をクリックします。
   
    ![ファイルの名前変更](./media/hdinsight-apache-spark-create-standalone-application/rename-scala-files.png)  
7. 以降の手順では、pom.xml を更新して、Spark Scala アプリケーションの依存関係を定義します。 これらの依存関係が自動的にダウンロードされ解決されるように、適宜、Maven を構成する必要があります。
   
    ![自動ダウンロードのための Maven の構成](./media/hdinsight-apache-spark-create-standalone-application/configure-maven.png)
   
   1. **[File (ファイル)]** メニューの **[Settings (設定)]** をクリックします。
   2. **[Settings (設定)]** ダイアログ ボックスで、**[Build, Execution, Deployment (ビルド、実行、デプロイ)]** > **[Build Tools (構築ツール)]** > **[Maven]** > **[Importing (インポート)]** の順に移動します。
   3. **[Import Maven projects automaticallyMaven (Maven プロジェクトを自動的にインポートする)]**オプションを選択します。
   4. **[Apply (適用)]** をクリックし、**[OK]** をクリックします。
8. 目的のアプリケーション コードを含むように、Scala ソース ファイルを更新します。 ファイルを開き、既存のサンプル コードを次のコードに置き換え、変更を保存します。 このコードでは、HVAC.csv (すべての HDInsight Spark クラスターで使用可能) からデータを読み取り、6 番目の列に 1 桁の数字のみが含まれる行を取得し、出力をクラスター用の既定のストレージ コンテナーの下にある **/HVACOut** に書き込みます。
   
        package com.microsoft.spark.example
   
        import org.apache.spark.SparkConf
        import org.apache.spark.SparkContext
   
        /**
          * Test IO to wasb
          */
        object WasbIOTest {
          def main (arg: Array[String]): Unit = {
            val conf = new SparkConf().setAppName("WASBIOTest")
            val sc = new SparkContext(conf)
   
            val rdd = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
   
            //find the rows which have only one digit in the 7th column in the CSV
            val rdd1 = rdd.filter(s => s.split(",")(6).length() == 1)
   
            rdd1.saveAsTextFile("wasbs:///HVACout")
          }
        }
9. pom.xml ファイルを更新します。
   
   1. `<project>\<properties>` に、以下を追加します。
      
          <scala.version>2.10.4</scala.version>
          <scala.compat.version>2.10.4</scala.compat.version>
          <scala.binary.version>2.10</scala.binary.version>
   2. `<project>\<dependencies>` に、以下を追加します。
      
           <dependency>
             <groupId>org.apache.spark</groupId>
             <artifactId>spark-core_${scala.binary.version}</artifactId>
             <version>1.4.1</version>
           </dependency>
      
      Pom.xml に変更内容を保存します。
10. jar ファイルを作成します。 IntelliJ IDEA では、JAR をプロジェクトのアーティファクトとして作成できます。 次の手順に従います。
    
    1. **[File (ファイル)]** メニューの **[Project Structure (プロジェクトの構造)]** をクリックします。
    2. **[Project Structure (プロジェクトの構造)]** ダイアログ ボックスで、**[Artifacts (アーティファクト)]** をクリックし、プラス記号をクリックします。 ポップアップ ダイアログ ボックスで、**[JAR]** をクリックし、**[From modules with dependencie] \(依存関係を持つモジュールから)** をクリックします。
       
        ![JAR の作成](./media/hdinsight-apache-spark-create-standalone-application/create-jar-1.png)
    3. **[Create JAR from Modules (モジュールから JAR を作成)]** ダイアログ ボックスで、**[Main Class (メイン クラス)]** の省略記号 (![省略記号](./media/hdinsight-apache-spark-create-standalone-application/ellipsis.png)) をクリックします。
    4. **[Select Main Class (メインクラスの選択)]** ダイアログ ボックスで、既定で表示されるクラスを選択し、**[OK]** をクリックします。
       
        ![JAR の作成](./media/hdinsight-apache-spark-create-standalone-application/create-jar-2.png)
    5. **[Create JAR from Modules] \(モジュールから JAR を作成)** ダイアログ ボックスで、**[extract to the target JAR] \(ターゲット JAR に抽出する)** オプションが選択されていることを確認し、**[OK]** をクリックします。 これにより、すべての依存関係を持つ 1 つの JAR が作成されます。
       
        ![JAR の作成](./media/hdinsight-apache-spark-create-standalone-application/create-jar-3.png)
    6. [Output Layout (出力レイアウト)] タブに、Maven プロジェクトの一部として取り込まれたすべての jar が一覧表示されます。 Scala アプリケーションと直接的な依存関係がないものについては、選択し削除できます。 ここで作成するアプリケーションの場合は、最後の 1 つ (**SparkSimpleApp compile output**) を除き、あとはすべて削除することができます。 削除する jar を選択し、 **[Delete (削除)]** アイコンをクリックします。
       
        ![JAR の作成](./media/hdinsight-apache-spark-create-standalone-application/delete-output-jars.png)
       
        **[Build on make] \(作成時にビルド)** ボックスが選択されていることを確認します。それにより、プロジェクトがビルドまたは更新されるたびに jar が確実に作成されます。 **[Apply (適用)]**、**[OK]** の順にクリックします。
    7. メニュー バーの **[Build (ビルド)]** をクリックし、**[Make Project (プロジェクトの作成)]** をクリックします。 **[Build Artifact (アーティファクトのビルド)]** をクリックして、jar を作成することもできます。 出力 jar が **\out\artifacts** の下に作成されます。
       
        ![JAR の作成](./media/hdinsight-apache-spark-create-standalone-application/output.png)

## <a name="run-the-application-on-the-spark-cluster"></a>Spark クラスターでアプリケーションを実行する
クラスターでアプリケーションを実行するには、次の手順を実行します。

* **Azure Storage BLOB にアプリケーション jar をコピーします** 。 コピーには、[**AzCopy**](../storage/storage-use-azcopy.md) コマンド ライン ユーティリティを使用できます。 他にも、データのアップロードに使用できるクライアントが多数あります。 詳細については、「[HDInsight での Hadoop ジョブ用データのアップロード](hdinsight-upload-data.md)」を参照してください。
* **アプリケーション ジョブをLivy を使用してリモートで送信** します。 HDInsight の Spark クラスターには、Spark ジョブをリモートで送信するための REST エンドポイントを公開する Livy が含まれています。 詳細については、「[Submit Spark jobs remotely using Livy with Spark clusters on HDInsight (HDInsight の Spark クラスターで Livy を使用して Spark ジョブをリモートで送信する)](hdinsight-apache-spark-livy-rest-interface.md)」を参照してください。

## <a name="seealso"></a>関連項目
* [概要: Azure HDInsight での Apache Spark](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>シナリオ
* [Spark と BI: HDInsight と BI ツールで Spark を使用した対話型データ分析の実行](hdinsight-apache-spark-use-bi-tools.md)
* [Spark と Machine Learning: HDInsight で Spark を使用して HVAC データを基に建物の温度を分析する](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Spark と Machine Learning: HDInsight で Spark を使用して食品の検査結果を予測する](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Spark ストリーミング: リアルタイム ストリーミング アプリケーションを作成するための HDInsight での Spark の使用](hdinsight-apache-spark-eventhub-streaming.md)
* [Website log analysis using Spark in HDInsight (HDInsight での Spark を使用した Web サイト ログ分析)](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>アプリケーションの作成と実行
* [Livy を使用して Spark クラスターでジョブをリモートで実行する](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>ツールと拡張機能
* [Use HDInsight Tools Plugin for IntelliJ IDEA to create and submit Spark Scala applicatons (Linux)](hdinsight-apache-spark-intellij-tool-plugin.md)
* [IntelliJ IDEA 用の HDInsight Tools プラグインを使用して Spark アプリケーションをリモートでデバッグする](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [HDInsight の Spark クラスターで Zeppelin Notebook を使用する](hdinsight-apache-spark-use-zeppelin-notebook.md)
* [HDInsight 用の Spark クラスターの Jupyter Notebook で使用可能なカーネル](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Jupyter Notebook で外部のパッケージを使用する](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Jupyter をコンピューターにインストールして HDInsight Spark クラスターに接続する](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>リソースの管理
* [Azure HDInsight での Apache Spark クラスターのリソースの管理](hdinsight-apache-spark-resource-manager.md)
* [HDInsight の Apache Spark クラスターで実行されるジョブの追跡とデバッグ](hdinsight-apache-spark-job-debugging.md)


