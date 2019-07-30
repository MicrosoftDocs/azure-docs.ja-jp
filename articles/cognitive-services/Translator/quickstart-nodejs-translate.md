---
title: クイック スタート:テキストを翻訳する (Node.js) - Translator Text API
titleSuffix: Azure Cognitive Services
description: このクイック スタートでは、Node.js で Translator Text API を使ってテキストを別の言語に翻訳します。
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: 06a58bc3d9b6383dd1c4cb6c33c8b24d62d5c851
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705499"
---
# <a name="quickstart-use-the-translator-text-api-to-translate-a-string-with-nodejs"></a>クイック スタート:Node.js と Translator Text API を使用して文字列を翻訳する

このクイック スタートでは、Node.js と Translator Text REST API を使用して、テキスト文字列を英語からイタリア語とドイツ語に翻訳する方法を学習します。

このクイック スタートでは、[Azure Cognitive Services アカウント](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)と Translator Text リソースが必要になります。 アカウントを持っていない場合は、[無料試用版](https://azure.microsoft.com/try/cognitive-services/)を使用してサブスクリプション キーを取得できます。

>[!TIP]
> すべてのコードを一度に見たい場合は、このサンプルのソース コードを [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-NodeJS) で入手できます。

## <a name="prerequisites"></a>前提条件

このクイック スタートでは以下が必要です。

* [Node 8.12.x 以降](https://nodejs.org/en/)
* Translator Text の Azure サブスクリプション キー

## <a name="create-a-project-and-import-required-modules"></a>プロジェクトの作成と必要なモジュールのインポート

任意の IDE またはエディターを使用して新しいプロジェクトを作成するか、`translate-text.js` という名前のファイルが含まれる新しいフォルダーをデスクトップ上に作成します。 次に、このコード スニペットをプロジェクト/ファイル内にコピーします。

```javascript
const request = require('request');
const uuidv4 = require('uuid/v4');
```

> [!NOTE]
> これらのモジュールを使用していない場合は、プログラムを実行する前にこれらをインストールする必要があります。 これらのパッケージをインストールするには、`npm install request uuidv4` を実行します。

これらのモジュールは、HTTP 要求を作成したり、`'X-ClientTraceId'` ヘッダーの一意識別子を作成したりする際に必要となります。

## <a name="set-the-subscription-key"></a>サブスクリプション キーの設定

このコードでは、環境変数 `TRANSLATOR_TEXT_KEY` から Translator Text のサブスクリプション キーが読み取られるよう試行されます。 環境変数を使い慣れていない場合は、`subscriptionKey` を文字列として設定し、条件ステートメントをコメント アウトすることができます。

このコードをプロジェクトにコピーします。

```javascript
/* Checks to see if the subscription key is available
as an environment variable. If you are setting your subscription key as a
string, then comment these lines out.

If you want to set your subscription key as a string, replace the value for
the Ocp-Apim-Subscription-Key header as a string. */
const subscriptionKey = process.env.TRANSLATOR_TEXT_KEY;
if (!subscriptionKey) {
  throw new Error('Environment variable for your subscription key is not set.')
};
```

## <a name="configure-the-request"></a>要求の構成

要求モジュールに用意されている `request()` メソッドには、HTTP メソッド、URL、要求パラメーター、ヘッダー、JSON 本文を `options` オブジェクトとして渡すことができます。 このコード スニペットで、実際の要求を構成してみましょう。

>[!NOTE]
> エンドポイント、ルート、および要求パラメーターの詳細については、「[Translator Text API 3.0: Translate](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate)」をご覧ください。

```javascript
let options = {
    method: 'POST',
    baseUrl: 'https://api.cognitive.microsofttranslator.com/',
    url: 'translate',
    qs: {
      'api-version': '3.0',
      'to': ['de', 'it']
    },
    headers: {
      'Ocp-Apim-Subscription-Key': subscriptionKey,
      'Content-type': 'application/json',
      'X-ClientTraceId': uuidv4().toString()
    },
    body: [{
          'text': 'Hello World!'
    }],
    json: true,
};
```

要求を認証する最も簡単な方法は、このサンプルで使用している `Ocp-Apim-Subscription-Key` ヘッダーとしてサブスクリプション キーを渡すことです。 または、アクセス トークンのサブスクリプション キーを交換し、アクセス トークンを一緒に `Authorization` ヘッダーとして渡して要求を検証することもできます。

Cognitive Services のマルチサービス サブスクリプションを使用している場合は、要求のヘッダーに `Ocp-Apim-Subscription-Region` も含める必要があります。

詳細については、[認証](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication)に関するページをご覧ください。

## <a name="make-the-request-and-print-the-response"></a>要求の実行と応答の出力

次に、`request()` メソッドを使用して要求を作成します。 前のセクションで作成した `options` オブジェクトを第 1 引数として渡すと、修飾された JSON 応答が出力されます。

```javascript
request(options, function(err, res, body){
    console.log(JSON.stringify(body, null, 4));
});
```

>[!NOTE]
> このサンプルでは、`options` オブジェクト内で HTTP 要求を定義しています。 一方、要求モジュールでは、`.post` や `.get` などの便利なメソッドもサポートされています。 詳細については、[便利なメソッド](https://github.com/request/request#convenience-methods)に関するセクションを参照してください。

## <a name="put-it-all-together"></a>すべてをまとめた配置

これで、Translator Text API を呼び出して JSON 応答を返す簡単なプログラムが完成しました。 ここで、プログラムを実行してみましょう。

```console
node translate-text.js
```

作成したコードをサンプル コードと比較したい場合は、完全なサンプルを [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-NodeJS) から入手できます。

## <a name="sample-response"></a>応答のサンプル

```json
[
    {
        "detectedLanguage": {
            "language": "en",
            "score": 1.0
        },
        "translations": [
            {
                "text": "Hallo Welt!",
                "to": "de"
            },
            {
                "text": "Salve, mondo!",
                "to": "it"
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

言語の検出に加え、Translator Text API を使用して次の操作を行う方法について学習します。

* [テキストを表記変換する](quickstart-nodejs-transliterate.md)
* [入力によって言語を識別する](quickstart-nodejs-detect.md)
* [別の翻訳を取得する](quickstart-nodejs-dictionary.md)
* [サポートされている言語の一覧を取得する](quickstart-nodejs-languages.md)
* [入力から文章の長さを判定する](quickstart-nodejs-sentences.md)
