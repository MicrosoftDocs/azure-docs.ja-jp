---
title: Long Audio API (プレビュー) - 音声サービス
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
ms.openlocfilehash: be38d3e78108a15c9f7875a15156e0eeba5a6211
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2020
ms.locfileid: "88167761"
---
# <a name="long-audio-api-preview"></a>Long Audio API (プレビュー)

Long Audio API は、長い形式のテキスト読み上げ (オーディオ ブック、ニュース記事、ドキュメントなど) の非同期合成のために設計されています。 この API は、合成された音声をリアルタイムで返しません。代わりに、応答をポーリングし、サービスから提供されている出力を使用することを想定しています。 Speech SDK によって使用されるテキスト読み上げ API とは異なり、Long Audio API は、10 分よりも長い合成された音声を作成できるため、パブリッシャーと音声コンテンツ プラットフォームには最適です。

Long Audio API のその他の利点は次のとおりです。

* サービスによって返される合成音声では、最適なニューラル音声が使用されます。
* リアルタイム バッチ モードによる音声合成は行われないため、音声エンドポイントをデプロイする必要はありません。

> [!NOTE]
> Long Audio API で、[パブリック ニューラル音声](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#neural-voices)と[カスタム ニューラル音声](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-custom-voice#custom-neural-voices)の両方がサポートされるようになりました。

## <a name="workflow"></a>ワークフロー

通常、Long Audio API を使用する場合は、合成するテキスト ファイルを送信し、状態をポーリングして、状態が正常である場合は音声出力をダウンロードできます。

次の図は、ワークフローの概要を示しています。

![Long Audio API のワークフロー図](media/long-audio-api/long-audio-api-workflow.png)

## <a name="prepare-content-for-synthesis"></a>合成用にコンテンツを準備する

テキスト ファイルを準備するときは、次のことを確認してください。

* プレーンテキスト (.txt) または SSML テキスト (.txt) のいずれかである
* [バイト オーダー マーク (BOM) 付きの UTF-8](https://www.w3.org/International/questions/qa-utf8-bom.en#bom) としてエンコードされている
* 単一のファイルであり、zip ではない
* プレーンテキストの場合は 400 文字以上、または SSML テキストの場合は 400 文字以上の[課金対象文字](https://docs.microsoft.com/azure/cognitive-services/speech-service/text-to-speech#pricing-note)を含み、1 万段落未満である
  * プレーンテキストの場合は、**Enter/Return** を押すことで各段落が区切られます - [プレーンテキストの入力例](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/en-US.txt)を参照してください
  * SSML テキストの場合、SSML の各要素は段落と見なされます。 SSML の要素は、異なる段落で区切る必要があります - [SSML テキスト入力の例](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/SSMLTextInputSample.txt)を参照してください
> [!NOTE]
> 中国語 (大陸)、中国語 (香港特別行政区)、中国語 (台湾)、日本語、および韓国語の場合、1 ワードが 2 文字としてカウントされます。 

## <a name="python-example"></a>Python の例

このセクションには、Long Audio API の基本的な使用法を示す Python の例が含まれています。 普段使用している IDE またはエディターで、新しい Python プロジェクトを作成します。 次に、このコード スニペットを `voice_synthesis_client.py` という名前のファイルにコピーします。

```python
import argparse
import json
import ntpath
import urllib3
import requests
import time
from json import dumps, loads, JSONEncoder, JSONDecoder
import pickle

urllib3.disable_warnings(urllib3.exceptions.InsecureRequestWarning)
```

これらのライブラリは、引数を解析し、HTTP 要求を構築し、テキスト読み上げの Long Audio REST API を呼び出すために使用されます。

### <a name="get-a-list-of-supported-voices"></a>サポートされている音声の一覧を取得する

このコードを使用すると、使用できる特定のリージョンまたはエンドポイントの音声の完全な一覧を取得できます。 このコードを `voice_synthesis_client.py` に追加してください。

```python
parser = argparse.ArgumentParser(description='Text-to-speech client tool to submit voice synthesis requests.')
parser.add_argument('--voices', action="store_true", default=False, help='print voice list')
parser.add_argument('-key', action="store", dest="key", required=True, help='the speech subscription key, like fg1f763i01d94768bda32u7a******** ')
parser.add_argument('-region', action="store", dest="region", required=True, help='the region information, could be centralindia, canadacentral or uksouth')
args = parser.parse_args()
baseAddress = 'https://%s.customvoice.api.speech.microsoft.com/api/texttospeech/v3.0-beta1/' % args.region

def getVoices():
    response=requests.get(baseAddress+"voicesynthesis/voices", headers={"Ocp-Apim-Subscription-Key":args.key}, verify=False)
    voices = json.loads(response.text)
    return voices

if args.voices:
    voices = getVoices()
    print("There are %d voices available:" % len(voices))
    for voice in voices:
        print ("Name: %s, Description: %s, Id: %s, Locale: %s, Gender: %s, PublicVoice: %s, Created: %s" % (voice['name'], voice['description'], voice['id'], voice['locale'], voice['gender'], voice['isPublicVoice'], voice['created']))
```

コマンド `python voice_synthesis_client.py --voices -key <your_key> -region <region>` を使用してスクリプトを実行し、次の値を置き換えます。

* `<your_key>` をお使いの音声サービスのサブスクリプションに置き換えます。 この情報は、[Azure portal](https://aka.ms/azureportal) のご利用のリソースの **[概要]** タブで確認できます。
* `<region>` を音声リソースが作成された場所 (`eastus` や `westus` など) に置換します。 この情報は、[Azure portal](https://aka.ms/azureportal) のご利用のリソースの **[概要]** タブで確認できます。

次のような出力が表示されます。

```console
There are xx voices available:

Name: Microsoft Server Speech Text to Speech Voice (en-US, xxx), Description: xxx , Id: xxx, Locale: en-US, Gender: Male, PublicVoice: xxx, Created: 2019-07-22T09:38:14Z
Name: Microsoft Server Speech Text to Speech Voice (zh-CN, xxx), Description: xxx , Id: xxx, Locale: zh-CN, Gender: Female, PublicVoice: xxx, Created: 2019-08-26T04:55:39Z
```

**PublicVoice** パラメーターが **True** の場合、音声はパブリック ニューラル音声です。 それ以外の場合は、カスタム ニューラル音声です。

### <a name="convert-text-to-speech"></a>テキストを音声に変換する

プレーンテキストまたは SSML テキストで入力テキスト ファイルを準備してから、次のコードを `voice_synthesis_client.py` に追加します。

> [!NOTE]
> "concatenateResult" はオプション パラメーターです。 このパラメーターを設定しなかった場合、オーディオ出力が段落ごとに生成されます。 このパラメーターを設定することで、複数の音声を 1 つの出力に連結することもできます。 既定では、オーディオ出力は riff-16khz-16bit-mono-pcm に設定されています。 サポートされているオーディオ出力の詳細については、「[音声出力形式](https://docs.microsoft.com/azure/cognitive-services/speech-service/long-audio-api#audio-output-formats)」を参照してください。

```python
parser.add_argument('--submit', action="store_true", default=False, help='submit a synthesis request')
parser.add_argument('--concatenateResult', action="store_true", default=False, help='If concatenate result in a single wave file')
parser.add_argument('-file', action="store", dest="file", help='the input text script file path')
parser.add_argument('-voiceId', action="store", nargs='+', dest="voiceId", help='the id of the voice which used to synthesis')
parser.add_argument('-locale', action="store", dest="locale", help='the locale information like zh-CN/en-US')
parser.add_argument('-format', action="store", dest="format", default='riff-16khz-16bit-mono-pcm', help='the output audio format')

def submitSynthesis():
    modelList = args.voiceId
    data={'name': 'simple test', 'description': 'desc...', 'models': json.dumps(modelList), 'locale': args.locale, 'outputformat': args.format}
    if args.concatenateResult:
        properties={'ConcatenateResult': 'true'}
        data['properties'] = json.dumps(properties)
    if args.file is not None:
        scriptfilename=ntpath.basename(args.file)
        files = {'script': (scriptfilename, open(args.file, 'rb'), 'text/plain')}
    response = requests.post(baseAddress+"voicesynthesis", data, headers={"Ocp-Apim-Subscription-Key":args.key}, files=files, verify=False)
    if response.status_code == 202:
        location = response.headers['Location']
        id = location.split("/")[-1]
        print("Submit synthesis request successful")
        return id
    else:
        print("Submit synthesis request failed")
        print("response.status_code: %d" % response.status_code)
        print("response.text: %s" % response.text)
        return 0

def getSubmittedSynthesis(id):
    response=requests.get(baseAddress+"voicesynthesis/"+id, headers={"Ocp-Apim-Subscription-Key":args.key}, verify=False)
    synthesis = json.loads(response.text)
    return synthesis

if args.submit:
    id = submitSynthesis()
    if (id == 0):
        exit(1)

    while(1):
        print("\r\nChecking status")
        synthesis=getSubmittedSynthesis(id)
        if synthesis['status'] == "Succeeded":
            r = requests.get(synthesis['resultsUrl'])
            filename=id + ".zip"
            with open(filename, 'wb') as f:  
                f.write(r.content)
                print("Succeeded... Result file downloaded : " + filename)
            break
        elif synthesis['status'] == "Failed":
            print("Failed...")
            break
        elif synthesis['status'] == "Running":
            print("Running...")
        elif synthesis['status'] == "NotStarted":
            print("NotStarted...")
        time.sleep(10)
```

コマンド `python voice_synthesis_client.py --submit -key <your_key> -region <region> -file <input> -locale <locale> -voiceId <voice_guid>` を使用してスクリプトを実行し、次の値を置き換えます。

* `<your_key>` をお使いの音声サービスのサブスクリプションに置き換えます。 この情報は、[Azure portal](https://aka.ms/azureportal) のご利用のリソースの **[概要]** タブで確認できます。
* `<region>` を音声リソースが作成された場所 (`eastus` や `westus` など) に置換します。 この情報は、[Azure portal](https://aka.ms/azureportal) のご利用のリソースの **[概要]** タブで確認できます。
* `<input>` を、テキスト読み上げ用に準備したテキスト ファイルのパスに置換します。
* `<locale>` を必要な出力ロケールに置換します。 詳細については、「[言語サポート](language-support.md#neural-voices)」を参照してください。
* `<voice_guid>` を必要な出力音声に置換します。 `/voicesynthesis/voices` エンドポイントの前回の呼び出しで返された音声のいずれかを使用します。

次のような出力が表示されます。

```console
Submit synthesis request successful

Checking status
NotStarted...

Checking status
Running...

Checking status
Running...

Checking status
Succeeded... Result file downloaded : xxxx.zip
```

結果には、入力テキストと、サービスによって生成されたオーディオ出力ファイルが含まれます。 それらのファイルを zip 形式でダウンロードできます。

> [!NOTE]
> 複数の入力ファイルがある場合は、複数の要求を送信する必要があります。 注意すべきいくつかの制限事項があります。 
> * クライアントは、各 Azure サブスクリプション アカウントに対して 1 秒あたり最大 **5** 個の要求をサーバーに送信できます。 制限を超えると、クライアントはエラー コード 429 (要求が多すぎます) を受け取ります。 1 秒あたりの要求の数を減らしてください
> * サーバーは、各 Azure サブスクリプション アカウントに対して最大 **120** 個の要求を実行およびキューに登録できます。 制限を超えた場合、サーバーはエラー コード 429 (要求が多すぎます) を返します。 いくつかの要求が完了するまで、新しい要求を送信しないでお待ちください

### <a name="remove-previous-requests"></a>前の要求を削除する

このサービスは、Azure サブスクリプション アカウントごとに最大 **20,000** 個の要求を保持します。 この制限を要求の量が超える場合は、新しい要求を送信する前に、以前の要求を削除してください。 既存の要求を削除しない場合、エラー通知が返されます。

`voice_synthesis_client.py` に次のコードを追加します。

```python
parser.add_argument('--syntheses', action="store_true", default=False, help='print synthesis list')
parser.add_argument('--delete', action="store_true", default=False, help='delete a synthesis request')
parser.add_argument('-synthesisId', action="store", nargs='+', dest="synthesisId", help='the id of the voice synthesis which need to be deleted')

def getSubmittedSyntheses():
    response=requests.get(baseAddress+"voicesynthesis", headers={"Ocp-Apim-Subscription-Key":args.key}, verify=False)
    syntheses = json.loads(response.text)
    return syntheses

def deleteSynthesis(ids):
    for id in ids:
        print("delete voice synthesis %s " % id)
        response = requests.delete(baseAddress+"voicesynthesis/"+id, headers={"Ocp-Apim-Subscription-Key":args.key}, verify=False)
        if (response.status_code == 204):
            print("delete successful")
        else:
            print("delete failed, response.status_code: %d, response.text: %s " % (response.status_code, response.text))

if args.syntheses:
    synthese = getSubmittedSyntheses()
    print("There are %d synthesis requests submitted:" % len(synthese))
    for synthesis in synthese:
        print ("ID : %s , Name : %s, Status : %s " % (synthesis['id'], synthesis['name'], synthesis['status']))

if args.delete:
    deleteSynthesis(args.synthesisId)
```

`python voice_synthesis_client.py --syntheses -key <your_key> -region <region>` を実行して、行った合成要求の一覧を取得します。 次のような出力が表示されます。

```console
There are <number> synthesis requests submitted:
ID : xxx , Name : xxx, Status : Succeeded
ID : xxx , Name : xxx, Status : Running
ID : xxx , Name : xxx : Succeeded
```

要求を削除するには、`python voice_synthesis_client.py --delete -key <your_key> -region <Region> -synthesisId <synthesis_id>` を実行し、`<synthesis_id>` を前回の要求から返された要求 ID の値に置き換えます。

> [!NOTE]
> 状態が "実行中"/"待機中" の要求は削除できません。

完成した `voice_synthesis_client.py` は、[GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Python/voiceclient.py) で入手できます。

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
| 削除    | 400 | その音声合成タスクはまだ使用中です。 | 削除できるのは、**完了した**要求、または**失敗した要求**のみです。 |
| GetByID   | 404 | 指定されたエンティティが見つかりません。 | 合成 ID が正しいことを確認してください。 |

## <a name="regions-and-endpoints"></a>リージョンとエンドポイント

Long Audio API は、一意のエンドポイントを持つ複数のリージョンで使用できます。

| リージョン | エンドポイント |
|--------|----------|
| オーストラリア東部 | `https://australiaeast.customvoice.api.speech.microsoft.com` |
| カナダ中部 | `https://canadacentral.customvoice.api.speech.microsoft.com` |
| 米国東部 | `https://eastus.customvoice.api.speech.microsoft.com` |
| インド中部 | `https://centralindia.customvoice.api.speech.microsoft.com` |
| 米国中南部 | `https://southcentralus.customvoice.api.speech.microsoft.com` |
| 東南アジア | `https://southeastasia.customvoice.api.speech.microsoft.com` |
| 英国南部 | `https://uksouth.customvoice.api.speech.microsoft.com` |
| 西ヨーロッパ | `https://westeurope.customvoice.api.speech.microsoft.com` |
| 米国西部 2 | `https://westus2.customvoice.api.speech.microsoft.com` |

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

## <a name="sample-code"></a>サンプル コード
Long Audio API のサンプル コードは、GitHub で入手できます。

* [サンプル コード: Python](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/Python)
* [サンプル コード: C#](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/CSharp)
* [サンプル コード: Java](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/)
