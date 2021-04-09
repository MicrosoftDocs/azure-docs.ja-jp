---
title: Text to Speech API リファレンス (REST) - Speech Service
titleSuffix: Azure Cognitive Services
description: Text to Speech REST API の使用方法について説明します。 この記事では、認可のオプションとクエリのオプション、さらに要求を構築する方法と応答を受信する方法について説明します。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/08/2021
ms.author: trbye
ms.custom: references_regions
ms.openlocfilehash: 9da83746cdaf693922b88841cd9c0fac432611c9
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104870842"
---
# <a name="text-to-speech-rest-api"></a>Text to Speech REST API

Speech Service では、一連の REST API を使用して、[テキストを合成音声に変換](#convert-text-to-speech)したり、[リージョンでサポートされている音声の一覧を取得](#get-a-list-of-voices)したりできます。 利用可能な各エンドポイントは、リージョンに関連付けられています。 使用を検討しているエンドポイント/リージョンのサブスクリプション キーが必要です。

Text to Speech REST API ではニューラルと標準のテキスト読み上げ音声が提供され、そのいずれでもロケールで識別される特定の言語と方言がサポートされています。

* 音声の全一覧については、[言語のサポート](language-support.md#text-to-speech)に関するページを参照してください。
* リージョン別の提供状況については、[リージョン](regions.md#text-to-speech)に関するページを参照してください。

> [!IMPORTANT]
> 料金は、標準音声、カスタム音声、ニューラル音声によって異なります。 詳細については、[価格](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)に関するページをご覧ください。

この API を使用する前に、次の点を理解しておいてください。

* Text to Speech REST API には、Authorization ヘッダーが必要です。 つまり、このサービスにアクセスするためには、トークンの交換が済んでいる必要があります。 詳細については、[認証](#authentication)に関するページをご覧ください。

> [!TIP]
> Azure Government と Azure China のエンドポイントについては、[こちらの記事](sovereign-clouds.md)を参照してください。

[!INCLUDE [](../../../includes/cognitive-services-speech-service-rest-auth.md)]

## <a name="get-a-list-of-voices"></a>音声の一覧を取得する

`voices/list` エンドポイントでは、特定のリージョン/エンドポイントの音声の完全な一覧を取得できます。

### <a name="regions-and-endpoints"></a>リージョンとエンドポイント

| リージョン | エンドポイント |
|--------|----------|
| オーストラリア東部 | `https://australiaeast.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| ブラジル南部 | `https://brazilsouth.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| カナダ中部 | `https://canadacentral.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| 米国中部 | `https://centralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| 東アジア | `https://eastasia.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| 米国東部 | `https://eastus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| 米国東部 2 | `https://eastus2.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| フランス中部 | `https://francecentral.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| インド中部 | `https://centralindia.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| 東日本 | `https://japaneast.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| 韓国中部 | `https://koreacentral.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| 米国中北部 | `https://northcentralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| 北ヨーロッパ | `https://northeurope.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| 南アフリカ北部 | `https://southafricanorth.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| 米国中南部 | `https://southcentralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| 東南アジア | `https://southeastasia.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| 英国南部 | `https://uksouth.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| 米国中西部 | `https://westcentralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| 西ヨーロッパ | `https://westeurope.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| 米国西部 | `https://westus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| 米国西部 2 | `https://westus2.tts.speech.microsoft.com/cognitiveservices/voices/list` |

> [!TIP]
> [プレビュー段階の音声](language-support.md#neural-voices-in-preview)は 3 つのリージョン (米国東部、西ヨーロッパ、東南アジア) でのみ使用できます。

### <a name="request-headers"></a>要求ヘッダー

この表は、テキスト読み上げ要求の必須のヘッダーと省略可能なヘッダーの一覧です。

| ヘッダー | 説明 | 必須/省略可能 |
|--------|-------------|---------------------|
| `Ocp-Apim-Subscription-Key` | 音声サービスのサブスクリプション キー。 | このヘッダーと `Authorization` のどちらかが必須となります。 |
| `Authorization` | 単語 `Bearer` が前に付いた認証トークン。 詳細については、[認証](#authentication)に関するページをご覧ください。 | このヘッダーと `Ocp-Apim-Subscription-Key` のどちらかが必須となります。 |



### <a name="request-body"></a>要求本文

このエンドポイントへの `GET` 要求の本文は不要です。

### <a name="sample-request"></a>要求のサンプル

この要求には Authorization ヘッダーのみが必要です。

```http
GET /cognitiveservices/voices/list HTTP/1.1

Host: westus.tts.speech.microsoft.com
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
```

### <a name="sample-response"></a>応答のサンプル

この応答は、応答の構造を説明するために切り詰められています。

> [!NOTE]
> 利用可能な音声はリージョン/エンドポイントによって異なります。

```json
[

    {
    "Name": "Microsoft Server Speech Text to Speech Voice (en-US, AriaNeural)",
    "DisplayName": "Aria",
    "LocalName": "Aria",
    "ShortName": "en-US-AriaNeural",
    "Gender": "Female",
    "Locale": "en-US",
    "StyleList": [
      "chat",
      "customerservice",
      "newscast-casual",
      "newscast-formal",
      "cheerful",
      "empathetic"
    ],
    "SampleRateHertz": "24000",
    "VoiceType": "Neural",
    "Status": "GA"
  },

  ...

     {
    "Name": "Microsoft Server Speech Text to Speech Voice (ga-IE, OrlaNeural)",
    "DisplayName": "Orla",
    "LocalName": "Orla",
    "ShortName": "ga-IE-OrlaNeural",
    "Gender": "Female",
    "Locale": "ga-IE",
    "SampleRateHertz": "24000",
    "VoiceType": "Neural",
    "Status": "Preview"
  },

  ...

   {
    "Name": "Microsoft Server Speech Text to Speech Voice (zh-CN, YunxiNeural)",
    "DisplayName": "Yunxi",
    "LocalName": "云希",
    "ShortName": "zh-CN-YunxiNeural",
    "Gender": "Male",
    "Locale": "zh-CN",
    "StyleList": [
      "Calm",
      "Fearful",
      "Cheerful",
      "Disgruntled",
      "Serious",
      "Angry",
      "Sad",
      "Depressed",
      "Embarrassed"
    ],
    "SampleRateHertz": "24000",
    "VoiceType": "Neural",
    "Status": "Preview"
  },

    ...

   {
    "Name": "Microsoft Server Speech Text to Speech Voice (ar-EG, Hoda)",
    "DisplayName": "Hoda",
    "LocalName": "هدى",
    "ShortName": "ar-EG-Hoda",
    "Gender": "Female",
    "Locale": "ar-EG",
    "SampleRateHertz": "16000",
    "VoiceType": "Standard",
    "Status": "GA"
  },

...

]
```

### <a name="http-status-codes"></a>HTTP 状態コード

各応答の HTTP 状態コードは、成功または一般的なエラーを示します。

| HTTP 状態コード | 説明 | 考えられる理由 |
|------------------|-------------|-----------------|
| 200 | [OK] | 要求は成功しました。 |
| 400 | 正しくない要求 | 必須パラメーターが指定されていない、空、または null です。 または、必須またはオプションのパラメーターに渡された値が無効です。 よくある問題はヘッダーが長すぎることです。 |
| 401 | 権限がありません | 要求が承認されていません。 サブスクリプション キーまたはトークンが有効であり、正しいリージョンにあることを確認してください。 |
| 429 | 要求が多すぎます | 使用中のサブスクリプションで許可されている要求のクォータまたは速度を超えています。 |
| 502 | 無効なゲートウェイ    | ネットワークまたはサーバー側の問題です。 無効なヘッダーを示す場合もあります。 |


## <a name="convert-text-to-speech"></a>テキストを音声に変換する

`v1` エンドポイントでは、[音声合成マークアップ言語 (SSML)](speech-synthesis-markup.md) を使用してテキストを音声に変換することができます。

### <a name="regions-and-endpoints"></a>リージョンとエンドポイント

この REST API を使用したテキスト読み上げは、以下のリージョンでサポートされます。 必ず、ご利用のサブスクリプションのリージョンと一致するエンドポイントを選択してください。

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]

### <a name="request-headers"></a>要求ヘッダー

この表は、テキスト読み上げ要求の必須のヘッダーと省略可能なヘッダーの一覧です。

| ヘッダー | 説明 | 必須/省略可能 |
|--------|-------------|---------------------|
| `Authorization` | 単語 `Bearer` が前に付いた認証トークン。 詳細については、[認証](#authentication)に関するページをご覧ください。 | 必須 |
| `Content-Type` | 指定したテキストのコンテンツ タイプを指定します。 指定できる値は `application/ssml+xml` です。 | 必須 |
| `X-Microsoft-OutputFormat` | オーディオ出力形式を指定します。 指定できる値の全一覧については、[オーディオ出力](#audio-outputs)に関するセクションを参照してください。 | 必須 |
| `User-Agent` | アプリケーション名です。 指定する値は 255 文字未満にする必要があります。 | 必須 |

### <a name="audio-outputs"></a>オーディオ出力

以下に示したのは、それぞれの要求の `X-Microsoft-OutputFormat` ヘッダーで送信することができるオーディオ形式の一覧です。 それぞれビットレートとエンコードの種類が含まれています。 Speech Service では、24 kHz、16 kHz、および 8 kHz のオーディオ出力がサポートされます。

```output
raw-16khz-16bit-mono-pcm            raw-8khz-8bit-mono-mulaw
riff-8khz-8bit-mono-alaw            riff-8khz-8bit-mono-mulaw
riff-16khz-16bit-mono-pcm           audio-16khz-128kbitrate-mono-mp3
audio-16khz-64kbitrate-mono-mp3     audio-16khz-32kbitrate-mono-mp3
raw-24khz-16bit-mono-pcm            riff-24khz-16bit-mono-pcm
audio-24khz-160kbitrate-mono-mp3    audio-24khz-96kbitrate-mono-mp3
audio-24khz-48kbitrate-mono-mp3     ogg-24khz-16bit-mono-opus
raw-48khz-16bit-mono-pcm            riff-48khz-16bit-mono-pcm
audio-48khz-96kbitrate-mono-mp3     audio-48khz-192kbitrate-mono-mp3
webm-16khz-16bit-mono-opus          webm-24khz-16bit-mono-opus
```

> [!NOTE]
> 選択した音声と出力形式のビット レートが異なる場合、オーディオは必要に応じて再サンプリングされます。
> ogg-24khz-16bit-mono-opus は [OPUS コーデック](https://opus-codec.org/downloads/)でデコードできます

### <a name="request-body"></a>要求本文

各 `POST` 要求の本文は[音声合成マークアップ言語 (SSML)](speech-synthesis-markup.md) として送信されます。 SSML では、テキスト読み上げサービスによって返される合成音声の声と言語を選択できます。 サポートされている声の全一覧については、[言語のサポート](language-support.md#text-to-speech)に関するページを参照してください。

> [!NOTE]
> カスタム音声を使用する場合は、プレーン テキスト (ASCII または UTF-8) として要求の本文を送信できます。

### <a name="sample-request"></a>要求のサンプル

この HTTP 要求は、SSML を使用して音声と言語を指定します。 本文が長すぎて、生成されるオーディオが 10 分を超える場合は、10 分に切り捨てられます。 つまり、オーディオの長さが 10 分を超えることはできません。

```http
POST /cognitiveservices/v1 HTTP/1.1

X-Microsoft-OutputFormat: raw-24khz-16bit-mono-pcm
Content-Type: application/ssml+xml
Host: westus.tts.speech.microsoft.com
Content-Length: 225
Authorization: Bearer [Base64 access_token]

<speak version='1.0' xml:lang='en-US'><voice xml:lang='en-US' xml:gender='Female'
    name='en-US-AriaNeural'>
        Microsoft Speech Service Text-to-Speech API
</voice></speak>
```

### <a name="http-status-codes"></a>HTTP 状態コード

各応答の HTTP 状態コードは、成功または一般的なエラーを示します。

| HTTP 状態コード | 説明 | 考えられる理由 |
|------------------|-------------|-----------------|
| 200 | [OK] | 要求は成功しました。応答本文はオーディオ ファイルです。 |
| 400 | 正しくない要求 | 必須パラメーターが指定されていない、空、または null です。 または、必須またはオプションのパラメーターに渡された値が無効です。 よくある問題はヘッダーが長すぎることです。 |
| 401 | 権限がありません | 要求が承認されていません。 サブスクリプション キーまたはトークンが有効であり、正しいリージョンにあることを確認してください。 |
| 415 | サポートされていないメディアの種類です | 間違った `Content-Type` が提供された可能性があります。 `Content-Type` は `application/ssml+xml` に設定する必要があります。 |
| 429 | 要求が多すぎます | 使用中のサブスクリプションで許可されている要求のクォータまたは速度を超えています。 |
| 502 | 無効なゲートウェイ    | ネットワークまたはサーバー側の問題です。 無効なヘッダーを示す場合もあります。 |

HTTP ステータスが `200 OK` の場合、応答の本文には要求された形式のオーディオ ファイルが含まれています。 このファイルは、転送と同時に再生することも、バッファーまたはファイルに保存することもできます。

## <a name="next-steps"></a>次のステップ

- [無料の Azure アカウントを作成](https://azure.microsoft.com/free/cognitive-services/)してください
- [長い形式の音声の非同期合成](./long-audio-api.md)
- [カスタム音声の概要](how-to-custom-voice.md)
