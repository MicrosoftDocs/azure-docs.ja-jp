---
title: クイック スタート:2 か国語辞書で単語を検索する、Python - Translator Text API
titleSuffix: Azure Cognitive Services
description: このクイック スタートでは、Python と Translator Text REST API を使用して、翻訳の代替候補を探す方法と特定のテキストの使用例を紹介します。
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: c95644dd0573bc7ad6ca78c5454288c811dd5d47
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67704395"
---
# <a name="quickstart-look-up-words-with-bilingual-dictionary-using-python"></a>クイック スタート:Python を使用して 2 か国語辞書で単語を検索する

このクイック スタートでは、Python と Translator Text REST API を使用して、翻訳の代替候補を探す方法と特定のテキストの使用例を紹介します。

このクイック スタートでは、[Azure Cognitive Services アカウント](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)と Translator Text リソースが必要になります。 アカウントを持っていない場合は、[無料試用版](https://azure.microsoft.com/try/cognitive-services/)を使用してサブスクリプション キーを取得できます。

>[!TIP]
> すべてのコードを一度に見たい場合は、このサンプルのソース コードを [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Python) で入手できます。

## <a name="prerequisites"></a>前提条件

このクイック スタートでは以下が必要です。

* Python 2.7.x または 3.x
* Translator Text の Azure サブスクリプション キー

## <a name="create-a-project-and-import-required-modules"></a>プロジェクトの作成と必要なモジュールのインポート

任意の IDE またはエディターを使用して新しい Python プロジェクトを作成するか、新しいフォルダーをデスクトップに作成します。 このコード スニペットをプロジェクトまたはフォルダーの `dictionary-lookup.py` という名前のファイルにコピーします。

```python
# -*- coding: utf-8 -*-
import os
import requests
import uuid
import json
```

> [!NOTE]
> これらのモジュールを使用していない場合は、プログラムを実行する前にこれらをインストールする必要があります。 これらのパッケージをインストールするには、`pip install requests uuid` を実行します。

最初のコメントでは、UTF-8 エンコードを使用するように Python インタープリターに指示しています。 次に、必要なモジュールをインポートして、環境変数からのサブスクリプション キーの読み取り、HTTP 要求の作成、一意識別子の作成、Translator Text API から返された JSON 応答の処理を行っています。

## <a name="set-the-subscription-key-base-url-and-path"></a>サブスクリプション キー、ベース URL、パスの設定

このサンプルでは、環境変数 `TRANSLATOR_TEXT_KEY` から Translator Text のサブスクリプション キーが読み取られるよう試行されます。 環境変数を使い慣れていない場合は、`subscriptionKey` を文字列として設定し、条件ステートメントをコメント アウトすることができます。

このコードをプロジェクトにコピーします。

```python
# Checks to see if the Translator Text subscription key is available
# as an environment variable. If you are setting your subscription key as a
# string, then comment these lines out.
if 'TRANSLATOR_TEXT_KEY' in os.environ:
    subscriptionKey = os.environ['TRANSLATOR_TEXT_KEY']
else:
    print('Environment variable for TRANSLATOR_TEXT_KEY is not set.')
    exit()
# If you want to set your subscription key as a string, uncomment the line
# below and add your subscription key. Then, be sure to delete your "os" import.
# subscriptionKey = 'put_your_key_here'
```

Translator Text のグローバル エンドポイントは、`base_url` として設定されます。 `path` によって、`dictionary/lookup` ルートが設定され、API のバージョン 3 を使用することが識別されます。

`params` は、ソース言語と出力言語を設定するために使用されます。 このサンプルでは、英語 (`en`) とスペイン語 (`es`) を使用します。

>[!NOTE]
> エンドポイント、ルート、および要求パラメーターの詳細については、「[Translator Text API 3.0: 辞書検索](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-dictionary-lookup)」をご覧ください。

```python
base_url = 'https://api.cognitive.microsofttranslator.com'
path = '/dictionary/lookup?api-version=3.0'
params = '&from=en&to=es'
constructed_url = base_url + path + params
```

## <a name="add-headers"></a>ヘッダーの追加

要求を認証する最も簡単な方法は、このサンプルで使用している `Ocp-Apim-Subscription-Key` ヘッダーとしてサブスクリプション キーを渡すことです。 または、アクセス トークンのサブスクリプション キーを交換し、アクセス トークンを一緒に `Authorization` ヘッダーとして渡して要求を検証することもできます。 詳細については、[認証](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication)に関するページをご覧ください。

このコード スニペットをプロジェクトにコピーします。

```python
headers = {
    'Ocp-Apim-Subscription-Key': subscriptionKey,
    'Content-type': 'application/json',
    'X-ClientTraceId': str(uuid.uuid4())
}
```

Cognitive Services のマルチサービス サブスクリプションを使用している場合は、要求のパラメーターに `Ocp-Apim-Subscription-Region` も含める必要があります。 [マルチサービス サブスクリプションを使用した認証の詳細を参照してください](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication)。

## <a name="create-a-request-to-find-alternate-translations"></a>翻訳の代替候補を探す要求の作成

翻訳したい 1 つまたは複数の文字列を定義します。

```python
# You can pass more than one object in body.
body = [{
    'text': 'Elephants'
}]
```

次に、`requests` モジュールを使用して POST 要求を作成します。 これは、連結された URL、要求ヘッダー、および要求本文の 3 つの引数を受け取ります。

```python
request = requests.post(constructed_url, headers=headers, json=body)
response = request.json()
```

## <a name="print-the-response"></a>応答の出力

最後の手順では、結果を出力します。 このコード スニペットでは、キーを並べ替え、インデントを設定し、項目とキーの区切りを宣言することによって、結果を整形します。

```python
print(json.dumps(response, sort_keys=True, indent=4,
                 ensure_ascii=False, separators=(',', ': ')))
```

## <a name="put-it-all-together"></a>すべてをまとめた配置

これで、Translator Text API を呼び出して JSON 応答を返す簡単なプログラムが完成しました。 ここで、プログラムを実行してみましょう。

```console
python alt-translations.py
```

作成したコードをサンプル コードと比較したい場合は、完全なサンプルを [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Python) から入手できます。

## <a name="sample-response"></a>応答のサンプル

```json
[
    {
        "displaySource": "elephants",
        "normalizedSource": "elephants",
        "translations": [
            {
                "backTranslations": [
                    {
                        "displayText": "elephants",
                        "frequencyCount": 1207,
                        "normalizedText": "elephants",
                        "numExamples": 5
                    }
                ],
                "confidence": 1.0,
                "displayTarget": "elefantes",
                "normalizedTarget": "elefantes",
                "posTag": "NOUN",
                "prefixWord": ""
            }
        ]
    }
]
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

サブスクリプション キーをプログラムにハードコーディングしている場合は、このクイック スタートを終了するときにサブスクリプション キーを必ず削除してください。

## <a name="next-steps"></a>次の手順

API のリファレンスを見て、Translator Text API でできるすべてのことを理解してください。

> [!div class="nextstepaction"]
> [API リファレンス](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)

## <a name="see-also"></a>関連項目

Translator Text API を使用して以下を実行する方法を確認します。

* [テキストを翻訳する](quickstart-python-translate.md)
* [テキストを表記変換する](quickstart-python-transliterate.md)
* [入力によって言語を識別する](quickstart-python-detect.md)
* [サポートされている言語の一覧を取得する](quickstart-python-languages.md)
* [入力から文章の長さを判定する](quickstart-python-sentences.md)
