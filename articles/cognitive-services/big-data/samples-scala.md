---
title: ビッグ データ向けの Cognitive Services - Scala サンプル
description: Azure Databricks 向けに Python で記述された Cognitive Services のサンプルを試して、ビッグ データ向けの MMLSpark パイプラインを実行してみましょう。
services: cognitive-services
author: mhamilton723
manager: nitinme
ms.service: cognitive-services
ms.topic: sample
ms.date: 07/06/2020
ms.author: marhamil
ms.openlocfilehash: 098b59b8de0d0d7e5c3929ce084276350c04810a
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2020
ms.locfileid: "86189312"
---
# <a name="quick-examples"></a>簡単な例

ここに示されているスニペットはすぐに実行できる状態になっており、Spark での Cognitive Services の使用を開始するのに役立ちます。 これらのサンプルは Scala で書かれています。

これらのサンプルでは、次の Cognitive Services が使用されています。

- Text Analytics - 文のセットのセンチメント (またはムード) を取得します。
- Computer Vision - 画像のセットに関連付けられたタグ (1 単語の描写) を取得します。
- Bing Image Search - Web で自然言語クエリに関連する画像を検索します。
- 音声テキスト変換 - 音声ファイルの書き起こしを行い、テキストベースのトランスクリプトを抽出します。
- Anomaly Detector - 時系列データ内の異常を検出します。

## <a name="prerequisites"></a>前提条件

1. 「[概要](getting-started.md)」の手順に従って、Azure Databricks と Cognitive Services の環境を設定します。 このチュートリアルでは、MMLSpark をインストールする方法と、Databricks で Spark クラスターを作成する方法について説明します。
1. Azure Databricks で新しいノートブックを作成した後、次の**共有コード**をコピーし、ノートブックの新しいセルに貼り付けます。
1. 下のサービス サンプルを選択し、コピーしてノートブックの 2 番目の新しいセルに貼り付けます。
1. サービス サブスクリプション キーのプレースホルダーをすべて実際のキーに置き換えます。
1. セルの右上隅にある実行ボタン (三角形のアイコン) を選択し、 **[セルの実行]** を選択します。
1. セルの下の表で結果を確認します。

## <a name="shared-code"></a>共有コード
作業を開始するには、次のコードを自分のプロジェクトに追加します。

```scala
import com.microsoft.ml.spark.cognitive._
import spark.implicits._ 

val serviceKey = "ADD-YOUR-SUBSCRIPTION-KEY"
val location = "eastus"
```

## <a name="text-analytics"></a>Text Analytics

[Text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/) サービスには、テキストからインテリジェントな分析情報を抽出するためのアルゴリズムがいくつか用意されています。 たとえば、指定された入力テキストのセンチメントを見つけることができます。 このサービスでは、`0.0` と `1.0` の間のスコアが返されます。低いスコアは否定的なセンチメントを示し、高いスコアは肯定的なセンチメントを示します。  次のサンプルでは、3 つの単純な文を使用し、それぞれのセンチメント スコアを返します。

```scala
import org.apache.spark.sql.functions.col

val df = Seq(
  ("I am so happy today, its sunny!", "en-US"),
  ("I am frustrated by this rush hour traffic", "en-US"),
  ("The cognitive services on spark aint bad", "en-US")
).toDF("text", "language")

val sentiment = new TextSentiment()
    .setTextCol("text")
    .setLocation(location)
    .setSubscriptionKey(serviceKey)
    .setOutputCol("sentiment")
    .setErrorCol("error")
    .setLanguageCol("language")

display(sentiment.transform(df).select(col("text"), col("sentiment")(0).getItem("score").alias("sentiment")))
```

### <a name="expected-result"></a>予想される結果

| text                                      | センチメント                                             |
|:------------------------------------------|:------------------------------------------------------|
| I am so happy today, its sunny!           | 0.9789592027664185                                    |
| I am frustrated by this rush hour traffic | 0.023795604705810547                                  |
| The cognitive services on spark aint bad  | 0.8888956308364868                                    |

## <a name="computer-vision"></a>Computer Vision

