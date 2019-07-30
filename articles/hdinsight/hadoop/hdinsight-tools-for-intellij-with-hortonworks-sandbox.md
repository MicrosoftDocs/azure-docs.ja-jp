---
title: Hortonworks Sandbox と Azure Toolkit for IntelliJ の使用
description: Hortonworks Sandbox と一緒に Azure Toolkit for IntelliJ の HDInsight ツールを使用する方法について説明します。
keywords: Hadoop ツール, Hive クエリ, Intellij, Hortonworks Sandbox, Azure Toolkit for IntelliJ
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: hrasheed
ms.openlocfilehash: 5234835f5fbfd5617e6b0440ff38fa900051470a
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67450123"
---
# <a name="use-hdinsight-tools-for-intellij-with-hortonworks-sandbox"></a>Hortonworks Sandbox と IntelliJ 用 HDInsight ツールを使用する

IntelliJ 用 HDInsight ツールを使用して、コンピューターで実行されている [Hortonworks Sandbox](https://hortonworks.com/products/sandbox/) で Apache Scala アプリケーションを開発し、そのアプリケーションをテストする方法について説明します。 

[IntelliJ IDEA](https://www.jetbrains.com/idea/) は、コンピューター ソフトウェアを開発するための Java 統合開発環境 (IDE) です。 Hortonworks Sandbox でアプリケーションを開発し、テストした後で、そのアプリケーションを [Azure HDInsight](apache-hadoop-introduction.md) に移動できます。

## <a name="prerequisites"></a>前提条件

この記事の操作を始める前に、以下を用意する必要があります。

- ローカル コンピューターで実行されている Hortonworks Data Platform (HDP) 2.4 on Hortonworks Sandbox。 HDP をセットアップするには、[仮想マシンの Hadoop サンドボックスを使用した Apache Hadoop エコシステムの概要](apache-hadoop-emulator-get-started.md)に関する記事を参照してください。 
    > [!NOTE]
    > IntelliJ 用 HDInsight ツールがテストされているのは HDP 2.4 のみです。 HDP 2.4 を入手するには、[Hortonworks Sandbox ダウンロード サイト](https://hortonworks.com/downloads/#sandbox)で、 **[Hortonworks Sandbox アーカイブ]** を展開します。

- [Java Developer Kit (JDK) バージョン 1.8 以降](https://aka.ms/azure-jdks)。 Azure Toolkit for IntelliJ には JDK が必要です。

- [IntelliJ IDEA コミュニティ エディション](https://www.jetbrains.com/idea/download)と、[Scala](https://plugins.jetbrains.com/idea/plugin/1347-scala) プラグインおよび [Azure Toolkit for IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij) プラグイン。 IntelliJ 用の HDInsight ツールは、Azure Toolkit for IntelliJ に付属しています。 

プラグインをインストールするには、次の手順に従います。

  1. IntelliJ IDEA を開きます。
  2. **[Welcome]\(ようこそ\)** ページで、 **[Configure]\(構成\)** を選択し、 **[Plugins]\(プラグイン\)** を選択します。
  3. 左下隅にある **[Install JetBrains plugin]\(JetBrains プラグインのインストール\)** を選択します。
  4. 検索機能を使用して**Scala** を検索し、 **[Install]\(インストール\)** をクリックします。
  5. インストールを完了するには、 **[Restart IntelliJ IDEA]\(IntelliJ IDEA の再起動\)** を選択します。
  6. 手順 4 と 5 を繰り返して、**Azure Toolkit for IntelliJ** をインストールします。 詳細については、「[Azure Toolkit for IntelliJ のインストール](https://docs.microsoft.com/azure/azure-toolkit-for-intellij-installation)」を参照してください。

## <a name="create-an-apache-spark-scala-application"></a>Apache Spark Scala アプリケーションを作成する

このセクションでは、IntelliJ IDEA を使用してサンプルの Scala プロジェクトを作成します。 次のセクションで、プロジェクトを送信する前に IntelliJ IDEA を Hortonworks Sandbox (エミュレーター) にリンクします。

1. コンピューターで IntelliJ IDEA を開きます。 **[新しいプロジェクト]** ダイアログ ボックスで、以下の手順を実行します。

   1. **[HDInsight]**  >  **[Spark on HDInsight (Scala)]\(HDInsight の Spark (Scala)\)** を選択します。
   2. **[Build tool]\(ビルド ツール\)** ボックスの一覧で、実際のシナリオに基づいて次のいずれかを選択します。

      * **Maven**: Scala プロジェクト作成ウィザードをサポートする場合。
      * **SBT**: 依存関係を管理し、Scala プロジェクトをビルドする場合。

   ![[New Project]\(新しいプロジェクト\) ダイアログ ボックス](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-scala-project.png)

2. **[次へ]** を選択します。
3. 次の **[New Project]\(新しいプロジェクト\)** ダイアログ ボックスで、以下の手順を実行します。

   1. **[Project name]\(プロジェクト名\)** ボックスにプロジェクト名を入力します。
   2. **[Project location]\(プロジェクトの場所\)** ボックスにプロジェクトの場所を入力します。
   3. **[Project SDK]\(プロジェクト SDK\)** ドロップダウン リストの横の **[New]\(新規\)** をクリックし、 **[JDK]** を選択して、Java JDK バージョン 1.7 以降のフォルダーを指定します。 Spark 2.x クラスターに **[Java 1.8]** を選択します。 Spark 1.x クラスターに **[Java 1.7]** を選択します。 既定の場所は、C:\Program Files\Java\jdk1.8.x_xxx です。
   4. **[Spark version]\(Spark のバージョン\)** ボックスの一覧には、Scala プロジェクト作成ウィザードによって Spark SDK と Scala SDK の適切なバージョンが統合されています。 Spark クラスターのバージョンが 2.0 より前の場合は、**Spark 1.x** を選択します。 それ以外の場合は、**Spark 2.x** を選択します。 この例では、Spark 1.6.2 (Scala 2.10.5) を使用します。 **Scala 2.10.x** と示されているリポジトリを使用してください。 Scala 2.11.x と示されているレポジトリは使用しないでください。
    
      ![IntelliJ Scala プロジェクトのプロパティを作成する](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-scala-project-properties.png)


4. **[完了]** を選択します。
5. **プロジェクト** ビューがまだ開いていない場合は、**Alt + 1** キーを押して開きます。
6. **Project Explorer** でプロジェクトを展開し、 **[src]** を選択します。
7. **[src]** を右クリックし、 **[New]\(新規\)** をポイントして、 **[Scala class]\(Scala クラス\)** をクリックします。
8. **[名前]** ボックスに、名前を入力します。 **[Kind]\(種類\)** ボックスで、 **[Object]\(オブジェクト\)** を選択します。 **[OK]** をクリックします。

    ![[Create New Scala Class]\(新規 Scala クラスの作成\) ダイアログ ボックス](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-new-scala-class.png)

9. .scala ファイルに次のコードを貼り付けます。

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
                // Enforce that everything has been calculated and in cache.
                pairs1.count

                println(pairs1.groupByKey(numReducers).count)
            }
        }

10. **[Build]\(ビルド\)** メニューの **[Build project]\(プロジェクトのビルド\)** をクリックします。 コンパイルが正常に終了することを確認します。


## <a name="link-to-the-hortonworks-sandbox"></a>Hortonworks Sandbox にリンクする

Hortonworks Sandbox (エミュレーター) にリンクするには、既存の IntelliJ アプリケーションが必要です。

エミュレーターにリンクするには、以下の手順を実行します。

1. IntelliJ でプロジェクトを開きます。
2. **[View]\(表示\)** メニューの **[Tools Windows]\(ツール ウィンドウ\)** を選択し、 **[Azure Explorer]** を選択します。
3. **[Azure]** を展開し、 **[HDInsight]** を右クリックして、 **[Link an Emulator]\(エミュレーターをリンクする\)** をクリックします。
4. **[Link A New Emulator]\(新しいエミュレーターのリンク\)** ダイアログ ボックスで、Hortonworks Sandbox のルート アカウントに設定したパスワードを入力します。 次に、次のスクリーン ショットで使用されているような値を入力します。 **[OK]** をクリックします。 

   ![[Link A New Emulator]\(新しいエミュレーターのリンク\) ダイアログ ボックス](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-link-an-emulator.png)

5. **[Yes]\(はい\)** をクリックしてエミュレーターを構成します。

エミュレーターが正常に接続されると、エミュレーター (Hortonworks Sandbox) が HDInsight ノードに表示されます。

## <a name="submit-the-spark-scala-application-to-the-hortonworks-sandbox"></a>Hortonworks Sandbox に Spark Scala アプリケーションを送信する

IntelliJ IDEA をエミュレーターにリンクしたら、プロジェクトを送信できます。

プロジェクトをエミュレーターに送信するには、次の手順を実行します。

1. **Project Explorer** でプロジェクトを右クリックし、 **[Submit Spark application to HDInsight]\(HDInsight への Spark アプリケーションの送信\)** を選択します。
2. 次の手順を完了します。

    1. **[Spark cluster (Linux only)]\(Spark クラスター (Linux のみ)\)** ドロップダウン リストで、ローカルの Hortonworks Sandbox を選択します。
    2. **[Main class name]\(メイン クラス名\)** ボックスで、メイン クラス名を選択するか入力します。 この記事では、名前は **GroupByTest** です。

3. **[Submit]\(送信\)** をクリックします。 ジョブ送信のログは、Spark の送信ツール ウィンドウに表示されます。

## <a name="next-steps"></a>次の手順

- [Azure Toolkit for IntelliJ の HDInsight ツールを使用して HDInsight Spark Linux クラスター向けの Apache Spark アプリケーションを作成する](../spark/apache-spark-intellij-tool-plugin.md)方法を確認します。

- IntelliJ 用 HDInsight ツールについては、[Apache Spark 開発向け IntelliJ 用 HDInsight ツールの紹介](https://www.youtube.com/watch?v=YTZzYVgut6c)に関する動画をご覧ください。

- [Azure Toolkit for IntelliJ を使用して HDInsight クラスター上で SSH により Apache Spark アプリケーションをリモートでデバッグする](../spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md)方法を確認します。

- [Azure Toolkit for IntelliJ の HDInsight ツールを使用して HDInsight Spark Linux クラスター上で Apache Spark アプリケーションをリモートでデバッグする](../spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)方法を確認します。

- [Azure Toolkit for Eclipse の HDInsight ツールを使用して Apache Spark アプリケーションを作成する](../spark/apache-spark-eclipse-tool-plugin.md)方法を確認します。

- Eclipse 用 HDInsight ツールのビデオについては、「[Use HDInsight Tools for Eclipse to create Spark applications (Eclipse 用 HDInsight ツールを使用した Spark アプリケーションの作成)](https://mix.office.com/watch/1rau2mopb6fha)」をご覧ください。
