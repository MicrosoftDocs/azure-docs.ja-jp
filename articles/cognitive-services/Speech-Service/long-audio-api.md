---
title: Long Audio API - Speech Service
titleSuffix: Azure Cognitive Services
description: 長い形式のテキスト読み上げ (オーディオ ブックなど) の非同期合成のために Long Audio API がどのように設計されているか説明します。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 08/11/2020
ms.author: trbye
ms.openlocfilehash: 65c0d80394317c2b2bfbf621d3cc2ad0c2e3448a
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102618408"
---
# <a name="long-audio-api"></a>Long Audio API

Long Audio API は、長い形式のテキスト読み上げ (オーディオ ブック、ニュース記事、ドキュメントなど) の非同期合成のために設計されています。 この API は、合成された音声をリアルタイムで返しません。代わりに、応答をポーリングし、サービスから提供されている出力を使用することを想定しています。 Speech SDK によって使用されるテキスト読み上げ API とは異なり、Long Audio API は、10 分よりも長い合成された音声を作成できるため、バッチ内にオーディオ ブックなどの長い音声コンテンツを作成する出版業者や音声コンテンツ プラットフォームに最適です。

Long Audio API のその他の利点は次のとおりです。

* サービスによって返される合成音声では、最適なニューラル音声が使用されます。
* リアルタイム バッチ モードによる音声合成は行われないため、音声エンドポイントをデプロイする必要はありません。

