---
author: aahill
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 01/20/2021
ms.author: aahi
ms.openlocfilehash: 715b0d16e33412003820044ffa99fee9e2431a7b
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/30/2021
ms.locfileid: "99090757"
---
<a name="HOLTop"></a>

# <a name="version-31-preview"></a>[バージョン 3.1 プレビュー](#tab/version-3-1)

[v3.1 リファレンス ドキュメント](/python/api/azure-ai-textanalytics/azure.ai.textanalytics) | [v3.1 ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics) | [v3.1 パッケージ (PiPy)](https://pypi.org/project/azure-ai-textanalytics/) | [v3.1 サンプル](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/samples)

# <a name="version-30"></a>[バージョン 3.0](#tab/version-3)

[v3 リファレンス ドキュメント](/python/api/azure-ai-textanalytics/azure.ai.textanalytics) | [v3 ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics) | [v3 パッケージ (PiPy)](https://pypi.org/project/azure-ai-textanalytics/) | [v3 サンプル](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/samples)

# <a name="version-21"></a>[バージョン 2.1](#tab/version-2)

[v2 リファレンス ドキュメント](/python/api/overview/azure/cognitiveservices/textanalytics) | [v2 ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-textanalytics) | [v2 パッケージ (PiPy)](https://pypi.org/project/azure-cognitiveservices-language-textanalytics/) | [v2 サンプル](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)

---

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services)
* [Python 3.x](https://www.python.org/)
* Azure サブスクリプションを入手したら、Azure portal で <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Text Analytics リソースを作成"  target="_blank">Text Analytics リソースを作成<span class="docon docon-navigate-external x-hidden-focus"></span></a>し、キーとエンドポイントを取得します。 デプロイされたら、 **[リソースに移動]** をクリックします。
    * アプリケーションを Text Analytics API に接続するには、作成するリソースのキーとエンドポイントが必要です。 このクイックスタートで後に示すコードに、自分のキーとエンドポイントを貼り付けます。
    * Free 価格レベル (`F0`) を使用してサービスを試用し、後から運用環境用の有料レベルにアップグレードすることができます。
* 分析機能を使用するには、Standard (S) 価格レベルの Text Analytics リソースが必要です。

## <a name="setting-up"></a>設定

### <a name="install-the-client-library"></a>クライアント ライブラリをインストールする

Python をインストールしたら、次を使用してクライアント ライブラリをインストールすることができます。

# <a name="version-31-preview"></a>[バージョン 3.1 プレビュー](#tab/version-3-1)

```console
pip install azure-ai-textanalytics --pre
```

> [!TIP]
> クイックスタートのコード ファイル全体を一度にご覧いただけます。 これは [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/TextAnalytics/python-v3-client-library.py) にあり、このクイックスタートのコード例が含まれています。 

# <a name="version-30"></a>[バージョン 3.0](#tab/version-3)

```console
pip install --upgrade azure-ai-textanalytics
```

> [!TIP]
> クイックスタートのコード ファイル全体を一度にご覧いただけます。 これは [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/TextAnalytics/python-v3-client-library.py) にあり、このクイックスタートのコード例が含まれています。 


# <a name="version-21"></a>[バージョン 2.1](#tab/version-2)

```console
pip install --upgrade azure-cognitiveservices-language-textanalytics
```

> [!TIP]
> クイックスタートのコード ファイル全体を一度にご覧いただけます。 これは [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/language/text_analytics_samples.py) にあり、このクイックスタートのコード例が含まれています。 

---

### <a name="create-a-new-python-application"></a>新しい Python アプリケーションを作成する

新しい Python ファイルを作成し、リソースの Azure エンドポイントとサブスクリプション キー用の変数を作成します。

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```python
key = "<paste-your-text-analytics-key-here>"
endpoint = "<paste-your-text-analytics-endpoint-here>"
```


## <a name="object-model"></a>オブジェクト モデル

# <a name="version-31-preview"></a>[バージョン 3.1 プレビュー](#tab/version-3-1)

Text Analytics クライアントは、Azure に対して認証を行う `TextAnalyticsClient` オブジェクトです。 このクライアントには、テキストを分析するためのメソッドがいくつか備わっています。 

処理時に、テキストは、`documents` のリスト (文字列のリスト、dict-like 形式のリスト、`TextDocumentInput/DetectLanguageInput` のリストのいずれか) として API に送信されます。 `dict-like` オブジェクトは、`id`、`text`、`language/country_hint` の組み合わせを含みます。 `text` 属性には、分析対象のテキストが元の `country_hint` で格納され、`id` には任意の値を指定できます。 

応答オブジェクトは、各ドキュメントの分析情報を格納するリストです。 

# <a name="version-30"></a>[バージョン 3.0](#tab/version-3)

Text Analytics クライアントは、キーを使用して Azure に対して認証を行う `TextAnalyticsClient` オブジェクトです。 このクライアントには、テキストをバッチとして分析するためのメソッドがいくつか備わっています。 

バッチ処理時に、テキストは、`documents` (使用したメソッドに応じて `id`、`text`、および `language` の各属性の組み合わせを保持する `dictionary` オブジェクト) の一覧として API に送信されます。 `text` 属性には、分析対象のテキストが元の `language` で格納され、`id` には任意の値を指定できます。 

応答オブジェクトは、各ドキュメントの分析情報を格納するリストです。 

# <a name="version-21"></a>[バージョン 2.1](#tab/version-2)

Text Analytics クライアントは、ご利用のキーを使用して Azure に対して認証を行う [TextAnalyticsClient](/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient) オブジェクトです。 このクライアントには、テキストを単一の文字列として、またはバッチとして分析するためのメソッドがいくつか備わっています。 

テキストは、`documents` (使用したメソッドに応じて `id`、`text`、`language` の各属性の組み合わせを保持する `dictionary` オブジェクト) のリストとして API に送信されます。 `text` 属性には、分析対象のテキストが元の `language` で格納され、`id` には任意の値を指定できます。 

---

## <a name="code-examples"></a>コード例

これらのコード スニペットでは、Python 用 Text Analytics クライアント ライブラリを使用して以下のタスクを実行する方法が示されています。

# <a name="version-31-preview"></a>[バージョン 3.1 プレビュー](#tab/version-3-1)

* [クライアントを認証する](#authenticate-the-client)
* [感情分析](#sentiment-analysis)
* [オピニオン マイニング](#opinion-mining)
* [言語検出](#language-detection)
* [名前付きエンティティの認識](#named-entity-recognition-ner) 
* [個人を特定できる情報の認識](#personally-identifiable-information-recognition) 
* [エンティティ リンク設定](#entity-linking)
* [キー フレーズ抽出](#key-phrase-extraction)


# <a name="version-30"></a>[Version 3.0](#tab/version-3)

* [クライアントを認証する](#authenticate-the-client)
* [感情分析](#sentiment-analysis)
* [言語検出](#language-detection)
* [名前付きエンティティの認識](#named-entity-recognition-ner) 
* [エンティティ リンク設定](#entity-linking)
* [キー フレーズ抽出](#key-phrase-extraction)

# <a name="version-21"></a>[バージョン 2.1](#tab/version-2)

* [クライアントを認証する](#authenticate-the-client)
* [感情分析](#sentiment-analysis)
* [言語検出](#language-detection)
* [名前付きエンティティの認識](#named-entity-recognition-ner) 
* [エンティティ リンク設定](#entity-linking)
* [キー フレーズ抽出](#key-phrase-extraction)

---

## <a name="authenticate-the-client"></a>クライアントを認証する

# <a name="version-31-preview"></a>[バージョン 3.1 プレビュー](#tab/version-3-1)

上で作成した `key` と `endpoint` を使用して `TextAnalyticsClient` オブジェクトをインスタンス化する関数を作成します。 次に、新しいクライアントを作成します。 

```python
from azure.ai.textanalytics import TextAnalyticsClient
from azure.core.credentials import AzureKeyCredential

def authenticate_client():
    ta_credential = AzureKeyCredential(key)
    text_analytics_client = TextAnalyticsClient(
            endpoint=endpoint, 
            credential=ta_credential)
    return text_analytics_client

client = authenticate_client()
```

# <a name="version-30"></a>[バージョン 3.0](#tab/version-3)

上で作成した `key` と `endpoint` を使用して `TextAnalyticsClient` オブジェクトをインスタンス化する関数を作成します。 次に、新しいクライアントを作成します。 バージョン 3.0 を使用するためには、`api_version=TextAnalyticsApiVersion.V3_0` を定義する必要があることに注意してください。

```python
# use this code if you're using SDK version is 5.0.0
from azure.ai.textanalytics import TextAnalyticsClient
from azure.core.credentials import AzureKeyCredential

def authenticate_client():
    ta_credential = AzureKeyCredential(key)
    text_analytics_client = TextAnalyticsClient(
            endpoint=endpoint, 
            credential=ta_credential) 
    return text_analytics_client

client = authenticate_client()
```

`pip install azure-ai-textanalytics --pre` を使用してクライアント ライブラリのバージョン 5.1.0 をインストールした場合は、クライアントの `api_version` パラメーターを使用して Text Analytics API のバージョン 3.0 を指定できます。 クライアントがバージョン 5.1.0 以降の場合にのみ、次の `authenticate_client()` メソッドを使用してください。

```python
# Only use the following code sample if you're using v5.1.0 of the client library, 
# and are looking to specify v3.0 of the Text Analytics API for your client
from azure.ai.textanalytics import TextAnalyticsClient, TextAnalyticsApiVersion
from azure.core.credentials import AzureKeyCredential
def authenticate_client():
   ta_credential = AzureKeyCredential(key)
   text_analytics_client = TextAnalyticsClient(
     endpoint=endpoint,
     credential=ta_credential,
     api_version=TextAnalyticsApiVersion.V3_0
   )
   
client = authenticate_client()
```

# <a name="version-21"></a>[バージョン 2.1](#tab/version-2)

[!code-python[imports statements](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=imports)]

上で作成した `key` と `endpoint` を使用して `TextAnalyticsClient` オブジェクトをインスタンス化する関数を作成します。 次に、新しいクライアントを作成します。 

[!code-python[version 2 authentication](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=authentication)]

--- 

## <a name="sentiment-analysis"></a>センチメント分析

# <a name="version-31-preview"></a>[バージョン 3.1 プレビュー](#tab/version-3-1)

クライアントを引数として受け取り、`analyze_sentiment()` 関数を呼び出す、`sentiment_analysis_example()` という名前の新しい関数を作成します。 返される応答オブジェクトには、入力ドキュメント全体のセンチメント ラベルとスコアが含まれます。また、各文の感情分析も含まれます。


```python
def sentiment_analysis_example(client):

    documents = ["I had the best day of my life. I wish you were there with me."]
    response = client.analyze_sentiment(documents=documents)[0]
    print("Document Sentiment: {}".format(response.sentiment))
    print("Overall scores: positive={0:.2f}; neutral={1:.2f}; negative={2:.2f} \n".format(
        response.confidence_scores.positive,
        response.confidence_scores.neutral,
        response.confidence_scores.negative,
    ))
    for idx, sentence in enumerate(response.sentences):
        print("Sentence: {}".format(sentence.text))
        print("Sentence {} sentiment: {}".format(idx+1, sentence.sentiment))
        print("Sentence score:\nPositive={0:.2f}\nNeutral={1:.2f}\nNegative={2:.2f}\n".format(
            sentence.confidence_scores.positive,
            sentence.confidence_scores.neutral,
            sentence.confidence_scores.negative,
        ))
          
sentiment_analysis_example(client)
```

### <a name="output"></a>出力

```console
Document Sentiment: positive
Overall scores: positive=1.00; neutral=0.00; negative=0.00 

Sentence: I had the best day of my life.
Sentence 1 sentiment: positive
Sentence score:
Positive=1.00
Neutral=0.00
Negative=0.00

Sentence: I wish you were there with me.
Sentence 2 sentiment: neutral
Sentence score:
Positive=0.21
Neutral=0.77
Negative=0.02
```

## <a name="opinion-mining"></a>意見マイニング

オピニオン マイニングを使用した感情分析を行うためには、クライアントを引数として受け取り、`analyze_sentiment()` 関数をオプション フラグ `show_opinion_mining=True` で呼び出す、`sentiment_analysis_with_opinion_mining_example()` という名前の新しい関数を作成します。 返される応答オブジェクトには、入力ドキュメント全体のセンチメント ラベルとスコアが、文ごとの感情分析と共に含まれるほか、アスペクトおよびオピニオン レベルの感情分析も含まれます。


```python
def sentiment_analysis_with_opinion_mining_example(client):

    documents = [
        "The food and service were unacceptable, but the concierge were nice"
    ]

    result = client.analyze_sentiment(documents, show_opinion_mining=True)
    doc_result = [doc for doc in result if not doc.is_error]

    positive_reviews = [doc for doc in doc_result if doc.sentiment == "positive"]
    negative_reviews = [doc for doc in doc_result if doc.sentiment == "negative"]

    positive_mined_opinions = []
    mixed_mined_opinions = []
    negative_mined_opinions = []

    for document in doc_result:
        print("Document Sentiment: {}".format(document.sentiment))
        print("Overall scores: positive={0:.2f}; neutral={1:.2f}; negative={2:.2f} \n".format(
            document.confidence_scores.positive,
            document.confidence_scores.neutral,
            document.confidence_scores.negative,
        ))
        for sentence in document.sentences:
            print("Sentence: {}".format(sentence.text))
            print("Sentence sentiment: {}".format(sentence.sentiment))
            print("Sentence score:\nPositive={0:.2f}\nNeutral={1:.2f}\nNegative={2:.2f}\n".format(
                sentence.confidence_scores.positive,
                sentence.confidence_scores.neutral,
                sentence.confidence_scores.negative,
            ))
            for mined_opinion in sentence.mined_opinions:
                aspect = mined_opinion.aspect
                print("......'{}' aspect '{}'".format(aspect.sentiment, aspect.text))
                print("......Aspect score:\n......Positive={0:.2f}\n......Negative={1:.2f}\n".format(
                    aspect.confidence_scores.positive,
                    aspect.confidence_scores.negative,
                ))
                for opinion in mined_opinion.opinions:
                    print("......'{}' opinion '{}'".format(opinion.sentiment, opinion.text))
                    print("......Opinion score:\n......Positive={0:.2f}\n......Negative={1:.2f}\n".format(
                        opinion.confidence_scores.positive,
                        opinion.confidence_scores.negative,
                    ))
            print("\n")
        print("\n")
          
sentiment_analysis_with_opinion_mining_example(client)
```

### <a name="output"></a>出力

```console
Document Sentiment: positive
Overall scores: positive=0.84; neutral=0.00; negative=0.16

Sentence: The food and service were unacceptable, but the concierge were nice
Sentence sentiment: positive
Sentence score:
Positive=0.84
Neutral=0.00
Negative=0.16

......'negative' aspect 'food'
......Aspect score:
......Positive=0.01
......Negative=0.99

......'negative' opinion 'unacceptable'
......Opinion score:
......Positive=0.01
......Negative=0.99

......'negative' aspect 'service'
......Aspect score:
......Positive=0.01
......Negative=0.99

......'negative' opinion 'unacceptable'
......Opinion score:
......Positive=0.01
......Negative=0.99

......'positive' aspect 'concierge'
......Aspect score:
......Positive=1.00
......Negative=0.00

......'positive' opinion 'nice'
......Opinion score:
......Positive=1.00
......Negative=0.00





Press any key to continue . . .

```

# <a name="version-30"></a>[バージョン 3.0](#tab/version-3)

クライアントを引数として受け取り、`analyze_sentiment()` 関数を呼び出す、`sentiment_analysis_example()` という名前の新しい関数を作成します。 返される応答オブジェクトには、入力ドキュメント全体のセンチメント ラベルとスコアが含まれます。また、各文の感情分析も含まれます。


```python
def sentiment_analysis_example(client):

    documents = ["I had the best day of my life. I wish you were there with me."]
    response = client.analyze_sentiment(documents = documents)[0]
    print("Document Sentiment: {}".format(response.sentiment))
    print("Overall scores: positive={0:.2f}; neutral={1:.2f}; negative={2:.2f} \n".format(
        response.confidence_scores.positive,
        response.confidence_scores.neutral,
        response.confidence_scores.negative,
    ))
    for idx, sentence in enumerate(response.sentences):
        print("Sentence: {}".format(sentence.text))
        print("Sentence {} sentiment: {}".format(idx+1, sentence.sentiment))
        print("Sentence score:\nPositive={0:.2f}\nNeutral={1:.2f}\nNegative={2:.2f}\n".format(
            sentence.confidence_scores.positive,
            sentence.confidence_scores.neutral,
            sentence.confidence_scores.negative,
        ))
          
sentiment_analysis_example(client)
```

### <a name="output"></a>出力

```console
Document Sentiment: positive
Overall scores: positive=1.00; neutral=0.00; negative=0.00 

Sentence: I had the best day of my life.
Sentence 1 sentiment: positive
Sentence score:
Positive=1.00
Neutral=0.00
Negative=0.00

Sentence: I wish you were there with me.
Sentence 2 sentiment: neutral
Sentence score:
Positive=0.21
Neutral=0.77
Negative=0.02
```

# <a name="version-21"></a>[バージョン 2.1](#tab/version-2)

クライアント オブジェクトを認証し、[sentiment()](/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient#sentiment-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) 関数を呼び出します。 結果を反復処理し、各ドキュメントの ID とセンチメント スコアを出力します。 0 に近いスコアは否定的センチメント、1 に近いスコアは肯定的センチメントを示します。

[!code-python[sentiment analysis](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=sentimentAnalysis)]

### <a name="output"></a>出力

```console
Document ID: 1 , Sentiment Score: 0.87
Document ID: 2 , Sentiment Score: 0.11
Document ID: 3 , Sentiment Score: 0.44
Document ID: 4 , Sentiment Score: 1.00
```

---

## <a name="language-detection"></a>言語検出

# <a name="version-31-preview"></a>[バージョン 3.1 プレビュー](#tab/version-3-1)

クライアントを引数として受け取り、`detect_language()` 関数を呼び出す、`language_detection_example()` という名前の新しい関数を作成します。 返される応答オブジェクトには、成功した場合は検出された言語が `primary_language` に含まれ、そうでない場合は `error` が含まれます。

> [!Tip]
> 場合によっては、入力に基づいて言語を明確に区別するのが困難なことがあります。 `country_hint` パラメーターを使用して、2 文字の国番号を指定できます。 API の既定では、既定の countryHint として "US" が使用されます。この動作を削除するには、この値を空の文字列に設定して (`country_hint : ""`)、このパラメーターをリセットします。 

```python
def language_detection_example(client):
    try:
        documents = ["Ce document est rédigé en Français."]
        response = client.detect_language(documents = documents, country_hint = 'us')[0]
        print("Language: ", response.primary_language.name)

    except Exception as err:
        print("Encountered exception. {}".format(err))
language_detection_example(client)
```


### <a name="output"></a>出力

```console
Language:  French
```

# <a name="version-30"></a>[バージョン 3.0](#tab/version-3)

クライアントを引数として受け取り、`detect_language()` 関数を呼び出す、`language_detection_example()` という名前の新しい関数を作成します。 返される応答オブジェクトには、成功した場合は検出された言語が `primary_language` に含まれ、そうでない場合は `error` が含まれます。

> [!Tip]
> 場合によっては、入力に基づいて言語を明確に区別するのが困難なことがあります。 `country_hint` パラメーターを使用して、2 文字の国番号を指定できます。 API の既定では、既定の countryHint として "US" が使用されます。この動作を削除するには、この値を空の文字列に設定して (`country_hint : ""`)、このパラメーターをリセットします。 

```python
def language_detection_example(client):
    try:
        documents = ["Ce document est rédigé en Français."]
        response = client.detect_language(documents = documents, country_hint = 'us')[0]
        print("Language: ", response.primary_language.name)

    except Exception as err:
        print("Encountered exception. {}".format(err))
language_detection_example(client)
```


### <a name="output"></a>出力

```console
Language:  French
```

# <a name="version-21"></a>[バージョン 2.1](#tab/version-2)

以前に作成したクライアントを使用して、[detect_language()](/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient#detect-language-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) を呼び出して結果を取得します。 結果を反復処理し、各ドキュメントの ID と最初に返された言語を印刷します。

[!code-python[language detection](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=languageDetection)]


### <a name="output"></a>出力

```console
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified
```

---

## <a name="named-entity-recognition-ner"></a>名前付きエンティティの認識 (NER)

# <a name="version-31-preview"></a>[バージョン 3.1 プレビュー](#tab/version-3-1)

> [!NOTE]
> バージョン `3.1`: 
> * エンティティ リンク設定は、NER から切り離された要求です。

クライアントを引数として受け取り、`recognize_entities()` 関数を呼び出して、結果を反復処理する、`entity_recognition_example` という名前の新しい関数を作成します。 返される応答オブジェクトには、成功した場合は検出されたエンティティの一覧が `entity` に含まれ、そうでない場合は `error` が含まれます。 検出されたエンティティごとに、カテゴリとサブカテゴリ (ある場合) を出力します。

```python
def entity_recognition_example(client):

    try:
        documents = ["I had a wonderful trip to Seattle last week."]
        result = client.recognize_entities(documents = documents)[0]

        print("Named Entities:\n")
        for entity in result.entities:
            print("\tText: \t", entity.text, "\tCategory: \t", entity.category, "\tSubCategory: \t", entity.subcategory,
                    "\n\tConfidence Score: \t", round(entity.confidence_score, 2), "\tLength: \t", entity.length, "\tOffset: \t", entity.offset, "\n")

    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_recognition_example(client)
```

### <a name="output"></a>出力

```console
Named Entities:

        Text:    trip   Category:        Event  SubCategory:     None
        Confidence Score:        0.61   Length:          4      Offset:          18

        Text:    Seattle        Category:        Location       SubCategory:     GPE
        Confidence Score:        0.82   Length:          7      Offset:          26

        Text:    last week      Category:        DateTime       SubCategory:     DateRange
        Confidence Score:        0.8    Length:          9      Offset:          34
```

### <a name="entity-linking"></a>Entity Linking

クライアントを引数として受け取り、`recognize_linked_entities()` 関数を呼び出して、結果を反復処理する、`entity_linking_example()` という名前の新しい関数を作成します。 返される応答オブジェクトには、成功した場合は検出されたエンティティの一覧が `entities` に含まれ、そうでない場合は `error` が含まれます。 リンクされたエンティティは一意に識別されるため、同じエンティティの出現は、`match` オブジェクトの一覧として `entity` オブジェクトの下にグループ化されます。

```python
def entity_linking_example(client):

    try:
        documents = ["""Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, 
        to develop and sell BASIC interpreters for the Altair 8800. 
        During his career at Microsoft, Gates held the positions of chairman,
        chief executive officer, president and chief software architect, 
        while also being the largest individual shareholder until May 2014."""]
        result = client.recognize_linked_entities(documents = documents)[0]

        print("Linked Entities:\n")
        for entity in result.entities:
            print("\tName: ", entity.name, "\tId: ", entity.data_source_entity_id, "\tUrl: ", entity.url,
            "\n\tData Source: ", entity.data_source)
            print("\tMatches:")
            for match in entity.matches:
                print("\t\tText:", match.text)
                print("\t\tConfidence Score: {0:.2f}".format(match.confidence_score))
                print("\t\tOffset: {}".format(match.offset))
                print("\t\tLength: {}".format(match.length))
            
    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_linking_example(client)
```

### <a name="output"></a>出力

```console
Linked Entities:

        Name:  Microsoft        Id:  Microsoft  Url:  https://en.wikipedia.org/wiki/Microsoft
        Data Source:  Wikipedia
        Matches:
                Text: Microsoft
                Confidence Score: 0.55
                Offset: 0
                Length: 9
                Text: Microsoft
                Confidence Score: 0.55
                Offset: 168
                Length: 9
        Name:  Bill Gates       Id:  Bill Gates         Url:  https://en.wikipedia.org/wiki/Bill_Gates
        Data Source:  Wikipedia
        Matches:
                Text: Bill Gates
                Confidence Score: 0.63
                Offset: 25
                Length: 10
                Text: Gates
                Confidence Score: 0.63
                Offset: 179
                Length: 5
        Name:  Paul Allen       Id:  Paul Allen         Url:  https://en.wikipedia.org/wiki/Paul_Allen
        Data Source:  Wikipedia
        Matches:
                Text: Paul Allen
                Confidence Score: 0.60
                Offset: 40
                Length: 10
        Name:  April 4  Id:  April 4    Url:  https://en.wikipedia.org/wiki/April_4
        Data Source:  Wikipedia
        Matches:
                Text: April 4
                Confidence Score: 0.32
                Offset: 54
                Length: 7
        Name:  BASIC    Id:  BASIC      Url:  https://en.wikipedia.org/wiki/BASIC
        Data Source:  Wikipedia
        Matches:
                Text: BASIC
                Confidence Score: 0.33
                Offset: 98
                Length: 5
        Name:  Altair 8800      Id:  Altair 8800        Url:  https://en.wikipedia.org/wiki/Altair_8800
        Data Source:  Wikipedia
        Matches:
                Text: Altair 8800
                Confidence Score: 0.88
                Offset: 125
                Length: 11
```

### <a name="personally-identifiable-information-recognition"></a>個人を特定できる情報の認識

クライアントを引数として受け取り、`recognize_pii_entities()` 関数を呼び出して、結果を反復処理する、`pii_recognition_example` という名前の新しい関数を作成します。 返される応答オブジェクトには、成功した場合は検出されたエンティティの一覧が `entity` に含まれ、そうでない場合は `error` が含まれます。 検出されたエンティティごとに、カテゴリとサブカテゴリ (ある場合) を出力します。

```python
def pii_recognition_example(client):
    documents = [
        "The employee's SSN is 859-98-0987.",
        "The employee's phone number is 555-555-5555."
    ]
    response = client.recognize_pii_entities(documents, language="en")
    result = [doc for doc in response if not doc.is_error]
    for doc in result:
        print("Redacted Text: {}".format(doc.redacted_text))
        for entity in doc.entities:
            print("Entity: {}".format(entity.text))
            print("\tCategory: {}".format(entity.category))
            print("\tConfidence Score: {}".format(entity.confidence_score))
            print("\tOffset: {}".format(entity.offset))
            print("\tLength: {}".format(entity.length))
pii_recognition_example(client)
```

### <a name="output"></a>出力

```console
Redacted Text: The employee's SSN is ***********.
Entity: 859-98-0987
        Category: U.S. Social Security Number (SSN)
        Confidence Score: 0.65
        Offset: 22
        Length: 11
Redacted Text: The employee's phone number is ************.
Entity: 555-555-5555
        Category: Phone Number
        Confidence Score: 0.8
        Offset: 31
        Length: 12
```

# <a name="version-30"></a>[Version 3.0](#tab/version-3)

> [!NOTE]
> バージョン `3.0`: 
> * エンティティ リンク設定は、NER から切り離された要求です。

クライアントを引数として受け取り、`recognize_entities()` 関数を呼び出して、結果を反復処理する、`entity_recognition_example` という名前の新しい関数を作成します。 返される応答オブジェクトには、成功した場合は検出されたエンティティの一覧が `entity` に含まれ、そうでない場合は `error` が含まれます。 検出されたエンティティごとに、カテゴリとサブカテゴリ (ある場合) を出力します。

```python
def entity_recognition_example(client):

    try:
        documents = ["I had a wonderful trip to Seattle last week."]
        result = client.recognize_entities(documents = documents)[0]

        print("Named Entities:\n")
        for entity in result.entities:
            print("\tText: \t", entity.text, "\tCategory: \t", entity.category, "\tSubCategory: \t", entity.subcategory,
                    "\n\tConfidence Score: \t", round(entity.confidence_score, 2), "\n")

    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_recognition_example(client)
```

### <a name="output"></a>出力

```console
Named Entities:

        Text:    trip   Category:        Event  SubCategory:     None
        Confidence Score:        0.61

        Text:    Seattle        Category:        Location       SubCategory:     GPE
        Confidence Score:        0.82

        Text:    last week      Category:        DateTime       SubCategory:     DateRange
        Confidence Score:        0.8
```

### <a name="entity-linking"></a>Entity Linking

クライアントを引数として受け取り、`recognize_linked_entities()` 関数を呼び出して、結果を反復処理する、`entity_linking_example()` という名前の新しい関数を作成します。 返される応答オブジェクトには、成功した場合は検出されたエンティティの一覧が `entities` に含まれ、そうでない場合は `error` が含まれます。 リンクされたエンティティは一意に識別されるため、同じエンティティの出現は、`match` オブジェクトの一覧として `entity` オブジェクトの下にグループ化されます。

```python
def entity_linking_example(client):

    try:
        documents = ["""Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, 
        to develop and sell BASIC interpreters for the Altair 8800. 
        During his career at Microsoft, Gates held the positions of chairman,
        chief executive officer, president and chief software architect, 
        while also being the largest individual shareholder until May 2014."""]
        result = client.recognize_linked_entities(documents = documents)[0]

        print("Linked Entities:\n")
        for entity in result.entities:
            print("\tName: ", entity.name, "\tId: ", entity.data_source_entity_id, "\tUrl: ", entity.url,
            "\n\tData Source: ", entity.data_source)
            print("\tMatches:")
            for match in entity.matches:
                print("\t\tText:", match.text)
                print("\t\tConfidence Score: {0:.2f}".format(match.confidence_score))
            
    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_linking_example(client)
```

### <a name="output"></a>出力

```console
Linked Entities:

        Name:  Altair 8800      Id:  Altair 8800        Url:  https://en.wikipedia.org/wiki/Altair_8800
        Data Source:  Wikipedia
        Matches:
                Text: Altair 8800
                Confidence Score: 0.88
        Name:  Bill Gates       Id:  Bill Gates         Url:  https://en.wikipedia.org/wiki/Bill_Gates
        Data Source:  Wikipedia
        Matches:
                Text: Bill Gates
                Confidence Score: 0.63
                Text: Gates
                Confidence Score: 0.63
        Name:  Paul Allen       Id:  Paul Allen         Url:  https://en.wikipedia.org/wiki/Paul_Allen
        Data Source:  Wikipedia
        Matches:
                Text: Paul Allen
                Confidence Score: 0.60
        Name:  Microsoft        Id:  Microsoft  Url:  https://en.wikipedia.org/wiki/Microsoft
        Data Source:  Wikipedia
        Matches:
                Text: Microsoft
                Confidence Score: 0.55
                Text: Microsoft
                Confidence Score: 0.55
        Name:  April 4  Id:  April 4    Url:  https://en.wikipedia.org/wiki/April_4
        Data Source:  Wikipedia
        Matches:
                Text: April 4
                Confidence Score: 0.32
        Name:  BASIC    Id:  BASIC      Url:  https://en.wikipedia.org/wiki/BASIC
        Data Source:  Wikipedia
        Matches:
                Text: BASIC
                Confidence Score: 0.33
```

# <a name="version-21"></a>[バージョン 2.1](#tab/version-2)

> [!NOTE]
> バージョン 2.1 では、エンティティ リンク設定が NER の応答に含まれています。

以前に作成したクライアントを使用して、[entities()](/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient#entities-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) 関数を呼び出して結果を取得します。 続いて、結果を反復処理し、各ドキュメントの ID とそれに含まれているエンティティを印刷します。

[!code-python[Entity recognition](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=entityRecognition)]

### <a name="output"></a>出力

```console
Document ID: 1
        Name: Microsoft,        Type: Organization,     Sub-Type: N/A
        Offset: 0, Length: 9,   Score: 1.0

        Name: Bill Gates,       Type: Person,   Sub-Type: N/A
        Offset: 25, Length: 10, Score: 0.999847412109375

        Name: Paul Allen,       Type: Person,   Sub-Type: N/A
        Offset: 40, Length: 10, Score: 0.9988409876823425

        Name: April 4,  Type: Other,    Sub-Type: N/A
        Offset: 54, Length: 7,  Score: 0.8

        Name: April 4, 1975,    Type: DateTime, Sub-Type: Date
        Offset: 54, Length: 13, Score: 0.8

        Name: BASIC,    Type: Other,    Sub-Type: N/A
        Offset: 89, Length: 5,  Score: 0.8

        Name: Altair 8800,      Type: Other,    Sub-Type: N/A
        Offset: 116, Length: 11,        Score: 0.8

Document ID: 2
        Name: Microsoft,        Type: Organization,     Sub-Type: N/A
        Offset: 21, Length: 9,  Score: 0.999755859375

        Name: Redmond (Washington),     Type: Location, Sub-Type: N/A
        Offset: 60, Length: 7,  Score: 0.9911284446716309

        Name: 21 kilómetros,    Type: Quantity, Sub-Type: Dimension
        Offset: 71, Length: 13, Score: 0.8

        Name: Seattle,  Type: Location, Sub-Type: N/A
        Offset: 88, Length: 7,  Score: 0.9998779296875
```

---

### <a name="key-phrase-extraction"></a>キー フレーズの抽出

# <a name="version-31-preview"></a>[バージョン 3.1 プレビュー](#tab/version-3-1)

クライアントを引数として受け取り、`extract_key_phrases()` 関数を呼び出す、`key_phrase_extraction_example()` という名前の新しい関数を作成します。 成功した場合は `key_phrases` 内で検出されたキー フレーズのリストが結果に含まれ、失敗した場合は `error` が含まれます。 検出されたキー フレーズを出力します。

```python
def key_phrase_extraction_example(client):

    try:
        documents = ["My cat might need to see a veterinarian."]

        response = client.extract_key_phrases(documents = documents)[0]

        if not response.is_error:
            print("\tKey Phrases:")
            for phrase in response.key_phrases:
                print("\t\t", phrase)
        else:
            print(response.id, response.error)

    except Exception as err:
        print("Encountered exception. {}".format(err))
        
key_phrase_extraction_example(client)
```


### <a name="output"></a>出力

```console
    Key Phrases:
         cat
         veterinarian
```

# <a name="version-30"></a>[バージョン 3.0](#tab/version-3)

クライアントを引数として受け取り、`extract_key_phrases()` 関数を呼び出す、`key_phrase_extraction_example()` という名前の新しい関数を作成します。 成功した場合は `key_phrases` 内で検出されたキー フレーズのリストが結果に含まれ、失敗した場合は `error` が含まれます。 検出されたキー フレーズを出力します。

```python
def key_phrase_extraction_example(client):

    try:
        documents = ["My cat might need to see a veterinarian."]

        response = client.extract_key_phrases(documents = documents)[0]

        if not response.is_error:
            print("\tKey Phrases:")
            for phrase in response.key_phrases:
                print("\t\t", phrase)
        else:
            print(response.id, response.error)

    except Exception as err:
        print("Encountered exception. {}".format(err))
        
key_phrase_extraction_example(client)
```


### <a name="output"></a>出力

```console
    Key Phrases:
         cat
         veterinarian
```

# <a name="version-21"></a>[バージョン 2.1](#tab/version-2)

以前に作成したクライアントを使用して、[key_phrases()](/python/api/azure-cognitiveservices-language-textanalytics/azure.cognitiveservices.language.textanalytics.textanalyticsclient#key-phrases-show-stats-none--documents-none--custom-headers-none--raw-false----operation-config-) 関数を呼び出して結果を取得します。 続いて、結果を反復処理し、各ドキュメントの ID とそれに含まれているキー フレーズを印刷します。

[!code-python[key phrase extraction](~/samples-cognitive-services-python-sdk/samples/language/text_analytics_samples.py?name=keyPhrases)]


### <a name="output"></a>出力

```console
Document ID: 1
         Key phrases:
                幸せ
Document ID: 2
         Key phrases:
                Stuttgart
                Hotel
                Fahrt
                Fu
Document ID: 3
         Key phrases:
                cat
                veterinarian
Document ID: 4
         Key phrases:
                fútbol
```

---

## <a name="use-the-api-asynchronously-with-the-analyze-operation"></a>分析操作で API を非同期的に使用する

# <a name="version-31-preview"></a>[バージョン 3.1 プレビュー](#tab/version-3-1)

[!INCLUDE [Analyze operation pricing](../analyze-operation-pricing-caution.md)]

クライアントを引数として受け取り、`begin_analyze()` 関数を呼び出す、`analyze_example()` という名前の新しい関数を作成します。 この操作の実行には時間がかかり、結果に対してポーリングされます。

```python
    def analyze_example(client):
        documents = [
            "Microsoft was founded by Bill Gates and Paul Allen."
        ]

        poller = text_analytics_client.begin_analyze(
            documents,
            display_name="Sample Text Analysis",
            entities_recognition_tasks=[EntitiesRecognitionTask()]
        )

        result = poller.result()

        for page in result:
            for task in page.entities_recognition_results:
                print("Results of Entities Recognition task:")
                
                docs = [doc for doc in task.results if not doc.is_error]
                for idx, doc in enumerate(docs):
                    print("\nDocument text: {}".format(documents[idx]))
                    for entity in doc.entities:
                        print("Entity: {}".format(entity.text))
                        print("...Category: {}".format(entity.category))
                        print("...Confidence Score: {}".format(entity.confidence_score))
                        print("...Offset: {}".format(entity.offset))
                    print("------------------------------------------")

analyze_example(client)
```

### <a name="output"></a>出力

```console
Results of Entities Recognition task:
Document text: Microsoft was founded by Bill Gates and Paul Allen.
Entity: Microsoft
...Category: Organization
...Confidence Score: 0.83
...Offset: 0
Entity: Bill Gates
...Category: Person
...Confidence Score: 0.85
...Offset: 25
Entity: Paul Allen
...Category: Person
...Confidence Score: 0.9
...Offset: 40
------------------------------------------
```

また、分析操作を使用して、PII とキー フレーズ抽出を検出することもできます。 GitHub の[分析のサンプル](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/textanalytics/azure-ai-textanalytics/samples/async_samples/sample_analyze_async.py)を参照してください。

# <a name="version-30"></a>[Version 3.0](#tab/version-3)

この機能はバージョン 3.0 では使用できません。

# <a name="version-21"></a>[バージョン 2.1](#tab/version-2)

この機能はバージョン 2.1 では使用できません。

---
