---
title: Speech Services の REST API シリーズ - Speech Service
titleSuffix: Azure Cognitive Services
description: Speech to Text REST API と Text to Speech REST API の使用方法について説明します。 この記事では、認可のオプションとクエリのオプション、さらに要求を構築する方法と応答を受信する方法について説明します。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 0ce33f20d44ac284655569ff66825533650b9d9c
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/11/2019
ms.locfileid: "55998947"
---
# <a name="speech-service-rest-apis"></a>音声サービスの REST API

[Speech SDK](speech-sdk.md) に代わる方法として、Speech Service は、一連の REST API を使用した音声テキスト変換とテキスト読み上げに対応しています。 アクセス可能な各エンドポイントは、リージョンに関連付けられています。 アプリケーションには、使用を検討しているエンドポイントのサブスクリプション キーが必要となります。

REST API を使用する前に、次の点を理解しておいてください。
* REST API を使用した音声テキスト変換要求が保持できる録音されたオーディオは 10 秒までです。
* Speech to Text REST API から返すことができるのは最終的な結果だけです。 結果を部分的に得ることはできません。
* Text to Speech REST API には、Authorization ヘッダーが必要です。 つまり、このサービスにアクセスするためには、トークンの交換が済んでいる必要があります。 詳細については、[認証](#authentication)に関するページをご覧ください。

## <a name="authentication"></a>Authentication

Speech to Text REST API または Text to Speech REST API に対して要求を行う際は、その都度 Authorization ヘッダーが必要となります。 次の表は、各サービスでサポートされているヘッダーを示したものです。

| サポートされている Authorization ヘッダー | 音声テキスト変換 | テキスト読み上げ |
|------------------------|----------------|----------------|
| Ocp-Apim-Subscription-Key | はい | いいえ |
| Authorization:ベアラー | はい | はい |

`Ocp-Apim-Subscription-Key` ヘッダーを使用する場合、指定する必要があるのはサブスクリプション キーだけです。 例: 

```
'Ocp-Apim-Subscription-Key': 'YOUR_SUBSCRIPTION_KEY'
```

`Authorization: Bearer` ヘッダーを使用する場合は、`issueToken` エンドポイントに対して要求を実行する必要があります。 この要求では、サブスクリプション キーを、10 分間有効なアクセス トークンと交換します。 以降のいくつかのセクションでは、トークンの取得方法と使用方法、更新方法について説明します。

### <a name="how-to-get-an-access-token"></a>アクセス トークンを取得する方法

アクセス トークンを取得するには、`Ocp-Apim-Subscription-Key` とサブスクリプション キーを使用して、`issueToken` エンドポイントに対して要求を実行する必要があります。

次のリージョンとエンドポイントがサポートされています。

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-token-service.md)]

アクセス トークン要求の作成にあたっては、以下のサンプルを使用してください。

#### <a name="http-sample"></a>HTTP のサンプル

この例は、トークンを取得するための単純な HTTP 要求です。 `YOUR_SUBSCRIPTION_KEY` は、お使いの Speech Service のサブスクリプション キーに置き換えてください。 お使いのサブスクリプションが米国西部リージョンにない場合は、`Host` ヘッダーをご自身のリージョンのホスト名に置き換えます。

```http
POST /sts/v1.0/issueToken HTTP/1.1
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.api.cognitive.microsoft.com
Content-type: application/x-www-form-urlencoded
Content-Length: 0
```

応答の本文には、JSON Web トークン (JWT) 形式のアクセス トークンが格納されます。

#### <a name="powershell-sample"></a>PowerShell のサンプル

この例は、アクセス トークンを取得するための単純な PowerShell スクリプトです。 `YOUR_SUBSCRIPTION_KEY` は、お使いの Speech Service のサブスクリプション キーに置き換えてください。 必ず、実際のサブスクリプションに合ったリージョンの正しいエンドポイントを使用してください。 この例では現在、米国西部に設定されています。

```Powershell
$FetchTokenHeader = @{
  'Content-type'='application/x-www-form-urlencoded';
  'Content-Length'= '0';
  'Ocp-Apim-Subscription-Key' = 'YOUR_SUBSCRIPTION_KEY'
}

$OAuthToken = Invoke-RestMethod -Method POST -Uri https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken
 -Headers $FetchTokenHeader

# show the token received
$OAuthToken

```

#### <a name="curl-sample"></a>cURL のサンプル

