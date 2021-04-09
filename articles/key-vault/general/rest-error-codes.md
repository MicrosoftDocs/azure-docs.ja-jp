---
title: REST API のエラー コード - Azure Key Vault
description: これらのエラー コードは、Azure Key Vault Web サービスに対する操作から返される場合があります。
keywords: ''
services: machine-learning
author: msmbaldwin
ms.custom: seodec18
ms.author: mbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: reference
ms.date: 12/16/2019
ms.openlocfilehash: 30b7e34f2a791cfd8dec1a6d8e81d706fa07939f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91631224"
---
# <a name="azure-key-vault-rest-api-error-codes"></a>Azure Key Vault REST API のエラー コード
 
次のエラー コードは、Azure Key Vault Web サービスに対する操作から返される場合があります。
 
## <a name="http-401-unauthenticated-request"></a>HTTP 401:認証されていない要求

401 は、要求が Key Vault に対して認証されていないことを意味します。 

次の場合、要求は認証されています。

- キー コンテナーが呼び出し元の ID を認識している。かつ
- 呼び出し元は、Key Vault リソースへのアクセスを試行できる。 

要求から 401 が返される理由はいくつかあります。

### <a name="no-authentication-token-attached-to-the-request"></a>要求に接続された認証トークンがない。 

シークレットの値を設定する PUT 要求の例を次に示します。

``` 
PUT https://putreqexample.vault.azure.net//secrets/DatabaseRotatingPassword?api-version=7.0 HTTP/1.1
x-ms-client-request-id: 03d275a2-52a4-4bed-82c8-6fe15165affb
accept-language: en-US
Authorization: Bearer     eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSIsImtpZCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSJ9.eyJhdWQiOiJodHRwczovL3ZhdWx0LmF6dXJlLm5ldCIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJpYXQiOjE1NDg2OTc1MTMsIm5iZiI6MTU0ODY5NzUxMywiZXhwIjoxNTQ4NzAxNDEzLCJhaW8iOiI0MkpnWUhoODVqaVBnZHF5ZlRGZE5TdHY3bGUvQkFBPSIsImFwcGlkIjoiZmFkN2Q1YjMtNjlkNi00YjQ4LTkyNTktOGQxMjEyNGUxY2YxIiwiYXBwaWRhY3IiOiIxIiwiaWRwIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3LyIsIm9pZCI6IjM5NzVhZWVkLTdkMDgtNDUzYi1iNmY0LTQ0NWYzMjY5ODA5MSIsInN1YiI6IjM5NzVhZWVkLTdkMDgtNDUzYi1iNmY0LTQ0NWYzMjY5ODA5MSIsInRpZCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsInV0aSI6IjItZ3JoUmtlSWs2QmVZLUxuNDJtQUEiLCJ2ZXIiOiIxLjAifQ.fgubiz1MKqTJTXI8dHIV7t9Fle6FdHrkaGYKcBeVRX1WtLVuk1QVxzIFDlZKLXJ7QPNs0KWpeiWQI9IWIRK-8wO38yCqKTfDlfHOiNWGOpkKddlG729KFqakVf2w0GPyGPFCONRDAR5wjQarN9Bt8I8YbHwZQz_M1hztlnv-Lmsk1jBmech9ujD9-lTMBmSfFFbHcqquev119V7sneI-zxBZLf8C0pIDkaXf1t8y6Xr8CUJDMdlWLslCf3pBCNIOy65_TyGvy4Z4AJryTPBarNBPwOkNAtjCfZ4BDc2KqUZM5QN_VK4foP64sVzUL6mSr0Gh7lQJIL5b1qIpJxjxyQ
User-Agent: FxVersion/4.7.3324.0 OSName/Windows OSVersion/6.2.9200.0 Microsoft.Azure.KeyVault.KeyVaultClient/3.0.3.0
Content-Type: application/json; charset=utf-8
Host: putreqexample.vault.azure.net
Content-Length: 31

{
   "value": "m*gBJ7$Zuoz)"
}
```

"Authorization" ヘッダーは、データプレーン操作のためにキー コンテナーを呼び出すたびに必要なアクセス トークンです。 このヘッダーがない場合、応答は 401 になります。

