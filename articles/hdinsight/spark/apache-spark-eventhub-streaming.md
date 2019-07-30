---
title: 'チュートリアル:Azure HDInsight で Apache Spark を使用して Azure Event Hubs からのデータを処理する '
description: チュートリアル - Azure HDInsight で Apache Spark を Azure Event Hubs に接続し、ストリーミング データを処理します。
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive,mvc
ms.topic: tutorial
ms.date: 05/24/2019
ms.openlocfilehash: c8c99d976f416d0c1d07fb3a266d37ecd6235fdb
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/20/2019
ms.locfileid: "67295353"
---
# <a name="tutorial-process-tweets-using-azure-event-hubs-and-apache-spark-in-hdinsight"></a>チュートリアル:HDInsight で Azure Event Hubs と Apache Spark を使用してツイートを処理する

このチュートリアルでは、Azure イベント ハブにツイートを送信する [Apache Spark](https://spark.apache.org/) ストリーミング アプリケーションを作成し、イベント ハブからツイートを読み取る別のアプリケーションを作成する方法について説明します。 Spark ストリーミングの詳細については、[Apache Spark ストリーミングの概要](https://spark.apache.org/docs/latest/streaming-programming-guide.html#overview)を参照してください。 HDInsight は、Azure の Spark クラスターに同じストリーミング機能をもたらします。

このチュートリアルでは、以下の内容を学習します。
> [!div class="checklist"]
> * Azure イベント ハブにメッセージを送信する
> * Azure イベント ハブからメッセージを読み取る

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

* HDInsight での Apache Spark クラスター。 [Apache Spark クラスターの作成](./apache-spark-jupyter-spark-sql-use-portal.md)に関するページを参照してください。

* HDInsight の Spark での Jupyter Notebook の使用方法を熟知していること。 詳細については、[HDInsight の Apache Spark を使用したデータの読み込みとクエリの実行](./apache-spark-load-data-run-query.md)に関するページを参照してください。

* [Twitter アカウント](https://twitter.com/i/flow/signup)。

## <a name="create-a-twitter-application"></a>Twitter アプリケーションを作成する

ツイートのストリームを受け取るには、Twitter でアプリケーションを作成します。 手順に従って Twitter アプリケーションを作成し、このチュートリアルの完了に必要な値を書き留めます。

1. [Twitter Application Management](https://apps.twitter.com/) を参照します。

1. **[Create New App]** を選択します。

1. 次の値を指定します。

    |プロパティ |値 |
    |---|---|
    |Name|アプリケーション名を指定します。 このチュートリアルで使用されている値は **HDISparkStreamApp0423** です。 この名前は、一意の名前にする必要があります。|
    |説明|アプリケーションの簡単な説明を指定します。 このチュートリアルで使用されている値は **A simple HDInsight Spark streaming application** です。|
    |Web サイト|アプリケーションの Web サイトを指定します。 有効な Web サイトである必要はありません。  このチュートリアルで使用されている値は **http://www.contoso.com** です。|
    |コールバック URL|空白のままにできます。|

1. **[Yes, I have read and agree to the Twitter Developer Agreement]** を選択し、 **[Create your Twitter application]** を選択します。

1. **[Keys and Access Tokens]** タブをクリックします。

1. ページの最後の **[Create my access token]** を選択します。

1. ページから、次の値を書き留めます。  これらの値は、このチュートリアルの後の方で必要になります。

    - **コンシューマー キー (API キー)**    
    - **コンシューマー シークレット (API シークレット)**  
    - **Access Token**
    - **アクセス トークン シークレット**   

## <a name="create-an-azure-event-hubs-namespace"></a>Azure Event Hubs 名前空間を作成する

このイベント ハブを使用して、ツイートを格納します。

1. [Azure Portal](https://portal.azure.com) にサインインします。 

2. 左側のメニューから、 **[すべてのサービス]** を選択します。  

3. **[モノのインターネット]** で、 **[Event Hubs]** を選択します。 

    ![Spark ストリーミング サンプルの Event Hub を作成](./media/apache-spark-eventhub-streaming/hdinsight-create-event-hub-for-spark-streaming.png "Spark ストリーミング サンプルの Event Hub を作成")

4. **[+ 追加]** を選択します。

5. 新しい Event Hubs 名前空間の次の値を入力します。

    |プロパティ |値 |
    |---|---|
    |Name|イベント ハブの名前を入力します。  このチュートリアルで使用されている値は **myeventhubns20180403** です。|
    |価格レベル|**[Standard]** を選択します。|
    |サブスクリプション|適切なサブスクリプションを選択します。|
    |リソース グループ|ドロップダウン リストから既存のリソース グループを選択するか、または **[新規作成]** を選択して新しいリソース グループを作成します。|
    |Location|待機時間とコストを削減するには、HDInsight の Apache Spark クラスターと同じ **[場所]** を選択します。|
    |[Enable Auto-Inflate] (自動インフレを有効にする) (省略可能) |自動インフレは、トラフィックがそれに割り当てられているスループット ユニットの容量を超えると、Event Hubs 名前空間に割り当てられたスループット ユニットの数を自動的にスケール調整します。  |
    |[Auto-Inflate Maximum Throughput Units] (自動インフレの最大スループット ユニット数) (省略可能)|このスライダーは、 **[Enable Auto-Inflate] (自動インフレを有効にする)** をオンにした場合にのみ表示されます。  |

    ![Spark ストリーミング サンプルの Event Hub 名を指定](./media/apache-spark-eventhub-streaming/hdinsight-provide-event-hub-name-for-spark-streaming.png "Spark ストリーミング サンプルの Event Hub 名を指定")

6. **[作成]** をクリックして、名前空間を作成します。  デプロイは数分で完了します。

## <a name="create-an-azure-event-hub"></a>Azure イベント ハブを作成する
Event Hubs 名前空間がデプロイされたら、イベント ハブを作成します。  ポータルから:

1. 左側のメニューから、 **[すべてのサービス]** を選択します。  

1. **[モノのインターネット]** で、 **[Event Hubs]** を選択します。  

1. 一覧から Event Hubs 名前空間を選択します。  

1. **[Event Hubs 名前空間]** ページから、 **[+ イベント ハブ]** を選択します。  
1. **[Create Event Hub] (イベント ハブの作成)** ページで、次の値を入力します。

    - **[名前]** :イベント ハブの名前を指定します。 
 
    - **[パーティション数]** : 10.  

    - **[メッセージの保持期間]** : 1.   
   
      ![Spark ストリーミング サンプルの Event Hub 詳細を指定](./media/apache-spark-eventhub-streaming/hdinsight-provide-event-hub-details-for-spark-streaming-example.png "Spark ストリーミング サンプルの Event Hub 詳細を指定")

1. **作成** を選択します。  デプロイは数秒で完了し、[Event Hubs 名前空間] ページに戻されます。

1. **[設定]** で、 **[共有アクセス ポリシー]** を選択します。

1. **[RootManageSharedAccessKey]** を選択します。
    
     ![Spark ストリーミング サンプルの Event Hub ポリシーを設定](./media/apache-spark-eventhub-streaming/hdinsight-set-event-hub-policies-for-spark-streaming-example.png "Spark ストリーミング サンプルの Event Hub ポリシーを設定")

1. **[主キー]** と **[接続文字列 - プライマリ キー]** の値を、チュートリアルの後半で使用するために保存します。

     ![Spark ストリーミング例の Event Hub ポリシー キーを表示](./media/apache-spark-eventhub-streaming/hdinsight-view-event-hub-policy-keys.png "Spark ストリーミング例の Event Hub ポリシー キーを表示")


## <a name="send-tweets-to-the-event-hub"></a>ツイートをイベント ハブに送信する

Jupyter Notebook を作成し、それに **SendTweetsToEventHub** という名前を付けます。 

1. 次のコードを実行して、外部 Apache Maven ライブラリを追加します。

    ```
    %%configure
    {"conf":{"spark.jars.packages":"com.microsoft.azure:azure-eventhubs-spark_2.11:2.2.0,org.twitter4j:twitter4j-core:4.0.6"}}
    ```

2. 次のコードを編集して、`<Event hub name>`、`<Event hub namespace connection string>`、`<CONSUMER KEY>`、`<CONSUMER SECRET>`、`<ACCESS TOKEN>`、および `<TOKEN SECRET>` を適切な値に置き換えます。 編集されたコードを実行して、イベント ハブにツイートを送信します。

    ```scala
    import java.util._
    import scala.collection.JavaConverters._
    import java.util.concurrent._
    
    import org.apache.spark._
    import org.apache.spark.streaming._
    import org.apache.spark.eventhubs.ConnectionStringBuilder

    // Event hub configurations
    // Replace values below with yours        
    val eventHubName = "<Event hub name>"
    val eventHubNSConnStr = "<Event hub namespace connection string>"
    val connStr = ConnectionStringBuilder(eventHubNSConnStr).setEventHubName(eventHubName).build 
    
    import com.microsoft.azure.eventhubs._
    val pool = Executors.newFixedThreadPool(1)
    val eventHubClient = EventHubClient.create(connStr.toString(), pool)
    
    def sendEvent(message: String) = {
          val messageData = EventData.create(message.getBytes("UTF-8"))
          eventHubClient.get().send(messageData)
          println("Sent event: " + message + "\n")
    }
    
    import twitter4j._
    import twitter4j.TwitterFactory
    import twitter4j.Twitter
    import twitter4j.conf.ConfigurationBuilder

    // Twitter application configurations
    // Replace values below with yours   
    val twitterConsumerKey = "<CONSUMER KEY>"
    val twitterConsumerSecret = "<CONSUMER SECRET>"
    val twitterOauthAccessToken = "<ACCESS TOKEN>"
    val twitterOauthTokenSecret = "<TOKEN SECRET>"
    
    val cb = new ConfigurationBuilder()
    cb.setDebugEnabled(true).setOAuthConsumerKey(twitterConsumerKey).setOAuthConsumerSecret(twitterConsumerSecret).setOAuthAccessToken(twitterOauthAccessToken).setOAuthAccessTokenSecret(twitterOauthTokenSecret)
    
    val twitterFactory = new TwitterFactory(cb.build())
    val twitter = twitterFactory.getInstance()

    // Getting tweets with keyword "Azure" and sending them to the Event Hub in realtime!
    
    val query = new Query(" #Azure ")
    query.setCount(100)
    query.lang("en")
    var finished = false
    while (!finished) {
      val result = twitter.search(query)
      val statuses = result.getTweets()
      var lowestStatusId = Long.MaxValue
      for (status <- statuses.asScala) {
        if(!status.isRetweet()){
          sendEvent(status.getText())
        }
        lowestStatusId = Math.min(status.getId(), lowestStatusId)
        Thread.sleep(2000)
      }
      query.setMaxId(lowestStatusId - 1)
    }
    
    // Closing connection to the Event Hub
    eventHubClient.get().close()
    ```

3. Azure Portal で、イベント ハブを開きます。  **[概要]** で、イベント ハブに送信されたメッセージを示すいくつかのグラフが表示されます。

## <a name="read-tweets-from-the-event-hub"></a>イベント ハブからツイートを読み取る

別の Jupyter Notebook を作成し、それに **ReadTweetsFromEventHub** という名前を付けます。 

1. 次のコードを実行して、外部 Apache Maven ライブラリを追加します。

    ```
    %%configure -f
    {"conf":{"spark.jars.packages":"com.microsoft.azure:azure-eventhubs-spark_2.11:2.2.0"}}
    ```

2. 次のコードを編集して、`<Event hub name>` と `<Event hub namespace connection string>` を適切な値に置き換えます。 編集されたコードを実行して、イベント ハブからツイートを読み取ります。

    ```scala
    import org.apache.spark.eventhubs._
    // Event hub configurations
    // Replace values below with yours        
    val eventHubName = "<Event hub name>"
    val eventHubNSConnStr = "<Event hub namespace connection string>"
    val connStr = ConnectionStringBuilder(eventHubNSConnStr).setEventHubName(eventHubName).build
    
    val customEventhubParameters = EventHubsConf(connStr).setMaxEventsPerTrigger(5)
    val incomingStream = spark.readStream.format("eventhubs").options(customEventhubParameters.toMap).load()
    //incomingStream.printSchema    
    
    import org.apache.spark.sql.types._
    import org.apache.spark.sql.functions._
    
    // Event Hub message format is JSON and contains "body" field
    // Body is binary, so you cast it to string to see the actual content of the message
    val messages = incomingStream.withColumn("Offset", $"offset".cast(LongType)).withColumn("Time (readable)", $"enqueuedTime".cast(TimestampType)).withColumn("Timestamp", $"enqueuedTime".cast(LongType)).withColumn("Body", $"body".cast(StringType)).select("Offset", "Time (readable)", "Timestamp", "Body")
    
    messages.printSchema
    
    messages.writeStream.outputMode("append").format("console").option("truncate", false).start().awaitTermination()
    ```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

HDInsight では、データが Azure Storage または Azure Data Lake Storage に格納されるため、クラスターは使用されていないときに安全に削除できます。 また、HDInsight クラスターは、使用していない場合でも課金されます。 すぐに次のチュートリアルを開始する場合は、クラスターをそのままにすることもできます。それ以外の場合は、処理を続行してクラスターを削除します。

Azure Portal で、クラスターを開き、 **[削除]** を選択します。

![HDInsight クラスターの削除](./media/apache-spark-load-data-run-query/hdinsight-azure-portal-delete-cluster.png "HDInsight クラスターの削除")

リソース グループ名を選択し、リソース グループ ページを開いて、 **[リソース グループの削除]** を選択することもできます。 リソース グループを削除すると、HDInsight Spark クラスターと既定のストレージ アカウントの両方が削除されます。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure イベント ハブにツイートを送信する Apache Spark ストリーミング アプリケーションを作成し、イベント ハブからツイートを読み取る別のアプリケーションを作成する方法について説明しました。  次の記事に進み、Machine Learning アプリケーションを作成できることを確認します。

> [!div class="nextstepaction"]
> [Machine Learning アプリケーションの作成](./apache-spark-ipython-notebook-machine-learning.md)
