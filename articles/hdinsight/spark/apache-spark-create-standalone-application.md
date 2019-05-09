---
title: チュートリアル:IntelliJ を使用した Azure HDInsight での Spark の Scala Maven アプリケーションの作成
description: Scala で記述され、ビルド システムとして Apache Maven を使用し、IntelliJ IDEA によって提供される Scala 用の既存の Maven アーキタイプを使用する Spark アプリケーションを作成します。
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.topic: tutorial
ms.date: 01/30/2019
ms.openlocfilehash: a969c026d702c423bee4871651c8b4fa26b3d37a
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/04/2019
ms.locfileid: "55700946"
---
# <a name="tutorial-create-a-scala-maven-application-for-apache-spark-in-hdinsight-using-intellij"></a>チュートリアル:IntelliJ を使用した HDInsight での Apache Spark の Scala Maven アプリケーションの作成

このチュートリアルでは、[Apache Maven](https://maven.apache.org/) と IntelliJ IDEA を利用し、[Scala](https://www.scala-lang.org/) で記述された [Apache Spark](https://spark.apache.org/) アプリケーションを作成する方法について説明します。 この記事では、ビルド システムとして Apache Maven を使用し、IntelliJ IDEA で提供されている Scala 用の既存の Maven アーキタイプから始めます。  IntelliJ IDEA での Scala アプリケーションには次の手順があります。

* ビルド システムとして Maven を使用します。
* プロジェクト オブジェクト モデル (POM) ファイルを更新して、Spark モジュールの依存関係を解決します。
* Scala でアプリケーションを作成します。
* HDInsight Spark クラスターに送信できる jar ファイルを生成します。
* Livy を使用して Spark クラスターでアプリケーションを実行します。

> [!NOTE]  
> HDInsight には、アプリケーションを作成して Linux の HDInsight Spark クラスターに送信するプロセスを容易にする IntelliJ IDEA プラグイン ツールも用意されています。 詳細については、[IntelliJ IDEA 用の HDInsight Tools プラグインを使用した Apache Spark アプリケーションの作成と送信](apache-spark-intellij-tool-plugin.md)に関するページを参照してください。

このチュートリアルでは、以下の内容を学習します。
> [!div class="checklist"]
> * IntelliJ を使用して Scala Maven アプリケーションを開発する

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

* HDInsight での Apache Spark クラスター。 手順については、「 [Create Apache Spark clusters in Azure HDInsight (Azure HDInsight での Apache Spark クラスターの作成)](apache-spark-jupyter-spark-sql.md)」を参照してください。
* [Oracle Java Development Kit](https://www.azul.com/downloads/azure-only/zulu/)。  このチュートリアルでは、Java バージョン 8.0.202 を使用します。
* Java IDE。 この記事では、[IntelliJ IDEA Community Version 2018.3.4](https://www.jetbrains.com/idea/download/) を使用します。
* Azure Toolkit for IntelliJ。  「[Azure Toolkit for IntelliJ のインストール](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-installation?view=azure-java-stable)」を参照してください。

## <a name="install-scala-plugin-for-intellij-idea"></a>IntelliJ IDEA 用の Scala プラグインをインストールする
Scala プラグインをインストールするには、次の手順を実行します。

1. IntelliJ IDEA を開きます。

2. ようこそ画面で **[構成]** > **[プラグイン]** の順に移動し、**[プラグイン]** ウィンドウを開きます。
   
    ![Scala プラグインの有効化](./media/apache-spark-create-standalone-application/enable-scala-plugin.png)

3. 新しいウィンドウに表示される Scala プラグインの **[インストール]** を選択します。  
 
    ![Scala プラグインのインストール](./media/apache-spark-create-standalone-application/install-scala-plugin.png)

4. プラグインが正常にインストールされたら、IDE を再起動する必要があります。


## <a name="use-intellij-to-create-application"></a>IntelliJ を使用してアプリケーションを作成する

1. IntelliJ IDEA を起動し、**[Create New Project]\(新しいプロジェクトの作成\)** を選択して、**[New Project]\(新しいプロジェクト\)** ウィンドウを開きます。

2. 左側のウィンドウの **[Azure Spark/HDInsight]** を選択します。

3. メイン ウィンドウで **[Spark Project (Scala)]\(Spark プロジェクト (Scala)\)** を選択します。

4. **[Build tool]\(ビルド ツール\)** ドロップダウン ボックスの一覧で、次のいずれかを選択します。
      * Scala プロジェクト作成ウィザードをサポートする場合は **Maven**。
      * 依存関係を管理し、Scala プロジェクトをビルドする場合は **SBT**。

   ![[New Project]\(新しいプロジェクト\) ダイアログ ボックス](./media/apache-spark-create-standalone-application/create-hdi-scala-app.png)

5. **[次へ]** を選択します。

6. **[New Project]\(新しいプロジェクト\)** ウィンドウで、次の情報を指定します。  

  	|  プロパティ   | 説明   |  
  	| ----- | ----- |  
  	|プロジェクト名| 名前を入力します。|  
  	|Project&nbsp;location (プロジェクトの場所)| プロジェクトを保存する任意の場所を入力します。|
  	|Project SDK (プロジェクト SDK)| IDEA を初めて使用するとき、これは空白になっています。  **[New]\(新規作成\)** を選択し、自分の JDK に移動します。|
  	|Spark バージョン|作成ウィザードにより、Spark SDK と Scala SDK の適切なバージョンが統合されます。 Spark クラスターのバージョンが 2.0 より前の場合は、**Spark 1.x** を選択します。 それ以外の場合は、**Spark 2.x** を選択します。 この例では、**Spark 2.3.0 (Scala 2.11.8)** を使用します。|

    ![Spark SDK の選択](./media/apache-spark-create-standalone-application/hdi-new-project.png)

7. **[完了]** を選択します。

## <a name="create-a-standalone-scala-project"></a>スタンドアロンの Scala プロジェクトを作成する

1. IntelliJ IDEA を起動し、**[Create New Project]\(新しいプロジェクトの作成\)** を選択して、**[New Project]\(新しいプロジェクト\)** ウィンドウを開きます。

2. 左側のウィンドウで、**[Maven]** を選択します。

3. **[Project SDK (プロジェクトのSDK)]** を指定します。 空白の場合は、**[New]\(新規作成\)** を選択し、Java のインストール ディレクトリに移動します。

4. **[Create from archetype]\(アーキタイプからの作成\)** チェック ボックスをオンにします。  

5. アーキタイプの一覧から、 **org.scala-tools.archetypes:scala-archetype-simple**を選択します。 このアーキタイプによって、正しいディレクトリ構造が作成され、Scala プログラムを作成するのに必要な既定の依存関係がダウンロードされます。

    ![Maven プロジェクトの作成](./media/apache-spark-create-standalone-application/create-maven-project.png)

6. **[次へ]** を選択します。

7. **[GroupId]**、**[ArtifactId]**、および **[Version]** に関連する値を指定します。 このチュートリアルでは、次の値を使用しています。

    - **GroupId:** com.microsoft.spark.example
    - **ArtifactId:** SparkSimpleApp

8. **[次へ]** を選択します。

9. 設定を確認してから、**[Next] (次へ)** を選択します。

10. プロジェクト名と場所を確認し、**[Finish] (完了)** をクリックします。  プロジェクトのインポートには数分かかります。

11. プロジェクトがインポートされたら、左側のウィンドウで **[SparkSimpleApp]** > **[src]\(ソース\)** > **[test]\(テスト\)** > **[scala]** > **[com]** > **[microsoft]** > **[spark]** > **[example]\(例\)** の順に移動します。  **[MySpec]** を右クリックし、**[Delete]\(削除\)** を選択します。このファイルはアプリケーションに必要ありません。  ダイアログ ボックスで **[OK]** を選択します。
  
12. 以降の手順では、**pom.xml** を更新して、Spark Scala アプリケーションの依存関係を定義します。 これらの依存関係が自動的にダウンロードされ解決されるように、適宜、Maven を構成する必要があります。

13. **[File]\(ファイル\)** メニューの **[Settings]\(設定\)** を選択し、**[Settings]\(設定\)** ウィンドウを開きます。

14. **[Settings]\(設定\)** ウィンドウで、**[Build, Execution, Deployment]\(ビルド、実行、デプロイ\)** > **[Build Tools]\(構築ツール\)** > **[Maven]** > **[Importing]\(インポート\)** の順に移動します。

15. **[Import Maven projects automatically]\(Maven プロジェクトを自動的にインポートする\)** チェック ボックスをオンにします。

16. **[Apply]\(適用\)** を選択し、次に **[OK]** を選択します。  元のプロジェクト ウィンドウが表示されます。
   
    ![自動ダウンロードのための Maven の構成](./media/apache-spark-create-standalone-application/configure-maven.png)
   

17. 左側のウィンドウで、**[src]\(ソース\)** > **[main]** > **[scala]** > **[com.microsoft.spark.example]** の順に移動し、**[App]** をダブルクリックして App.scala を開きます。

18. 既存のサンプル コードを次のコードに置き換え、変更を保存します。 このコードでは、HVAC.csv (すべての HDInsight Spark クラスターで使用可能) からデータを読み取り、6 番目の列に 1 桁の数字のみが含まれる行を取得し、出力をクラスター用の既定のストレージ コンテナーの下にある **/HVACOut** に書き込みます。

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
   
            val rdd = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
   
            //find the rows which have only one digit in the 7th column in the CSV
            val rdd1 = rdd.filter(s => s.split(",")(6).length() == 1)
   
            rdd1.saveAsTextFile("wasb:///HVACout")
          }
        }
19. 左ウィンドウで **pom.xml** をダブルクリックします。  
   
20. `<project>\<properties>` に、以下のセグメントを追加します。
      
          <scala.version>2.11.8</scala.version>
          <scala.compat.version>2.11.8</scala.compat.version>
          <scala.binary.version>2.11</scala.binary.version>

21. `<project>\<dependencies>` に、以下のセグメントを追加します。
      
           <dependency>
             <groupId>org.apache.spark</groupId>
             <artifactId>spark-core_${scala.binary.version}</artifactId>
             <version>2.3.0</version>
           </dependency>
      
    Pom.xml に変更内容を保存します。

22. jar ファイルを作成します。 IntelliJ IDEA では、JAR をプロジェクトのアーティファクトとして作成できます。 次の手順に従います。
    
    1. **[File]\(ファイル\)** メニューの **[Project Structure]\(プロジェクトの構造\)** を選択します。

    2. **[Project Structure]\(プロジェクトの構造\)** ウィンドウで、**[Artifacts]\(成果物\)** > **プラス記号 (+)** > **[JAR]** > **[From modules with dependencies]\(依存関係を持つモジュールから\)** の順に移動します。
       
        ![JAR の作成](./media/apache-spark-create-standalone-application/create-jar-1.png)

    3. **[Create JAR from Modules]\(モジュールから JAR を作成\)** ウィンドウで、**[Main Class]\(メイン クラス\)** ボックスのフォルダー アイコンを選択します。

    4. **[Select Main Class]\(メイン クラスの選択\)** ウィンドウで、既定で表示されるクラスを選択し、**[OK]** を選択します。
       
        ![JAR の作成](./media/apache-spark-create-standalone-application/create-jar-2.png)

    5. **[Create JAR from Modules]\(モジュールから JAR を作成\)** ウィンドウで、**[extract to the target JAR]\(ターゲット JAR に抽出する\)** オプションが選択されていることを確認し、**[OK]** を選択します。  これにより、すべての依存関係を持つ 1 つの JAR が作成されます。
       
        ![JAR の作成](./media/apache-spark-create-standalone-application/create-jar-3.png)

    6. **[Output Layout (出力レイアウト)]** タブに、Maven プロジェクトの一部として取り込まれたすべての jar が一覧表示されます。 Scala アプリケーションと直接的な依存関係がないものについては、選択し削除できます。 ここで作成するアプリケーションの場合は、最後の 1 つ (**SparkSimpleApp compile output**) を除き、あとはすべて削除することができます。 削除する jar を選択し、マイナス記号 (**-**) を選択します。
       
        ![JAR の作成](./media/apache-spark-create-standalone-application/delete-output-jars.png)
       
        **[Include in project build]\(プロジェクト ビルドに含める\)** ボックスがオンになっていることを確認します。それにより、プロジェクトがビルドまたは更新されるたびに jar が確実に作成されます。 **[Apply]\(適用\)**、**[OK]** の順に選択します。

    7. jar を作成するには、**[Build]\(ビルド\)** > **[Build Artifacts]\(ビルド成果物\)** > **[Build]\(ビルド\)** の順に移動します。 プロジェクトは 30 秒ほどでコンパイルされます。  出力 jar が **\out\artifacts** の下に作成されます。
       
        ![JAR の作成](./media/apache-spark-create-standalone-application/output.png)

## <a name="run-the-application-on-the-apache-spark-cluster"></a>Apache Spark クラスターでアプリケーションを実行する
クラスターでアプリケーションを実行するには、次のアプローチを使用できます。

* **Azure Storage BLOB にアプリケーション jar をコピーします** 。 コピーには、[**AzCopy**](../../storage/common/storage-use-azcopy.md) コマンドライン ユーティリティを使用できます。 他にも、データのアップロードに使用できるクライアントが多数あります。 詳細については、[HDInsight での Apache Hadoop ジョブ用データのアップロード](../hdinsight-upload-data.md)に関するページを参照してください。

* Spark クラスターに、**Apache Livy を使用してリモートからアプリケーション ジョブを送信**します。 HDInsight の Spark クラスターには、Spark ジョブをリモートで送信するための REST エンドポイントを公開する Livy が含まれています。 詳細については、[HDInsight の Spark クラスターで Apache Livy を使用してリモートから Apache Spark ジョブを送信する方法](apache-spark-livy-rest-interface.md)に関するページを参照してください。

## <a name="next-step"></a>次のステップ

この記事では、Apache Spark Scala アプリケーションの作成方法について説明しました。 Livy を使用して HDInsight Spark クラスター上でこのアプリケーションを実行する方法を確認するには、次の記事に進みます。

> [!div class="nextstepaction"]
>[Apache Livy を使用して Apache Spark クラスターでジョブをリモートから実行する](./apache-spark-livy-rest-interface.md)
