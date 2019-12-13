---
title: クイック スタート:テキストを音声に変換する、Python - Speech サービス
titleSuffix: Azure Cognitive Services
description: このクイック スタートでは、Python と Text to Speech REST API を使用してテキストを音声に変換する方法について説明します。 このガイドに含まれているサンプル テキストは、音声合成マークアップ言語 (SSML) として構成されています。 そのため、音声応答の音声と言語を選択することができます。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: erhopf
ms.openlocfilehash: 639120b283775bcdb5fe4316d78c538c9db9cf4a
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74975909"
---
# <a name="quickstart-convert-text-to-speech-using-python"></a>クイック スタート: Python を使用してテキストを音声に変換する

このクイック スタートでは、Python と Text to Speech REST API を使用してテキストを音声に変換する方法について説明します。 このガイドでは、要求本文が[音声合成マークアップ言語 (SSML)](speech-synthesis-markup.md) として構成されており、応答の音声と言語を選択することができます。

このクイックスタートでは、[Azure Cognitive Services アカウント](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)と Speech サービス リソースが必要になります。 アカウントを持っていない場合は、[無料試用版](get-started.md)を使用してサブスクリプション キーを取得できます。

## <a name="prerequisites"></a>前提条件

このクイック スタートでは以下が必要です。

* Python 2.7.x または 3.x
* [Visual Studio](https://visualstudio.microsoft.com/downloads/)、[Visual Studio Code](https://code.visualstudio.com/download)、または任意のテキスト エディター
* Speech サービス用の Azure サブスクリプション キー

## <a name="create-a-project-and-import-required-modules"></a>プロジェクトの作成と必要なモジュールのインポート

普段使用している IDE またはエディターで、新しい Python プロジェクトを作成します。 次に、このコード スニペットをプロジェクトの `tts.py` という名前のファイルにコピーします。

```python
import os
import requests
import time
from xml.etree import ElementTree
```

> [!NOTE]
> これらのモジュールを使用していない場合は、プログラムを実行する前にこれらをインストールする必要があります。 これらのパッケージをインストールするには、`pip install requests` を実行します。

これらのモジュールは、音声応答をタイムスタンプと共にファイルに書き込み、HTTP 要求を構成して、Text to Speech API を呼び出す目的で使用されます。

## <a name="set-the-subscription-key-and-create-a-prompt-for-tts"></a>サブスクリプション キーを設定して TTS のプロンプトを作成する

以降いくつかのセクションで、承認を処理して Text to Speech API を呼び出し、応答を検証するためのメソッドを作成します。 まず、このサンプルが Python 2.7.x と 3.x で動作することを確認するコードを追加します。

```python
try:
    input = raw_input
except NameError:
    pass
```

次にクラスを作成しましょう。 トークンを交換したり Text to Speech API を呼び出したりするためのメソッドは、ここに記述します。

```python
class TextToSpeech(object):
    def __init__(self, subscription_key):
        self.subscription_key = subscription_key
        self.tts = input("What would you like to convert to speech: ")
        self.timestr = time.strftime("%Y%m%d-%H%M")
        self.access_token = None
```

`subscription_key` は、Azure portal から入手できる一意のキーです。 `tts` により、音声に変換するテキストを入力するようユーザーに促します。 この入力は文字列リテラルであるため、文字をエスケープする必要はありません。 最後に、`timestr` で現在の時刻を取得します。これは、ファイルに名前を付ける際に使用します。

## <a name="get-an-access-token"></a>アクセス トークンを取得する

Text to Speech REST API は、認証のためのアクセス トークンを必要とします。 アクセス トークンを取得するためには、交換が必要です。 このサンプルでは、`issueToken` エンドポイントを使用して、Speech サービスのサブスクリプション キーをアクセス トークンと交換します。

このサンプルでは、ご利用の Speech サービスのサブスクリプションが米国西部リージョンにあることを想定しています。 別のリージョンを使用している場合は、`fetch_token_url` の値を更新してください。 完全な一覧については、[リージョン](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis)に関するセクションを参照してください。

このコードを `TextToSpeech` クラスにコピーします。

```python
def get_token(self):
    fetch_token_url = "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken"
    headers = {
        'Ocp-Apim-Subscription-Key': self.subscription_key
    }
    response = requests.post(fetch_token_url, headers=headers)
    self.access_token = str(response.text)
```

> [!NOTE]
> 認証の詳細については、[アクセス トークンによる認証](https://docs.microsoft.com/azure/cognitive-services/authentication#authenticate-with-an-authentication-token)に関するセクションを参照してください。

## <a name="make-a-request-and-save-the-response"></a>要求を実行して応答を保存する

ここでは、要求を構築して音声応答を保存します。 まず、`base_url` と `path` を設定する必要があります。 このサンプルでは、米国西部のエンドポイントを使用していることを想定しています。 ご使用のリソースが別のリージョンに登録されている場合は、必ず `base_url` を更新してください。 詳細については、[Speech サービスのリージョン](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech)に関するページを参照してください。

次に、要求に必要なヘッダーを追加する必要があります。 必ず `User-Agent` を (Azure portal にある) ご使用のリソースの名前に更新し、`X-Microsoft-OutputFormat` を、優先するオーディオ出力に設定してください。 出力形式の全一覧については、[オーディオ出力](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)に関するセクションを参照してください。

次に、音声合成マークアップ言語 (SSML) を使用して要求本文を構成します。 このサンプルでは、その構造を定義し、先ほど作成した `tts` 入力を使用しています。

>[!NOTE]
> このサンプルでは、`Guy24KRUS` の音声フォントを使用しています。 Microsoft から提供されている音声/言語の全一覧については、[言語のサポート](language-support.md)に関するページを参照してください。
> 認識できる独自の音声を自社ブランド用に作成することを検討している場合は、「[カスタム音声フォントの作成](how-to-customize-voice-font.md)」を参照してください。

最後に、サービスに対して要求を実行します。 要求が成功して状態コード 200 が返された場合、タイムスタンプの付いたファイルに音声応答が書き込まれます。

このコードを `TextToSpeech` クラスにコピーします。

```python
def save_audio(self):
    base_url = 'https://westus.tts.speech.microsoft.com/'
    path = 'cognitiveservices/v1'
    constructed_url = base_url + path
    headers = {
        'Authorization': 'Bearer ' + self.access_token,
        'Content-Type': 'application/ssml+xml',
        'X-Microsoft-OutputFormat': 'riff-24khz-16bit-mono-pcm',
        'User-Agent': 'YOUR_RESOURCE_NAME'
    }
    xml_body = ElementTree.Element('speak', version='1.0')
    xml_body.set('{http://www.w3.org/XML/1998/namespace}lang', 'en-us')
    voice = ElementTree.SubElement(xml_body, 'voice')
    voice.set('{http://www.w3.org/XML/1998/namespace}lang', 'en-US')
    voice.set(
        'name', 'Microsoft Server Speech Text to Speech Voice (en-US, Guy24KRUS)')
    voice.text = self.tts
    body = ElementTree.tostring(xml_body)

    response = requests.post(constructed_url, headers=headers, data=body)
    if response.status_code == 200:
        with open('sample-' + self.timestr + '.wav', 'wb') as audio:
            audio.write(response.content)
            print("\nStatus code: " + str(response.status_code) +
                  "\nYour TTS is ready for playback.\n")
    else:
        print("\nStatus code: " + str(response.status_code) +
              "\nSomething went wrong. Check your subscription key and headers.\n")
```

## <a name="put-it-all-together"></a>すべてをまとめた配置

あともう少しで終了です。 最後の手順として、クラスをインスタンス化して、必要な関数を呼び出します。

```python
if __name__ == "__main__":
    subscription_key = "YOUR_KEY_HERE"
    app = TextToSpeech(subscription_key)
    app.get_token()
    app.save_audio()
```

## <a name="run-the-sample-app"></a>サンプル アプリを実行する

以上で、テキスト読み上げのサンプル アプリを実行する準備が整いました。 コマンド ライン (またはターミナル セッション) でプロジェクト ディレクトリに移動して、次のコマンドを実行します。

```console
python tts.py
```

プロンプトが表示されたら、音声に変換したいテキストを何か入力してください。 成功した場合、プロジェクト フォルダーに音声ファイルが格納されます。 任意のメディア プレーヤーを使用して再生してください。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

サブスクリプション キーなどの秘密情報は、サンプル アプリのソース コードからすべて確実に削除してください。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [GitHub で Python のサンプルを詳しく見てみる](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/Python)

## <a name="see-also"></a>関連項目

* [Text-to-speech API リファレンス](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Python と Speech SDK を使用してテキストを音声に変換する](quickstarts/speech-to-text-from-microphone.md)
* [カスタム音声フォントの作成](how-to-customize-voice-font.md)
* [カスタム音声を作成するための音声サンプルを録音する](record-custom-voice-samples.md)
