---
title: クイック スタート:Python を使用して Text Analytics API を呼び出す
titleSuffix: Azure Cognitive Services
description: Azure Cognitive Services の Text Analytics API の使用をすぐに開始するために役立つ情報とコード サンプルを提供します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 04/16/2019
ms.author: aahi
ms.openlocfilehash: 69eb3789586233b824da1ef6a9c338b07281f324
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2019
ms.locfileid: "60001390"
---
# <a name="quickstart-using-python-to-call-the-text-analytics-cognitive-service"></a>クイック スタート:Python を使用して Text Analytics Cognitive Service を呼び出す 
<a name="HOLTop"></a>

このチュートリアルでは、T[Text Analytics API](//go.microsoft.com/fwlink/?LinkID=759711) を Python で使用して、[言語の検出](#Detect)、[センチメントの分析](#SentimentAnalysis)、および[キー フレーズの抽出](#KeyPhraseExtraction)を行う方法について説明します。

この例は、コマンド ラインから実行するか、起動 Binder バッジ上でクリックすることで [MyBinder](https://mybinder.org) の Jupyter Notebook として実行できます。

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=TextAnalytics.ipynb)

### <a name="command-line"></a>コマンド ライン

Jupyter のカーネルである [IPython](https://ipython.org/install.html) を更新する必要がある場合があります。
```bash
pip install --upgrade IPython
```

[Requests](http://docs.python-requests.org/en/master/) ライブラリを更新する必要がある場合があります。
```bash
pip install requests
```

API の技術ドキュメントについては、[API の定義](//go.microsoft.com/fwlink/?LinkID=759346)に関するページを参照してください。

## <a name="prerequisites"></a>前提条件

* [!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

* サインアップ時に生成される[エンドポイントとアクセス キー](../How-tos/text-analytics-how-to-access-key.md)。

* 以下のインポート、サブスクリプション キー、および `text_analytics_base_url` が、以下のすべてのクイック スタートで使用されます。 インポートを追加します。

    ```python
    import requests
    # pprint is pretty print (formats the JSON)
    from pprint import pprint
    from IPython.display import HTML
    ```
    
    以下の行を追加し、`subscription_key` を、前に取得した有効なサブスクリプション キーに置き換えます。
    
    ```python
    subscription_key = '<ADD KEY HERE>'
    assert subscription_key
    ```
    
    次に、以下の行を追加し、`text_analytics_base_url` のリージョンが、サービスの設定時に使用したリージョンに対応していることを確認します。 試用版のキーを使用している場合は、何も変更する必要はありません。
    
    ```python
    text_analytics_base_url = "https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.1/"
    ```

<a name="Detect"></a>

## <a name="detect-languages"></a>言語を検出する

言語検出 API では、[言語検出メソッド](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7)を使用してテキスト ドキュメントの言語を検出します。 お使いのリージョン用の言語検出 API のサービス エンドポイントは、次の URL 経由で利用できます。

```python
language_api_url = text_analytics_base_url + "languages"
print(language_api_url)
```

    https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.1/languages


API に対するペイロードは、`documents` の一覧で構成され、それぞれに `id` と `text` 属性が含まれます。 `text` 属性は、分析対象のテキストを格納します。 

`documents` ディクショナリを、言語検出を行うための任意のテキストに置き換えます。

```python
documents = { 'documents': [
    { 'id': '1', 'text': 'This is a document written in English.' },
    { 'id': '2', 'text': 'Este es un document escrito en Español.' },
    { 'id': '3', 'text': '这是一个用中文写的文件' }
]}
```

次の数行の新しいコードは、Python の `requests` ライブラリを使用して言語検出 API を呼び出して、ドキュメントの言語を判別します。

```python
headers   = {"Ocp-Apim-Subscription-Key": subscription_key}
response  = requests.post(language_api_url, headers=headers, json=documents)
languages = response.json()
pprint(languages)
```

次のコード行は、JSON データを HTML テーブルとして表示します。

```python
table = []
for document in languages["documents"]:
    text  = next(filter(lambda d: d["id"] == document["id"], documents["documents"]))["text"]
    langs = ", ".join(["{0}({1})".format(lang["name"], lang["score"]) for lang in document["detectedLanguages"]])
    table.append("<tr><td>{0}</td><td>{1}</td>".format(text, langs))
HTML("<table><tr><th>Text</th><th>Detected languages(scores)</th></tr>{0}</table>".format("\n".join(table)))
```

正常な JSON 応答:

```json
    {'documents': [{'detectedLanguages': [{'iso6391Name': 'en',
                                           'name': 'English',
                                           'score': 1.0}],
                    'id': '1'},
                   {'detectedLanguages': [{'iso6391Name': 'es',
                                           'name': 'Spanish',
                                           'score': 1.0}],
                    'id': '2'},
                   {'detectedLanguages': [{'iso6391Name': 'zh_chs',
                                           'name': 'Chinese_Simplified',
                                           'score': 1.0}],
                    'id': '3'}],
     'errors': []}
```

<a name="SentimentAnalysis"></a>

## <a name="analyze-sentiment"></a>センチメントを分析する

Sentiment Analysis API では、[Sentiment メソッド](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9)を使用して、一連のテキスト レコードのセンチメント (肯定から否定までの範囲) を検出します。 次の例では、英語とスペイン語の 2 つのドキュメントをスコア付けしています。

感情分析用のサービス エンドポイントは、次の URL 経由で、お使いのリージョンで利用できます。

```python
sentiment_api_url = text_analytics_base_url + "sentiment"
print(sentiment_api_url)
```

    https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.1/sentiment

言語検出の例に示すように、サービスには、ドキュメントの一覧で構成された `documents` キー付きのディクショナリが備わっています。 各ドキュメントは、`id`、分析対象の `text`、およびテキストの `language` で構成されるタプルです。 前のセクションで説明した言語検出 API を使用して、このフィールドを設定できます。

```python
documents = {'documents' : [
  {'id': '1', 'language': 'en', 'text': 'I had a wonderful experience! The rooms were wonderful and the staff was helpful.'},
  {'id': '2', 'language': 'en', 'text': 'I had a terrible time at the hotel. The staff was rude and the food was awful.'},  
  {'id': '3', 'language': 'es', 'text': 'Los caminos que llevan hasta Monte Rainier son espectaculares y hermosos.'},  
  {'id': '4', 'language': 'es', 'text': 'La carretera estaba atascada. Había mucho tráfico el día de ayer.'}
]}
```

これで、センチメント API を使用して、ドキュメントのセンチメントを分析できます。

```python
headers   = {"Ocp-Apim-Subscription-Key": subscription_key}
response  = requests.post(sentiment_api_url, headers=headers, json=documents)
sentiments = response.json()
pprint(sentiments)
```

正常な JSON 応答:

```json
{'documents': [{'id': '1', 'score': 0.7673527002334595},
                {'id': '2', 'score': 0.18574094772338867},
                {'id': '3', 'score': 0.5}],
    'errors': []}
```

ドキュメントのセンチメント スコアは 0.0 と 1.0 の間であり、高いスコアは肯定的なセンチメントを示します。

<a name="KeyPhraseExtraction"></a>

## <a name="extract-key-phrases"></a>キー フレーズを抽出する

Key Phrase Extraction API では、[Key Phrases メソッド](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6)を使用して、テキスト ドキュメントからキー フレーズを抽出します。 チュートリアルのこのセクションでは、英語とスペイン語の両方のドキュメントのキー フレーズを抽出します。

キー フレーズ抽出サービス用のサービス エンドポイントは、次の URL 経由でアクセスされます。

```python
key_phrase_api_url = text_analytics_base_url + "keyPhrases"
print(key_phrase_api_url)
```

    https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.1/keyPhrases

ドキュメントのコレクションは、感情分析で使用したものと同じです。

```python
documents = {'documents' : [
  {'id': '1', 'language': 'en', 'text': 'I had a wonderful experience! The rooms were wonderful and the staff was helpful.'},
  {'id': '2', 'language': 'en', 'text': 'I had a terrible time at the hotel. The staff was rude and the food was awful.'},  
  {'id': '3', 'language': 'es', 'text': 'Los caminos que llevan hasta Monte Rainier son espectaculares y hermosos.'},  
  {'id': '4', 'language': 'es', 'text': 'La carretera estaba atascada. Había mucho tráfico el día de ayer.'}
]}
```

JSON オブジェクトは、次のコード行を使用して HTML テーブルとして表示されます。

```python
table = []
for document in key_phrases["documents"]:
    text    = next(filter(lambda d: d["id"] == document["id"], documents["documents"]))["text"]    
    phrases = ",".join(document["keyPhrases"])
    table.append("<tr><td>{0}</td><td>{1}</td>".format(text, phrases))
HTML("<table><tr><th>Text</th><th>Key phrases</th></tr>{0}</table>".format("\n".join(table)))
```

次の数行の新しいコードは、Python の `requests` ライブラリを使用して言語検出 API を呼び出して、ドキュメントの言語を判別します。
```python
headers   = {'Ocp-Apim-Subscription-Key': subscription_key}
response  = requests.post(key_phrase_api_url, headers=headers, json=documents)
key_phrases = response.json()
pprint(key_phrases)
```

正常な JSON 応答:
```json
{'documents': [
    {'keyPhrases': ['wonderful experience', 'staff', 'rooms'], 'id': '1'},
    {'keyPhrases': ['food', 'terrible time', 'hotel', 'staff'], 'id': '2'},
    {'keyPhrases': ['Monte Rainier', 'caminos'], 'id': '3'},
    {'keyPhrases': ['carretera', 'tráfico', 'día'], 'id': '4'}],
    'errors': []
}
```

## <a name="identify-entities"></a>エンティティの識別

Entities API は、[Entities メソッド](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)を使用して、テキスト ドキュメント内のよく知られたエンティティを識別します。 次の例では、英語のドキュメントのエンティティを識別しています。

エンティティ リンク サービス用のサービス エンドポイントは、次の URL 経由でアクセスされます。

```python
entity_linking_api_url = text_analytics_base_url + "entities"
print(entity_linking_api_url)
```

    https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.1/entities

ドキュメントのコレクションを次に示します。

```python
documents = {'documents' : [
  {'id': '1', 'text': 'Microsoft is an It company.'}
]}
```
これで、ドキュメントを Text Analytics API に送信して、応答を受信できます。

```python
headers   = {"Ocp-Apim-Subscription-Key": subscription_key}
response  = requests.post(entity_linking_api_url, headers=headers, json=documents)
entities = response.json()
```

正常な JSON 応答:
```json
{  
   "documents":[  
      {  
         "id":"1",
         "entities":[  
            {  
               "name":"Microsoft",
               "matches":[  
                  {  
                     "wikipediaScore":0.20872054383103444,
                     "entityTypeScore":0.99996185302734375,
                     "text":"Microsoft",
                     "offset":0,
                     "length":9
                  }
               ],
               "wikipediaLanguage":"en",
               "wikipediaId":"Microsoft",
               "wikipediaUrl":"https://en.wikipedia.org/wiki/Microsoft",
               "bingId":"a093e9b9-90f5-a3d5-c4b8-5855e1b01f85",
               "type":"Organization"
            },
            {  
               "name":"Technology company",
               "matches":[  
                  {  
                     "wikipediaScore":0.82123868042800585,
                     "text":"It company",
                     "offset":16,
                     "length":10
                  }
               ],
               "wikipediaLanguage":"en",
               "wikipediaId":"Technology company",
               "wikipediaUrl":"https://en.wikipedia.org/wiki/Technology_company",
               "bingId":"bc30426e-22ae-7a35-f24b-454722a47d8f"
            }
         ]
      }
   ],
    "errors":[]
}
```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Text Analytics と Power BI](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>関連項目 

 [Text Analytics の概要](../overview.md)  
 [よく寄せられる質問 (FAQ)](../text-analytics-resource-faq.md)