[Computer Vision](https://docs.microsoft.com/azure/cognitive-services/computer-vision/) は、画像を分析して、顔、物体、自然言語による記述などの構造を識別します。
このサンプルでは、一連の画像にタグを付けます。 タグは、認識可能な物体、人物、風景、アクションなど、画像内のものを 1 単語で表したものです。

```scala
// Create a dataframe with the image URLs
val df = Seq(
    ("https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/objects.jpg"),
    ("https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/dog.jpg"),
    ("https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/house.jpg")
).toDF("image")

// Run the Computer Vision service. Analyze Image extracts infortmation from/about the images.
val analysis = new AnalyzeImage()
    .setLocation(location)
    .setSubscriptionKey(serviceKey)
    .setVisualFeatures(Seq("Categories","Color","Description","Faces","Objects","Tags"))
    .setOutputCol("results")
    .setImageUrlCol("image")
    .setErrorCol("error"))

// Show the results of what you wanted to pull out of the images.
display(analysis.transform(df).select(col("image"), col("results").getItem("tags").getItem("name")).alias("results")))

// Uncomment for full results with all visual feature requests
//display(analysis.transform(df).select(col("image"), col("results")))
``` 

### <a name="expected-result"></a>予想される結果

| image | tags |
|:------------------------------------------------------------------------------------------------------------------------------|:--------------------------------------------------------------------------|
| https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/objects.jpg | ['skating' 'person' 'man' 'outdoor' 'riding' 'sport' 'skateboard' 'young' 'board' 'shirt' 'air' 'black' 'park' 'boy' 'side' 'jumping' 'trick' 'ramp' 'doing' 'flying']
| https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/dog.jpg | ['dog' 'outdoor' 'fence' 'wooden' 'small' 'brown' 'building' 'sitting' 'front' 'bench' 'standing' 'table' 'walking' 'board' 'beach' 'white' 'holding' 'bridge' 'track']                
| https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/house.jpg | ['outdoor' 'grass' 'house' 'building' 'old' 'home' 'front' 'small' 'church' 'stone' 'large' 'grazing' 'yard' 'green' 'sitting' 'leading' 'sheep' 'brick' 'bench' 'street' 'white' 'country' 'clock' 'sign' 'parked' 'field' 'standing' 'garden' 'water' 'red' 'horse' 'man' 'tall' 'fire' 'group'] 

## <a name="bing-image-search"></a>Bing Image Search

[Bing Image Search](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview) では、Web を検索して、ユーザーの自然言語クエリに関連する画像を取得します。 このサンプルでは、引用付きの画像を検索するテキスト クエリを使用しています。 クエリに関連する写真を含む画像 URL のリストが返されます。


```scala
import org.apache.spark.ml.Pipeline

// Number of images Bing will return per query
val imgsPerBatch = 10

// A list of offsets, used to page into the search results
val df = (0 until 100).map(o => Tuple1(o*imgsPerBatch)).toSeq.toDF("offset")

// Run the Bing Image Search service with our text query
val bingSearch = new BingImageSearch()
    .setSubscriptionKey(bingSearchKey)
    .setOffsetCol("offset")
    .setQuery("Martin Luther King Jr. quotes")
    .setCount(imgsPerBatch)
    .setOutputCol("images")

// Transformer that extracts and flattens the richly structured output of Bing Image Search into a simple URL column
val getUrls = BingImageSearch.getUrlTransformer("images", "url")

// This displays the full results returned, uncomment to use
// display(bingSearch.transform(bingParameters))

// Since we have two services, they are put into a pipeline
val pipeline = new Pipeline().setStages(Array(bingSearch, getUrls))

// Show the results of your search: image URLs
display(pipeline.fit(df).transform(df))
```

### <a name="expected-result"></a>予想される結果

| url |
|:-------------------------------------------------------------------------------------------------------------------|
| https://iheartintelligence.com/wp-content/uploads/2019/01/powerful-quotes-martin-luther-king-jr.jpg      |
| http://everydaypowerblog.com/wp-content/uploads/2014/01/Martin-Luther-King-Jr.-Quotes-16.jpg             |
| http://www.sofreshandsogreen.com/wp-content/uploads/2012/01/martin-luther-king-jr-quote-sofreshandsogreendotcom.jpg |
| https://everydaypowerblog.com/wp-content/uploads/2014/01/Martin-Luther-King-Jr.-Quotes-18.jpg            |
| https://tsal-eszuskq0bptlfh8awbb.stackpathdns.com/wp-content/uploads/2018/01/MartinLutherKingQuotes.jpg  |

## <a name="speech-to-text"></a>音声テキスト変換

[音声テキスト変換](https://docs.microsoft.com/azure/cognitive-services/speech-service/index-speech-to-text)サービスでは、音声のストリームまたはファイルをテキストに変換します。 このサンプルでは、2 つのオーディオ ファイルを文字に起こします。 最初のファイルは簡単に理解できますが、2 番目のファイルはより難しくなっています。

```scala
import org.apache.spark.sql.functions.col

// Create a dataframe with audio URLs, tied to the column called "url"
val df = Seq(("https://mmlspark.blob.core.windows.net/datasets/Speech/audio2.wav"),
             ("https://mmlspark.blob.core.windows.net/datasets/Speech/audio3.mp3")).toDF("url")

// Run the Speech-to-text service to translate the audio into text
val speechToText = new SpeechToTextSDK()
    .setSubscriptionKey(serviceKey)
    .setLocation("eastus")
    .setOutputCol("text")
    .setAudioDataCol("url")
    .setLanguage("en-US")
    .setProfanity("Masked")

// Show the results of the translation
display(speechToText.transform(df).select(col("url"), col("text").getItem("DisplayText")))
```

### <a name="expected-result"></a>予想される結果

| url | DisplayText |
|:------------------------------------------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| https://mmlspark.blob.core.windows.net/datasets/Speech/audio2.wav | Custom speech provides tools that allow you to visually inspect the recognition quality of a model by comparing audio data with the corresponding recognition result from the custom speech portal. You can playback uploaded audio and determine if the provided recognition result is correct. This tool allows you to quickly inspect quality of Microsoft's baseline speech to text model or a trained custom model without having to transcribe any audio data. |
| https://mmlspark.blob.core.windows.net/datasets/Speech/audio3.mp3 | Add a gentleman Sir thinking visual check.    |
| https://mmlspark.blob.core.windows.net/datasets/Speech/audio3.mp3 | I hear me. |
| https://mmlspark.blob.core.windows.net/datasets/Speech/audio3.mp3 | I like the reassurance for radio that I can hear it as well. |

## <a name="anomaly-detector"></a>Anomaly Detector

[Anomaly Detector](https://docs.microsoft.com/azure/cognitive-services/anomaly-detector/) は、時系列データ内の不規則性を検出するのに適しています。 このサンプルでは、このサービスを使用して、時系列全体での異常を検出します。

```scala
import org.apache.spark.sql.functions.{col, lit}

val anomalyKey = "84a2c303cc7e49f6a44d692c27fb9967"

val df = Seq(
    ("1972-01-01T00:00:00Z", 826.0),
    ("1972-02-01T00:00:00Z", 799.0),
    ("1972-03-01T00:00:00Z", 890.0),
    ("1972-04-01T00:00:00Z", 900.0),
    ("1972-05-01T00:00:00Z", 766.0),
    ("1972-06-01T00:00:00Z", 805.0),
    ("1972-07-01T00:00:00Z", 821.0),
    ("1972-08-01T00:00:00Z", 20000.0),
    ("1972-09-01T00:00:00Z", 883.0),
    ("1972-10-01T00:00:00Z", 898.0),
    ("1972-11-01T00:00:00Z", 957.0),
    ("1972-12-01T00:00:00Z", 924.0),
    ("1973-01-01T00:00:00Z", 881.0),
    ("1973-02-01T00:00:00Z", 837.0),
    ("1973-03-01T00:00:00Z", 9000.0)
  ).toDF("timestamp", "value").withColumn("group", lit("series1"))

// Run the Anomaly Detector service to look for irregular data
val anamolyDetector = new SimpleDetectAnomalies()
    .setSubscriptionKey(anomalyKey)
    .setLocation("eastus")
    .setTimestampCol("timestamp")
    .setValueCol("value")
    .setOutputCol("anomalies")
    .setGroupbyCol("group")
    .setGranularity("monthly")

// Show the full results of the analysis with the anomalies marked as "True"
display(anamolyDetector.transform(df).select("timestamp", "value", "anomalies.isAnomaly"))
```

### <a name="expected-result"></a>予想される結果

| timestamp            |   value | isAnomaly   |
|:---------------------|--------:|:------------|
| 1972-01-01T00:00:00Z |     826 | False       |
| 1972-02-01T00:00:00Z |     799 | False       |
| 1972-03-01T00:00:00Z |     890 | False       |
| 1972-04-01T00:00:00Z |     900 | False       |
| 1972-05-01T00:00:00Z |     766 | False       |
| 1972-06-01T00:00:00Z |     805 | False       |
| 1972-07-01T00:00:00Z |     821 | False       |
| 1972-08-01T00:00:00Z |   20000 | True        |
| 1972-09-01T00:00:00Z |     883 | False       |
| 1972-10-01T00:00:00Z |     898 | False       |
| 1972-11-01T00:00:00Z |     957 | False       |
| 1972-12-01T00:00:00Z |     924 | False       |
| 1973-01-01T00:00:00Z |     881 | False       |
| 1973-02-01T00:00:00Z |     837 | False       |
| 1973-03-01T00:00:00Z |    9000 | True        |