> [!NOTE]
> Long Audio API で、[パブリック ニューラル音声](./language-support.md#neural-voices)と[カスタム ニューラル音声](./how-to-custom-voice.md#custom-neural-voices)の両方がサポートされるようになりました。

## <a name="workflow"></a>ワークフロー

通常、Long Audio API を使用する場合は、合成するテキスト ファイルを送信し、状態をポーリングして、状態が正常である場合は音声出力をダウンロードできます。

次の図は、ワークフローの概要を示しています。

![Long Audio API のワークフロー図](media/long-audio-api/long-audio-api-workflow.png)

## <a name="prepare-content-for-synthesis"></a>合成用にコンテンツを準備する

テキスト ファイルを準備するときは、次のことを確認してください。

* プレーンテキスト (.txt) または SSML テキスト (.txt) のいずれかである
* [バイト オーダー マーク (BOM) 付きの UTF-8](https://www.w3.org/International/questions/qa-utf8-bom.en#bom) としてエンコードされている
* 単一のファイルであり、zip ではない
* プレーンテキストの場合は 400 文字以上、または SSML テキストの場合は 400 文字以上の[課金対象文字](./text-to-speech.md#pricing-note)を含み、1 万段落未満である
  * プレーンテキストの場合は、**Enter/Return** を押すことで各段落が区切られます - [プレーンテキストの入力例](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/en-US.txt)を参照してください
  * SSML テキストの場合、SSML の各要素は段落と見なされます。 SSML の要素は、異なる段落で区切る必要があります - [SSML テキスト入力の例](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/SSMLTextInputSample.txt)を参照してください

## <a name="sample-code"></a>サンプル コード
このページの残りの部分では Python に焦点を当てていますが、Long Audio API のサンプル コードは、次のプログラミング言語用のものを GitHub から入手できます。

* [サンプル コード: Python](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/Python)
* [サンプル コード: C#](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/CSharp)
* [サンプル コード: Java](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/)

## <a name="python-example"></a>Python の例

このセクションには、Long Audio API の基本的な使用法を示す Python の例が含まれています。 普段使用している IDE またはエディターで、新しい Python プロジェクトを作成します。 次に、このコード スニペットを `long_audio_synthesis_client.py` という名前のファイルにコピーします。

```python
import json
import ntpath
import requests
```

これらのライブラリは、HTTP 要求を構築し、テキスト読み上げの Long Audio REST API を呼び出すために使用されます。

### <a name="get-a-list-of-supported-voices"></a>サポートされている音声の一覧を取得する

サポートされている音声の一覧を取得するには、GET 要求を `https://<endpoint>/api/texttospeech/v3.0/longaudiosynthesis/voices` に送信します。


このコードを使用すると、使用できる特定のリージョンまたはエンドポイントの音声の完全な一覧を取得できます。
```python
def get_voices():
    region = '<region>'
    key = '<your_key>'
    url = 'https://{}.customvoice.api.speech.microsoft.com/api/texttospeech/v3.0/longaudiosynthesis/voices'.format(region)
    header = {
        'Ocp-Apim-Subscription-Key': key
    }

    response = requests.get(url, headers=header)
    print(response.text)

get_voices()
```

次の値を置き換えます。

* `<your_key>` をお使いの音声サービスのサブスクリプションに置き換えます。 この情報は、[Azure portal](https://aka.ms/azureportal) のご利用のリソースの **[概要]** タブで確認できます。
* `<region>` を音声リソースが作成された場所 (`eastus` や `westus` など) に置換します。 この情報は、[Azure portal](https://aka.ms/azureportal) のご利用のリソースの **[概要]** タブで確認できます。

次のような出力が表示されます。

```console
{
  "values": [
    {
      "locale": "en-US",
      "voiceName": "en-US-AriaNeural",
      "description": "",
      "gender": "Female",
      "createdDateTime": "2020-05-21T05:57:39.123Z",
      "properties": {
        "publicAvailable": true
      }
    },
    {
      "id": "8fafd8cd-5f95-4a27-a0ce-59260f873141"
      "locale": "en-US",
      "voiceName": "my custom neural voice",
      "description": "",
      "gender": "Male",
      "createdDateTime": "2020-05-21T05:25:40.243Z",
      "properties": {
        "publicAvailable": false
      }
    }
  ]
}
```

**properties.publicAvailable** が **true** の場合、その音声はパブリック ニューラル音声です。 それ以外の場合は、カスタム ニューラル音声です。

### <a name="convert-text-to-speech"></a>テキストを音声に変換する

プレーンテキストまたは SSML テキストで入力テキスト ファイルを準備してから、次のコードを `long_audio_synthesis_client.py` に追加します。

> [!NOTE]
> `concatenateResult` は省略可能なパラメーターです。 このパラメーターを設定しなかった場合、オーディオ出力が段落ごとに生成されます。 このパラメーターを設定することで、複数の音声を 1 つの出力に連結することもできます。 
> `outputFormat` も省略可能です。 既定では、オーディオ出力は riff-16khz-16bit-mono-pcm に設定されています。 サポートされているオーディオ出力形式の詳細については、「[音声出力形式](#audio-output-formats)」を参照してください。

```python
def submit_synthesis():
    region = '<region>'
    key = '<your_key>'
    input_file_path = '<input_file_path>'
    locale = '<locale>'
    url = 'https://{}.customvoice.api.speech.microsoft.com/api/texttospeech/v3.0/longaudiosynthesis'.format(region)
    header = {
        'Ocp-Apim-Subscription-Key': key
    }

    voice_identities = [
        {
            'voicename': '<voice_name>'
        }
    ]

    payload = {
        'displayname': 'long audio synthesis sample',
        'description': 'sample description',
        'locale': locale,
        'voices': json.dumps(voice_identities),
        'outputformat': 'riff-16khz-16bit-mono-pcm',
        'concatenateresult': True,
    }

    filename = ntpath.basename(input_file_path)
    files = {
        'script': (filename, open(input_file_path, 'rb'), 'text/plain')
    }

    response = requests.post(url, payload, headers=header, files=files)
    print('response.status_code: %d' % response.status_code)
    print(response.headers['Location'])

submit_synthesis()
```

次の値を置き換えます。

* `<your_key>` をお使いの音声サービスのサブスクリプションに置き換えます。 この情報は、[Azure portal](https://aka.ms/azureportal) のご利用のリソースの **[概要]** タブで確認できます。
* `<region>` を音声リソースが作成された場所 (`eastus` や `westus` など) に置換します。 この情報は、[Azure portal](https://aka.ms/azureportal) のご利用のリソースの **[概要]** タブで確認できます。
* `<input_file_path>` を、テキスト読み上げ用に準備したテキスト ファイルのパスに置換します。
* `<locale>` を必要な出力ロケールに置換します。 詳細については、「[言語サポート](language-support.md#neural-voices)」を参照してください。

`/voices` エンドポイントの前回の呼び出しで返された音声のいずれかを使用します。

* パブリック ニューラル音声を使用している場合は、`<voice_name>` を目的の出力音声に置き換えます。
* カスタム ニューラル音声を使用するには、`voice_identities` 変数を次のように置き換え、`<voice_id>` をカスタム ニューラル音声の `id` に置き換えます。
```Python
voice_identities = [
    {
        'id': '<voice_id>'
    }
]
```

次のような出力が表示されます。

```console
response.status_code: 202
https://<endpoint>/api/texttospeech/v3.0/longaudiosynthesis/<guid>
```

> [!NOTE]
> 複数の入力ファイルがある場合は、複数の要求を送信する必要があります。 注意すべきいくつかの制限事項があります。
> * クライアントは、各 Azure サブスクリプション アカウントに対して 1 秒あたり最大 **5** 個の要求をサーバーに送信できます。 制限を超えると、クライアントはエラー コード 429 (要求が多すぎます) を受け取ります。 1 秒あたりの要求の数を減らしてください
> * サーバーは、各 Azure サブスクリプション アカウントに対して最大 **120** 個の要求を実行およびキューに登録できます。 制限を超えた場合、サーバーはエラー コード 429 (要求が多すぎます) を返します。 いくつかの要求が完了するまで、新しい要求を送信しないでお待ちください。

出力の URL は、要求の状態を取得するために使用できます。

### <a name="get-information-of-a-submitted-request"></a>送信された要求の情報の取得

送信された合成要求の状態を取得するには、前の手順で返された URL に GET 要求を送信するだけです。
```Python

def get_synthesis():
    url = '<url>'
    key = '<your_key>'
    header = {
        'Ocp-Apim-Subscription-Key': key
    }
    response = requests.get(url, headers=header)
    print(response.text)

get_synthesis()
```
出力は次のようになります。
```console
response.status_code: 200
{
  "models": [
    {
      "voiceName": "en-US-AriaNeural"
    }
  ],
  "properties": {
    "outputFormat": "riff-16khz-16bit-mono-pcm",
    "concatenateResult": false,
    "totalDuration": "PT5M57.252S",
    "billableCharacterCount": 3048
  },
  "id": "eb3d7a81-ee3e-4e9a-b725-713383e71677",
  "lastActionDateTime": "2021-01-14T11:12:27.240Z",
  "status": "Succeeded",
  "createdDateTime": "2021-01-14T11:11:02.557Z",
  "locale": "en-US",
  "displayName": "long audio synthesis sample",
  "description": "sample description"
}
```

`status` プロパティから、この要求の状態を読み取ることができます。 要求は `NotStarted` 状態から開始され、`Running` に変わり、最終的に `Succeeded` または `Failed`になります。 ループを使用すると、状態が `Succeeded` になるまでこの API をポーリングできます。

### <a name="download-audio-result"></a>オーディオ結果のダウンロード

合成要求が成功したら、GET `/files` API を呼び出して、オーディオ結果をダウンロードできます。

```python
def get_files():
    id = '<request_id>'
    region = '<region>'
    key = '<your_key>'
    url = 'https://{}.customvoice.api.speech.microsoft.com/api/texttospeech/v3.0/longaudiosynthesis/{}/files'.format(region, id)
    header = {
        'Ocp-Apim-Subscription-Key': key
    }

    response = requests.get(url, headers=header)
    print('response.status_code: %d' % response.status_code)
    print(response.text)

get_files()
```
`<request_id>` を、結果をダウンロードする要求の ID に置き換えます。 これは、前の手順の応答にあります。

出力は次のようになります。
```console
response.status_code: 200
{
  "values": [
    {
      "name": "2779f2aa-4e21-4d13-8afb-6b3104d6661a.txt",
      "kind": "LongAudioSynthesisScript",
      "properties": {
        "size": 4200
      },
      "createdDateTime": "2021-01-14T11:11:02.410Z",
      "links": {
        "contentUrl": "https://customvoice-usw.blob.core.windows.net/artifacts/input.txt?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=e05d8d56-9675-448b-820c-4318ae64c8d5"
      }
    },
    {
      "name": "voicesynthesis_waves.zip",
      "kind": "LongAudioSynthesisResult",
      "properties": {
        "size": 9290000
      },
      "createdDateTime": "2021-01-14T11:12:27.226Z",
      "links": {
        "contentUrl": "https://customvoice-usw.blob.core.windows.net/artifacts/voicesynthesis_waves.zip?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=e05d8d56-9675-448b-820c-4318ae64c8d5"
      }
    }
  ]
}
```
出力には、2 つのファイルの情報が含まれています。 `"kind": "LongAudioSynthesisScript"` がある 1 つは、送信された入力スクリプトです。 `"kind": "LongAudioSynthesisResult"` があるもう 1 つは、この要求の結果です。
この結果は、生成されたオーディオ出力ファイルと入力テキストのコピーが含まれている zip です。

両方のファイルは、`links.contentUrl` プロパティの URL からダウンロードできます。

### <a name="get-all-synthesis-requests"></a>すべての合成要求の取得

次のコードを使用して、送信されたすべての要求の一覧を取得できます。

```python
def get_synthesis():
    region = '<region>'
    key = '<your_key>'
    url = 'https://{}.customvoice.api.speech.microsoft.com/api/texttospeech/v3.0/longaudiosynthesis/'.format(region)    
    header = {
        'Ocp-Apim-Subscription-Key': key
    }

    response = requests.get(url, headers=header)
    print('response.status_code: %d' % response.status_code)
    print(response.text)

get_synthesis()
```

出力は次のようになります。
```console
response.status_code: 200
{
  "values": [
    {
      "models": [
        {
          "id": "8fafd8cd-5f95-4a27-a0ce-59260f873141",
          "voiceName": "my custom neural voice"
        }
      ],
      "properties": {
        "outputFormat": "riff-16khz-16bit-mono-pcm",
        "concatenateResult": false,
        "totalDuration": "PT1S",
        "billableCharacterCount": 5
      },
      "id": "f9f0bb74-dfa5-423d-95e7-58a5e1479315",
      "lastActionDateTime": "2021-01-05T07:25:42.433Z",
      "status": "Succeeded",
      "createdDateTime": "2021-01-05T07:25:13.600Z",
      "locale": "en-US",
      "displayName": "Long Audio Synthesis",
      "description": "Long audio synthesis sample"
    },
    {
      "models": [
        {
          "voiceName": "en-US-AriaNeural"
        }
      ],
      "properties": {
        "outputFormat": "riff-16khz-16bit-mono-pcm",
        "concatenateResult": false,
        "totalDuration": "PT5M57.252S",
        "billableCharacterCount": 3048
      },
      "id": "eb3d7a81-ee3e-4e9a-b725-713383e71677",
      "lastActionDateTime": "2021-01-14T11:12:27.240Z",
      "status": "Succeeded",
      "createdDateTime": "2021-01-14T11:11:02.557Z",
      "locale": "en-US",
      "displayName": "long audio synthesis sample",
      "description": "sample description"
    }
  ]
}
```

`values` プロパティには、合成要求の一覧が含まれています。 一覧は改ページ調整され、最大ページ サイズは 100 です。 100 個を超える要求がある場合は、ページ分割されたリストの次のページを取得するために、`"@nextLink"` プロパティが提供されます。

```console
  "@nextLink": "https://<endpoint>/api/texttospeech/v3.0/longaudiosynthesis/?top=100&skip=100"
```

また、URL パラメーターに `skip` と `top` を指定して、ページ サイズとスキップ数をカスタマイズすることもできます。

### <a name="remove-previous-requests"></a>前の要求を削除する

このサービスは、Azure サブスクリプション アカウントごとに最大 **20,000** 個の要求を保持します。 この制限を要求の量が超える場合は、新しい要求を送信する前に、以前の要求を削除してください。 既存の要求を削除しない場合、エラー通知が返されます。

次のコードは、特定の合成要求を削除する方法を示しています。
```python
def delete_synthesis():
    id = '<request_id>'
    region = '<region>'
    key = '<your_key>'
    url = 'https://{}.customvoice.api.speech.microsoft.com/api/texttospeech/v3.0/longaudiosynthesis/{}/'.format(region, id)
    header = {
        'Ocp-Apim-Subscription-Key': key
    }

    response = requests.delete(url, headers=header)
    print('response.status_code: %d' % response.status_code)
```

要求が正常に削除された場合、応答状態コードは HTTP 204 (コンテンツなし) になります。

```console
response.status_code: 204
```

> [!NOTE]
> `NotStarted` または `Running` の状態の要求を削除することはできません。

完成した `long_audio_synthesis_client.py` は、[GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Python/voiceclient.py) で入手できます。

## <a name="http-status-codes"></a>HTTP 状態コード

次の表に、REST API からの HTTP 応答コードとメッセージの詳細を示します。

| API | HTTP 状態コード | 説明 | 解決策 |
|-----|------------------|-------------|----------|
| 作成 | 400 | 音声合成はこのリージョンでは有効になっていません。 | 音声サブスクリプション キーをサポートされているリージョンに変更してください。 |
|        | 400 | このリージョンでは **Standard** 音声サブスクリプションのみが有効です。 | 音声サブスクリプション キーを "Standard" 価格レベルに変更してください。 |
|        | 400 | Azure アカウントの要求数の上限である 20,000 個を超えています。 新しい要求を送信する前に、いくつかの要求を削除してください。 | サーバーでは、Azure アカウントごとに最大 20,000 個の要求が保持されます。 新しい要求を送信する前に、いくつかの要求を削除してください。 |
|        | 400 | 音声合成ではこのモデルを使用することはできません : {modelID}。 | {modelID} の状態が正しいことを確認してください。 |
|        | 400 | 要求のリージョンがこのモデルのリージョンと一致しません: {modelID}。 | {modelID} のリージョンが要求のリージョンと一致することを確認してください。 |
|        | 400 | 音声合成では、バイト順マーカー付きのUTF-8 エンコードのテキスト ファイルのみがサポートされます。 | 入力ファイルが、バイト順マーカー付きの UTF-8 エンコードになっていることを確認します。 |
|        | 400 | 音声合成要求では、有効な SSML 入力のみが許可されます。 | 入力 SSML 式が正しいことを確認してください。 |
|        | 400 | 入力ファイル内に音声名 {voiceName} が見つかりません。 | 入力 SSML の音声名がモデル ID と一致しません。 |
|        | 400 | 入力ファイル内の段落数は 10,000 未満である必要があります。 | ファイル内の段落数が 10,000 未満であることを確認してください。 |
|        | 400 | 入力ファイルの文字数は 400 文字を越えている必要があります。 | 入力ファイルが 400 文字を超えていることを確認してください。 |
|        | 404 | 音声合成定義内に宣言されたモデルが見つかりません: {modelID}。 | {modelID} が正しいことを確認してください。 |
|        | 429 | アクティブな音声合成の制限を超えています。 いくつかの要求が完了するまでお待ちください。 | サーバーでは、Azure アカウントごとに最大 120 個の要求を実行およびキューに登録できます。 いくつかの要求が完了するまで、新しい要求を送信しないでお待ちください。 |
| All       | 429 | 要求が多すぎます。 | クライアントでは、Azure アカウントごとに 1 秒あたり最大 5 個の要求をサーバーに送信することが許可されています。 1 秒あたりの要求の数を減らしてください |
| 削除    | 400 | その音声合成タスクはまだ使用中です。 | 削除できるのは、**完了した** 要求、または **失敗した要求** のみです。 |
| GetByID   | 404 | 指定されたエンティティが見つかりません。 | 合成 ID が正しいことを確認してください。 |

## <a name="regions-and-endpoints"></a>リージョンとエンドポイント

Long Audio API は、一意のエンドポイントを持つ複数のリージョンで使用できます。

| リージョン | エンドポイント |
|--------|----------|
| 米国東部 | `https://eastus.customvoice.api.speech.microsoft.com` |
| インド中部 | `https://centralindia.customvoice.api.speech.microsoft.com` |
| 東南アジア | `https://southeastasia.customvoice.api.speech.microsoft.com` |
| 英国南部 | `https://uksouth.customvoice.api.speech.microsoft.com` |
| 西ヨーロッパ | `https://westeurope.customvoice.api.speech.microsoft.com` |

## <a name="audio-output-formats"></a>音声出力形式

柔軟なオーディオ出力形式をサポートしています。 段落ごとにオーディオ出力を生成したり、'concatenateResult' パラメーターを設定して複数のオーディオ出力を単一の出力に連結したりできます。 Long Audio API では、次の音声出力形式がサポートされています。

> [!NOTE]
> 既定の音声形式は、riff-16khz-16bit-mono-pcm です。

* riff-8khz-16bit-mono-pcm
* riff-16khz-16bit-mono-pcm
* riff-24khz-16bit-mono-pcm
* riff-48khz-16bit-mono-pcm
* audio-16khz-32kbitrate-mono-mp3
* audio-16khz-64kbitrate-mono-mp3
* audio-16khz-128kbitrate-mono-mp3
* audio-24khz-48kbitrate-mono-mp3
* audio-24khz-96kbitrate-mono-mp3
* audio-24khz-160kbitrate-mono-mp3
