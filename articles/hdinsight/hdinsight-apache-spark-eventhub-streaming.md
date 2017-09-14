---
title: "Azure HDInsight で Event Hub と共に Apache Spark ストリーミングを使用する | Microsoft Docs"
description: "Apache Spark ストリーミング サンプルを作成して、Azure Event Hub にデータ ストリームを送信し、Scala アプリケーションを使用して HDInsight Spark クラスターでイベントを受信します。"
keywords: "apache spark ストリーミング,spark ストリーミング,spark サンプル,apache spark ストリーミング例,event hub azure サンプル,spark サンプル"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 68894e75-3ffa-47bd-8982-96cdad38b7d0
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: nitinme
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 175a2ad70b1f554d05846eb62fb685d4f259af7e
ms.contentlocale: ja-jp
ms.lasthandoff: 08/22/2017

---
# <a name="apache-spark-streaming-process-data-from-azure-event-hubs-with-spark-cluster-on-hdinsight"></a>Apache Spark ストリーミング: HDInsight で Spark クラスターを使用して Azure Event Hub のデータを処理する

この記事では、次の手順で Apache Spark ストリーミング サンプルを作成します。

1. スタンドアロン アプリケーションを使用して、Azure Event Hub にメッセージを取り込みます。

2. 2 つの異なる方法で、Azure HDInsight の Spark クラスターで実行されるアプリケーションを使用して、Event Hub からリアルタイムでメッセージを取得します。

