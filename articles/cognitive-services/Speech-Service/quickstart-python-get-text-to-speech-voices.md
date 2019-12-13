---
title: クイック スタート:テキスト読み上げ音声を一覧表示する、Python - Speech サービス
titleSuffix: Azure Cognitive Services
description: このクイック スタートでは、Python を使用してリージョン/エンドポイントの標準音声およびニューラル音声の完全な一覧を取得する方法について説明します。 一覧は JSON として返されます。音声の可用性はリージョンによって異なります。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: erhopf
ms.openlocfilehash: c645d01dd37fd370911019446c99c76c9da6221c
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74975977"
---
# <a name="quickstart-get-the-list-of-text-to-speech-voices-using-python"></a>クイック スタート:Python を使用してテキスト読み上げ音声の一覧を取得する

このクイック スタートでは、Python を使用してリージョン/エンドポイントの標準音声およびニューラル音声の完全な一覧を取得する方法について説明します。 一覧は JSON として返されます。音声の可用性はリージョンによって異なります。 サポートされているリージョンの一覧については、[リージョン](regions.md)に関するページをご覧ください。

このクイックスタートでは、[Azure Cognitive Services アカウント](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)と Speech サービス リソースが必要になります。 アカウントを持っていない場合は、[無料試用版](get-started.md)を使用してサブスクリプション キーを取得できます。

## <a name="prerequisites"></a>前提条件

このクイック スタートでは以下が必要です。

* Python 2.7.x または 3.x
* [Visual Studio](https://visualstudio.microsoft.com/downloads/)、[Visual Studio Code](https://code.visualstudio.com/download)、または任意のテキスト エディター
* Speech サービス用の Azure サブスクリプション キー

## <a name="create-a-project-and-import-required-modules"></a>プロジェクトの作成と必要なモジュールのインポート

普段使用している IDE またはエディターで、新しい Python プロジェクトを作成します。 次に、このコード スニペットをプロジェクトの `get-voices.py` という名前のファイルにコピーします。

```python
import requests
```

> [!NOTE]
> これらのモジュールを使用していない場合は、プログラムを実行する前にこれらをインストールする必要があります。 これらのパッケージをインストールするには、`pip install requests` を実行します。

Requests はテキスト読み上げサービスへの HTTP 要求に使用されます。

## <a name="set-the-subscription-key-and-create-a-prompt-for-tts"></a>サブスクリプション キーを設定して TTS のプロンプトを作成する

以降いくつかのセクションで、承認を処理して Text to Speech API を呼び出し、応答を検証するためのメソッドを作成します。 最初にクラスを作成しましょう。 トークンを交換したり Text to Speech API を呼び出したりするためのメソッドは、ここに記述します。

```python
class GetVoices(object):
    def __init__(self, subscription_key):
        self.subscription_key = subscription_key
        self.access_token = None
```

`subscription_key` は、Azure portal から入手できる一意のキーです。

## <a name="get-an-access-token"></a>アクセス トークンを取得する

このエンドポイントには認証のためのアクセス トークンが必要です。 アクセス トークンを取得するためには、交換が必要です。 このサンプルでは、`issueToken` エンドポイントを使用して、Speech サービスのサブスクリプション キーをアクセス トークンと交換します。

このサンプルでは、ご利用の Speech サービスのサブスクリプションが米国西部リージョンにあることを想定しています。 別のリージョンを使用している場合は、`fetch_token_url` の値を更新してください。 完全な一覧については、[リージョン](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis)に関するセクションを参照してください。

このコードを `GetVoices` クラスにコピーします。

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

ここでは要求を構築して、返された音声の一覧を保存します。 まず、`base_url` と `path` を設定する必要があります。 このサンプルでは、米国西部のエンドポイントを使用していることを想定しています。 ご使用のリソースが別のリージョンに登録されている場合は、必ず `base_url` を更新してください。 詳細については、[Speech サービスのリージョン](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech)に関するページを参照してください。

次に、要求に必要なヘッダーを追加します。 最後に、サービスに対して要求を実行します。 要求が成功し、状態コード 200 が返された場合、応答がファイルに書き込まれます。

このコードを `GetVoices` クラスにコピーします。

```python
def get_voices(self):
    base_url = 'https://westus.tts.speech.microsoft.com'
    path = '/cognitiveservices/voices/list'
    get_voices_url = base_url + path
    headers = {
        'Authorization': 'Bearer ' + self.access_token
    }
    response = requests.get(get_voices_url, headers=headers)
    if response.status_code == 200:
        with open('voices.json', 'wb') as voices:
            voices.write(response.content)
            print("\nStatus code: " + str(response.status_code) +
                  "\nvoices.json is ready to view.\n")
    else:
        print("\nStatus code: " + str(
            response.status_code) + "\nSomething went wrong. Check your subscription key and headers.\n")
```

## <a name="put-it-all-together"></a>すべてをまとめた配置

あともう少しで終了です。 最後の手順として、クラスをインスタンス化して、必要な関数を呼び出します。

```python
if __name__ == "__main__":
    subscription_key = "YOUR_KEY_HERE"
    app = GetVoices(subscription_key)
    app.get_token()
    app.get_voices()
```

## <a name="run-the-sample-app"></a>サンプル アプリを実行する

以上で、サンプルを実行する準備が整いました。 コマンド ライン (またはターミナル セッション) でプロジェクト ディレクトリに移動して、次のコマンドを実行します。

```console
python get-voices.py
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

サブスクリプション キーなどの秘密情報は、サンプル アプリのソース コードからすべて確実に削除してください。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [GitHub で Python のサンプルを詳しく見てみる](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/Python)

## <a name="see-also"></a>関連項目

* [Text-to-speech API リファレンス](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [カスタム音声フォントの作成](how-to-customize-voice-font.md)
* [カスタム音声を作成するための音声サンプルを録音する](record-custom-voice-samples.md)
