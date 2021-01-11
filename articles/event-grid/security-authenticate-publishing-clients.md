---
title: Event Grid のカスタム トピックまたはドメインにイベントを発行するクライアントを認証する
description: この記事では、Event Grid のカスタム トピックにイベントを発行するクライアントを認証するさまざまな方法について説明します。
ms.topic: conceptual
ms.date: 07/07/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: e934ce0d8f5e31dc8dd7592a2e553cd278af2b10
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2020
ms.locfileid: "89019115"
---
# <a name="authenticate-publishing-clients-azure-event-grid"></a>発行クライアントを認証する (Azure Event Grid)
この記事では、**アクセス キー**または **Shared Access Signature (SAS)** トークンを使用して、Azure Event Grid のトピックまたはドメインにイベントを発行するクライアントを認証する方法について説明します。 SAS トークンを使用することをお勧めしますが、キー認証はプログラミングが簡単で、既存の多くの Webhook 発行元と互換性があります。  

## <a name="authenticate-using-an-access-key"></a>アクセス キーを使用して認証する
アクセス キーによる認証は、最も簡単な形式の認証です。 アクセス キーは、HTTP ヘッダーまたは URL クエリ パラメーターとして渡すことができます。 

### <a name="access-key-in-a-http-header"></a>HTTP ヘッダー内のアクセス キー
HTTP ヘッダー `aeg-sas-key` の値としてアクセス キーを渡します。

```
aeg-sas-key: XXXXXXXXXXXXXXXXXX0GXXX/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

### <a name="access-key-as-a-query-parameter"></a>クエリ パラメーターとしてのアクセス キー
クエリ パラメーターとして `aeg-sas-key` を指定することもできます。 

```
https://<yourtopic>.<region>.eventgrid.azure.net/api/events?aeg-sas-key=XXXXXXXX53249XX8XXXXX0GXXX/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

トピックまたはドメインに対するアクセス キーを取得する方法については、[アクセス キーの取得](get-access-keys.md)に関する記事を参照してください。

## <a name="authenticate-using-a-sas-token"></a>SAS トークンを使用して認証する
Event Grid リソースに対する SAS トークンには、リソース、有効期限、署名が含まれます。 SAS トークンの形式は `r={resource}&e={expiration}&s={signature}` です。

リソースは、イベントを送信する Event Grid グリッド トピックのパスです。 たとえば、有効なリソース パスは `https://<yourtopic>.<region>.eventgrid.azure.net/api/events` です。 サポートされているすべての API バージョンについては、「[Microsoft.EventGrid リソースの種類](/azure/templates/microsoft.eventgrid/allversions)」を参照してください。 

最初に、プログラムで SAS トークンを生成してから、`aeg-sas-token` ヘッダーまたは `Authorization SharedAccessSignature` ヘッダーを使用して Event Grid での認証を行います。 

### <a name="generate-sas-token-programmatically"></a>プログラムで SAS トークンを生成する
次の例では、Event Grid で使用する SAS トークンを作成します。

```cs
static string BuildSharedAccessSignature(string resource, DateTime expirationUtc, string key)
{
    const char Resource = 'r';
    const char Expiration = 'e';
    const char Signature = 's';

    string encodedResource = HttpUtility.UrlEncode(resource);
    var culture = CultureInfo.CreateSpecificCulture("en-US");
    var encodedExpirationUtc = HttpUtility.UrlEncode(expirationUtc.ToString(culture));

    string unsignedSas = $"{Resource}={encodedResource}&{Expiration}={encodedExpirationUtc}";
    using (var hmac = new HMACSHA256(Convert.FromBase64String(key)))
    {
        string signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(unsignedSas)));
        string encodedSignature = HttpUtility.UrlEncode(signature);
        string signedSas = $"{unsignedSas}&{Signature}={encodedSignature}";

        return signedSas;
    }
}
```

### <a name="using-aeg-sas-token-header"></a>aeg-sas-token ヘッダーの使用
`aeg-sas-toke` ヘッダーの値として SAS トークンを渡す例を次に示します。 

```http
aeg-sas-token: r=https%3a%2f%2fmytopic.eventgrid.azure.net%2fapi%2fevent&e=6%2f15%2f2017+6%3a20%3a15+PM&s=XXXXXXXXXXXXX%2fBPjdDLOrc6THPy3tDcGHw1zP4OajQ%3d
```

### <a name="using-authorization-header"></a>Authorization ヘッダーの使用
`Authorization` ヘッダーの値として SAS トークンを渡す例を次に示します。 

```http
Authorization: SharedAccessSignature r=https%3a%2f%2fmytopic.eventgrid.azure.net%2fapi%2fevent&e=6%2f15%2f2017+6%3a20%3a15+PM&s=XXXXXXXXXXXXX%2fBPjdDLOrc6THPy3tDcGHw1zP4OajQ%3d
```

## <a name="next-steps"></a>次のステップ
イベントを配信するためのイベント ハンドラーでの認証の詳細については、[イベント配信の認証](security-authentication.md)に関する記事を参照してください。 