3. ストリーミング分析パイプラインをビルドして、別のストレージ システムにデータを保持するか、その場でデータから洞察を得ます。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 [Azure 無料試用版の取得](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。

* HDInsight での Apache Spark クラスター。 手順については、 [Azure HDInsight での Apache Spark クラスターの作成](hdinsight-apache-spark-jupyter-spark-sql.md)に関するページを参照してください。

## <a name="spark-streaming-concepts"></a>Spark ストリーミングの概念

Spark ストリーミングの詳細については、[Apache Spark ストリーミングの概要](http://spark.apache.org/docs/latest/streaming-programming-guide.html#overview)を参照してください。 HDInsight は、Azure の Spark クラスターに同じストリーミング機能をもたらします。  

## <a name="what-does-this-solution-do"></a>このソリューションの内容

この記事では、Spark ストリーミングのサンプルを作成するために、次の手順を実行します。

1. イベントのストリームを受信する Azure Event Hub を作成します。

2. イベントを生成し、それを Azure Event Hub にプッシュするローカルのスタンドアロン アプリケーションを実行します。 サンプル アプリケーションは [https://github.com/hdinsight/spark-streaming-data-persistence-examples](https://github.com/hdinsight/spark-streaming-data-persistence-examples)に公開されています。

3. Azure Event Hub からイベント ストリームを読み取るストリーミング アプリケーションを Spark クラスターでリモートに実行し、さまざまなデータ処理/分析を実行します。

## <a name="create-an-azure-event-hub"></a>Azure Event Hub を作成します

1. [Azure Portal](https://ms.portal.azure.com) にログインし、画面の左上にある **[新規]** をクリックします。

2. **[モノのインターネット]**、**[イベント ハブ]** の順にクリックします。

    ![Spark ストリーミング サンプルの Event Hub を作成](./media/hdinsight-apache-spark-eventhub-streaming/hdinsight-create-event-hub-for-spark-streaming.png "Spark ストリーミング サンプルの Event Hub を作成")

3. **[名前空間の作成]** ブレードで、名前空間の名前を入力します。 価格レベル (Basic または Standard) を選択します。 Azure サブスクリプション、リソース グループ、リソースが作成される場所を選択します。 **[作成]** をクリックして、名前空間を作成します。

      ![Spark ストリーミング サンプルの Event Hub 名を指定](./media/hdinsight-apache-spark-eventhub-streaming/hdinsight-provide-event-hub-name-for-spark-streaming.png "Spark ストリーミング サンプルの Event Hub 名を指定")

    > [!NOTE]
    > 待機時間とコストを削減するために、HDInsight の Apache Spark クラスターと同じ **[場所]** を選択する必要があります。
    >
    >

4. Event Hubs 名前空間の一覧で、新しく作成された名前空間をクリックします。      


5. 名前空間ブレードで **[Event Hubs]** をクリックし、**[+ Event Hub]** をクリックして新しいイベント ハブを作成します。
   
    ![Spark ストリーミング サンプルの Event Hub を作成](./media/hdinsight-apache-spark-eventhub-streaming/hdinsight-open-event-hubs-blade-for-spark-streaming-example.png "Spark ストリーミング サンプルの Event Hub を作成")

6. Event Hub の名前を入力して、パーティションの数を 10 に設定し、メッセージのリテンション期間を 1 に設定します。 このソリューション内のメッセージはアーカイブされていないため、残りを既定値のままにして、**[作成]** をクリックできます。
   
    ![Spark ストリーミング サンプルの Event Hub 詳細を指定](./media/hdinsight-apache-spark-eventhub-streaming/hdinsight-provide-event-hub-details-for-spark-streaming-example.png "Spark ストリーミング サンプルの Event Hub 詳細を指定")

7. 新しく作成した Event Hub が Event Hub のブレードに表示されます。
    
     ![Spark ストリーミング サンプルの Event Hub を表示](./media/hdinsight-apache-spark-eventhub-streaming/hdinsight-view-event-hub-for-spark-streaming-example.png "Spark ストリーミング サンプルの Event Hub を表示")

8. 名前空間ブレード (特定のイベント ハブ ブレードではなく) に戻り、**[共有アクセス ポリシー]** をクリックし、**[RootManageSharedAccessKey]** をクリックします。
    
     ![Spark ストリーミング サンプルの Event Hub ポリシーを設定](./media/hdinsight-apache-spark-eventhub-streaming/hdinsight-set-event-hub-policies-for-spark-streaming-example.png "Spark ストリーミング サンプルの Event Hub ポリシーを設定")

9. コピー ボタンをクリックして **RootManageSharedAccessKey** プライマリ キーと接続文字列をクリップボードにコピーします。 このチュートリアルの後半で使用するためにこれらを保存します。
    
     ![Spark ストリーミング例の Event Hub ポリシー キーを表示](./media/hdinsight-apache-spark-eventhub-streaming/hdinsight-view-event-hub-policy-keys.png "Spark ストリーミング例の Event Hub ポリシー キーを表示")

## <a name="send-messages-to-azure-event-hub-using-a-sample-scala-application"></a>サンプル Scala アプリケーションを使用して Azure Event Hub にメッセージを送信する

このセクションでは、イベントのストリームを生成するローカルのスタンドアロン Scala アプリケーションを使用して、以前に作成した Azure Event Hub に送信します。 このアプリケーションは GitHub ( [https://github.com/hdinsight/eventhubs-sample-event-producer](https://github.com/hdinsight/eventhubs-sample-event-producer)) で入手できます。 ここに示す手順は、この GitHub リポジトリが既にフォークされていることを前提とします。

1. このアプリケーションを実行するコンピューターに次のソフトウェアがインストールされていることを確認します。

    * Oracle Java Development kit。 [こちら](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)からインストールできます。
    * Apache Maven。 [ここ](https://maven.apache.org/download.cgi)からダウンロードできます。 Maven をインストールする方法については、[こちら](https://maven.apache.org/install.html)を参照してください。

2. コマンド プロンプトを開き、サンプル Scala アプリケーション用の GitHub リポジトリを複製した場所に移動し、次のコマンドを実行してアプリケーションをビルドします。

        mvn package

3. アプリケーションの出力 jar **com-microsoft-azure-eventhubs-client-example-0.2.0.jar** が **/target** ディレクトリの下に作成されます。 この記事の後半で、この JAR を使用して完全なソリューションをテストします。

## <a name="create-application-to-receive-messages-from-event-hub-into-a-spark-cluster"></a>Event Hub から Spark クラスターを受け取るアプリケーションを作成する 

Spark Streaming と Azure Event Hubs の接続には、受信側ベースの接続と Direct DStream ベースの接続という 2 つの方法があります。 Direct DStream ベースの接続は、2017 年 1 月の 2.0.3 リリースに導入されています。 パフォーマンスが高く、効率にリソースを使用できるため、受信側ベースの接続の後継となることが予期されています。 詳細については、[https://github.com/hdinsight/spark-eventhubs](https://github.com/hdinsight/spark-eventhubs) を参照してください。 Direct DStream は Spark 2.0 以降のみをサポートします。

### <a name="build-applications-with-the-dependency-to-spark-eventhubs-connector"></a>Spark-EventHubs コネクタとの依存関係を持つアプリケーションをビルドする

Spark EventHubs のステージング バージョンも GitHub に公開されます。 Spark EventHubs のステージング バージョンを使用するための最初の手順は、pom.xml に次のエントリを追加することで、ソース リポジトリとして GitHub を示すことです。

```xml
<repository>
      <id>spark-eventhubs</id>
      <url>https://raw.github.com/hdinsight/spark-eventhubs/maven-repo/</url>
      <snapshots>
        <enabled>true</enabled>
        <updatePolicy>always</updatePolicy>
      </snapshots>
</repository>
```

その後、次の依存関係をプロジェクトに追加して、プレリリース バージョンを用意します。

Maven の依存関係

```xml
<!-- https://mvnrepository.com/artifact/com.microsoft.azure/spark-streaming-eventhubs_2.11 -->
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spark-streaming-eventhubs_2.11</artifactId>
    <version>2.0.4</version>
</dependency>
```

SBT の依存関係

```
// https://mvnrepository.com/artifact/com.microsoft.azure/spark-streaming-eventhubs_2.11
libraryDependencies += "com.microsoft.azure" % "spark-streaming-eventhubs_2.11" % "2.0.4"
```

### <a name="direct-dstream-connection"></a>Direct DStream 接続

Direct DStream の使用例を含むビルド前の jar ファイルは、[http://central.maven.org/maven2/com/microsoft/azure/spark-streaming-eventhubs_2.11/2.0.4/spark-streaming-eventhubs_2.11-2.0.4.jar](http://central.maven.org/maven2/com/microsoft/azure/spark-streaming-eventhubs_2.11/2.0.4/spark-streaming-eventhubs_2.11-2.0.4.jar) でダウンロードできます。

この jar ファイルには 3 つの例が含まれています。それらのソース コードは [https://github.com/hdinsight/spark-eventhubs/tree/master/examples/src/main/scala/com/microsoft/spark/streaming/examples/directdstream](https://github.com/hdinsight/spark-eventhubs/tree/master/examples/src/main/scala/com/microsoft/spark/streaming/examples/directdstream) で入手できます。

例として [WindowingWordCount](https://github.com/hdinsight/spark-eventhubs/blob/master/examples/src/main/scala/com/microsoft/spark/streaming/examples/directdstream/WindowingWordCount.scala) を使用します。

```scala
private def createStreamingContext(
  sparkCheckpointDir: String,
  batchDuration: Int,
  namespace: String,
  eventHunName: String,
  eventhubParams: Map[String, String],
  progressDir: String) = {
val ssc = new StreamingContext(new SparkContext(), Seconds(batchDuration))
ssc.checkpoint(sparkCheckpointDir)
val inputDirectStream = EventHubsUtils.createDirectStreams(
  ssc,
  namespace,
  progressDir,
  Map(eventHunName -> eventhubParams))

inputDirectStream.map(receivedRecord => (new String(receivedRecord.getBody), 1)).
  reduceByKeyAndWindow((v1, v2) => v1 + v2, (v1, v2) => v1 - v2, Seconds(batchDuration * 3),
    Seconds(batchDuration)).print()

ssc
}

def main(args: Array[String]): Unit = {

if (args.length != 8) {
  println("Usage: program progressDir PolicyName PolicyKey EventHubNamespace EventHubName" +
    " BatchDuration(seconds) Spark_Checkpoint_Directory maxRate")
  sys.exit(1)
}

val progressDir = args(0)
val policyName = args(1)
val policykey = args(2)
val namespace = args(3)
val name = args(4)
val batchDuration = args(5).toInt
val sparkCheckpointDir = args(6)
val maxRate = args(7)

val eventhubParameters = Map[String, String] (
  "eventhubs.policyname" -> policyName,
  "eventhubs.policykey" -> policykey,
  "eventhubs.namespace" -> namespace,
  "eventhubs.name" -> name,
  "eventhubs.partition.count" -> "32",
  "eventhubs.consumergroup" -> "$Default",
  "eventhubs.maxRate" -> s"$maxRate"
)

val ssc = StreamingContext.getOrCreate(sparkCheckpointDir,
  () => createStreamingContext(sparkCheckpointDir, batchDuration, namespace, name,
    eventhubParameters, progressDir))

ssc.start()
ssc.awaitTermination()
}
```

上記の例では、`eventhubParameters` パラメーターは、1 つの EventHubs インスタンスに固有のパラメーターです。このパラメーターを Event Hubs 名前空間に対する Direct DStream オブジェクト マッピングを構築する `createDirectStreams` API に渡す必要があります。 Direct DStream オブジェクトには、Spark Streaming API フレームワークによって提供されるすべての DStream API を呼び出すことができます。 この例では、各単語の頻度を最後の 3 つのマイクロ バッチ間隔内で計算します。

### <a name="receiver-based-connection"></a>受信側ベースの接続

Scala で記述された Spark ストリーミングのサンプル アプリケーションは、イベントを受信し、それをさまざまな宛先にルーティングします。これは [https://github.com/hdinsight/spark-streaming-data-persistence-examples](https://github.com/hdinsight/spark-streaming-data-persistence-examples) で入手できます。 以下の手順に従って、Event Hub 構成についてアプリケーションを更新し、出力 jar を作成してください。

1. IntelliJ IDEA を起動し、起動画面から **[Check out from Version Control]\(バージョン管理からチェックアウト)** を選択し、**[Git]** をクリックします。
   
    ![Apache Spark ストリーミング サンプル - Git からソースを取得](./media/hdinsight-apache-spark-eventhub-streaming/spark-streaming-example-get-source-from-git.png "Apache Spark ストリーミング サンプル - Git からソースを取得")

2. **[Clone Repository] \(リポジトリの複製)** ダイアログ ボックスで、複製元の Git リポジトリの URL および複製先のディレクトリを指定し、**[Clone] \(複製)** をクリックします。
   
    ![Apache Spark ストリーミング サンプル - Git から複製](./media/hdinsight-apache-spark-eventhub-streaming/spark-streaming-example-clone-from-git.png "Apache Spark ストリーミング サンプル - Git から複製")
3. プロジェクトが完全に複製されるまで、画面の指示に従います。 **Alt + 1** キーを押して、**[Project View] \(プロジェクト ビュー)** を開きます。 次のようになります。
   
    ![Apache Spark ストリーミング サンプル - プロジェクト ビュー](./media/hdinsight-apache-spark-eventhub-streaming/spark-streaming-example-project-view.png "Apache Spark ストリーミング サンプル - プロジェクト ビュー")
4. アプリケーション コードが Java8 でコンパイルされるように設定します。 このためには、**[File] \(ファイル)**、**[Project Structure] \(プロジェクトの構造)** の順にクリックし、**[Project] \(プロジェクト)** タブで [Project language level] \(プロジェクトの言語レベル) を **[8 - Lambdas, type annotations, etc.] \(8 - ラムダ、型注釈など)** に設定します。
   
    ![Apache Spark ストリーミング サンプル - コンパイラーの設定](./media/hdinsight-apache-spark-eventhub-streaming/spark-streaming-example-java-8-compiler.png "Apache Spark ストリーミング サンプル - コンパイラーの設定")
5. **pom.xml** を開き、Spark のバージョンが正しいことを確認します。 `<properties>` ノードで、次のスニペットを検索し、Spark のバージョンを確認します。

        <scala.version>2.11.8</scala.version>
        <scala.compat.version>2.11.8</scala.compat.version>
        <scala.binary.version>2.11</scala.binary.version>
        <spark.version>2.0.0</spark.version>

6. アプリケーションでは、**JDBC driver jar** と呼ばれる依存関係 jar が必要です。 Event Hub から受信したメッセージを Azure SQL データベースに書き込むために必要です。 この jar (v4.1 以降) を[ここ](https://msdn.microsoft.com/sqlserver/aa937724.aspx)からダウンロードできます。 プロジェクト ライブラリでこの jar への参照を追加します。 次の手順に従います。
     
     1. アプリケーションが開かれている IntelliJ IDEA ウィンドウで、**[File] \(ファイル)**、**[Project Structure] \(プロジェクトの構造)**、**[Libraries] \(ライブラリ)** の順にクリックします。 
     2. 追加アイコン (![追加アイコン](./media/hdinsight-apache-spark-eventhub-streaming/add-icon.png)) をクリックし、 **[Java]**をクリックして、JDBC driver jar をダウンロードした場所に移動します。 画面の指示に従って、Jar ファイルをプロジェクト ライブラリに追加します。

         ![不足している依存関係の追加](./media/hdinsight-apache-spark-eventhub-streaming/add-missing-dependency-jars.png "不足している依存関係 jar の追加")
     3. **[Apply]**をクリックします。

7. 出力 jar ファイルを作成します。 次の手順に従います。

   1. **[Project Structure] \(プロジェクトの構造)** ダイアログ ボックスで、**[Artifacts] \(アーティファクト)** をクリックし、プラス記号をクリックします。 ポップアップ ダイアログ ボックスで、**[JAR]** をクリックし、**[From modules with dependencie] \(依存関係を持つモジュールから)** をクリックします。      
       
       ![Apache Spark ストリーミング サンプル - JAR の作成](./media/hdinsight-apache-spark-eventhub-streaming/spark-streaming-example-create-jar.png "Apache Spark ストリーミング サンプル - JAR の作成")
   2. **[Create JAR from Modules] \(モジュールから JAR を作成)** ダイアログ ボックスで、**[Main Class] \(メイン クラス)** の省略記号 (![省略記号](./media/hdinsight-apache-spark-eventhub-streaming/ellipsis.png)) をクリックします。
   3. **[Select Main Class] \(メイン クラスの選択)** ダイアログ ボックスで、使用可能なクラスのいずれかを選択し、**[OK]** をクリックします。
      
       ![Apache Spark ストリーミング サンプル - jar のクラスの選択](./media/hdinsight-apache-spark-eventhub-streaming/spark-streaming-example-select-class-for-jar.png "Apache Spark ストリーミング サンプル - jar のクラスの選択")
   4. **[Create JAR from Modules] \(モジュールから JAR を作成)** ダイアログ ボックスで、**[extract to the target JAR] \(ターゲット JAR に抽出する)** オプションが選択されていることを確認し、**[OK]** をクリックします。 これにより、すべての依存関係を持つ 1 つの JAR が作成されます。
      
       ![Apache Spark ストリーミング サンプル - モジュールから jar を作成](./media/hdinsight-apache-spark-eventhub-streaming/spark-streaming-example-create-jar-from-modules.png "Apache Spark ストリーミング サンプル - モジュールから jar を作成")
   5. **[Output Layout (出力レイアウト)]** タブに、Maven プロジェクトの一部として取り込まれたすべての jar が一覧表示されます。 Scala アプリケーションと直接的な依存関係がないものについては、選択し削除できます。 ここで作成するアプリケーションの場合は、最後の 1 つ (**spark-streaming-data-persistence-examples compile output**) を除き、あとはすべて削除することができます。 削除する jar を選択し、**[Delete] \(削除)** アイコン (![削除アイコン](./media/hdinsight-apache-spark-eventhub-streaming/delete-icon.png)) をクリックします。
      
       ![Apache Spark ストリーミング サンプル - 抽出した jar を削除](./media/hdinsight-apache-spark-eventhub-streaming/spark-streaming-example-delete-output-jars.png "Apache Spark ストリーミング サンプル - 抽出した jar を削除")
      
       **[Build on make] \(作成時にビルド)** ボックスが選択されていることを確認します。それにより、プロジェクトがビルドまたは更新されるたびに jar が確実に作成されます。 **[Apply]**をクリックします。
   6. **[Output Layout] \(出力レイアウト)** タブで、右側にある **[Available Elements] \(利用可能な要素)** ボックスの下部に、先ほどプロジェクト ライブラリに追加した SQL JDBC jar があります。 これは **[Output Layout (出力レイアウト)]** タブに追加する必要があります。jar ファイルを右クリックし、 **[Extract Into Output Root (出力ルートに抽出)]**をクリックします。
      
       ![Apache Spark ストリーミング サンプル - 依存関係 jar の抽出](./media/hdinsight-apache-spark-eventhub-streaming/spark-streaming-example-extract-dependency-jar.png "Apache Spark ストリーミング サンプル - 依存関係 jar の抽出")  
      
       これで、 **[Output Layout (出力レイアウト)]** タブは次のようになります。
      
       ![Apache Spark ストリーミング サンプル - 最終的な出力のタブ](./media/hdinsight-apache-spark-eventhub-streaming/spark-streaming-example-final-output-tab.png "Apache Spark ストリーミング サンプル - 最終的な出力のタブ")        
      
       **[Project Structure] \(プロジェクトの構造)** ダイアログ ボックスで、**[Apply] \(適用)** をクリックし、**[OK]** をクリックします。    
   7. メニュー バーの **[Build] \(ビルド)** をクリックし、**[Make Project] \(プロジェクトの作成)** をクリックします。 **[Build Artifact (アーティファクトのビルド)]** をクリックして、jar を作成することもできます。 出力 jar が **\classes\artifacts** の下に作成されます。
      
       ![Apache Spark ストリーミング サンプル - 出力 JAR](./media/hdinsight-apache-spark-eventhub-streaming/spark-streaming-example-output-jar.png "Apache Spark ストリーミング サンプル - 出力 JAR")

## <a name="run-the-application-remotely-on-a-spark-cluster-using-livy"></a>Livy を使用して Spark クラスターでアプリケーションをリモートで実行する

この記事では、Livy を使用して Spark クラスターで Apache Spark ストリーミング アプリケーションをリモートで実行します。 HDInsight Spark クラスターで Livy を使用する方法の詳細については、「 [Azure HDInsight の Apache Spark クラスターへのジョブのリモート送信](hdinsight-apache-spark-livy-rest-interface.md)」を参照してください。 Spark ストリーミング アプリケーションを実行するには、事前にいくつかの操作を実行する必要があります。

1. ローカルのスタンドアロン アプリケーションを起動して、イベントを生成し、Event Hub に送信します。 それを行うには、次のコマンドを使用します。

        java -cp com-microsoft-azure-eventhubs-client-example-0.2.0.jar com.microsoft.eventhubs.client.example.EventhubsClientDriver --eventhubs-namespace "mysbnamespace" --eventhubs-name "myeventhub" --policy-name "mysendpolicy" --policy-key "<policy key>" --message-length 32 --thread-count 32 --message-count -1

2. ストリーミング jar (**spark-streaming-data-persistence-examples.jar**) を、クラスターに関連付けられた Azure Blob Storage にコピーします。 これにより、jar で Livy にアクセスできるようになります。 コピーには、[**AzCopy**](../storage/common/storage-use-azcopy.md) コマンド ライン ユーティリティを使用できます。 データのアップロードに使用できるクライアントは、他にも多数あります。 詳細については、「 [HDInsight での Hadoop ジョブ用データのアップロード](hdinsight-upload-data.md)」を参照してください。
3. これらのアプリケーションを実行するコンピューターに CURL をインストールします。 CURL を使用して、ジョブをリモートで実行する Livy エンドポイントを呼び出します。

### <a name="run-the-spark-streaming-application-to-receive-the-events-into-an-azure-storage-blob-as-text"></a>Azure Storage Blob へのイベントをテキストとして受信する Spark ストリーミング アプリケーションを実行する

コマンド プロンプトを開き、CURL をインストールしたディレクトリに移動し、次のコマンドを実行します (ユーザー名/パスワードおよびクラスター名を置き換える)。

    curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\inputBlob.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

**inputBlob.txt** ファイル内のパラメーターは次のように定義されています。

    { "file":"wasb:///example/jars/spark-streaming-data-persistence-examples.jar", "className":"com.microsoft.spark.streaming.examples.workloads.EventhubsEventCount", "args":["--eventhubs-namespace", "mysbnamespace", "--eventhubs-name", "myeventhub", "--policy-name", "myreceivepolicy", "--policy-key", "<put-your-key-here>", "--consumer-group", "$default", "--partition-count", 10, "--batch-interval-in-seconds", 20, "--checkpoint-directory", "/EventCheckpoint", "--event-count-folder", "/EventCount/EventCount10"], "numExecutors":20, "executorMemory":"1G", "executorCores":1, "driverMemory":"2G" }

入力ファイル内のパラメーターについて説明します。

* **file** は、クラスターに関連付けられた Azure ストレージ アカウントにあるアプリケーション jar ファイルへのパスです。
* **className** は、jar 内のクラスの名前です。
* **args** は、クラスに必要な引数の一覧です。
* **numExecutors** は、ストリーミング アプリケーションを実行するときに Spark が使用するコアの数です。 これは常に Event Hub パーティションの数の少なくとも 2 倍になる必要があります。
* **executorMemory**、**executorCores**、**driverMemory** は、必要なリソースをストリーミング アプリケーションに割り当てるために使用するパラメーターです。

> [!NOTE]
> パラメーターとして使用される出力フォルダー (EventCheckpoint、EventCount/EventCount10) を作成する必要はありません。 ストリーミング アプリケーションによって自動的に作成されます。
>
>

コマンドを実行したとき、出力が次のようになることを確認します。

    < HTTP/1.1 201 Created
    < Content-Type: application/json; charset=UTF-8
    < Location: /18
    < Server: Microsoft-IIS/8.5
    < X-Powered-By: ARR/2.5
    < X-Powered-By: ASP.NET
    < Date: Tue, 01 Dec 2015 05:39:10 GMT
    < Content-Length: 37
    <
    {"id":1,"state":"starting","log":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact

出力の最後行にあるバッチ ID (この例では '1') をメモします。 アプリケーションが正常に実行していることを確認するには、クラスターに関連付けられた Azure ストレージ アカウントを調べて、**/EventCount/EventCount10** フォルダーが作成されていることを確かめます。 このフォルダーには、パラメーター **batch-interval-in-seconds** で指定された期間内に処理されたイベント数をキャプチャする BLOB が含まれている必要があります。

Spark ストリーミング アプリケーションは、強制終了するまで、実行を継続します。 強制終了するには、次のコマンドを使用します。

    curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/1"

### <a name="run-the-applications-to-receive-the-events-into-an-azure-storage-blob-as-json"></a>Azure Storage BLOB へのイベントを JSON として受信するアプリケーションを実行する
コマンド プロンプトを開き、CURL をインストールしたディレクトリに移動し、次のコマンドを実行します (ユーザー名/パスワードおよびクラスター名を置き換える)。

    curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\inputJSON.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

**inputJSON.txt** ファイル内のパラメーターは次のように定義されています。

    { "file":"wasb:///example/jars/spark-streaming-data-persistence-examples.jar", "className":"com.microsoft.spark.streaming.examples.workloads.EventhubsToAzureBlobAsJSON", "args":["--eventhubs-namespace", "mysbnamespace", "--eventhubs-name", "myeventhub", "--policy-name", "myreceivepolicy", "--policy-key", "<put-your-key-here>", "--consumer-group", "$default", "--partition-count", 10, "--batch-interval-in-seconds", 20, "--checkpoint-directory", "/EventCheckpoint", "--event-count-folder", "/EventCount/EventCount10", "--event-store-folder", "/EventStore10"], "numExecutors":20, "executorMemory":"1G", "executorCores":1, "driverMemory":"2G" }

パラメーターは、前の手順でテキスト出力用に指定したものと似ています。 繰り返しになりますが、パラメーターとして使用される出力フォルダー (EventCheckpoint、EventCount/EventCount10) を作成する必要はありません。 ストリーミング アプリケーションによって自動的に作成されます。

 コマンドを実行したら、クラスターに関連付けられた Azure ストレージ アカウントを調べて、**/EventCount/EventCount10** フォルダーが作成されていることを確かめます。 **part-** というプレフィックスの付いたファイルを開き、JSON 形式で処理されるイベントがあることを確認します。

### <a name="run-the-applications-to-receive-the-events-into-a-hive-table"></a>Hive テーブルへのイベントを受信するアプリケーションを実行する
Hive テーブルにイベントをストリーミングするSpark ストリーミング アプリケーションを実行するには、追加のコンポーネントがいくつか必要です。 次のとおりです。

* datanucleus-api-jdo-3.2.6.jar
* datanucleus-rdbms-3.2.9.jar
* datanucleus-core-3.2.10.jar
* hive-site.xml

**.jar** ファイルは、`/usr/hdp/current/spark-client/lib` にある HDInsight Spark クラスターで入手できます。 **Hive-site.xml** は、`/usr/hdp/current/spark-client/conf` で入手できます。

これらのファイルは、 [WinScp](http://winscp.net/eng/download.php) を使用して、クラスターからローカル コンピューターにコピーすることができます。 次に、これらのファイルはツールを使用して、クラスターに関連付けられているストレージ アカウントにコピーすることができます。 ストレージ アカウントにファイルをアップロードする方法の詳細については、「 [HDInsight での Hadoop ジョブ用データのアップロード](hdinsight-upload-data.md)」を参照してください。

ファイルが Azure ストレージ アカウントにコピーされたら、コマンド プロンプトを開き、CURL をインストールしたディレクトリに移動し、次のコマンドを実行します (ユーザー名/パスワードおよびクラスター名を置き換える)。

    curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\inputHive.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

**inputHive.txt** ファイル内のパラメーターは次のように定義されています。

    { "file":"wasb:///example/jars/spark-streaming-data-persistence-examples.jar", "className":"com.microsoft.spark.streaming.examples.workloads.EventhubsToHiveTable", "args":["--eventhubs-namespace", "mysbnamespace", "--eventhubs-name", "myeventhub", "--policy-name", "myreceivepolicy", "--policy-key", "<put-your-key-here>", "--consumer-group", "$default", "--partition-count", 10, "--batch-interval-in-seconds", 20, "--checkpoint-directory", "/EventCheckpoint", "--event-count-folder", "/EventCount/EventCount10", "--event-hive-table", "EventHiveTable10" ], "jars":["wasb:///example/jars/datanucleus-api-jdo-3.2.6.jar", "wasb:///example/jars/datanucleus-rdbms-3.2.9.jar", "wasb:///example/jars/datanucleus-core-3.2.10.jar"], "files":["wasb:///example/jars/hive-site.xml"], "numExecutors":20, "executorMemory":"1G", "executorCores":1, "driverMemory":"2G" }

パラメーターは、前の手順でテキスト出力用に指定したものと似ています。 繰り返しになりますが、パラメーターとして使用される出力フォルダー (EventCheckpoint、EventCount/EventCount10) または出力 Hive テーブル (EventHiveTable10) を作成する必要はありません。 ストリーミング アプリケーションによって自動的に作成されます。 **jars** および **files** オプションには、ストレージ アカウントにコピーした .jar ファイルおよび hive-site.xml へのパスが含まれています。

Hive テーブルが正常に作成されたことを確認するために、クラスターに SSH 接続して、Hive クエリを実行することができます。 手順については、「 [SSH による HDInsight での Hive と Hadoop の使用](hdinsight-hadoop-use-hive-ssh.md)」を参照してください。 SSH を使用して接続したら、次のコマンドを実行して、 **EventHiveTable10**という Hive テーブルが作成されていることを確認できます。

    show tables;

次のような出力が表示されます。

    OK
    eventhivetable10
    hivesampletable

SELECT クエリを実行して、テーブルの内容を表示することもできます。

    SELECT * FROM eventhivetable10 LIMIT 10;

出力は次のように表示されます。

    ZN90apUSQODDTx7n6Toh6jDbuPngqT4c
    sor2M7xsFwmaRW8W8NDwMneFNMrOVkW1
    o2HcsU735ejSi2bGEcbUSB4btCFmI1lW
    TLuibq4rbj0T9st9eEzIWJwNGtMWYoYS
    HKCpPlWFWAJILwR69MAq863nCWYzDEw6
    Mvx0GQOPYvPR7ezBEpIHYKTKiEhYammQ
    85dRppSBSbZgThLr1s0GMgKqynDUqudr
    5LAWkNqorLj3ZN9a2mfWr9rZqeXKN4pF
    ulf9wSFNjD7BZXCyunozecov9QpEIYmJ
    vWzM3nvOja8DhYcwn0n5eTfOItZ966pa
    Time taken: 4.434 seconds, Fetched: 10 row(s)


### <a name="run-the-applications-to-receive-the-events-into-an-azure-sql-database-table"></a>Azure SQL データベース テーブルへのイベントを受信するアプリケーションを実行する
この手順を実行する前に、Azure SQL データベースが作成されていることを確認してください。 手順については、[数分での SQL データベースの作成](../sql-database/sql-database-get-started.md)に関する記事を参照してください。 このセクションを完了するには、データベース名、データベース サーバー名、およびデータベース管理者の資格情報がパラメーターとして必要です。 ただし、データベース テーブルを作成する必要はありません。 Spark ストリーミング アプリケーションによって自動的に作成されます。

コマンド プロンプトを開き、CURL をインストールしたディレクトリに移動し、次のコマンドを実行します。

    curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\inputSQL.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

**inputSQL.txt** ファイル内のパラメーターは次のように定義されています。

    { "file":"wasb:///example/jars/spark-streaming-data-persistence-examples.jar", "className":"com.microsoft.spark.streaming.examples.workloads.EventhubsToAzureSQLTable", "args":["--eventhubs-namespace", "mysbnamespace", "--eventhubs-name", "myeventhub", "--policy-name", "myreceivepolicy", "--policy-key", "<put-your-key-here>", "--consumer-group", "$default", "--partition-count", 10, "--batch-interval-in-seconds", 20, "--checkpoint-directory", "/EventCheckpoint", "--event-count-folder", "/EventCount/EventCount10", "--sql-server-fqdn", "<database-server-name>.database.windows.net", "--sql-database-name", "mysparkdatabase", "--database-username", "sparkdbadmin", "--database-password", "<put-password-here>", "--event-sql-table", "EventContent" ], "numExecutors":20, "executorMemory":"1G", "executorCores":1, "driverMemory":"2G" }

アプリケーションが正常に実行していることを確認するには、SQL Server Management Studio を使用して Azure SQL データベースに接続します。 手順については、「 [Connect to SQL Database with SQL Server Management Studio (SQL Server Management Studio を使用して SQL Database に接続する)](../sql-database/sql-database-connect-query-ssms.md)」を参照してください。 データベースに接続したら、ストリーミング アプリケーションによって作成された **EventContent** テーブルに移動します。 簡単なクエリを実行してテーブルからデータを取得します。 次のクエリを実行します。

    SELECT * FROM EventCount

次のような出力が表示されます。

    00046b0f-2552-4980-9c3f-8bba5647c8ee
    000b7530-12f9-4081-8e19-90acd26f9c0c
    000bc521-9c1b-4a42-ab08-dc1893b83f3b
    00123a2a-e00d-496a-9104-108920955718
    0017c68f-7a4e-452d-97ad-5cb1fe5ba81b
    001KsmqL2gfu5ZcuQuTqTxQvVyGCqPp9
    001vIZgOStka4DXtud0e3tX7XbfMnZrN
    00220586-3e1a-4d2d-a89b-05c5892e541a
    0029e309-9e54-4e1b-84be-cd04e6fce5ec
    003333cf-874f-4045-9da3-9f98c2b4ea49
    0043c07e-8d73-420a-9af7-1fcb94575356
    004a11a9-0c2c-4bc0-a7d5-2e0ebd947ab9


## <a name="seealso"></a>関連項目
* [概要: Azure HDInsight での Apache Spark](hdinsight-apache-spark-overview.md)
* [受信側に基づく接続と Direct DStream の設計](https://www.slideshare.net/NanZhu/seattle-sparkmeetup032317)

### <a name="scenarios"></a>シナリオ
* [Spark と BI: HDInsight と BI ツールで Spark を使用した対話型データ分析の実行](hdinsight-apache-spark-use-bi-tools.md)
* [Spark と Machine Learning: HDInsight で Spark を使用して HVAC データを基に建物の温度を分析する](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Spark with Machine Learning: Use Spark in HDInsight to predict food inspection results (Spark と Machine Learning: HDInsight で Spark を使用して食品の検査結果を予測する)](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Website log analysis using Spark in HDInsight (HDInsight での Spark を使用した Web サイト ログ分析)](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>アプリケーションの作成と実行
* [Scala を使用してスタンドアロン アプリケーションを作成する](hdinsight-apache-spark-create-standalone-application.md)
* [Livy を使用して Spark クラスターでジョブをリモートで実行する](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>ツールと拡張機能
* [Use HDInsight Tools Plugin for IntelliJ IDEA to create and submit Spark Scala applicatons (Linux)](hdinsight-apache-spark-intellij-tool-plugin.md)
* [IntelliJ IDEA 用の HDInsight Tools プラグインを使用して Spark アプリケーションをリモートでデバッグする](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [HDInsight の Spark クラスターで Zeppelin Notebook を使用する](hdinsight-apache-spark-zeppelin-notebook.md)
* [HDInsight 用の Spark クラスターの Jupyter Notebook で使用可能なカーネル](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Jupyter Notebook で外部のパッケージを使用する](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Jupyter をコンピューターにインストールして HDInsight Spark クラスターに接続する](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>リソースの管理
* [Azure HDInsight での Apache Spark クラスターのリソースの管理](hdinsight-apache-spark-resource-manager.md)
* [HDInsight の Apache Spark クラスターで実行されるジョブの追跡とデバッグ](hdinsight-apache-spark-job-debugging.md)

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-create-storageaccount]: ../storage-create-storage-account/

