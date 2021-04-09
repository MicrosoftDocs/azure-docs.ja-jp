---
title: モバイルアプリから Web API を呼び出す | Azure
titleSuffix: Microsoft identity platform
description: Web API を呼び出すモバイル アプリを構築する方法について説明します。 (Web API を呼び出す。)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/18/2020
ms.author: jmprieur
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 656cb167fb47e44c28922afed75d8c46a460aaf1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99584399"
---
# <a name="call-a-web-api-from-a-mobile-app"></a>モバイル アプリから Web API を呼び出す

アプリがユーザーをサインインさせ、トークンを受信すると、Microsoft Authentication Library (MSAL) では、ユーザー、ユーザーの環境、発行されたトークンに関する情報を公開します。 アプリではこれらの値が使用され、Web API を呼び出したり、ウェルカム メッセージをユーザーに表示したりすることができます。

この記事では、最初に MSAL の結果を確認します。 次に、`AuthenticationResult` または `result` からのアクセス トークンを使用して保護された Web API を呼び出す方法を説明します。

## <a name="msal-result"></a>MSAL の結果
MSAL には次のような値があります。 

- `AccessToken` は、HTTP ベアラー要求で保護された Web API を呼び出します。
- `IdToken` には、サインインしたユーザーに関する有用な情報が含まれています。 この情報には、ユーザー名、ホーム テナント、ストレージの一意識別子などが含まれます。
- `ExpiresOn` はトークンの有効期限です。 MSAL は、アプリの自動更新を処理します。
- `TenantId` は、ユーザーがサインインした場所のテナントの識別子です。 Azure Active Directory (Azure AD) B2B のゲスト ユーザーの場合、この値によって、ユーザーがサインインしたテナントが識別されます。 この値は、ユーザーのホーム テナントを識別しません。  
- `Scopes` は、お使いのトークンで付与されたスコープを示します。 付与されたスコープは、要求したスコープのサブセットである場合があります。

また、MSAL では `Account` 値の抽象化も提供されます。 `Account` 値は、現在のユーザーのサインインしたアカウントを表します。

- `HomeAccountIdentifier` は、ユーザーのホーム テナントの識別子を識別します。
- `UserName` は、ユーザーの推奨ユーザー名です。 Azure AD B2C ユーザーの場合、この値は空の場合があります。
- `AccountIdentifier` は、サインインしているユーザーを識別します。 ほとんどの場合、ユーザーが別のテナントのゲストでない限り、この値は `HomeAccountIdentifier` 値と同じです。

## <a name="call-an-api"></a>API を呼び出す

アクセス トークンがあれば、Web API を呼び出すことができます。 お使いのアプリではトークンを使って HTTP 要求が作成され、実行されます。

### <a name="android"></a>Android

```Java
        RequestQueue queue = Volley.newRequestQueue(this);
        JSONObject parameters = new JSONObject();

        try {
            parameters.put("key", "value");
        } catch (Exception e) {
            // Error when constructing.
        }
        JsonObjectRequest request = new JsonObjectRequest(Request.Method.GET, MSGRAPH_URL,
                parameters,new Response.Listener<JSONObject>() {
            @Override
            public void onResponse(JSONObject response) {
                // Successfully called Graph. Process data and send to UI.
            }
        }, new Response.ErrorListener() {
            @Override
            public void onErrorResponse(VolleyError error) {
                // Error.
            }
        }) {
            @Override
            public Map<String, String> getHeaders() throws AuthFailureError {
                Map<String, String> headers = new HashMap<>();
                
                // Put access token in HTTP request.
                headers.put("Authorization", "Bearer " + authResult.getAccessToken());
                return headers;
            }
        };

        request.setRetryPolicy(new DefaultRetryPolicy(
                3000,
                DefaultRetryPolicy.DEFAULT_MAX_RETRIES,
                DefaultRetryPolicy.DEFAULT_BACKOFF_MULT));
        queue.add(request);
```

### <a name="msal-for-ios-and-macos"></a>iOS および macOS 用の MSAL

トークンを取得するメソッドは `MSALResult` オブジェクトを返します。 `MSALResult` は `accessToken` プロパティを公開します。 `accessToken` を使用して、Web API を呼び出すことができます。 保護された Web API にアクセスするための呼び出しを行う前に、このプロパティを HTTP Authorization ヘッダーに追加してください。

```objc
NSMutableURLRequest *urlRequest = [NSMutableURLRequest new];
urlRequest.URL = [NSURL URLWithString:"https://contoso.api.com"];
urlRequest.HTTPMethod = @"GET";
urlRequest.allHTTPHeaderFields = @{ @"Authorization" : [NSString stringWithFormat:@"Bearer %@", accessToken] };
        
NSURLSessionDataTask *task =
[[NSURLSession sharedSession] dataTaskWithRequest:urlRequest
     completionHandler:^(NSData * _Nullable data, NSURLResponse * _Nullable response, NSError * _Nullable error) {}];
[task resume];
```

```swift
let urlRequest = NSMutableURLRequest()
urlRequest.url = URL(string: "https://contoso.api.com")!
urlRequest.httpMethod = "GET"
urlRequest.allHTTPHeaderFields = [ "Authorization" : "Bearer \(accessToken)" ]
     
let task = URLSession.shared.dataTask(with: urlRequest as URLRequest) { (data: Data?, response: URLResponse?, error: Error?) in }
task.resume()
```

### <a name="xamarin"></a>Xamarin

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

## <a name="make-several-api-requests"></a>複数の API 要求を行う

同じ API を複数回呼び出す場合、または複数の API を呼び出す場合は、アプリを構築するときに、次の点を考慮してください。

- **増分同意**:Microsoft ID プラットフォームでは、すべて開始時にではなく、アクセス許可が必要なときに、アプリがユーザーの同意を得られるようにしています。 アプリが API を呼び出す準備ができたら、毎回、必要なスコープのみを要求します。

- **条件付きアクセス**:複数の API 要求を行うときに、特定のシナリオでは、追加の条件付きアクセスの要件を満たさなければならない場合があります。 最初の要求に条件付きアクセス ポリシーが適用されておらず、アプリが条件付きアクセスを必要とする新しい API にサイレントでアクセスしようとする場合に、要件がこのように増加することがあります。 この問題に対処するため、必ずサイレント要求からのエラーをキャッチし、対話型の要求を行えるように準備します。  詳細については、[条件付きアクセスについてのガイダンス](../azuread-dev/conditional-access-dev-guide.md)に関するページを参照してください。

## <a name="call-several-apis-by-using-incremental-consent-and-conditional-access"></a>増分同意と条件付きアクセスを使用して複数の API を呼び出す

同じユーザーに対して複数の API を呼び出す場合、ユーザーのトークンを取得した後、続けて `AcquireTokenSilent` を呼び出してトークンを取得すれば、ユーザーに何度も資格情報の入力を求める必要がなくなります。

```csharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
```

対話は、次の場合に必要です。

- ユーザーが最初の API については同意したが、より多くのスコープについて同意する必要が生じた。 この場合は、増分同意を使用します。
- 最初の API は[多要素認証](../authentication/concept-mfa-howitworks.md)を必要としなかったが、次の API は多要素認証を必要とする。

```csharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

try
{
 result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
}
catch(MsalUiRequiredException ex)
{
 result = await app.AcquireTokenInteractive("scopeApi2")
                  .WithClaims(ex.Claims)
                  .ExecuteAsync();
}
```

## <a name="next-steps"></a>次のステップ

このシナリオの次の記事「[運用環境に移行する](scenario-mobile-production.md)」に進みます。
