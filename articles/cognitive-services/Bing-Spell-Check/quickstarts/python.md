---
title: クイック スタート:REST API と Python を使用してスペルをチェックする - Bing Spell Check
titleSuffix: Azure Cognitive Services
description: このクイックスタートで、Bing Spell Check REST API を使用してスペルと文法をチェックしてみましょう。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 05/21/2020
ms.author: aahi
ms.custom: devx-track-python
ms.openlocfilehash: e121aaf6725c179189b25dff6534b019c5de730c
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2020
ms.locfileid: "87852737"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-python"></a>クイック スタート:Bing Spell Check REST API と Python を使用してスペルをチェックする

このクイック スタートを使用して、Bing Spell Check の REST API を呼び出してみましょう。 このシンプルな Python アプリケーションは、API に要求を送信して、一連の修正候補を返します。 

このアプリケーションは Python で記述されていますが、API はほとんどのプログラミング言語と互換性のある RESTful Web サービスです。 このアプリケーションのソース コードは、[GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingEntitySearchv7.py) で入手できます。

## <a name="prerequisites"></a>前提条件

* Python [3.x](https://www.python.org)

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="initialize-the-application"></a>アプリケーションを初期化する

1. 好みの IDE またはエディターで新しい Python ファイルを作成し、次の import ステートメントを追加します。

   ```python
   import requests
   import json
   ```

2. スペル チェックの対象となるテキスト、サブスクリプション キー、Bing Spell Check エンドポイントに使用する変数を作成します。 次のコードのグローバル エンドポイントを使用するか、Azure portal に表示される、対象のリソースの[カスタム サブドメイン](../../../cognitive-services/cognitive-services-custom-subdomains.md) エンドポイントを使用することができます。

    ```python
    api_key = "<ENTER-KEY-HERE>"
    example_text = "Hollo, wrld" # the text to be spell-checked
    endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/SpellCheck"
    ```

## <a name="create-the-parameters-for-the-request"></a>要求のパラメーターを作成する

1. `text` をキーとし、対象のテキストを値とする新しいディクショナリを作成します。

    ```python
    data = {'text': example_text}
    ```

2. 要求のパラメーターを追加します。 

   1. `=` 演算子を使用して、`mkt` パラメーターに市場コードを割り当てます。 市場コードは、要求の送信元となる国/地域のコードです。 

   1. `&` 演算子を使用して `mode` パラメーターを追加し、スペルチェック モードを割り当てます。 モードは `proof` (スペルまたは文法のほとんどのエラーが検出されます)、または `spell` (スペル ミスはほとんど検出されますが、文法エラーの検出数は相対的に少なくなります) のいずれかにすることができます。 
 
    ```python
    params = {
        'mkt':'en-us',
        'mode':'proof'
        }
    ```

3. `Content-Type` ヘッダーと、`Ocp-Apim-Subscription-Key` ヘッダーにサブスクリプション キーを追加します。

    ```python
    headers = {
        'Content-Type': 'application/x-www-form-urlencoded',
        'Ocp-Apim-Subscription-Key': api_key,
        }
    ```

## <a name="send-the-request-and-read-the-response"></a>要求を送信して応答を読み取ります。

1. 要求ライブラリを使用して POST 要求を送信します。

    ```python
    response = requests.post(endpoint, headers=headers, params=params, data=data)
    ```

2. JSON 応答を取得して出力します。

    ```python
    json_response = response.json()
    print(json.dumps(json_response, indent=4))
    ```


## <a name="run-the-application"></a>アプリケーションの実行

コマンド ラインを使用している場合は、次のコマンドを使用してアプリケーションを実行します。

```bash
python <FILE_NAME>.py
```

## <a name="example-json-response"></a>JSON の応答例

成功した応答は、次の例に示すように JSON で返されます。

```json
{
   "_type": "SpellCheck",
   "flaggedTokens": [
      {
         "offset": 0,
         "token": "Hollo",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "Hello",
               "score": 0.9115257530801
            },
            {
               "suggestion": "Hollow",
               "score": 0.858039839213461
            },
            {
               "suggestion": "Hallo",
               "score": 0.597385084464481
            }
         ]
      },
      {
         "offset": 7,
         "token": "wrld",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "world",
               "score": 0.9115257530801
            }
         ]
      }
   ]
}
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [単一ページの Web アプリの作成](../tutorials/spellcheck.md)

- [Bing Spell Check API とは](../overview.md)
- [Bing Spell Check API v7 リファレンス](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)