cURL は Linux (および Windows Subsystem for Linux) で使用できるコマンドライン ツールです。 この cURL コマンドは、アクセス トークンを取得する方法を示しています。 `YOUR_SUBSCRIPTION_KEY` は、お使いの Speech Service のサブスクリプション キーに置き換えてください。 必ず、実際のサブスクリプションに合ったリージョンの正しいエンドポイントを使用してください。 この例では現在、米国西部に設定されています。

```cli
curl -v -X POST
 "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken" \
 -H "Content-type: application/x-www-form-urlencoded" \
 -H "Content-Length: 0" \
 -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

#### <a name="c-sample"></a>C# のサンプル

この C# クラスは、アクセス トークンを取得する方法を示しています。 クラスをインスタンス化するときに、お使いの Speech Service のサブスクリプション キーを渡す必要があります。 お使いのサブスクリプションが米国西部リージョンにない場合は、実際のサブスクリプションのリージョンに合わせて `FetchTokenUri` の値を変更してください。

```cs
/*
    * This class demonstrates how to get a valid access token.
    */
public class Authentication
{
    public static readonly string FetchTokenUri =
        "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken";
    private string subscriptionKey;
    private string token;

    public Authentication(string subscriptionKey)
    {
        this.subscriptionKey = subscriptionKey;
        this.token = FetchTokenAsync(FetchTokenUri, subscriptionKey).Result;
    }

    public string GetAccessToken()
    {
        return this.token;
    }

    private async Task<string> FetchTokenAsync(string fetchUri, string subscriptionKey)
    {
        using (var client = new HttpClient())
        {
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
            UriBuilder uriBuilder = new UriBuilder(fetchUri);

            var result = await client.PostAsync(uriBuilder.Uri.AbsoluteUri, null);
            Console.WriteLine("Token Uri: {0}", uriBuilder.Uri.AbsoluteUri);
            return await result.Content.ReadAsStringAsync();
        }
    }
}
```

### <a name="how-to-use-an-access-token"></a>アクセス トークンを使用する方法

このサービスには、アクセス トークンを `Authorization: Bearer <TOKEN>` ヘッダーとして送信する必要があります。 各アクセス トークンは 10 分間有効です。 新しいトークンはいつでも取得できますが、ネットワークのトラフィックと待ち時間を最小限に抑えるために、同じトークンを 9 分間使用することをお勧めします。

次に示したのは、Text to Speech REST API に対するサンプル HTTP 要求です。

```http
POST /cognitiveservices/v1 HTTP/1.1
Authorization: Bearer YOUR_ACCESS_TOKEN
Host: westus.tts.speech.microsoft.com
Content-type: application/ssml+xml
Content-Length: 199
Connection: Keep-Alive

<speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice name='Microsoft Server Speech Text to Speech Voice (en-US, Jessa24kRUS)'>
    Hello, world!
</voice></speak>
```

### <a name="how-to-renew-an-access-token-using-c"></a>C# を使用してアクセス トークンを更新する方法

この C# コードは前出のクラスの代わりとして、そのまま使用することができます。 `Authentication` クラスはタイマーを使用して 9 分ごとに新しいアクセス トークンを自動的に取得します。 この方法により、プログラムの実行中に有効なトークンが常に利用されます。

> [!NOTE]
> タイマーを使用する代わりに、最後のトークンが取得されたときのタイムスタンプを保存することができます。 その場合、期限切れが近い場合にのみ新しいトークンを要求できます。 この方法により不要な新しいトークンを要求することを防ぎます。これは、音声の要求を不定期に行うプログラムにより適している可能性があります。

前述のように、`FetchTokenUri` の値をサブスクリプション リージョンと一致させてください。 クラスをインスタンス化するときにサブスクリプション キーを渡します。

```cs
public class Authentication
{
    public static readonly string FetchTokenUri =
        "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken";
    private string subscriptionKey;
    private string token;
    private Timer accessTokenRenewer;

    //Access token expires every 10 minutes. Renew it every 9 minutes.
    private const int RefreshTokenDuration = 9;

    public Authentication(string subscriptionKey)
    {
        this.subscriptionKey = subscriptionKey;
        this.token = FetchToken(FetchTokenUri, subscriptionKey).Result;

        // renew the token on set duration.
        accessTokenRenewer = new Timer(new TimerCallback(OnTokenExpiredCallback),
                                        this,
                                        TimeSpan.FromMinutes(RefreshTokenDuration),
                                        TimeSpan.FromMilliseconds(-1));
    }

    public string GetAccessToken()
    {
        return this.token;
    }

