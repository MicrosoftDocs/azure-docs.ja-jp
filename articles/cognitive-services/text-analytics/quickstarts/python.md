---
title: クイック スタート:Python を使用して Text Analytics API を呼び出す
titleSuffix: Azure Cognitive Services
description: このクイックスタートでは、Azure Cognitive Services の Text Analytics API の使用をすぐに開始するために役立つ情報とコード サンプルを提供します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 07/20/2020
ms.author: aahi
ms.custom: devx-track-python
ms.openlocfilehash: 34d1b62ed97b966c000ff81e8f7676c30338b6a1
ms.sourcegitcommit: dea88d5e28bd4bbd55f5303d7d58785fad5a341d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2020
ms.locfileid: "87876769"
---
# <a name="quickstart-using-the-python-rest-api-to-call-the-text-analytics-cognitive-service"></a>クイック スタート:Python REST API を使用して Text Analytics Cognitive Service を呼び出す 
<a name="HOLTop"></a>

このクイックスタートを使用して、Text Analytics REST API および Python を使用した言語の分析を開始します。 この記事では、[言語の検出](#Detect)、[感情分析](#SentimentAnalysis)、[キー フレーズの抽出](#KeyPhraseExtraction)、および[リンクされているエンティティの識別](#Entities)を行う方法について説明します。

[!INCLUDE [text-analytics-api-references](../includes/text-analytics-api-references.md)]

## <a name="prerequisites"></a>前提条件

* [Python 3.x](https://python.org)

* Python の要求ライブラリ
    
    このコマンドでライブラリをインストールできます。

    ```console
    pip install --upgrade requests
    ```

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]


## <a name="create-a-new-python-application"></a>新しい Python アプリケーションを作成する

お気に入りのエディターまたは IDE で、新しい Python アプリケーションを作成します。 以下のインポートをご使用のファイルに追加します。

```python
import requests
# pprint is used to format the JSON response
from pprint import pprint
```

リソースの Azure エンドポイントとサブスクリプション キー用の変数を作成します。
    
```python
import os

subscription_key = "<paste-your-text-analytics-key-here>"
endpoint = "<paste-your-text-analytics-endpoint-here>"
```

以降のセクションでは、API の各機能を呼び出す方法について説明します。

<a name="Detect"></a>

## <a name="detect-languages"></a>言語を検出する

Text Analytics ベース エンドポイントに `/text/analytics/v3.0/languages` を追加して、言語検出 URL を形成します。 例: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/languages`
    
```python
language_api_url = endpoint + "/text/analytics/v3.0/languages"
```

API に対するペイロードは、`id` 属性と `text` 属性を含むタプルである `documents` の一覧で構成されます。 `text` 属性には、分析対象のテキストが格納され、`id` には任意の値を指定できます。 

```python
documents = {"documents": [
    {"id": "1", "text": "This is a document written in English."},
    {"id": "2", "text": "Este es un document escrito en Español."},
    {"id": "3", "text": "这是一个用中文写的文件"}
]}
```

要求ライブラリを使用して、ドキュメントを API に送信します。 `Ocp-Apim-Subscription-Key` ヘッダーにサブスクリプション キーを追加し、`requests.post()` を使用して要求を送信します。 

```python
headers = {"Ocp-Apim-Subscription-Key": subscription_key}
response = requests.post(language_api_url, headers=headers, json=documents)
languages = response.json()
pprint(languages)
```

### <a name="output"></a>出力

```json
{
    "documents": [
        {
            "id": "1",
            "detectedLanguage": {
                "name": "English",
                "iso6391Name": "en",
                "confidenceScore": 1.0
            },
            "warnings": []
        },
        {
            "id": "2",
            "detectedLanguage": {
                "name": "Spanish",
                "iso6391Name": "es",
                "confidenceScore": 1.0
            },
            "warnings": []
        },
        {
            "id": "3",
            "detectedLanguage": {
                "name": "Chinese_Simplified",
                "iso6391Name": "zh_chs",
                "confidenceScore": 1.0
            },
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2019-10-01"
}
```

<a name="SentimentAnalysis"></a>

## <a name="analyze-sentiment"></a>感情を分析する

一連のドキュメントのセンチメント (正または負の範囲) を検出するには、Text Analytics ベース エンドポイントに `/text/analytics/v3.0/sentiment` を追加して言語検出 URL を形成します。 例: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/sentiment`
    
```python
sentiment_url = endpoint + "/text/analytics/v3.0/sentiment"
```

言語検出の例に示すように、サービスには、ドキュメントの一覧で構成された `documents` キーを使用してディクショナリを作成します。 各ドキュメントは、`id`、分析対象の `text`、およびテキストの `language` で構成されるタプルです。 

```python
documents = {"documents": [
    {"id": "1", "language": "en",
        "text": "I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable."},
    {"id": "2", "language": "es",
        "text": "Este ha sido un dia terrible, llegué tarde al trabajo debido a un accidente automobilistico."}
]}
```

要求ライブラリを使用して、ドキュメントを API に送信します。 `Ocp-Apim-Subscription-Key` ヘッダーにサブスクリプション キーを追加し、`requests.post()` を使用して要求を送信します。 

```python
headers = {"Ocp-Apim-Subscription-Key": subscription_key}
response = requests.post(sentiment_url, headers=headers, json=documents)
sentiments = response.json()
pprint(sentiments)
```

### <a name="output"></a>出力

ドキュメントのセンチメント スコアは 0.0 と 1.0 の間であり、高いスコアは肯定的なセンチメントを示します。

```json
{
    "documents": [
        {
            "id": "1",
            "sentiment": "positive",
            "confidenceScores": {
                "positive": 1.0,
                "neutral": 0.0,
                "negative": 0.0
            },
            "sentences": [
                {
                    "sentiment": "positive",
                    "confidenceScores": {
                        "positive": 1.0,
                        "neutral": 0.0,
                        "negative": 0.0
                    },
                    "offset": 0,
                    "length": 102,
                    "text": "I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable."
                }
            ],
            "warnings": []
        },
        {
            "id": "2",
            "sentiment": "negative",
            "confidenceScores": {
                "positive": 0.02,
                "neutral": 0.05,
                "negative": 0.93
            },
            "sentences": [
                {
                    "sentiment": "negative",
                    "confidenceScores": {
                        "positive": 0.02,
                        "neutral": 0.05,
                        "negative": 0.93
                    },
                    "offset": 0,
                    "length": 92,
                    "text": "Este ha sido un dia terrible, llegué tarde al trabajo debido a un accidente automobilistico."
                }
            ],
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2020-04-01"
}
```

<a name="KeyPhraseExtraction"></a>

## <a name="extract-key-phrases"></a>キー フレーズを抽出する
 
一連のドキュメントからキー フレーズを抽出するには、Text Analytics ベース エンドポイントに `/text/analytics/v3.0/keyPhrases` を追加して言語検出 URL を形成します。 例: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/keyPhrases`
    
```python
keyphrase_url = endpoint + "/text/analytics/v3.0/keyphrases"
```

このドキュメントのコレクションは、感情分析の例で使用したものと同じです。

```python
documents = {"documents": [
    {"id": "1", "language": "en",
        "text": "I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable."},
    {"id": "2", "language": "es",
        "text": "Si usted quiere comunicarse con Carlos, usted debe de llamarlo a su telefono movil. Carlos es muy responsable, pero necesita recibir una notificacion si hay algun problema."},
    {"id": "3", "language": "en",
        "text": "The Grand Hotel is a new hotel in the center of Seattle. It earned 5 stars in my review, and has the classiest decor I've ever seen."}
]}
```

要求ライブラリを使用して、ドキュメントを API に送信します。 `Ocp-Apim-Subscription-Key` ヘッダーにサブスクリプション キーを追加し、`requests.post()` を使用して要求を送信します。 

```python
headers = {"Ocp-Apim-Subscription-Key": subscription_key}
response = requests.post(keyphrase_url, headers=headers, json=documents)
key_phrases = response.json()
pprint(key_phrases)
```

### <a name="output"></a>出力

```json
{
    "documents": [
        {
            "id": "1",
            "keyPhrases": [
                "HDR resolution",
                "new XBox",
                "clean look"
            ],
            "warnings": []
        },
        {
            "id": "2",
            "keyPhrases": [
                "Carlos",
                "notificacion",
                "algun problema",
                "telefono movil"
            ],
            "warnings": []
        },
        {
            "id": "3",
            "keyPhrases": [
                "new hotel",
                "Grand Hotel",
                "review",
                "center of Seattle",
                "classiest decor",
                "stars"
            ],
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2019-10-01"
}
```

<a name="Entities"></a>

## <a name="identify-entities"></a>エンティティの識別

テキスト ドキュメント内の既知のエンティティ (人、場所、物) を特定するには、Text Analytics ベース エンドポイントに `/text/analytics/v3.0/entities/recognition/general` を追加して言語検出 URL を形成します。 例: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/entities/recognition/general`
    
```python
entities_url = endpoint + "/text/analytics/v3.0/entities/recognition/general"
```

前の例と同様に、ドキュメントのコレクションを作成します。 

```python
documents = {"documents": [
    {"id": "1", "text": "Microsoft is an It company."}
]}
```

要求ライブラリを使用して、ドキュメントを API に送信します。 `Ocp-Apim-Subscription-Key` ヘッダーにサブスクリプション キーを追加し、`requests.post()` を使用して要求を送信します。

```python
headers = {"Ocp-Apim-Subscription-Key": subscription_key}
response = requests.post(entities_url, headers=headers, json=documents)
entities = response.json()
pprint(entities)
```

### <a name="output"></a>出力

```json
{
    "documents": [
        {
            "id": "1",
            "entities": [
                {
                    "text": "Microsoft",
                    "category": "Organization",
                    "offset": 0,
                    "length": 9,
                    "confidenceScore": 0.86
                },
                {
                    "text": "IT",
                    "category": "Skill",
                    "offset": 16,
                    "length": 2,
                    "confidenceScore": 0.8
                }
            ],
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2020-04-01"
}
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Text Analytics と Power BI](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>関連項目 

 [Text Analytics の概要](../overview.md)  
 [よく寄せられる質問 (FAQ)](../text-analytics-resource-faq.md)
