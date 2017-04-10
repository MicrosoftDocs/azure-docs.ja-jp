---
title: "Hortonworks Sandbox と Azure Toolkit for IntelliJ の使用 | Microsoft Docs"
description: "Hortonworks Sandbox と一緒に Azure Toolkit for IntelliJ の HDInsight ツールを使用する方法について説明します。"
keywords: "Hadoop ツール, Hive クエリ, Intellij, Hortonworks Sandbox, Azure Toolkit for IntelliJ"
services: HDInsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: b587cc9b-a41a-49ac-998f-b54d6c0bdfe0
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/07/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: bb700c7de96712666bc4be1f8e430a2e94761f69
ms.openlocfilehash: b52e5bcf443afd05bf8cc37ce436cb80ab41412f
ms.lasthandoff: 01/24/2017


---
#<a name="use-hdinsight-tools-for-intellij-with-hortonworks-sandbox"></a>Hortonworks Sandbox と IntelliJ 用 HDInsight ツールを使用する

IntelliJ 用 HDInsight ツールを使用して、ワークステーションで実行されている [Hortonworks Sandbox](http://hortonworks.com/products/sandbox/) で Apache Scala アプリケーションを開発し、そのアプリケーションをテストする方法について説明します。 [IntelliJ IDEA](https://www.jetbrains.com/idea/) は、コンピューター ソフトウェアを開発するための Java 統合開発環境 (IDE) です。 Hortonworks Sandbox でアプリケーションを開発し、テストした後で、そのアプリケーションを [Azure HDInsight](hdinsight-hadoop-introduction.md) に移動できます。

##<a name="prerequisites"></a>前提条件

このチュートリアルを読み始める前に、次の項目を用意する必要があります。

- ローカル環境で実行されている HDP 2.4 on Hortonworks Sandbox。 構成するには、「[仮想マシンの Hadoop サンドボックスを使用した Hadoop エコシステム入門](hdinsight-hadoop-emulator-get-started.md)」を参照してください。 IntelliJ 用 HDInsight ツールがテストされているのは、HDP 2.4 のみです。 [Hortonworks Sandbox ダウンロード サイト](http://hortonworks.com/downloads/#sandbox)から、**[Hortonworks Sandbox Archive]** を展開して入手します。
- [Java Developer Kit (JDK) バージョン 1.8 以降](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)。 JDK は、Azure Toolkit for IntelliJ で必要です。
- [IntelliJ IDEA コミュニティ エディション](https://www.jetbrains.com/idea/download)と、[Scala](https://plugins.jetbrains.com/idea/plugin/1347-scala) プラグインおよび [Azure Toolkit for IntelliJ](../azure-toolkit-for-intellij.md) プラグイン。 IntelliJ 用 HDInsight ツールは、Azure Toolkit for IntelliJ に付属しています。 

  **プラグインをインストールするには、次の手順に従います。**

  1. IntelliJ IDEA を開きます。
  2. [Welcome (ようこそ)] 画面で、**[Configure (構成)]** をクリックし、**[Plugins (プラグイン)]** をクリックします。
  3. 左下隅にある **[Install JetBrains plugin (JetBrains プラグインのインストール)]** をクリックします。
  4. 検索機能を使用して**Scala** を検索し、**[Install (インストール)]** をクリックします。
  5. **[Restart IntelliJ IDEA (IntelliJ IDEA の再起動)]** をクリックしてインストールを完了します。
  6. 手順 4 と 5 を繰り返して、**Azure Toolkit for IntelliJ** をインストールします。 詳細については、[Azure Toolkit for IntelliJ のインストール](../azure-toolkit-for-intellij-installation.md)に関するページをご覧ください。

## <a name="create-a-spark-scala-application"></a>Spark Scala アプリケーションを作成する

このセクションでは、IntelliJ IDEA を使用してサンプルの Scala プロジェクトを作成します。 次のセクションで、プロジェクトを送信する前に IntelliJ IDEA を Hortonworks Sandbox (エミュレーター) にリンクします。

1. ワークステーションから IntelliJ IDEA を開きます。
2. **[新しいプロジェクトの作成]** をクリックします。
3. 左側のウィンドウの **[HDInsight]** をクリックして、右側のウィンドウの **[Spark on HDInsight (Scala) (HDInsight の Spark (Scala))]**、**[次へ]** の順にクリックします。

    ![IntelliJ Scala プロジェクトを作成する](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-scala-project.png)
4. 次の情報を入力します。

    ![IntelliJ Scala プロジェクトのプロパティを作成する](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-scala-project-properties.png)

    - **プロジェクト名**: プロジェクト名を指定します。
    - **プロジェクトの場所**: プロジェクトの場所を指定します。
    - **プロジェクトの SDK**: **[New (新規)]**、**[JDK]** の順にクリックし、Java JDK バージョン 7 以降のフォルダーを指定します。  既定の場所は、C:\Program Files\Java\jdk1.8.x_xxx です。
    - **Scala SDK**: **[Select (選択)]** をクリックし、バージョン**[2.10.6]** を選択して、**[OK]** をクリックします。 バージョンが表示されない場合は、**[Download (ダウンロード)]** をクリックし、**[Scala バージョン]** を選択して **[OK]** をクリックします。 バージョン 2.11.x は使用しないでください。 この記事では、バージョン 2.10.6 を使用します。
    - **Spark SDK**: [SDK](http://go.microsoft.com/fwlink/?LinkID=723585&clcid=0x409) をダウンロードします。 これを無視し、代わりに Spark Maven リポジトリを使用することもできますが、Spark アプリケーションの開発に適した maven リポジトリがインストールされていることを確認してください。 (たとえば、Spark ストリーミングを使用する場合は、Spark ストリーミング部分がインストールされていることを確認する必要があります。また、Scala 2.10 とマークされているリポジトリを使用していることを確認してください。Scala 2.11 とマークされているリポジトリは使用しないでください)。
5. **[完了]**をクリックします。
6. プロジェクト ビューが開いていない場合は、**Alt +&1;** キーを押して開きます。
7. **Project Explorer** から、プロジェクトを展開し、**[src]** をクリックします。
8. **[src]** を右クリックし、**[New (新規)]** をポイントして、**[Scala class (Scala クラス)]** をクリックします。
9. 名前を入力し、**[Kind (種類)]** で **[Object (オブジェクト)]** を選択して **[OK]** をクリックします。

    ![新しい IntelliJ Scala クラスを作成する](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-new-scala-class.png)
10. .scala ファイルに次のコードを貼り付けます。

        import java.util.Random
        import org.apache.spark.{SparkConf, SparkContext}
        import org.apache.spark.SparkContext._

        /**
        * Usage: GroupByTest [numMappers] [numKVPairs] [valSize] [numReducers]
        */
        object GroupByTest {
            def main(args: Array[String]) {
                val sparkConf = new SparkConf().setAppName("GroupBy Test")
                var numMappers = 3
                var numKVPairs = 10
                var valSize = 10
                var numReducers = 2

                val sc = new SparkContext(sparkConf)

                val pairs1 = sc.parallelize(0 until numMappers, numMappers).flatMap { p =>
                val ranGen = new Random
                var arr1 = new Array[(Int, Array[Byte])](numKVPairs)
                for (i <- 0 until numKVPairs) {
                    val byteArr = new Array[Byte](valSize)
                    ranGen.nextBytes(byteArr)
                    arr1(i) = (ranGen.nextInt(Int.MaxValue), byteArr)
                }
                arr1
                }.cache
                // Enforce that everything has been calculated and in cache
                pairs1.count

                println(pairs1.groupByKey(numReducers).count)
            }
        }

11. **[ビルド]** メニューの **[Build project (プロジェクトのビルド)]** をクリックします。 コンパイルが正常に完了したことを確認します。


## <a name="link-to-the-hortonworks-sandbox"></a>HortonWorks Sandbox にリンクする

Hortonworks Sandbox (エミュレーター) にリンクするには、既存の IntelliJ アプリケーションが必要です。

**エミュレーターにリンクするには**

1. まだ開いていない場合は、IntelliJ でプロジェクトを開きます。
2. **[表示]** メニューで、**[ツール ウィンドウ]** をクリックし、**[Azure Explorer]** をクリックします。
3. **[Azure]** を展開し、**[HDInsight]** を右クリックして、**[Link an Emulator (エミュレーターをリンクする)]** をクリックします。
4. Hortonworks Sandbox の root アカウント用に構成したパスワードを入力し、残りの部分には次のスクリーン ショットと同様の値を入力して、**[OK]** をクリックします。 

  ![IntelliJ でエミュレーターをリンクする](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-link-an-emulator.png)

5. **[はい]** をクリックしてエミュレーターを構成します。

  正常に接続できたら、エミュレーター (Hortonworks Sandbox) が HDInsight ノードの下に表示されます。

## <a name="submit-the-spark-scala-application-to-the-sandbox"></a>Sandbox に Spark Scala アプリケーションを送信する

IntelliJ IDEA をエミュレーターにリンクしたら、プロジェクトを送信できます。

**プロジェクトをエミュレーターに送信するには**

1. **Project Explorer** で、プロジェクトを右クリックし、**[Submit Spark Application to HDInsight (HDInsight への Spark アプリケーションの送信)]** をクリックします。
2. 次のフィールドを指定します。

    - **Spark cluster (Linux only) (Spark クラスター (Linux のみ))**: ローカルの Hortonworks Sandbox を選択します。
    - **Main class name (メイン クラス名)**: メイン クラス名を選択するか、入力します。  このチュートリアルでは、**GroupByTest** です。
3. **[送信]**をクリックします。 ジョブ送信のログは、Spark の送信ツール ウィンドウに表示されます。

##<a name="next-steps"></a>次のステップ:

- IntelliJ 用 HDInsight ツールを使用して HDInsight の Spark アプリケーションを作成する方法については、「[Azure Toolkit for IntelliJ の HDInsight ツールを使用して HDInsight Spark Linux クラスター向けの Spark アプリケーションを作成する](hdinsight-apache-spark-intellij-tool-plugin.md)」を参照してください。
- IntelliJ 用 HDInsight ツールのビデオについては、「[Introduce HDInsight Tools for IntelliJ for Spark Development (Spark 開発向け IntelliJ 用 HDInsight ツールの紹介)](https://mix.office.com/watch/1nqkqjt5xonza)」を参照してください。
- HDInsight でリモートからツールキットを使用して Spark アプリケーションをデバッグする方法については、「[Azure Toolkit for IntelliJ の HDInsight ツールを使用して HDInsight Spark Linux クラスター上で Spark アプリケーションをリモートでデバッグする](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)」を参照してください。
- Eclipse 用 HDInsight ツールを使用して Spark アプリケーションを作成する方法については、「[Azure Toolkit for Eclipse の HDInsight ツールを使用して Spark アプリケーションを作成する](hdinsight-apache-spark-eclipse-tool-plugin.md)」を参照してください。
- Eclipse 用 HDInsight ツールのビデオについては、「[Use HDInsight Tool for Eclipse to create Spark applications (Eclipse 用 HDInsight ツールを使用した Spark アプリケーションの作成)](https://mix.office.com/watch/1rau2mopb6fha)」を参照してください。