    private void RenewAccessToken()
    {
        this.token = FetchToken(FetchTokenUri, this.subscriptionKey).Result;
        Console.WriteLine("Renewed token.");
    }

    private void OnTokenExpiredCallback(object stateInfo)
    {
        try
        {
            RenewAccessToken();
        }
        catch (Exception ex)
        {
            Console.WriteLine(string.Format("Failed renewing access token. Details: {0}", ex.Message));
        }
        finally
        {
            try
            {
                accessTokenRenewer.Change(TimeSpan.FromMinutes(RefreshTokenDuration), TimeSpan.FromMilliseconds(-1));
            }
            catch (Exception ex)
            {
                Console.WriteLine(string.Format("Failed to reschedule the timer to renew access token. Details: {0}", ex.Message));
            }
        }
    }

    private async Task<string> FetchToken(string fetchUri, string subscriptionKey)
    {
        using (var client = new HttpClient())
        {
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
            UriBuilder uriBuilder = new UriBuilder(fetchUri);

            var result = await client.PostAsync(uriBuilder.Uri.AbsoluteUri, null);
            Console.WriteLine("Token Uri: {0}", uriBuilder.Uri.AbsoluteUri);
            return await result.Content.ReadAsStringAsync();
        }
    }
}
```

## <a name="speech-to-text-api"></a>Speech to Text API

Speech to Text REST API でサポートされるのは短い発話のみです。 要求に含めることができるオーディオは最大 10 秒で、要求に許容される継続時間は合計 14 秒となります。 REST API は最終的な結果のみを返し、部分的な結果や中間の結果が返されることはありません。

それを超える長さのオーディオをアプリケーションで送信する必要がある場合は、[Speech SDK](speech-sdk.md) または[バッチ文字起こし](batch-transcription.md)の使用を検討してください。

### <a name="regions-and-endpoints"></a>リージョンとエンドポイント

この REST API を使用した音声テキスト変換による文字起こしは、次のリージョンでサポートされます。 必ず、ご利用のサブスクリプションのリージョンと一致するエンドポイントを選択してください。

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)]

### <a name="query-parameters"></a>クエリ パラメーター

REST 要求のクエリ文字列には、次のパラメーターを含めることができます。

| パラメーター | 説明 | 必須/省略可能 |
|-----------|-------------|---------------------|
| `language` | 認識の対象として発話された言語を識別します。 [サポートされている言語](language-support.md#speech-to-text)を参照してください。 | 必須 |
| `format` | 結果の形式を指定します。 指定できる値は、`simple` と `detailed` です。 単純な結果には `RecognitionStatus`、`DisplayText`、`Offset`、`Duration`が含まれます。 詳細な応答には、信頼度の値と 4 つの異なる表現を持った複数の結果が含まれます。 既定の設定は `simple`です。 | 省略可能 |
| `profanity` | 認識結果内の不適切な表現をどう扱うかを指定します。 指定できる値は、`masked` (不適切な表現をアスタリスクに置き換える)、`removed` (すべての不適切な表現を結果から除去する)、または `raw` (不適切な表現を結果に含める) です。 既定の設定は `masked`です。 | 省略可能 |

### <a name="request-headers"></a>要求ヘッダー

この表は、音声テキスト変換要求の必須のヘッダーと省略可能なヘッダーの一覧です。

|ヘッダー| 説明 | 必須/省略可能 |
|------|-------------|---------------------|
| `Ocp-Apim-Subscription-Key` | Speech Service のサブスクリプション キー。 | このヘッダーと `Authorization` のどちらかが必須となります。 |
| `Authorization` | 単語 `Bearer` が前に付いた認証トークン。 詳細については、[認証](#authentication)に関するページをご覧ください。 | このヘッダーと `Ocp-Apim-Subscription-Key` のどちらかが必須となります。 |
| `Content-type` | 指定したオーディオ データの形式とコーデックを記述します。 指定できる値は、`audio/wav; codecs=audio/pcm; samplerate=16000` と `audio/ogg; codecs=opus` です。 | 必須 |
| `Transfer-Encoding` | オーディオを個別のファイルとしてではなくチャンク データとして送信することを指定します。 このヘッダーは、オーディオ データをチャンクにする場合にのみ使用してください。 | 省略可能 |
| `Expect` | チャンク転送を使用する場合、`Expect: 100-continue` を送信します。 Speech Service は最初の要求を確認し、追加のデータを待ちます。| オーディオのチャンク データを送信する場合は必須となります。 |
| `Accept` | 指定する場合は、`application/json` とする必要があります。 Speech Service からは、結果が JSON 形式で返されます。 指定しない場合、一部の Web 要求フレームワークでは互換性のない既定値が提供されるため、常に `Accept` を含めることをお勧めします。 | 省略可能ですが、指定することをお勧めします。 |

### <a name="audio-formats"></a>オーディオの形式

オーディオは HTTP `POST` 要求の本文で送信されます。 この表内のいずれかの形式にする必要があります。

| 形式 | コーデック | Bitrate | サンプル レート |
|--------|-------|---------|-------------|
| WAV | PCM 0 | 16 ビット | 16 kHz、モノラル |
| OGG | OPUS | 16 ビット | 16 kHz、モノラル |

>[!NOTE]
>上の形式は、Speech Service の REST API と WebSocket を介してサポートされます。 現在、[Speech SDK](speech-sdk.md) では PCM コーデックの WAV 形式のみがサポートされています。

### <a name="sample-request"></a>要求のサンプル

これは一般的な HTTP 要求です。 以下のサンプルには、ホスト名と必須のヘッダーが含まれています。 サービスにはオーディオ データも必要であることに注意してください。このサンプルにオーディオ データは含まれていません。 前述のようにチャンクにすることをお勧めしますが、必須ではありません。

```HTTP
POST speech/recognition/conversation/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codecs=audio/pcm; samplerate=16000
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.stt.speech.microsoft.com
Transfer-Encoding: chunked
Expect: 100-continue
```

### <a name="http-status-codes"></a>HTTP 状態コード

各応答の HTTP 状態コードは、成功または一般的なエラーを示します。

| HTTP 状態コード | 説明 | 考えられる理由 |
|------------------|-------------|-----------------|
| 100 | Continue | 最初の要求が受け付けられました。 残りのデータの送信を続行します。 (チャンク転送で使用されます。) |
| 200 | OK | 要求は成功しました。応答本文は JSON オブジェクトです。 |
| 400 | 正しくない要求 | 言語コードが提供されていないか、サポートされていない言語です。無効なオーディオファイルです。 |
| 401 | 権限がありません | サブスクリプション キーまたは認証トークンが指定のリージョンで無効であるか、または無効なエンドポイントです。 |
| 403 | 許可されていません | サブスクリプション キーまたは認証トークンがありません。 |

### <a name="chunked-transfer"></a>チャンク転送

チャンク転送 (`Transfer-Encoding: chunked`) を使用すると、オーディオ ファイルの送信中に Speech Service がオーディオ ファイルの処理を開始できるようになるため、認識待ち時間の短縮に役立ちます。 REST API は部分的または中間的な結果を提供しません。 このオプションは、単に応答性を向上させるためのものです。

このコード サンプルは、オーディオをチャンクで送信する方法を示しています。 最初のチャンクだけに、オーディオ ファイルのヘッダーが含まれている必要があります。 `request` は、適切な REST エンドポイントに接続された HTTPWebRequest オブジェクトです。 `audioFile` はディスク上のオーディオ ファイルのパスです。

```csharp

    HttpWebRequest request = null;
    request = (HttpWebRequest)HttpWebRequest.Create(requestUri);
    request.SendChunked = true;
    request.Accept = @"application/json;text/xml";
    request.Method = "POST";
    request.ProtocolVersion = HttpVersion.Version11;
    request.Host = host;
    request.ContentType = @"audio/wav; codecs=audio/pcm; samplerate=16000";
    request.Headers["Ocp-Apim-Subscription-Key"] = args[1];
    request.AllowWriteStreamBuffering = false;