### <a name="the-token-lacks-the-correct-resource-associated-with-it"></a>トークンに適切なリソースが関連付けられていない。 

Azure OAUTH エンドポイントからアクセス トークンを要求するときは、"resource" というパラメーターが必須です。 この値を使うと、用途に合わせてトークンのスコープを決定できるため、トークン プロバイダーにとって重要です。 Key Vault にアクセスする **すべての** トークンのリソースは、*https:\//vault.keyvault.net* (末尾のスラッシュなし) です。

### <a name="the-token-is-expired"></a>トークンの有効期限が切れている

トークンは base64 でエンコードされます。また、その値は [http://jwt.calebb.net](http://jwt.calebb.net) などの Web サイトでデコードできます。 上記のトークンをデコードすると、次のようになります。

```
    {
 typ: "JWT",
 alg: "RS256",
 x5t: "nbCwW11w3XkB-xUaXwKRSLjMHGQ",
 kid: "nbCwW11w3XkB-xUaXwKRSLjMHGQ"
}.
{
 aud: "https://vault.azure.net",
 iss: "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
 iat: 1548697513,
 nbf: 1548697513,
 exp: 1548701413,
 aio: "42JgYHh85jiPgdqyfTFdNStv7le/BAA=",
 appid: "fad7d5b3-69d6-4b48-9259-8d12124e1cf1",
 appidacr: "1",
 idp: "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
 oid: "3975aeed-7d08-453b-b6f4-445f32698091",
 sub: "3975aeed-7d08-453b-b6f4-445f32698091",
 tid: "72f988bf-86f1-41af-91ab-2d7cd011db47",
 uti: "2-grhRkeIk6BeY-Ln42mAA",
 ver: "1.0"
}.
[signature]
```

このトークンには、次のように重要な部分が多くあります。

- aud (対象ユーザー):トークンのリソース。 これは `https://vault.azure.net` であることに注意してください。 このトークンは、graph など、この値と明示的に一致しないリソースに対しては機能しません。
- iat (発行時刻):トークンが発行されたときのエポックの開始以降のティック数。
- nbf (期間の開始時刻):このトークンが有効になったときのエポックの開始以降のティック数。
- exp (有効期限):このトークンの有効期限が切れたときのエポックの開始以降のティック数。
- appid (アプリケーション ID):この要求を行っているアプリケーション ID の GUID。
- tid (テナント ID):この要求を行っているプリンシパルのテナント ID の GUID

要求が機能するには、トークンのすべての値が正しく識別されていることが重要です。 すべてが正しい場合、要求は 401 の結果になりません。

### <a name="troubleshooting-401"></a>401 のトラブルシューティング

401 は、キー コンテナーに要求を行う前に、トークンの生成時点から調査する必要があります。 一般に、コードはトークンを要求するために使用されます。 トークンが受け取られると、Key Vault 要求に渡されます。 コードがローカルで実行されている場合は、Fiddler を使用して `https://login.microsoftonline.com` への要求と応答をキャプチャできます。 要求は次のようになります。

``` 
POST https://login.microsoftonline.com/<key vault tenant ID>/oauth2/token HTTP/1.1
Accept: application/json
Content-Type: application/x-www-form-urlencoded; charset=utf-8
Host: login.microsoftonline.com
Content-Length: 192

resource=https%3A%2F%2Fvault.azure.net&client_id=<registered-app-ID>&client_secret=<registered-app-secret>&client_info=1&grant_type=client_credentials
```

次のユーザー指定の情報は正しい必要があります。

- キー コンテナー テナント ID
- https%3A%2F%2Fvault.azure.net (URL エンコード済み) に設定されたリソース値
- クライアント ID
- クライアント シークレット

要求の残りの部分がほぼ同じであることを確認します。

応答アクセス トークンのみを取得できる場合は、(上記のように) デコードして、テナント ID、クライアント ID (アプリ ID)、およびリソースを確認できます。

## <a name="http-403-insufficient-permissions"></a>HTTP 403:アクセス許可が不十分である

HTTP 403 は、要求は認証されました (要求元の ID は認識されている) が、要求されたリソースにアクセスするためのアクセス許可がその ID にないことを意味します。 次の 2 つの原因が考えられます。

- ID のアクセス ポリシーがない。
- 要求しているリソースの IP アドレスが、キー コンテナーのファイアウォール設定で承認されていない。

HTTP 403 は、顧客のアプリケーションが、顧客が考えているクライアント ID を使用していない場合によく発生します。 通常、これは実際の呼び出し ID に対してアクセス ポリシーが正しく設定されていないことを意味します。

### <a name="troubleshooting-403"></a>403 のトラブルシューティング

まず、ログ記録を有効にします。 その方法については、「[Azure Key Vault のログ記録](logging.md)」を参照してください。

ログ記録を有効にすると、403 の原因がアクセス ポリシーとファイアウォール ポリシーのどちらであるかを判断できます。

#### <a name="error-due-to-firewall-policy"></a>ファイアウォール ポリシーによるエラー

"Client address (00.00.00.00) is not authorized and caller is not a trusted service" (クライアント アドレス (00.00.00.00) は承認されておらず、呼び出し元は信頼できるサービスではありません)

"Azure の信頼されたサービス" の限定的な一覧があります。 Azure Websites は、信頼された Azure サービスでは "**ありません**"。 詳細については、ブログ記事「[Key Vault Firewall access by Azure App Services (Azure App Services による Key Vault ファイアウォールへのアクセス)](https://azidentity.azurewebsites.net/post/2019/01/03/key-vault-firewall-access-by-azure-app-services)」を参照してください。

Azure Web サイトの IP アドレスを機能させるには、キー コンテナーに追加する必要があります。

アクセス ポリシーが原因の場合は、要求のオブジェクト ID を見つけ、ユーザーがアクセス ポリシーを割り当てようとしているオブジェクトと、オブジェクト ID が一致することを確認します。 多くの場合、AAD には同じ名前のオブジェクトが複数あるため、正しいオブジェクトを選択することが非常に重要です。 アクセス ポリシーを削除して再度追加すると、同じ名前のオブジェクトが複数存在するかどうかを確認できます。

さらに、ほとんどのアクセス ポリシーでは、ポータルに表示される "承認されているアプリケーション" の使用は必要ありません。 承認されているアプリケーションは、"代理" 認証シナリオに使用されますが、それはまれです。 


## <a name="http-429-too-many-requests"></a>HTTP 429:要求が多すぎます

要求の数が指定された時間枠の最大値を超えると、調整が発生します。 調整が発生した場合、キー コンテナーの応答は HTTP 429 になります。 作成される要求の種類には、最大値が指定されています。 たとえば、HSM 2048 ビット キーの作成は 5 要求/10 秒ですが、他のすべての HSM トランザクションには 1000 要求/10 秒の制限があります。 そのため、調整の原因を特定する際には、どの種類の呼び出しが行われているかを把握することが重要です。
一般に、キー コンテナーへの要求は、2000 要求/10 秒に制限されます。 [Key Vault サービスの制限](service-limits.md)に関するドキュメントに記載されているように、例外はキー操作です。

### <a name="troubleshooting-429"></a>429 のトラブルシューティング
調整を回避するには、次の手法を使用します。

- 要求されたリソースにパターンがあるかどうかを判断し、呼び出し元アプリケーションでそれらのキャッシュを試行することで、キー コンテナーに対して行われる要求の数を減らします。 

- Key Vault の調整が発生した場合は、再試行にエクスポネンシャル バックオフを使用するように要求コードを変更します。 アルゴリズムについては、次を参照してください。[アプリをスロットルする方法](overview-throttling.md#how-to-throttle-your-app-in-response-to-service-limits)

- キャッシュによって要求の数を減らすことができず、時間指定のバックオフが機能しない場合は、キーを複数のキー コンテナーに分割することを検討します。 1 つのサブスクリプションのサービス制限は、個々のキー コンテナー制限の 5 倍です。 使用しているキー コンテナー数が 5 個を超える場合は、複数サブスクリプションの使用を検討考慮することをお勧めします。 

制限の引き上げ要求などの詳細なガイダンスについては、次を参照してください。[Key Vault のスロットル ガイダンス](overview-throttling.md)
