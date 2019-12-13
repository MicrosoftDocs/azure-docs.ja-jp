---
title: クイック スタート:テキストを音声に変換する、Node.js - Speech サービス
titleSuffix: Azure Cognitive Services
description: このクイック スタートでは、Node.js と Text to Speech REST API を使用してテキストを音声に変換する方法について説明します。 このガイドに含まれているサンプル テキストは、音声合成マークアップ言語 (SSML) として構成されています。 そのため、音声応答の音声と言語を選択することができます。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: erhopf
ms.openlocfilehash: b120acd25874585a744fb774aafe15d32d7baf08
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74976504"
---
# <a name="quickstart-convert-text-to-speech-using-nodejs"></a>クイック スタート:Node.js を使用してテキストを音声に変換する

このクイック スタートでは、Node.js と Text to Speech REST API を使用してテキストを音声に変換する方法について説明します。 このガイドでは、要求本文が[音声合成マークアップ言語 (SSML)](speech-synthesis-markup.md) として構成されており、応答の音声と言語を選択することができます。

このクイックスタートでは、[Azure Cognitive Services アカウント](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)と Speech サービス リソースが必要になります。 アカウントを持っていない場合は、[無料試用版](get-started.md)を使用してサブスクリプション キーを取得できます。

## <a name="prerequisites"></a>前提条件

このクイック スタートでは以下が必要です。

* [Node 8.12.x 以降](https://nodejs.org/en/)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/)、[Visual Studio Code](https://code.visualstudio.com/download)、または任意のテキスト エディター
* Speech サービス用の Azure サブスクリプション キー。 [1 つ無料で取得できます](get-started.md)。

## <a name="create-a-project-and-require-dependencies"></a>プロジェクトと必要な依存関係を作成する

普段使用している IDE またはエディターで新しい Node.js プロジェクトを作成します。 次に、このコード スニペットをプロジェクトの `tts.js` という名前のファイルにコピーします。

```javascript
// Requires request and request-promise for HTTP requests
// e.g. npm install request request-promise
const rp = require('request-promise');
// Requires fs to write synthesized speech to a file
const fs = require('fs');
// Requires readline-sync to read command line inputs
const readline = require('readline-sync');
// Requires xmlbuilder to build the SSML body
const xmlbuilder = require('xmlbuilder');
```

> [!NOTE]
> これらのモジュールを使用していない場合は、プログラムを実行する前にこれらをインストールする必要があります。 これらのパッケージをインストールするには、`npm install request request-promise xmlbuilder readline-sync` を実行します。

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

次のセクションで、Text to Speech API を呼び出し、合成された音声応答を保存する関数を作成します。

## <a name="make-a-request-and-save-the-response"></a>要求を実行して応答を保存する

ここでは、Text to Speech API への要求を作成し、音声応答を保存します。 このサンプルでは、米国西部のエンドポイントを使用していることを想定しています。 ご使用のリソースが別のリージョンに登録されている場合は、必ず `uri` を更新してください。 詳細については、[Speech サービスのリージョン](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech)に関するページを参照してください。

次に、要求に必要なヘッダーを追加する必要があります。 必ず `User-Agent` を (Azure portal にある) ご使用のリソースの名前に更新し、`X-Microsoft-OutputFormat` を、優先するオーディオ出力に設定してください。 出力形式の全一覧については、[オーディオ出力](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)に関するセクションを参照してください。

次に、音声合成マークアップ言語 (SSML) を使用して要求本文を構成します。 このサンプルでは、その構造を定義し、先ほど作成した `text` 入力を使用しています。

>[!NOTE]
> このサンプルでは、`JessaRUS` の音声フォントを使用しています。 Microsoft から提供されている音声/言語の全一覧については、[言語のサポート](language-support.md)に関するページを参照してください。
> 認識できる独自の音声を自社ブランド用に作成することを検討している場合は、「[カスタム音声フォントの作成](how-to-customize-voice-font.md)」を参照してください。

最後に、サービスに対して要求を実行します。 要求が成功して状態コード 200 が返された場合、音声応答が `TTSOutput.wav` として書き込まれます。

```javascript
// Make sure to update User-Agent with the name of your resource.
// You can also change the voice and output formats. See:
// https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#text-to-speech
function textToSpeech(accessToken, text) {
    // Create the SSML request.
    let xml_body = xmlbuilder.create('speak')
        .att('version', '1.0')
        .att('xml:lang', 'en-us')
        .ele('voice')
        .att('xml:lang', 'en-us')
        .att('name', 'Microsoft Server Speech Text to Speech Voice (en-US, Guy24KRUS)')
        .txt(text)
        .end();
    // Convert the XML into a string to send in the TTS request.
    let body = xml_body.toString();

    let options = {
        method: 'POST',
        baseUrl: 'https://westus.tts.speech.microsoft.com/',
        url: 'cognitiveservices/v1',
        headers: {
            'Authorization': 'Bearer ' + accessToken,
            'cache-control': 'no-cache',
            'User-Agent': 'YOUR_RESOURCE_NAME',
            'X-Microsoft-OutputFormat': 'riff-24khz-16bit-mono-pcm',
            'Content-Type': 'application/ssml+xml'
        },
        body: body
    }

    let request = rp(options)
        .on('response', (response) => {
            if (response.statusCode === 200) {
                request.pipe(fs.createWriteStream('TTSOutput.wav'));
                console.log('\nYour file is ready.\n')
            }
        });
    return request;
}
```

## <a name="put-it-all-together"></a>すべてをまとめた配置

あともう少しで終了です。 最後の手順では、非同期関数を作成します。 この関数は、環境変数からサブスクリプション キーを読み取り、テキストの入力を求め、トークンを取得し、要求が完了するのを待機した後、テキスト読み上げを変換して、オーディオを .wav として保存します。

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
    // Prompts the user to input text.
    const text = readline.question('What would you like to convert to speech? ');

    try {
        const accessToken = await getAccessToken(subscriptionKey);
        await textToSpeech(accessToken, text);
    } catch (err) {
        console.log(`Something went wrong: ${err}`);
    }
}

main()
```

## <a name="run-the-sample-app"></a>サンプル アプリを実行する

以上で、テキスト読み上げのサンプル アプリを実行する準備が整いました。 コマンド ライン (またはターミナル セッション) でプロジェクト ディレクトリに移動して、次のコマンドを実行します。

```console
node tts.js
```

プロンプトが表示されたら、音声に変換したいテキストを何か入力してください。 成功した場合、プロジェクト フォルダーに音声ファイルが格納されます。 任意のメディア プレーヤーを使用して再生してください。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

サブスクリプション キーなどの秘密情報は、サンプル アプリのソース コードからすべて確実に削除してください。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [GitHub で Node.js のサンプルを詳しく見てみる](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/NodeJS)

## <a name="see-also"></a>関連項目

* [Text-to-speech API リファレンス](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [カスタム音声フォントの作成](how-to-customize-voice-font.md)
* [カスタム音声を作成するための音声サンプルを録音する](record-custom-voice-samples.md)