using (fs = new FileStream(audioFile, FileMode.Open, FileAccess.Read))
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

### <a name="response-parameters"></a>応答パラメーター

結果は JSON 形式で返されます。 `simple` 形式には、次の最上位レベル フィールドが含まれます。

| パラメーター | 説明  |
|-----------|--------------|
|`RecognitionStatus`|認識成功を表す `Success` などのステータス。 次の表を参照してください。|
|`DisplayText`|大文字化、句読点、逆テキスト正規化 (「two hundred」から「200」に、または「doctor smith」から「Dr. Smith」のように、音声テキストをより短い形式に変換すること)、および不適切な表現のマスキングの後に認識されたテキスト。 成功時にのみ存在します。|
|`Offset`|認識された音声がオーディオ ストリーム内で開始する時間 (100 ナノ秒単位)。|
|`Duration`|認識された音声のオーディオ ストリーム内での持続時間 (100 ナノ秒単位)。|

`RecognitionStatus` フィールドには、次の値が格納されます。

| Status | 説明 |
|--------|-------------|
| `Success` | 認識が成功し、`DisplayText` フィールドが存在する。 |
| `NoMatch` | 音声ストリーム内に音声が検出されたが、ターゲット言語の単語は見つからなかった。 通常は、認識言語がユーザーが話している言語とは異なる言語であることを意味します。 |
| `InitialSilenceTimeout` | 音声ストリームの先頭に無音状態しか含まれておらず、音声の待機中にサービスがタイムアウトした。 |
| `BabbleTimeout` | 音声ストリームの先頭に雑音しか含まれておらず、音声の待機中にサービスがタイムアウトした。 |
| `Error` | 認識サービスで内部エラーが発生し、処理を継続できない。 可能であれば再試行してください。 |

