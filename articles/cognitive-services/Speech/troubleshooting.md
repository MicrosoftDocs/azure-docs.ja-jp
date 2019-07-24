---
title: Bing Speech のトラブルシューティング | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Bing Speech 使用時の問題を解決する方法です。
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: e70e7b79be7dd4ea55c56898eaf8007d25732366
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/22/2019
ms.locfileid: "56673020"
---
# <a name="troubleshooting-bing-speech"></a>Bing Speech のトラブルシューティング

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

## <a name="error-http-403-forbidden"></a>エラー `HTTP 403 Forbidden`

Speech Recognition API の使用中に `HTTP 403 Forbidden` エラーが返される。

### <a name="cause"></a>原因

多くの場合、このエラーは、認証の問題が原因で発生します。 有効な `Ocp-Apim-Subscription-Key` や `Authorization` ヘッダーがない接続要求は、`HTTP 403 Forbidden` 応答によりサービスによって拒否されます。

認証にサブスクリプション キーを使用している場合、理由は次のいずれかが考えられます。

- サブスクリプション キーが見つからないか無効である
- サブスクリプション キーの使用量クォータを超過している
- REST API が呼び出されたときに `Ocp-Apim-Subscription-Key` フィールドが要求ヘッダーに設定されていない

認証に承認トークンを使用している場合、次の理由によりエラーが発生することがあります。

- REST を使用しているとき、要求に `Authorization` ヘッダーがない
- Authorization ヘッダーに指定されている承認トークンが無効である
- 承認トークンの有効期限が切れている。 アクセス トークンの有効期限は 10 分間です。

認証について詳しくは、[認証](How-to/how-to-authentication.md)に関するページをご覧ください。

### <a name="troubleshooting-steps"></a>トラブルシューティングの手順

#### <a name="verify-that-your-subscription-key-is-valid"></a>サブスクリプション キーが有効であることを確認する

確認のために次のコマンドを実行できます。 *YOUR_SUBSCRIPTION_KEY* を自身のサブスクリプション キーに置き換えてください。 サブスクリプション キーが有効である場合、応答で承認トークンを JSON Web トークン (JWT) として受け取ります。 そうでない場合、応答でエラーを受け取ります。

> [!NOTE]
> `YOUR_SUBSCRIPTION_KEY` を自身のサブスクリプション キーに置き換えます。

# <a name="powershelltabazure-powershell"></a>[Powershell](#tab/azure-powershell)

```Powershell
$FetchTokenHeader = @{
  'Content-type'='application/x-www-form-urlencoded';
  'Content-Length'= '0';
  'Ocp-Apim-Subscription-Key' = 'YOUR_SUBSCRIPTION_KEY'
}

$OAuthToken = Invoke-RestMethod -Method POST -Uri https://api.cognitive.microsoft.com/sts/v1.0/issueToken -Headers $FetchTokenHeader

# show the token received
$OAuthToken

```

# <a name="curltabcurl"></a>[curl](#tab/curl)

例では、Bash とともに Linux で curl を使用します。 お使いのプラットフォームで使用できない場合、curl をインストールする必要がある場合があります。 この例は、Windows、Git Bash、zsh、およびその他のシェル上の Cygwin でも機能します。

```
curl -v -X POST "https://api.cognitive.microsoft.com/sts/v1.0/issueToken" -H "Content-type: application/x-www-form-urlencoded" -H "Content-Length: 0" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```
---

お使いのアプリケーションや REST 要求で、前に使用した同じサブスクリプション キーを使用してください。

#### <a name="verify-the-authorization-token"></a>承認トークンを確認する

この手順は、認証に承認トークンを使用する場合にのみ必要です。 承認トークンがまだ有効であることを確認するには、次のコマンドを実行します。 このコマンドは、サービスに対して POST 要求を実行し、そのサービスから応答メッセージを受け取ります。 まだ HTTP `403 Forbidden` エラーを受け取る場合は、アクセス トークンが正しく設定されており、有効期限内であることをダブルチェックしてください。

> [!IMPORTANT]
> トークンの有効期限は 10 分間です。
> [!NOTE]
> `YOUR_AUDIO_FILE` を録音済みのオーディオ ファイルへのパスに、`YOUR_ACCESS_TOKEN` を前の手順で返された承認トークンに置き換えます。

# <a name="powershelltabazure-powershell"></a>[Powershell](#tab/azure-powershell)

```Powershell

$SpeechServiceURI =
'https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed'

# $OAuthToken is the authrization token returned by the token service.
$RecoRequestHeader = @{
  'Authorization' = 'Bearer '+ $OAuthToken;
  'Transfer-Encoding' = 'chunked'
  'Content-type' = 'audio/wav; codec=audio/pcm; samplerate=16000'
}

# Read audio into byte array
$audioBytes = [System.IO.File]::ReadAllBytes("YOUR_AUDIO_FILE")

$RecoResponse = Invoke-RestMethod -Method POST -Uri $SpeechServiceURI -Headers $RecoRequestHeader -Body $audioBytes

# Show the result
$RecoResponse

```

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -v -X POST "https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed" -H "Transfer-Encoding: chunked" -H "Authorization: Bearer YOUR_ACCESS_TOKEN" -H "Content-type: audio/wav; codec=audio/pcm; samplerate=16000" --data-binary @YOUR_AUDIO_FILE
```

---

## <a name="error-http-400-bad-request"></a>エラー `HTTP 400 Bad Request`

これは通常、要求本文に無効なオーディオ データが含まれていることが原因です。 現在は、WAV ファイルのみがサポートされています。

## <a name="error-http-408-request-timeout"></a>エラー `HTTP 408 Request Timeout`

このエラーは主に、サービスにオーディオ データが送信されず、タイムアウトした後にそのサービスが返します。 REST API の場合、オーディオ データは要求本文に入れる必要があります。

## <a name="the-recognitionstatus-in-the-response-is-initialsilencetimeout"></a>要求内の `RecognitionStatus` は `InitialSilenceTimeout` である

この問題の原因は、通常はオーディオ データです。 たとえば、次のように入力します。

- オーディオの最初に長い無音時間がある。 数秒後、サービスは認識を停止し、`InitialSilenceTimeout` を返します。
- オーディオがサポートされていないコーデック形式を使用している。これにより、オーディオ データが無音として扱われます。
