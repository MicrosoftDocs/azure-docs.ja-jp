---
title: REST を使用した Bing Speech 認識 API の開始 | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: REST を使用して Microsoft Cognitive Services の音声認識 API にアクセスし、音声をテキストに変換します。
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: e962a12c6c27737f95e78e80036e51bac41147d5
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2019
ms.locfileid: "70965778"
---
# <a name="quickstart-use-the-bing-speech-recognition-rest-api"></a>クイック スタート:Bing Speech 認識 REST API を使用する

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

クラウドベースの Bing Speech Service を使用すれば、REST API を使用して音声をテキストに変換することにより、アプリケーションを開発できます。

## <a name="prerequisites"></a>前提条件

### <a name="subscribe-to-the-speech-api-and-get-a-free-trial-subscription-key"></a>Speech API をサブスクライブし、無料試用版のサブスクリプション キーを取得する

Speech API は、Cognitive Services (以前の Project Oxford) の一部です。 無料試用版のサブスクリプション キーは、[Cognitive Services サブスクリプション](https://azure.microsoft.com/try/cognitive-services/) ページから取得できます。 Speech API を選択したら、 **[Get API Key]** (API キーの取得) を選択してキーを取得します。 プライマリ キーおよびセカンダリ キーを返します。 両方のキーが同じクォータに関連付けられているため、どちらのキーでも使用できます。

> [!IMPORTANT]
>* サブスクリプション キーを取得します。 REST API にアクセスするには、その前に[サブスクリプション キー](https://azure.microsoft.com/try/cognitive-services/)を入手する必要があります。
>
>* サブスクリプション キーを使用します。 次の REST サンプルでは、YOUR_SUBSCRIPTION_KEY を自身のサブスクリプション キーに置き換えてください。
>
>* サブスクリプション キーの取得方法については、[認証](../how-to/how-to-authentication.md)ページをご覧ください。

### <a name="prerecorded-audio-file"></a>録音済みのオーディオ ファイル

この例では、録音されたオーディオ ファイルを使用して、REST API 使用方法について説明します。 短い句を自身で吹き込んでオーディオ ファイルを録音します。 「今日の天気はどうですか?」や 「おもしろい映画を見つけて」などです。 音声認識 API は、外部マイク入力もサポートしています。

> [!NOTE]
> 例では、音声は **PCM シングル チャンネル (モノラル)、16 KHz** の WAV ファイルとして録音する必要があります。

## <a name="build-a-recognition-request-and-send-it-to-the-speech-recognition-service"></a>認識要求をビルドし、音声認識サービスに送信する

音声認識の次の手順では、適切な要求ヘッダーと本文を含む POST 要求を、音声 HTTP エンドポイントに送信します。

### <a name="service-uri"></a>サービス URI

音声認識サービス URI は、[認識モード](../concepts.md#recognition-modes)と[認識言語](../concepts.md#recognition-languages)に基づいて定義されます。

```HTTP
https://speech.platform.bing.com/speech/recognition/<RECOGNITION_MODE>/cognitiveservices/v1?language=<LANGUAGE_TAG>&format=<OUTPUT_FORMAT>
```

`<RECOGNITION_MODE>` は、認識モードを指定し、`interactive`、`conversation`、`dictation` のいずれかの値にする必要があります。 それは URI 内の必須リソース パスです。 詳細については、[認識モード](../concepts.md#recognition-modes)に関するページをご覧ください。

`<LANGUAGE_TAG>` は、クエリ文字列内の必須パラメーターです。 これは、音声変換の対象言語を、たとえば英語 (米国) には `en-US` と定義します。 詳細については、[認識言語](../concepts.md#recognition-languages)に関するページをご覧ください。

`<OUTPUT_FORMAT>` は、クエリ文字列内のオプション パラメーターです。 使用できる値は、`simple` と `detailed` です。 既定では、サービスは `simple` 形式で結果を返します。 詳細については、[出力形式](../concepts.md#output-format)に関するページをご覧ください。

サービスの URI の一部の例が、次の表に一覧表示されます。

| 認識モード  | 言語 | 出力形式 | サービス URI |
|---|---|---|---|
| `interactive` | pt-BR | Default | https:\//speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=pt-BR |
| `conversation` | en-US | 詳細 | https:\//speech.platform.bing.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US&format=detailed |
| `dictation` | fr-FR | シンプル | https:\//speech.platform.bing.com/speech/recognition/dictation/cognitiveservices/v1?language=fr-FR&format=simple |

> [!NOTE]
> サービス URI は、アプリケーションでは、REST API を使用して、音声認識サービスを呼び出す場合にのみ必要です。 いずれかの[クライアント ライブラリ](GetStartedClientLibraries.md)を使用する場合、通常、どの URI が使用されるかを知っている必要はありません。 クライアント ライブラリは異なるサービス URI を使用することがあり、それぞれ特定のクライアント ライブラリにのみ適用できます。 詳しくは、ご使用のクライアント ライブラリをご覧ください。

### <a name="request-headers"></a>要求ヘッダー

次のフィールドは、要求ヘッダーで設定する必要があります。

- `Ocp-Apim-Subscription-Key`:毎回、サービスを呼び出すごとに、`Ocp-Apim-Subscription-Key` ヘッダーでサブスクリプション キーを渡す必要があります。 音声サービスは、サブスクリプション キーの代わりに承認トークンを渡すこともサポートしています。 詳細については、[認証](../How-to/how-to-authentication.md)に関するページをご覧ください。
- `Content-type`:`Content-type` フィールドには、音声ストリームの形式とコーデックが記されます。 現在、WAV ファイルと PCM Mono 16000 エンコードだけがサポートされています。 この形式のコンテンツの種類値は `audio/wav; codec=audio/pcm; samplerate=16000` です。

`Transfer-Encoding` フィールドは省略可能です。 このフィールドを `chunked` に設定すると、音声を小さなチャンクにオーディオを分割できます。 詳細については、[チャンク転送](../How-to/how-to-chunked-transfer.md)に関するページをご覧ください。

要求ヘッダーのサンプルを次に示します。

```HTTP
POST https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codec=audio/pcm; samplerate=16000
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: speech.platform.bing.com
Transfer-Encoding: chunked
Expect: 100-continue
```

### <a name="send-a-request-to-the-service"></a>サービスに要求を送信する

次の例では、音声 REST エンドポイントに音声認識要求を送信する方法を示します。 `interactive` 認識モードを使用します。

> [!NOTE]
> `YOUR_AUDIO_FILE` を、録音済みのオーディオ ファイルへのパスに置き換えます。 `YOUR_SUBSCRIPTION_KEY` を自身のサブスクリプション キーに置き換えます。

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```Powershell

$SpeechServiceURI =
'https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed'

# $OAuthToken is the authorization token returned by the token service.
$RecoRequestHeader = @{
  'Ocp-Apim-Subscription-Key' = 'YOUR_SUBSCRIPTION_KEY';
  'Transfer-Encoding' = 'chunked';
  'Content-type' = 'audio/wav; codec=audio/pcm; samplerate=16000'
}

# Read audio into byte array
$audioBytes = [System.IO.File]::ReadAllBytes("YOUR_AUDIO_FILE")

$RecoResponse = Invoke-RestMethod -Method POST -Uri $SpeechServiceURI -Headers $RecoRequestHeader -Body $audioBytes

# Show the result
$RecoResponse

```

# <a name="curltabcurl"></a>[curl](#tab/curl)

例では、Bash とともに Linux で curl を使用します。 お使いのプラットフォームで使用できない場合、curl をインストールする必要がある場合があります。 この例は、Windows、Git Bash、zsh、およびその他のシェル上の Cygwin でも機能します。

> [!NOTE]
> `--data-binary` の値がデータではなくファイル名であることを示すので、`YOUR_AUDIO_FILE` を録音済みのオーディオ ファイルへのパスに置き換えるときに、オーディオ ファイル名の前に `@` を保持します。

```
curl -v -X POST "https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed" -H "Transfer-Encoding: chunked" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY" -H "Content-type: audio/wav; codec=audio/pcm; samplerate=16000" --data-binary @YOUR_AUDIO_FILE
```

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```cs
HttpWebRequest request = null;
request = (HttpWebRequest)HttpWebRequest.Create(requestUri);
request.SendChunked = true;
request.Accept = @"application/json;text/xml";
request.Method = "POST";
request.ProtocolVersion = HttpVersion.Version11;
request.ContentType = @"audio/wav; codec=audio/pcm; samplerate=16000";
request.Headers["Ocp-Apim-Subscription-Key"] = "YOUR_SUBSCRIPTION_KEY";

// Send an audio file by 1024 byte chunks
using (FileStream fs = new FileStream(YOUR_AUDIO_FILE, FileMode.Open, FileAccess.Read))
{

    /*
    * Open a request stream and write 1024 byte chunks in the stream one at a time.
    */
    byte[] buffer = null;
    int bytesRead = 0;
    using (Stream requestStream = request.GetRequestStream())
    {
        /*
        * Read 1024 raw bytes from the input audio file.
        */
        buffer = new Byte[checked((uint)Math.Min(1024, (int)fs.Length))];
        while ((bytesRead = fs.Read(buffer, 0, buffer.Length)) != 0)
        {
            requestStream.Write(buffer, 0, bytesRead);
        }

        // Flush
        requestStream.Flush();
    }
}
```

---

## <a name="process-the-speech-recognition-response"></a>音声認識の応答を処理する

要求を処理した後は、音声サービスは、JSON 形式として、応答で結果を返します。

> [!NOTE]
> 上記のコードでエラーが返された場合は、[トラブルシューティング](../troubleshooting.md)を参照して、考えられる原因を特定してください。

次のコード スニペットは、ストリームからの応答の読み取り方法を示します。

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```Powershell
# show the response in JSON format
ConvertTo-Json $RecoResponse
```

# <a name="curltabcurl"></a>[curl](#tab/curl)

この例では、curl は直接、応答メッセージを文字列で返します。 JSON 形式で表示する場合、jq などのその他のツールを使用できます。

```
curl -X POST "https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed" -H "Transfer-Encoding: chunked" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY" -H "Content-type: audio/wav; codec=audio/pcm; samplerate=16000" --data-binary @YOUR_AUDIO_FILE | jq
```

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```cs
/*
* Get the response from the service.
*/
Console.WriteLine("Response:");
using (WebResponse response = request.GetResponse())
{
    Console.WriteLine(((HttpWebResponse)response).StatusCode);

    using (StreamReader sr = new StreamReader(response.GetResponseStream()))
    {
        responseString = sr.ReadToEnd();
    }

    Console.WriteLine(responseString);
    Console.ReadLine();
}
```

---

次の例は、JSON 応答を示します。

```json
OK
{
  "RecognitionStatus": "Success",
  "Offset": 22500000,
  "Duration": 21000000,
  "NBest": [{
    "Confidence": 0.941552162,
    "Lexical": "find a funny movie to watch",
    "ITN": "find a funny movie to watch",
    "MaskedITN": "find a funny movie to watch",
    "Display": "Find a funny movie to watch."
  }]
}
```

## <a name="limitations"></a>制限事項

REST API にはいくつかの制限があります。

- サポートするオーディオ ストリームは最大 15 秒です。
- 認識中の中間結果はサポートしません。 ユーザーは、最終的な認識結果のみを受信します。

これらの制限を削除するには、音声[クライアント ライブラリ](GetStartedClientLibraries.md)を使用します。 または、[音声 WebSocket プロトコル](../API-Reference-REST/websocketprotocol.md)で直接操作できます。

## <a name="whats-next"></a>参照トピック

- C# や Java などの REST API の使用方法を確認するには、これらの[サンプル アプリケーション](../samples.md)をご覧ください。
- エラーを探し修正するには、[トラブルシューティング](../troubleshooting.md)に関するページをご覧ください。
- 高度な機能を使用するには、音声[クライアント ライブラリ](GetStartedClientLibraries.md)を使用して開始する方法を参照してください。

### <a name="license"></a>License

すべての Cognitive Services SDK およびサンプルは、MIT ライセンスがあります。 詳しくは、[ライセンス](https://github.com/Microsoft/Cognitive-Speech-STT-JavaScript/blob/master/LICENSE.md)に関するページをご覧ください。