> [!NOTE]
> オーディオが不適切な表現のみで構成されており、`profanity` クエリ パラメーターが `remove` に設定されている場合、サービスは音声結果を返しません。

`detailed` 形式には、`simple` 形式と同じデータに加え、`NBest` が含まれます。これは、同じ音声認識結果に対する他の解釈のリストです。 これらの結果は、可能性が最も高いものから順にランク付けされます。1 つ目のエントリは、メインの認識結果と同じです。  `detailed` 形式を使用しているときは、`NBest` リストに含まれるそれぞれの結果について、`DisplayText` が `Display` として返されます。

`NBest` リスト内の各オブジェクトには以下のものが含まれます。

| パラメーター | 説明 |
|-----------|-------------|
| `Confidence` | 0.0 (信頼度なし) から1.0 (完全信頼) までのエントリの信頼度スコア |
| `Lexical` | 認識されたテキストの語彙形式: 認識された実際の単語。 |
| `ITN` | 認識されたテキストの逆テキスト正規化 ("カノニカル") 形式。電話番号、数字、略語 (「doctor smith」から「dr smith」)、およびその他の変換を適用したものです。 |
| `MaskedITN` | 要求された場合、不適切な表現のマスキングを適用した ITN 形式。 |
| `Display` | 認識されたテキストの表示形式。句読点と大文字化を追加したものです。 このパラメーターは、形式を `simple` に設定したときに返される `DisplayText` と同じです。 |

### <a name="sample-responses"></a>応答のサンプル

これは、`simple` 認識の典型的な応答です。

```json
{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": "1236645672289",
  "Duration": "1236645672289"
}
```

これは、`detailed` 認識の典型的な応答です。

```json
{
  "RecognitionStatus": "Success",
  "Offset": "1236645672289",
  "Duration": "1236645672289",
  "NBest": [
      {
        "Confidence" : "0.87",
        "Lexical" : "remind me to buy five pencils",
        "ITN" : "remind me to buy 5 pencils",
        "MaskedITN" : "remind me to buy 5 pencils",
        "Display" : "Remind me to buy 5 pencils.",
      },
      {
        "Confidence" : "0.54",
        "Lexical" : "rewind me to buy five pencils",
        "ITN" : "rewind me to buy 5 pencils",
        "MaskedITN" : "rewind me to buy 5 pencils",
        "Display" : "Rewind me to buy 5 pencils.",
      }
  ]
}
```

## <a name="text-to-speech-api"></a>Text to Speech API

Text to Speech REST API ではニューラルと標準のテキスト読み上げ音声が提供され、そのいずれでもロケールで識別される特定の言語と方言がサポートされています。

