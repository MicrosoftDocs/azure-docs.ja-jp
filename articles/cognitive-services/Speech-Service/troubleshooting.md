---
title: Speech SDK のトラブルシューティング - Speech Services
titleSuffix: Azure Cognitive Services
description: この記事では、Speech SDK を使用しているときに発生する可能性がある問題の解決に役立つ情報を提供します。
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: jhakulin
ms.openlocfilehash: 99cb23afcdb40f74485a7dcec34435a46d0e7476
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2019
ms.locfileid: "68405909"
---
# <a name="troubleshoot-the-speech-sdk"></a>Speech SDK のトラブルシューティング

この記事では、Speech SDK を使用しているときに発生する可能性がある問題の解決に役立つ情報を提供します。

## <a name="error-websocket-upgrade-failed-with-an-authentication-error-403"></a>エラー:認証エラー (403) で WebSocket をアップグレードできませんでした

リージョンまたはサービスのエンドポイントが間違っている可能性があります。 URI を調べて、それが正しいことを確認してください。

また、サブスクリプション キーまたは承認トークンに問題がある可能性もあります。 詳細については、次のセクションを参照してください。

## <a name="error-http-403-forbidden-or-http-401-unauthorized"></a>エラー:HTTP 403 許可されていません、または HTTP 401 (権限がありません)

多くの場合、このエラーは、認証の問題が原因で発生します。 有効な `Ocp-Apim-Subscription-Key` または `Authorization` ヘッダーがない接続要求は、状態 403 または 401 で拒否されます。

* 認証にサブスクリプション キーを使用している場合は、以下が原因である可能性があります。

    - サブスクリプション キーが見つからないか無効である
    - サブスクリプションの使用量クォータを超えている

* 認証に認証トークンを使用している場合は、以下が原因である可能性があります。

    - 承認トークンが無効である
    - 承認トークンの有効期限が切れている

### <a name="validate-your-subscription-key"></a>サブスクリプション キーを検証する

次のコマンドのいずれかを実行することで、有効なサブスクリプション キーを持っているかどうかを確認できます。

> [!NOTE]
> `YOUR_SUBSCRIPTION_KEY` と `YOUR_REGION` を、お使いのサブスクリプション キーと関連するリージョンに置き換えてください。

* PowerShell

    ```Powershell
    $FetchTokenHeader = @{
      'Content-type'='application/x-www-form-urlencoded'
      'Content-Length'= '0'
      'Ocp-Apim-Subscription-Key' = 'YOUR_SUBSCRIPTION_KEY'
    }
    $OAuthToken = Invoke-RestMethod -Method POST -Uri https://YOUR_REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken -Headers $FetchTokenHeader
    $OAuthToken
    ```

* cURL

    ```
    curl -v -X POST "https://YOUR_REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY" -H "Content-type: application/x-www-form-urlencoded" -H "Content-Length: 0"
    ```

有効なサブスクリプション キーを入力した場合、このコマンドで承認トークンが返されます。それ以外の場合はエラーが返されます。

### <a name="validate-an-authorization-token"></a>承認トークンを検証する

認証に承認トークンを使用している場合は、次のコマンドのいずれかを実行して、認証トークンがまだ有効であることを確認します。 トークンは 10 分間有効です。

> [!NOTE]
> `YOUR_AUDIO_FILE` を、録音済みのオーディオ ファイルへのパスに置き換えます。 `YOUR_ACCESS_TOKEN` を、上記のステップで返された承認トークンに置き換えます。 `YOUR_REGION` を正しい地域と置き換えます。

* PowerShell

    ```Powershell
    $SpeechServiceURI =
    'https://YOUR_REGION.stt.speech.microsoft.com/speech/recognition/interactive/cognitiveservices/v1?language=en-US'

    # $OAuthToken is the authorization token returned by the token service.
    $RecoRequestHeader = @{
      'Authorization' = 'Bearer '+ $OAuthToken
      'Transfer-Encoding' = 'chunked'
      'Content-type' = 'audio/wav; codec=audio/pcm; samplerate=16000'
    }

    # Read audio into byte array.
    $audioBytes = [System.IO.File]::ReadAllBytes("YOUR_AUDIO_FILE")

    $RecoResponse = Invoke-RestMethod -Method POST -Uri $SpeechServiceURI -Headers $RecoRequestHeader -Body $audioBytes

    # Show the result.
    $RecoResponse
    ```

* cURL

    ```
    curl -v -X POST "https://YOUR_REGION.stt.speech.microsoft.com/speech/recognition/interactive/cognitiveservices/v1?language=en-US" -H "Authorization: Bearer YOUR_ACCESS_TOKEN" -H "Transfer-Encoding: chunked" -H "Content-type: audio/wav; codec=audio/pcm; samplerate=16000" --data-binary @YOUR_AUDIO_FILE
    ```

有効な承認トークンを入力した場合、このコマンドでオーディオ ファイルのトランスクリプトが返されます。それ以外の場合はエラーが返されます。

---

## <a name="error-http-400-bad-request"></a>エラー:HTTP 400 Bad Request

このエラーは、通常は、要求本文に無効なオーディオ データが含まれているときに発生します。 サポートされているのは WAV 形式のみです。 要求のヘッダーもチェックして、`Content-Type` と `Content-Length` に適切な値を指定していることを確認します。

## <a name="error-http-408-request-timeout"></a>エラー:HTTP 408 要求タイムアウト

このエラーのもっとも可能性が高い原因は、オーディオ データがサービスに送信されていないことです。 このエラーは、ネットワークの問題によっても発生する可能性があります。

## <a name="recognitionstatus-in-the-response-is-initialsilencetimeout"></a>応答の "RecognitionStatus" が "InitialSilenceTimeout"

通常この問題はオーディオ データによって発生します。 このエラーは、次の理由で発生する場合があります。

* オーディオの先頭が長時間サイレント状態になっています。 その場合、サービスは数秒後に認識を停止し、`InitialSilenceTimeout` を返します。

* オーディオがサポートされていないコーデック形式を使用しており、これによりオーディオ データが無音として扱われています。

## <a name="next-steps"></a>次の手順

* [リリース ノートを確認します](releasenotes.md)
