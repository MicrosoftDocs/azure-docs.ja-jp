---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 03/29/2019
ms.author: erhopf
ms.openlocfilehash: 22a95be43f06e95a6067b179b3023ba94ee5795d
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2019
ms.locfileid: "68362562"
---
## <a name="authentication"></a>Authentication

各要求には Authorization ヘッダーが必要です。 次の表は、各サービスでサポートされているヘッダーを示したものです。

| サポートされている Authorization ヘッダー | 音声テキスト変換 | テキスト読み上げ |
|------------------------|----------------|----------------|
| Ocp-Apim-Subscription-Key | はい | いいえ |
| Authorization:Bearer | はい | はい |

`Ocp-Apim-Subscription-Key` ヘッダーを使用する場合、指定する必要があるのはサブスクリプション キーだけです。 例:

```
'Ocp-Apim-Subscription-Key': 'YOUR_SUBSCRIPTION_KEY'
```

`Authorization: Bearer` ヘッダーを使用する場合は、`issueToken` エンドポイントに対して要求を実行する必要があります。 この要求では、サブスクリプション キーを、10 分間有効なアクセス トークンと交換します。 以降のいくつかのセクションでは、トークンの取得方法と使用方法について説明します。

### <a name="how-to-get-an-access-token"></a>アクセス トークンを取得する方法

アクセス トークンを取得するには、`Ocp-Apim-Subscription-Key` とサブスクリプション キーを使用して、`issueToken` エンドポイントに対して要求を実行する必要があります。

次のリージョンとエンドポイントがサポートされています。

[!INCLUDE [](./cognitive-services-speech-service-endpoints-token-service.md)]

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

#### <a name="python-sample"></a>Python のサンプル

```python
# Request module must be installed.
# Run pip install requests if necessary.
import requests

subscription_key = 'REPLACE_WITH_YOUR_KEY'


def get_token(subscription_key):
    fetch_token_url = 'https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken'
    headers = {
        'Ocp-Apim-Subscription-Key': subscription_key
    }
    response = requests.post(fetch_token_url, headers=headers)
    access_token = str(response.text)
    print(access_token)
```

### <a name="how-to-use-an-access-token"></a>アクセス トークンを使用する方法

このサービスには、アクセス トークンを `Authorization: Bearer <TOKEN>` ヘッダーとして送信する必要があります。 各アクセス トークンは 10 分間有効です。 新しいトークンはいつでも取得できますが、ネットワークのトラフィックと待ち時間を最小限に抑えるために、同じトークンを 9 分間使用することをお勧めします。

次に示したのは、Text to Speech REST API に対するサンプル HTTP 要求です。

```http
POST /cognitiveservices/v1 HTTP/1.1
Authorization: Bearer YOUR_ACCESS_TOKEN
Host: westus.stt.speech.microsoft.com
Content-type: application/ssml+xml
Content-Length: 199
Connection: Keep-Alive

// Message body here...
```