* 音声の全一覧については、[言語のサポート](language-support.md#text-to-speech)に関するページを参照してください。
* リージョン別の提供状況については、[リージョン](regions.md#text-to-speech)に関するページを参照してください。

> [!IMPORTANT]
> 料金は、標準音声、カスタム音声、ニューラル音声によって異なります。 詳細については、[価格](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)に関するページをご覧ください。

### <a name="request-headers"></a>要求ヘッダー

この表は、音声テキスト変換要求の必須のヘッダーと省略可能なヘッダーの一覧です。

| ヘッダー | 説明 | 必須/省略可能 |
|--------|-------------|---------------------|
| `Authorization` | 単語 `Bearer` が前に付いた認証トークン。 詳細については、[認証](#authentication)に関するページを参照してください。 | 必須 |
| `Content-Type` | 指定したテキストのコンテンツ タイプを指定します。 指定できる値は `application/ssml+xml` です。 | 必須 |
| `X-Microsoft-OutputFormat` | オーディオ出力形式を指定します。 指定できる値の全一覧については、[オーディオ出力](#audio-outputs)に関するセクションを参照してください。 | 必須 |
| `User-Agent` | アプリケーションの名前。 255 文字未満にしてください。 | 必須 |

### <a name="audio-outputs"></a>オーディオ出力

以下に示したのは、それぞれの要求の `X-Microsoft-OutputFormat` ヘッダーで送信することができるオーディオ形式の一覧です。 それぞれビットレートとエンコードの種類が含まれています。 Speech Service では、24 kHz、16 kHz、および 8 kHz のオーディオ出力がサポートされます。

|||
|-|-|
| `raw-16khz-16bit-mono-pcm` | `raw-8khz-8bit-mono-mulaw` |
| `riff-8khz-8bit-mono-alaw` | `riff-8khz-8bit-mono-mulaw` |
| `riff-16khz-16bit-mono-pcm` | `audio-16khz-128kbitrate-mono-mp3` |
| `audio-16khz-64kbitrate-mono-mp3` | `audio-16khz-32kbitrate-mono-mp3` |
| `raw-24khz-16bit-mono-pcm` | `riff-24khz-16bit-mono-pcm` |
| `audio-24khz-160kbitrate-mono-mp3` | `audio-24khz-96kbitrate-mono-mp3` |
| `audio-24khz-48kbitrate-mono-mp3` | |

> [!NOTE]
> 選択した音声と出力形式のビット レートが異なる場合、オーディオは必要に応じて再サンプリングされます。 ただし、24 khz の音声は `audio-16khz-16kbps-mono-siren` や `riff-16khz-16kbps-mono-siren` の出力形式をサポートしていません。

### <a name="request-body"></a>要求本文

各 `POST` 要求の本文は[音声合成マークアップ言語 (SSML)](speech-synthesis-markup.md) として送信されます。 SSML では、テキスト読み上げサービスによって返される合成音声の声と言語を選択できます。 サポートされている声の全一覧については、[言語のサポート](language-support.md#text-to-speech)に関するページを参照してください。

> [!NOTE]
> カスタムの音声を使用する場合は、プレーン テキスト (ASCII または UTF-8) として要求の本文を送信できます。

### <a name="sample-request"></a>要求のサンプル

この HTTP 要求は、SSML を使用して音声と言語を指定します。 1,000 文字を超える本文は許可されません。

```http
POST /cognitiveservices/v1 HTTP/1.1

X-Microsoft-OutputFormat: raw-16khz-16bit-mono-pcm
Content-Type: application/ssml+xml
Host: westus.tts.speech.microsoft.com
Content-Length: 225
Authorization: Bearer [Base64 access_token]

<speak version='1.0' xml:lang='en-US'><voice xml:lang='en-US' xml:gender='Female'
    name='Microsoft Server Speech Text to Speech Voice (en-US, ZiraRUS)'>
        Microsoft Speech Service Text-to-Speech API
</voice></speak>
```

### <a name="http-status-codes"></a>HTTP 状態コード

各応答の HTTP 状態コードは、成功または一般的なエラーを示します。

| HTTP 状態コード | 説明 | 考えられる理由 |
|------------------|-------------|-----------------|
| 200 | OK | 要求は成功しました。応答本文はオーディオ ファイルです。 |
| 400 | 正しくない要求 | 必須パラメーターが指定されていない、空、または null です。 または、必須またはオプションのパラメーターに渡された値が無効です。 よくある問題はヘッダーが長すぎることです。 |
| 401 | 権限がありません | 要求が承認されていません。 サブスクリプション キーまたはトークンが有効であり、正しいリージョンにあることを確認してください。 |
| 413 | 要求のエンティティが大きすぎます | SSML 入力が 1024 文字を超えています |
| 429 | 要求が多すぎます | 使用中のサブスクリプションで許可されている要求のクォータまたは速度を超えています。 |
| 502 | 無効なゲートウェイ | ネットワークまたはサーバー側の問題です。 無効なヘッダーを示す場合もあります。 |

HTTP ステータスが `200 OK` の場合、応答の本文には要求された形式のオーディオ ファイルが含まれています。 このファイルは、転送と同時に再生することも、バッファーまたはファイルに保存することもできます。

## <a name="next-steps"></a>次の手順

- [Speech 試用版サブスクリプションを取得する](https://azure.microsoft.com/try/cognitive-services/)
- [音響モデルをカスタマイズする](how-to-customize-acoustic-models.md)
- [言語モデルをカスタマイズする](how-to-customize-language-model.md)
