---
title: Bing Speech に対する認証を行う | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Bing Speech API を使用するための認証を要求します
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: d1e708ff29293b87935d0d191ba44ad4a11917a0
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2019
ms.locfileid: "70965652"
---
# <a name="authenticate-to-the-speech-api"></a>Speech API を認証する

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Bing Speech の認証には、次を使用できます。

- サブスクリプション キー。
- 承認トークン。

## <a name="use-a-subscription-key"></a>サブスクリプション キーを使用する

Speech Service を使用するには、まず Cognitive Services (旧 Project Oxford) の一部である Speech API にサブスクライブする必要があります。 無料試用版のサブスクリプション キーは、[Cognitive Services サブスクリプション](https://azure.microsoft.com/try/cognitive-services/) ページから取得できます。 Speech API を選択したら、 **[Get API Key]** (API キーの取得) を選択してキーを取得します。 プライマリ キーおよびセカンダリ キーを返します。 両方のキーが同じクォータに関連付けられているため、どちらのキーでも使用できます。

長期間使用する場合や、クォータが増加した場合は、[Azure アカウント](https://azure.microsoft.com/free/)にサインアップしてください。

Speech REST API を使用するには、要求ヘッダーの `Ocp-Apim-Subscription-Key` フィールドにサブスクリプション キーを渡す必要があります。

名前| 形式| 説明
----|-------|------------
Ocp-Apim-Subscription-Key | ASCII | YOUR_SUBSCRIPTION_KEY

要求ヘッダーの例を次に示します。

```HTTP
POST https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codec=audio/pcm; samplerate=16000
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: speech.platform.bing.com
Transfer-Encoding: chunked
Expect: 100-continue
```

> [!IMPORTANT]
> アプリケーションに[クライアント ライブラリ](../GetStarted/GetStartedClientLibraries.md)を使用する場合は、次のセクションで説明するように、お使いのサブスクリプション キーで承認トークンを取得できることを確認します。 クライアント ライブラリは、サブスクリプション キーを使用して承認トークンを取得し、そのトークンを認証に使用します。

## <a name="use-an-authorization-token"></a>承認トークンを使用する

代わりに、認証/承認の証明として、認証に承認トークンを使用することができます。 このトークンを取得するには、[前のセクション](#use-a-subscription-key)で説明するように、まず Speech API からサブスクリプション キーを取得する必要があります。

### <a name="get-an-authorization-token"></a>承認トークンを取得する

有効なサブスクリプション キーを取得したら、Cognitive Services のトークン サービスに POST 要求を送信します。 応答で、承認トークンを JSON Web トークン (JWT) として受け取ります。

> [!NOTE]
> トークンの有効期限は 10 分間です。 トークンを更新するには、次のセクションをご覧ください。

トークン サービスの URI はこちらにあります。

```
https://api.cognitive.microsoft.com/sts/v1.0/issueToken
```

次のコード例は、アクセス トークンを取得する方法を示します。 `YOUR_SUBSCRIPTION_KEY` を、お使いのサブスクリプション キーに置き換えます。

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

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

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```cs
    /*
     * This class demonstrates how to get a valid access token.
     */
    public class Authentication
    {
        public static readonly string FetchTokenUri = "https://api.cognitive.microsoft.com/sts/v1.0";
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
                uriBuilder.Path += "/issueToken";

                var result = await client.PostAsync(uriBuilder.Uri.AbsoluteUri, null);
                Console.WriteLine("Token Uri: {0}", uriBuilder.Uri.AbsoluteUri);
                return await result.Content.ReadAsStringAsync();
            }
        }
    }
```

---

次に POST 要求のサンプルを示します。

```HTTP
POST https://api.cognitive.microsoft.com/sts/v1.0/issueToken HTTP/1.1
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: api.cognitive.microsoft.com
Content-type: application/x-www-form-urlencoded
Content-Length: 0
Connection: Keep-Alive
```

トークン サービスから承認トークンを取得できない場合は、サブスクリプション キーがまだ有効であるかどうかを確認してください。 無料試用版のキーを使用している場合は、[Cognitive Services のサブスクリプション](https://azure.microsoft.com/try/cognitive-services/) ページに移動し、[ログイン] をクリックして無料試用版を申し込んだアカウントを使用してログインし、サブスクリプション キーの有効期限が切れていないか、クォータを超過していないかを確認します。

### <a name="use-an-authorization-token-in-a-request"></a>要求に承認トークンを使用する

Speech API を呼び出すたびに、`Authorization` ヘッダーに承認トークンを渡す必要があります。 `Authorization` ヘッダーには JWT アクセス トークンを含める必要があります。

次の例は、Speech REST API を呼び出すときに、承認トークンを使用する方法を示します。

> [!NOTE]
> `YOUR_AUDIO_FILE` を、録音済みのオーディオ ファイルへのパスに置き換えます。 `YOUR_ACCESS_TOKEN` を、前の手順「[承認トークンを取得する](#get-an-authorization-token)」で取得した承認トークンに置き換えます。

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

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

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```cs
HttpWebRequest request = null;
request = (HttpWebRequest)HttpWebRequest.Create(requestUri);
request.SendChunked = true;
request.Accept = @"application/json;text/xml";
request.Method = "POST";
request.ProtocolVersion = HttpVersion.Version11;
request.Host = @"speech.platform.bing.com";
request.ContentType = @"audio/wav; codec=audio/pcm; samplerate=16000";
request.Headers["Authorization"] = "Bearer " + token;

// Send an audio file by 1024 byte chunks
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

---

### <a name="renew-an-authorization-token"></a>承認トークンを更新する

承認トークンは、一定期間 (現在は 10 分) が経過すると期限切れになります。 有効期限が切れる前に、承認トークンを更新する必要があります。

次のコードは、承認トークンを更新する方法の C# の実装例を示します。

```cs
    /*
     * This class demonstrates how to get a valid O-auth token.
     */
    public class Authentication
    {
        public static readonly string FetchTokenUri = "https://api.cognitive.microsoft.com/sts/v1.0";
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
                uriBuilder.Path += "/issueToken";

                var result = await client.PostAsync(uriBuilder.Uri.AbsoluteUri, null);
                Console.WriteLine("Token Uri: {0}", uriBuilder.Uri.AbsoluteUri);
                return await result.Content.ReadAsStringAsync();
            }
        }
    }
```
