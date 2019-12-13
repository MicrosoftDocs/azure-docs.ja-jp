---
title: クイック スタート:テキスト読み上げ音声を一覧表示する、Node.js - Speech サービス
titleSuffix: Azure Cognitive Services
description: このクイック スタートでは、Node.js を使用してリージョンまたはエンドポイントの標準音声およびニューラル音声の完全な一覧を取得する方法について説明します。 この一覧は JSON として返されます。利用可能な音声はリージョンによって異なります。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: erhopf
ms.openlocfilehash: 7a929794ffaea4f863ffaef7227e58c7ccf901f0
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74976572"
---
# <a name="quickstart-get-the-list-of-text-to-speech-voices-using-nodejs"></a>クイック スタート:Node.js を使用してテキスト読み上げ音声の一覧を取得する

このクイック スタートでは、Node.js を使用してリージョンまたはエンドポイントの標準音声およびニューラル音声の完全な一覧を取得する方法について説明します。 この一覧は JSON として返されます。利用可能な音声はリージョンによって異なります。 サポートされているリージョンの一覧については、[リージョン](regions.md)に関するページをご覧ください。

このクイックスタートでは、[Azure Cognitive Services アカウント](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)と Speech サービス リソースが必要になります。 アカウントを持っていない場合は、[無料試用版](get-started.md)を使用してサブスクリプション キーを取得できます。

## <a name="prerequisites"></a>前提条件

このクイック スタートでは以下が必要です。

* [Node 8.12.x 以降](https://nodejs.org/en/)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/)、[Visual Studio Code](https://code.visualstudio.com/download)、または任意のテキスト エディター
* Speech サービス用の Azure サブスクリプション キー。 [1 つ無料で取得できます](get-started.md)。

## <a name="create-a-project-and-require-dependencies"></a>プロジェクトと必要な依存関係を作成する

普段使用している IDE またはエディターで新しい Node.js プロジェクトを作成します。 次に、このコード スニペットをプロジェクトの `get-voices.js` という名前のファイルにコピーします。

```javascript
// Requires request and request-promise for HTTP requests
// e.g. npm install request request-promise
const rp = require('request-promise');
// Requires fs to write the list of languages to a file
const fs = require('fs');
```

> [!NOTE]
> これらのモジュールを使用していない場合は、プログラムを実行する前にこれらをインストールする必要があります。 これらのパッケージをインストールするには、`npm install request request-promise` を実行します。

## <a name="get-an-access-token"></a>アクセス トークンを取得する

Text to Speech REST API は、認証のためのアクセス トークンを必要とします。 アクセス トークンを取得するためには、交換が必要です。 この関数では、`issueToken` エンドポイントを使用して、ご利用の Speech サービスのサブスクリプション キーをアクセス トークンと交換します。

このサンプルでは、ご利用の Speech サービスのサブスクリプションが米国西部リージョンにあることを想定しています。 別のリージョンを使用している場合は、`uri` の値を更新してください。 完全な一覧については、[リージョン](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis)に関するセクションを参照してください。

このコードをプロジェクトにコピーします。

```javascript
// Gets an access token.
function getAccessToken(subscriptionKey) {
    let options = {
        method: 'POST',
        uri: 'https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken',
        headers: {
            'Ocp-Apim-Subscription-Key': subscriptionKey
        }
    }
    return rp(options);
}
```

> [!NOTE]
> 認証の詳細については、[アクセス トークンによる認証](https://docs.microsoft.com/azure/cognitive-services/authentication#authenticate-with-an-authentication-token)に関するセクションを参照してください。

次のセクションでは、音声の一覧を取得して JSON 出力をファイルに保存するための関数を作成します。

## <a name="make-a-request-and-save-the-response"></a>要求を実行して応答を保存する

ここでは要求を構築して、返された音声の一覧を保存します。 このサンプルでは、米国西部のエンドポイントを使用していることを想定しています。 ご使用のリソースが別のリージョンに登録されている場合は、必ず `uri` を更新してください。 詳細については、[Speech サービスのリージョン](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech)に関するページを参照してください。

次に、要求に必要なヘッダーを追加します。 最後に、サービスに対して要求を実行します。 要求が成功し、状態コード 200 が返された場合、応答がファイルに書き込まれます。

```javascript
function textToSpeech(accessToken) {
    let options = {
        method: 'GET',
        baseUrl: 'https://westus.tts.speech.microsoft.com/',
        url: 'cognitiveservices/voices/list',
        headers: {
            'Authorization': 'Bearer ' + accessToken,
            'Content-Type': 'application/json'
        }
    }

    let request = rp(options)
        .on('response', (response) => {
            if (response.statusCode === 200) {
                request.pipe(fs.createWriteStream('voices.json'));
                console.log('\nYour file is ready.\n')
            }
        });
    return request;
}
```

## <a name="put-it-all-together"></a>すべてをまとめた配置

あともう少しで終了です。 最後の手順では、非同期関数を作成します。 この関数は、環境変数からサブスクリプション キーを読み取り、トークンを取得し、要求が完了するのを待機した後、JSON 応答をファイルに書き込みます。

環境変数に慣れていない場合や、文字列としてハードコーディングされたサブスクリプション キーをテストする場合は、`process.env.SPEECH_SERVICE_KEY` をサブスクリプション キー文字列に置き換えてください。

```javascript
// Use async and await to get the token before attempting
// to convert text to speech.
async function main() {
    // Reads subscription key from env variable.
    // You can replace this with a string containing your subscription key. If
    // you prefer not to read from an env variable.
    // e.g. const subscriptionKey = "your_key_here";
    const subscriptionKey = process.env.SPEECH_SERVICE_KEY;
    if (!subscriptionKey) {
        throw new Error('Environment variable for your subscription key is not set.')
    };
    try {
        const accessToken = await getAccessToken(subscriptionKey);
        await textToSpeech(accessToken);
    } catch (err) {
        console.log(`Something went wrong: ${err}`);
    }
}

main()
```

## <a name="run-the-sample-app"></a>サンプル アプリを実行する

以上で、サンプル アプリを実行する準備が整いました。 コマンド ライン (またはターミナル セッション) でプロジェクト ディレクトリに移動して、次のコマンドを実行します。

```console
node get-voices.js
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

サブスクリプション キーなどの秘密情報は、サンプル アプリのソース コードからすべて確実に削除してください。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [GitHub で Node.js のサンプルを詳しく見てみる](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/NodeJS)

## <a name="see-also"></a>関連項目

* [Text-to-speech API リファレンス](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [カスタム音声フォントの作成](how-to-customize-voice-font.md)
* [カスタム音声を作成するための音声サンプルを録音する](record-custom-voice-samples.md)
