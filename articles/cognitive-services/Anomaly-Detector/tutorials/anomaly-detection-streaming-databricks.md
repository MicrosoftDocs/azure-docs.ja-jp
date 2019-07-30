---
title: チュートリアル:Azure Databricks を使用した、ストリーミング データの異常検出
titleSuffix: Azure Cognitive Services
description: Anomaly Detector API と Azure Databricks を使用して、データ内の異常を監視します。
titlesuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: tutorial
ms.date: 05/08/2019
ms.author: aahi
ms.openlocfilehash: 8d3f5d0e10fadd31fd8bde77339b872c1b90451f
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2019
ms.locfileid: "67721474"
---
# <a name="tutorial-anomaly-detection-on-streaming-data-using-azure-databricks"></a>チュートリアル:Azure Databricks を使用した、ストリーミング データの異常検出

[Azure Databricks](https://azure.microsoft.com/services/databricks/) は、高速で使いやすい、コラボレーション対応の Apache Spark ベースの分析サービスです。 Azure Cognitive Services の一部である Anomaly Detector API は、時系列データを監視するための方法を提供します。 このチュートリアルを使用して、Azure Databricks を使用したほぼリアルタイムでのデータ ストリームの異常検出を実行します。 Azure Event Hubs を使用して twitter データを取り込み、Spark Event Hubs コネクタを使用して Azure Databricks にインポートします。 その後、この API を使用して、ストリーミングされたデータの異常を検出します。 

次の図に、アプリケーション フローを示します。

![Event Hubs と Cognitive Services を使用した Azure Databricks](../media/tutorials/databricks-cognitive-services-tutorial.png "Event Hubs と Cognitive Services を使用した Azure Databricks")

このチュートリアルに含まれるタスクは次のとおりです。

> [!div class="checklist"]
> * Azure Databricks ワークスペースを作成する
> * Azure Databricks で Spark クラスターを作成する
> * ストリーミング データにアクセスする Twitter アプリを作成する
> * Azure Databricks でノートブックを作成する
> * Event Hubs と Twitter API のライブラリをアタッチする
> * Anomaly Detector リソースを作成してアクセス キーを取得する
> * ツイートを Event Hubs に送信する
> * Event Hubs からツイートを読み取る
> * ツイートの異常検出を実行する

> [!Note]
> このチュートリアルでは、Anomaly Detector API について推奨される[ソリューション アーキテクチャ](https://azure.microsoft.com/solutions/architecture/anomaly-detector-process/)を実装するための方法を紹介します。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

> [!Note]
> このチュートリアルは、Anomaly Detector API の無料試用版のキーで実行できません。 無料アカウントを使用して Azure Databricks クラスターを作成するには、クラスターを作成する前に、プロファイルにアクセスし、サブスクリプションを**従量課金制**に変更します。 詳細については、[Azure 無料アカウント](https://azure.microsoft.com/free/)に関するページをご覧ください。

## <a name="prerequisites"></a>前提条件

- [Azure Event Hubs 名前空間](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)とイベント ハブ。

- Event Hubs 名前空間にアクセスするための[接続文字列](../../../event-hubs/event-hubs-get-connection-string.md)。 接続文字列は次のような形式になります。

    `Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=<key name>;SharedAccessKey=<key value>` 

- Event Hubs の共有アクセス ポリシー名とポリシー キー。

名前空間とイベント ハブの作成については、Azure Event Hubs の[クイック スタート](../../../event-hubs/event-hubs-create.md)を参照してください。

## <a name="create-an-azure-databricks-workspace"></a>Azure Databricks ワークスペースを作成する

このセクションでは、[Azure portal](https://portal.azure.com/) を使用して Azure Databricks ワークスペースを作成します。

1. Azure portal で、 **[リソースの作成]**  >  **[分析]**  >  **[Azure Databricks]** の順に選択します。

    ![Azure Portal の Databricks](../media/tutorials/azure-databricks-on-portal.png "Azure Portal の Databricks")

3. **[Azure Databricks サービス]** で次の値を指定して、Databricks ワークスペースを作成します。


    |プロパティ  |説明  |
    |---------|---------|
    |**ワークスペース名**     | Databricks ワークスペースの名前を指定します        |
    |**サブスクリプション**     | ドロップダウンから Azure サブスクリプションを選択します。        |
    |**リソース グループ**     | 新しいリソース グループを作成するか、既存のリソース グループを使用するかを指定します。 リソース グループは、Azure ソリューションの関連するリソースを保持するコンテナーです。 詳しくは、[Azure リソース グループの概要](../../../azure-resource-manager/resource-group-overview.md)に関するページをご覧ください。 |
    |**Location**     | **米国東部 2**またはその他の利用可能なリージョンのいずれかを選択します。 使用可能なリージョンについては、「[リージョン別の利用可能な製品](https://azure.microsoft.com/regions/services/)」をご覧ください。        |
    |**価格レベル**     |  **Standard** と **Premium** のいずれかを選択します。 **[Trial]\(試用版\)** を選択しないでください。 これらのレベルの詳細については、[Databricks の価格に関するページ](https://azure.microsoft.com/pricing/details/databricks/)を参照してください。       |

    **作成** を選択します。

4. ワークスペースの作成には数分かかります。 

## <a name="create-a-spark-cluster-in-databricks"></a>Databricks に Spark クラスターを作成する

1. Azure Portal で、作成した Databricks ワークスペースに移動して、 **[Launch Workspace]\(ワークスペースの起動\)** を選択します。

2. Azure Databricks ポータルにリダイレクトされます。 ポータルで **[New Cluster]\(新しいクラスター\)** を選択します。

    ![Azure の Databricks](../media/tutorials/databricks-on-azure.png "Azure の Databricks")

3. **[New Cluster]\(新しいクラスター\)** ページで、クラスターを作成するための値を指定します。

    ![Azure で Databricks Spark クラスターを作成する](../media/tutorials/create-databricks-spark-cluster.png "Azure で Databricks Spark クラスターを作成する")

    以下を除くすべての値は、既定値のままにします。

   * クラスターの名前を入力します。
   * この記事では、**5.2** ランタイムを使用してクラスターを作成します。 **5.3** ランタイムを選択しないでください。
   * **[Terminate after \_\_ minutes of inactivity]** \(アクティビティが \_\_ 分ない場合は終了する\) チェック ボックスをオンにしてください。 クラスターが使用されていない場合は、クラスターを終了するまでの時間 (分単位) を指定します。

     **[クラスターの作成]** を選択します。 
4. クラスターの作成には数分かかります。 クラスターが実行されたら、ノートブックをクラスターにアタッチして、Spark ジョブを実行できます。

## <a name="create-a-twitter-application"></a>Twitter アプリケーションを作成する

ツイートのストリームを受け取るには、Twitter でアプリケーションを作成する必要があります。 手順に従って Twitter アプリケーションを作成し、このチュートリアルの完了に必要な値を記録してください。

1. Web ブラウザーで [Twitter アプリケーション管理](https://apps.twitter.com/)に移動して、 **[Create New App]\(新しいアプリの作成\)** を選択します。

    ![Twitter アプリケーションを作成する](../media/tutorials/databricks-create-twitter-app.png "Twitter アプリケーションを作成する")

2. **[Create an application]\(アプリケーションの作成\)** ページで新しいアプリの詳細を入力し、 **[Create your Twitter application]\(Twitter アプリケーションの作成\)** を選択します。

    ![Twitter アプリケーションの詳細](../media/tutorials/databricks-provide-twitter-app-details.png "Twitter アプリケーションの詳細")

3. アプリケーションのページで **[Keys and Access Tokens]\(キーとアクセス トークン\)** タブを選択し、 **[Consumer Key]\(コンシューマー キー\)** と **[Consumer Secret]\(コンシューマー シークレット\)** の値をコピーします。 さらに、 **[Create my access token]\(アクセス トークンの作成\)** を選択してアクセス トークンを生成します。 **[Access Token]\(アクセス トークン\)** と **[Access Token Secret]\(アクセス トークン シークレット\)** の値をコピーします。

    ![Twitter アプリケーションの詳細](../media/tutorials/twitter-app-key-secret.png "Twitter アプリケーションの詳細")

Twitter アプリケーションについて取得した値を保存します。 値は、このチュートリアルの後の方で必要になります。

## <a name="attach-libraries-to-spark-cluster"></a>ライブラリを Spark クラスターにアタッチする

このチュートリアルでは、Twitter API を使用してツイートを Event Hubs に送信します。 さらに、[Apache Spark Event Hubs コネクタ](https://github.com/Azure/azure-event-hubs-spark)を使用して、Azure Event Hubs に対するデータの読み取りと書き込みを行います。 これらの API をクラスターの一部として使用するには、それらをライブラリとして Azure Databricks に追加し、Spark クラスターに関連付けます。 次の手順では、ワークスペースの **[共有]** フォルダーにライブラリを追加する方法について説明します。

1. Azure Databricks ワークスペースで **[ワークスペース]** を選択し、 **[共有]** を右クリックします。 コンテキスト メニューで **[作成]**  >  **[ライブラリ]** の順に選択します。

   ![[ライブラリの追加] ダイアログ ボックス](../media/tutorials/databricks-add-library-option.png "[ライブラリの追加] ダイアログ ボックス")

2. [新しいライブラリ] ページの **[ソース]** で、 **[Maven]** を選択します。 **[座標]** には、追加したいパッケージの座標を入力します。 ここでは、このチュートリアルで使用するライブラリの Maven 座標です。

   * Spark Event Hubs コネクタ - `com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.10`
   * Twitter API - `org.twitter4j:twitter4j-core:4.0.7`

     ![Maven 座標を入力する](../media/tutorials/databricks-eventhub-specify-maven-coordinate.png "Maven 座標を入力する")

3. **作成** を選択します。

4. ライブラリを追加したフォルダーを選択し、ライブラリ名を選択します。

    ![追加するライブラリを選択する](../media/tutorials/select-library.png "追加するライブラリを選択する")

5. ライブラリのページにクラスターがない場合は、 **[クラスター]** を選択し、作成したクラスターを実行します。 状態に [実行中] と表示されるまで待機し、ライブラリのページに戻ります。
ライブラリのページで、ライブラリを使用するクラスターを選択し、 **[インストール]** を選択します。 ライブラリとクラスターの関連付けに成功したら、状態はすぐに **[インストール済み]** に変わります。

    ![ライブラリをクラスターにインストールする](../media/tutorials/databricks-library-attached.png "ライブラリをクラスターにインストールする")

6. Twitter パッケージ `twitter4j-core:4.0.7` について、これらの手順を繰り返します。

## <a name="get-a-cognitive-services-access-key"></a>Cognitive Services のアクセス キーを取得する

このチュートリアルでは、[Azure Cognitive Services Anomaly Detector API](../overview.md) を使用して、ツイートのストリームに対して、ほぼリアルタイムで異常検出を実行します。 API を使用する前に、Azure で Anomaly Detector リソースを作成し、Anomaly Detector API を使用してアクセス キーを取得する必要があります。

1. [Azure Portal](https://portal.azure.com/) にサインインします。

2. **[+ リソースの作成]** を選択します。

3. Azure Marketplace で、 **[AI + Machine Learning]\(AI + 機械学習\)**  >  **[See all]\(すべて表示\)**  >  **[Cognitive Services - More]\(Cognitive Services - 詳細\)**  >  **[Anomaly Detector]** を選択します。 または、[このリンク](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector)を使用して、 **[Create]\(作成\)** ダイアログ ボックスに直接アクセスすることもできます。

    ![Anomaly Detector リソースの作成](../media/tutorials/databricks-cognitive-services-anomaly-detector.png "Anomaly Detector リソースの作成")

4. **[作成]** ダイアログ ボックスで、次の値を入力します。

    |値 |説明  |
    |---------|---------|
    |EnableAdfsAuthentication     | Anomaly Detector リソースの名前。        |
    |Subscription     | リソースが関連付けられる Azure サブスクリプション。        |
    |Location     | Azure の場所。        |
    |価格レベル     | サービスの価格レベル。 Anomaly Detector の価格の詳細については、[料金ページ](https://azure.microsoft.com/pricing/details/cognitive-services/anomaly-detector/)を参照してください。        |
    |Resource group     | 新しいリソース グループを作成するか、既存のリソース グループを選択するかを指定します。        |


     **作成** を選択します。

5. リソースを作成した後、スクリーン ショットに示すように、 **[概要]** タブで、 **[エンドポイント]** URL をコピーして保存します。 次に、 **[アクセス キーを表示]** を選択します。

    ![アクセス キーを表示する](../media/tutorials/cognitive-services-get-access-keys.png "アクセス キーを表示する")

6. **[キー]** で、使用したいキーのコピー アイコンを選択します。 アクセス キーを保存します。

    ![アクセス キーをコピーする](../media/tutorials/cognitive-services-copy-access-keys.png "アクセス キーをコピーする")

## <a name="create-notebooks-in-databricks"></a>Databricks でノートブックを作成する

このセクションでは、次の名前を使用して、Databricks ワークスペース内に 2 つのノートブックを作成します

- **SendTweetsToEventHub** - Twitter からツイートを取得し、Event Hubs にそれらをストリーム配信するために使用されるプロデューサー ノートブック。
- **AnalyzeTweetsFromEventHub** - Event Hubs からのツイートの読み取りと異常検出析の実行に使用されるコンシューマー ノートブック。

1. Azure Databricks ワークスペースで、左側ウィンドウの **[ワークスペース]** を選択します。 **[ワークスペース]** ドロップダウンの **[作成]** を選択して、 **[ノートブック]** を選択します。

    ![Databricks でノートブックを作成する](../media/tutorials/databricks-create-notebook.png "Databricks でノートブックを作成する")

2. **[Create Notebook]\(ノートブックの作成\)** ダイアログ ボックスに、名前として「**SendTweetsToEventHub**」と入力し、言語として **[Scala]** を選択し、前に作成した Spark クラスターを選択します。

    ![Databricks でノートブックを作成する](../media/tutorials/databricks-notebook-details.png "Databricks でノートブックを作成する")

    **作成** を選択します。

3. 手順を繰り返して **AnalyzeTweetsFromEventHub** ノートブックを作成します。

## <a name="send-tweets-to-event-hubs"></a>ツイートを Event Hubs に送信する

**SendTweetsToEventHub** ノートブックで次のコードを貼り付けて、プレースホルダーを、先ほど作成した Event Hubs 名前空間と Twitter アプリケーションの値に置き換えます。 このノートブックは、キーワード "Azure" を含むツイートからの作成時刻と "いいね!" の数を抽出し、これらをイベントとしてリアルタイムで Event Hubs にストリーミングします。

```scala
//
// Send Data to Eventhub
//

import scala.collection.JavaConverters._
import com.microsoft.azure.eventhubs._
import java.util.concurrent._
import com.google.gson.{Gson, GsonBuilder, JsonParser}
import java.util.Date
import scala.util.control._
import twitter4j._
import twitter4j.TwitterFactory
import twitter4j.Twitter
import twitter4j.conf.ConfigurationBuilder

// Event Hub Config
val namespaceName = "[Placeholder: EventHub namespace]"
val eventHubName = "[Placeholder: EventHub name]"
val sasKeyName = "[Placeholder: EventHub access key name]"
val sasKey = "[Placeholder: EventHub access key key]"
val connStr = new ConnectionStringBuilder()
  .setNamespaceName(namespaceName)
  .setEventHubName(eventHubName)
  .setSasKeyName(sasKeyName)
  .setSasKey(sasKey)

// Connect to the Event Hub
val pool = Executors.newScheduledThreadPool(1)
val eventHubClient = EventHubClient.create(connStr.toString(), pool)

def sendEvent(message: String) = {
  val messageData = EventData.create(message.getBytes("UTF-8"))
  eventHubClient.get().send(messageData)
  System.out.println("Sent event: " + message + "\n")
}

case class MessageBody(var timestamp: Date, var favorite: Int)
val gson: Gson = new GsonBuilder().setDateFormat("yyyy-MM-dd'T'HH:mm:ss.SSS'Z'").create()

val twitterConsumerKey = "[Placeholder: Twitter consumer key]"
val twitterConsumerSecret = "[Placeholder: Twitter consumer seceret]"
val twitterOauthAccessToken = "[Placeholder: Twitter oauth access token]"
val twitterOauthTokenSecret = "[Placeholder: Twitter oauth token secret]"

val cb = new ConfigurationBuilder()
cb.setDebugEnabled(true)
  .setOAuthConsumerKey(twitterConsumerKey)
  .setOAuthConsumerSecret(twitterConsumerSecret)
  .setOAuthAccessToken(twitterOauthAccessToken)
  .setOAuthAccessTokenSecret(twitterOauthTokenSecret)

val twitterFactory = new TwitterFactory(cb.build())
val twitter = twitterFactory.getInstance()

// Getting tweets with keyword "Azure" and sending them to the Event Hub in realtime!

val query = new Query(" #Azure ")
query.setCount(100)
query.lang("en")

var finished = false
var maxStatusId = Long.MinValue
var preMaxStatusId = Long.MinValue
val innerLoop = new Breaks
while (!finished) {
  val result = twitter.search(query)
  val statuses = result.getTweets()
  var lowestStatusId = Long.MaxValue
  innerLoop.breakable {
    for (status <- statuses.asScala) {
      if (status.getId() <= preMaxStatusId) {
        preMaxStatusId = maxStatusId
        innerLoop.break
      }
      if(!status.isRetweet()) {
        sendEvent(gson.toJson(new MessageBody(status.getCreatedAt(), status.getFavoriteCount())))
      }
      lowestStatusId = Math.min(status.getId(), lowestStatusId)
      maxStatusId = Math.max(status.getId(), maxStatusId)
    }
  }
  
  if (lowestStatusId == Long.MaxValue) {
    preMaxStatusId = maxStatusId
  }
  Thread.sleep(10000)
  query.setMaxId(lowestStatusId - 1)
}

// Close connection to the Event Hub
eventHubClient.get().close()
pool.shutdown()
```

ノートブックを実行するには、**Shift + Enter** キーを押します。 次のスニペットに示されているような出力が表示されます。 出力内の各イベントは、Event Hubs に取り込まれたタイムスタンプと "いいね!" の数の組み合わせです。

    Sent event: {"timestamp":"2019-04-24T09:39:40.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:38:48.000Z","favorite":1}

    Sent event: {"timestamp":"2019-04-24T09:38:36.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:37:27.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:37:00.000Z","favorite":2}

    Sent event: {"timestamp":"2019-04-24T09:31:11.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:30:15.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:30:02.000Z","favorite":1}

    ...
    ...

## <a name="read-tweets-from-event-hubs"></a>Event Hubs からツイートを読み取る

**AnalyzeTweetsFromEventHub** ノートブックで次のコードを貼り付けて、プレースホルダーを、先ほど作成した Anomaly Detector リソースの値に置き換えます。 このノートブックによって、先ほど **SendTweetsToEventHub** ノートブックを使用して Event Hubs にストリーム配信したツイートが読み取られます。

最初に、Anomaly Detector を呼び出すクライアントを作成します。 
```scala

//
// Anomaly Detection Client
//

import java.io.{BufferedReader, DataOutputStream, InputStreamReader}
import java.net.URL
import java.sql.Timestamp

import com.google.gson.{Gson, GsonBuilder, JsonParser}
import javax.net.ssl.HttpsURLConnection

case class Point(var timestamp: Timestamp, var value: Double)
case class Series(var series: Array[Point], var maxAnomalyRatio: Double, var sensitivity: Int, var granularity: String)
case class AnomalySingleResponse(var isAnomaly: Boolean, var isPositiveAnomaly: Boolean, var isNegativeAnomaly: Boolean, var period: Int, var expectedValue: Double, var upperMargin: Double, var lowerMargin: Double, var suggestedWindow: Int)
case class AnomalyBatchResponse(var expectedValues: Array[Double], var upperMargins: Array[Double], var lowerMargins: Array[Double], var isAnomaly: Array[Boolean], var isPositiveAnomaly: Array[Boolean], var isNegativeAnomaly: Array[Boolean], var period: Int)

object AnomalyDetector extends Serializable {

  // Cognitive Services API connection settings
  val subscriptionKey = "[Placeholder: Your Anomaly Detector resource access key]"
  val endpoint = "[Placeholder: Your Anomaly Detector resource endpoint]"
  val latestPointDetectionPath = "/anomalydetector/v1.0/timeseries/last/detect"
  val batchDetectionPath = "/anomalydetector/v1.0/timeseries/entire/detect";
  val latestPointDetectionUrl = new URL(endpoint + latestPointDetectionPath)
  val batchDetectionUrl = new URL(endpoint + batchDetectionPath)
  val gson: Gson = new GsonBuilder().setDateFormat("yyyy-MM-dd'T'HH:mm:ss.SSS'Z'").setPrettyPrinting().create()

  def getConnection(path: URL): HttpsURLConnection = {
    val connection = path.openConnection().asInstanceOf[HttpsURLConnection]
    connection.setRequestMethod("POST")
    connection.setRequestProperty("Content-Type", "text/json")
    connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey)
    connection.setDoOutput(true)
    return connection
  }

  // Handles the call to Cognitive Services API.
  def processUsingApi(request: String, path: URL): String = {
    println(request)
    val encoded_text = request.getBytes("UTF-8")
    val connection = getConnection(path)
    val wr = new DataOutputStream(connection.getOutputStream())
    wr.write(encoded_text, 0, encoded_text.length)
    wr.flush()
    wr.close()

    val response = new StringBuilder()
    val in = new BufferedReader(new InputStreamReader(connection.getInputStream()))
    var line = in.readLine()
    while (line != null) {
      response.append(line)
      line = in.readLine()
    }
    in.close()
    return response.toString()
  }

  // Calls the Latest Point Detection API.
  def detectLatestPoint(series: Series): Option[AnomalySingleResponse] = {
    try {
      println("Process Timestamp: " + series.series.apply(series.series.length-1).timestamp.toString + ", size: " + series.series.length)
      val response = processUsingApi(gson.toJson(series), latestPointDetectionUrl)
      println(response)
      // Deserializing the JSON response from the API into Scala types
      val anomaly = gson.fromJson(response, classOf[AnomalySingleResponse])
      Thread.sleep(5000)
      return Some(anomaly)
    } catch {
      case e: Exception => {
        println(e)
        e.printStackTrace()
        return None
      }
    }
  }

  // Calls the Batch Detection API.
  def detectBatch(series: Series): Option[AnomalyBatchResponse] = {
    try {
      val response = processUsingApi(gson.toJson(series), batchDetectionUrl)
      println(response)
      // Deserializing the JSON response from the API into Scala types
      val anomaly = gson.fromJson(response, classOf[AnomalyBatchResponse])
      Thread.sleep(5000)
      return Some(anomaly)
    } catch {
      case e: Exception => {
        println(e)
        return None
      }
    }
  }
}
```

ノートブックを実行するには、**Shift + Enter** キーを押します。 次のスニペットに示されているような出力が表示されます。

    import java.io.{BufferedReader, DataOutputStream, InputStreamReader}
    import java.net.URL
    import java.sql.Timestamp
    import com.google.gson.{Gson, GsonBuilder, JsonParser}
    import javax.net.ssl.HttpsURLConnection
    defined class Point
    defined class Series
    defined class AnomalySingleResponse
    defined class AnomalyBatchResponse
    defined object AnomalyDetector

次に、将来使用するための集計関数を作成します。
```scala
//
// User Defined Aggregation Function for Anomaly Detection
//

import org.apache.spark.sql.Row
import org.apache.spark.sql.expressions.{MutableAggregationBuffer, UserDefinedAggregateFunction}
import org.apache.spark.sql.types.{StructType, TimestampType, FloatType, MapType, BooleanType, DataType}
import scala.collection.immutable.ListMap

class AnomalyDetectorAggregationFunction extends UserDefinedAggregateFunction {
  override def inputSchema: StructType = new StructType().add("timestamp", TimestampType).add("value", FloatType)
  
  override def bufferSchema: StructType = new StructType().add("point", MapType(TimestampType, FloatType))
  
  override def dataType: DataType = BooleanType
  
  override def deterministic: Boolean = false
  
  override def initialize(buffer: MutableAggregationBuffer): Unit = {
    buffer(0) = Map()
  }
  
  override def update(buffer: MutableAggregationBuffer, input: Row): Unit = {
    buffer(0) = buffer.getAs[Map[java.sql.Timestamp, Float]](0) + (input.getTimestamp(0) -> input.getFloat(1))
  }
  
  override def merge(buffer1: MutableAggregationBuffer, buffer2: Row): Unit = {
    buffer1(0) = buffer1.getAs[Map[java.sql.Timestamp, Float]](0) ++ buffer2.getAs[Map[java.sql.Timestamp, Float]](0)
  }
  
  override def evaluate(buffer: Row): Any = {
    val points = buffer.getAs[Map[java.sql.Timestamp, Float]](0)
    if (points.size > 12) {
      val sorted_points = ListMap(points.toSeq.sortBy(_._1.getTime):_*)
      var detect_points: List[Point] = List()
      sorted_points.keys.foreach {
        key => detect_points = detect_points :+ new Point(key, sorted_points(key))
      }
      
      
      // 0.25 is maxAnomalyRatio. It represents 25%, max anomaly ratio in a time series.
      // 95 is the sensitivity of the algorithms.
      // Check Anomaly detector API reference (https://aka.ms/anomaly-detector-rest-api-ref)
      
      val series: Series = new Series(detect_points.toArray, 0.25, 95, "hourly")
      val response: Option[AnomalySingleResponse] = AnomalyDetector.detectLatestPoint(series)
      if (!response.isEmpty) {
        return response.get.isAnomaly
      }
    }
    
    return None
  }
}

```

ノートブックを実行するには、**Shift + Enter** キーを押します。 次のスニペットに示されているような出力が表示されます。

    import org.apache.spark.sql.Row
    import org.apache.spark.sql.expressions.{MutableAggregationBuffer, UserDefinedAggregateFunction}
    import org.apache.spark.sql.types.{StructType, TimestampType, FloatType, MapType, BooleanType, DataType}
    import scala.collection.immutable.ListMap
    defined class AnomalyDetectorAggregationFunction

次に、異常検出についてのデータをイベント ハブから読み込みます。 プレースホルダーを、先ほど作成した Azure Event Hubs の値に置き換えます。

```scala
//
// Load Data from Eventhub
//

import org.apache.spark.eventhubs._
import org.apache.spark.sql.types._
import org.apache.spark.sql.functions._

val connectionString = ConnectionStringBuilder("[Placeholder: EventHub namespace connection string]")
  .setEventHubName("[Placeholder: EventHub name]")
  .build

val customEventhubParameters =
  EventHubsConf(connectionString)
  .setConsumerGroup("$Default")
  .setMaxEventsPerTrigger(100)

val incomingStream = spark.readStream.format("eventhubs").options(customEventhubParameters.toMap).load()

val messages =
  incomingStream
  .withColumn("enqueuedTime", $"enqueuedTime".cast(TimestampType))
  .withColumn("body", $"body".cast(StringType))
  .select("enqueuedTime", "body")

val bodySchema = new StructType().add("timestamp", TimestampType).add("favorite", IntegerType)

val msgStream = messages.select(from_json('body, bodySchema) as 'fields).select("fields.*")

msgStream.printSchema

display(msgStream)

```

出力結果は、以下のイメージのようになります。 データはリアルタイムであるため、表の日付はこのチュートリアルの日付と異なる場合があることに注意してください。
![イベント ハブからのデータの読み込み](../media/tutorials/load-data-from-eventhub.png "イベント ハブからのデータの読み込み")

これで、Apache Spark 用の Event Hubs コネクタを使用して、Azure Event Hubs から Azure Databricks にデータをほぼリアルタイムでストリーム配信できました。 Spark 用の Event Hubs コネクタを使用する方法の詳細については、[コネクタに関するドキュメント](https://github.com/Azure/azure-event-hubs-spark/tree/master/docs)を参照してください。



## <a name="run-anomaly-detection-on-tweets"></a>ツイートの異常検出を実行する

このセクションでは、Anomaly Detector API を使用して、受信したツイートに対して異常検出を実行します。 このセクションでは同じ **AnalyzeTweetsFromEventHub** ノートブックにコード スニペットを追加します。

異常検出を実行するには、最初にメトリック カウントを 1 時間分集計する必要があります。
```scala
//
// Aggregate Metric Count by Hour
//

// If you want to change granularity, change the groupBy window. 
val groupStream = msgStream.groupBy(window($"timestamp", "1 hour"))
  .agg(avg("favorite").alias("average"))
  .withColumn("groupTime", $"window.start")
  .select("groupTime", "average")

groupStream.printSchema

display(groupStream)
```
これで、次のようなスニペットが出力されます。
```
groupTime                       average
2019-04-23T04:00:00.000+0000    24
2019-04-26T19:00:00.000+0000    47.888888888888886
2019-04-25T12:00:00.000+0000    32.25
2019-04-26T09:00:00.000+0000    63.4
...
...

```

次に、集計された出力結果を Delta に取得します。 異常検出には長期間の履歴が必要であるため、検出するポイントに対する履歴データを保持するために Delta を使用します。 "[Placeholder: table name]" を、作成される修飾された Delta テーブル名に置き換えます ("tweets" など)。 "[Placeholder: folder name for checkpoints]" を、このコードを実行するたびに固有となる文字列値に置き換えます ("etl-from-eventhub-20190605" など)。
Azure Databricks の Delta Lake に関する詳細については、[Delta Lake ガイド](https://docs.azuredatabricks.net/delta/index.html)を参照してください


```scala
//
// Output Aggregation Result to Delta
//

groupStream.writeStream
  .format("delta")
  .outputMode("complete")
  .option("checkpointLocation", "/delta/[Placeholder: table name]/_checkpoints/[Placeholder: folder name for checkpoints]")
  .table("[Placeholder: table name]")

```

"[Placeholder: table name]" を、上記で選択したのと同じ Delta テーブル名で置き換えます。
```scala
//
// Show Aggregate Result
//

val twitterCount = spark.sql("SELECT COUNT(*) FROM [Placeholder: table name]")
twitterCount.show()

val twitterData = spark.sql("SELECT * FROM [Placeholder: table name] ORDER BY groupTime")
twitterData.show(200, false)

display(twitterData)
```
出力は次のようになります。 
```
groupTime                       average
2019-04-08T01:00:00.000+0000    25.6
2019-04-08T02:00:00.000+0000    6857
2019-04-08T03:00:00.000+0000    71
2019-04-08T04:00:00.000+0000    55.111111111111114
2019-04-08T05:00:00.000+0000    2203.8
...
...

```

これで、集計された時系列データは Delta に継続的に取り込まれます。 その後、1 時間ごとのジョブをスケジュールして最新ポイントの異常を検出することができます。 "[Placeholder: table name]" を、上記で選択したのと同じ Delta テーブル名で置き換えます。

```scala
//
// Anomaly Detection
//

import java.time.Instant
import java.time.format.DateTimeFormatter
import java.time.ZoneOffset
import java.time.temporal.ChronoUnit

val detectData = spark.read.format("delta").table("[Placeholder: table name]")

// You could use Databricks to schedule an hourly job and always monitor the latest data point
// Or you could specify a const value here for testing purpose
// For example, val endTime = Instant.parse("2019-04-16T00:00:00Z")
val endTime = Instant.now()

// This is when your input of anomaly detection starts. It is hourly time series in this tutorial, so 72 means 72 hours ago from endTime.
val batchSize = 72
val startTime = endTime.minus(batchSize, ChronoUnit.HOURS)

val DATE_TIME_FORMATTER = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss").withZone(ZoneOffset.UTC);

val series = detectData.filter($"groupTime" <= DATE_TIME_FORMATTER.format(endTime))
  .filter($"groupTime" > DATE_TIME_FORMATTER.format(startTime))
  .sort($"groupTime")

series.createOrReplaceTempView("series")

//series.show()

// Register the function to access it
spark.udf.register("anomalydetect", new AnomalyDetectorAggregationFunction)

val adResult = spark.sql("SELECT '" + endTime.toString + "' as datetime, anomalydetect(groupTime, average) as anomaly FROM series")
adResult.show()
```
結果は次のようになります。 

```
+--------------------+-------+
|           timestamp|anomaly|
+--------------------+-------+
|2019-04-16T00:00:00Z|  false|
+--------------------+-------+
```

これで完了です。 Azure Databricks を使用して、正常に Azure Event Hubs にデータをストリーミングし、Event Hubs コネクタを使用してストリーム データを消費し、さらにストリーミング データに対してほぼリアルタイムで異常検出を実行することができました。
このチュートリアルでは粒度は 1 時間ですが、粒度はニーズに合わせていつでも変更できます。 

## <a name="clean-up-resources"></a>リソースのクリーンアップ

チュートリアルの実行が完了したら、クラスターを終了できます。 それを行うには、Azure Databricks ワークスペースで、左側のウィンドウから **[クラスター]** を選択します。 終了するクラスターで、カーソルを **[アクション]** 列の下の省略記号に移動し、 **[終了]** アイコンを選択してから **[確認]** を選択します。

![Databricks クラスターを停止する](../media/tutorials/terminate-databricks-cluster.png "Databricks クラスターを停止する")

クラスターの作成時に **[Terminate after \_\_ minutes of inactivity]** \(アクティビティが \_\_ 分ない場合は終了する\) チェック ボックスをオンにしていた場合、手動で終了しなくともクラスターは自動で停止します。 このような場合、クラスターは、一定の時間だけ非アクティブな状態が続くと自動的に停止します。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure Databricks を使用して、データを Azure Event Hubs にストリーム配信し、Event Hubs からストリーミング データをリアルタイムで読み取る方法について説明しました。 Anomaly Detector API を呼び出し、Power BI Desktop を使用して異常を視覚化する方法について学習するには、次のチュートリアルに進んでください。 

> [!div class="nextstepaction"]
>[Power BI Desktop でのバッチ異常検出](batch-anomaly-detection-powerbi.md)
