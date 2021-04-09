---
title: Sign in with Apple を構成する (プレビュー)
description: App Service または Azure Functions アプリ用の ID プロバイダーとして、Sign in with Apple を構成する方法について説明します。
ms.topic: article
ms.date: 11/19/2020
ms.reviewer: mikarmar
ms.openlocfilehash: b77e0613f502d003b5e4651e34be4cadbd4209a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96603004"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-sign-in-using-a-sign-in-with-apple-provider-preview"></a>Sign in with Apple プロバイダーを使用してサインインするように、App Service または Azure Functions アプリを構成する (プレビュー)

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

この記事では、認証プロバイダーとして Sign in with Apple を使用するように、Azure App Service または Azure Functions を構成する方法について説明します。 

この記事の手順を完了するには、Apple Developer Program に登録しておく必要があります。 Apple Developer Program に登録するには、[developer.apple.com/programs/enroll](https://developer.apple.com/programs/enroll/) に移動します。

> [!CAUTION]
> Sign in with Apple を有効にすると、Azure portal、Azure CLI、Azure PowerShell などの一部のクライアントを介したアプリケーションの App Service の認証または承認機能の管理が無効になります。 この機能は、すべての管理エクスペリエンスに関してまだ考慮されていない、プレビュー段階の新しい API サーフェスに依存しています。

[comment]: <Remove this caution block when V2 becomes available for use.> 

## <a name="create-an-application-in-the-apple-developer-portal"></a><a name="createApplication"> </a>Apple Developer ポータルでアプリケーションを作成する
アプリ ID とサービス ID は、Apple Developer ポータルで作成する必要があります。

1. Apple Developer ポータルで、 **[Certificates, Identifiers, & Profiles]\(証明書、ID、プロファイル\)** に移動します。
2. **[Identifiers]\(ID\)** タブで、 **[+]** ボタンを選択します。
3. **[Register a New Identifier]\(新しい ID の登録\)** ページで、 **[App IDs]\(アプリ ID\)** を選び、 **[Continue]\(続行\)** を選択します (アプリ ID には 1 つまたは複数のサービス ID が含まれます)。![Apple Developer ポータルでの新しいアプリ識別子の登録](media/configure-authentication-provider-apple/apple-register-app.jpg)
4. **[Register an App ID]\(アプリ ID の登録\)** ページで、説明とバンドル ID を入力し、機能一覧から **[Sign in with Apple]** を選択します。 その後 **[続行]** を選択します。 この手順のご自分の **[App ID Prefix (Team ID)]\(アプリ ID プレフィックス (チーム ID)\)** をメモしておいてください。これは後で必要になります。
![Apple Developer ポータルでの新しいアプリ識別子の構成](media/configure-authentication-provider-apple/apple-configure-app.jpg)
5. アプリの登録情報を確認し、 **[Register]\(登録\)** を選択します。
6. ここでも、 **[Identifiers]\(ID\)** タブで **[+]** ボタンを選択します。
![Apple Developer ポータルでの新しいサービス識別子の作成](media/configure-authentication-provider-apple/apple-new-app.jpg) 
7. **[Register a New Identifier]\(新しい ID の登録\)** ページで、 **[Services IDs]\(サービス ID\)** を選び、 **[Continue]\(続行\)** を選択します。
![Apple Developer ポータルでの新しいサービス識別子の登録](media/configure-authentication-provider-apple/apple-register-service.jpg)
8. **[Register a Services ID]\(サービス ID の登録\)** ページで、説明と識別子を入力します。 説明は、同意画面でユーザーに表示される内容です。 この識別子は、App Service で Apple プロバイダーを構成するときに使用されるクライアント ID になります。 次に、 **[構成]** を選択します。
![説明と識別子の入力](media/configure-authentication-provider-apple/apple-configure-service-1.jpg)
9. ポップアップ ウィンドウで、[Primary App ID]\(プライマリ アプリ ID\) を、先ほど作成した App ID に設定します。 ドメイン セクションでアプリケーションのドメインを指定します。 リターン URL には、`<app-url>/.auth/login/apple/callback` URL を使用します。 たとえば、「 `https://contoso.azurewebsites.net/.auth/login/apple/callback` 」のように入力します。 その後、 **[Add]\(追加\)** 、 **[Save]\(保存\)** の順に選択します。
![登録用のドメインとリターン URL の指定](media/configure-authentication-provider-apple/apple-configure-service-2.jpg)
10. サービスの登録情報を確認し、 **[Save]\(保存\)** を選択します。

## <a name="generate-the-client-secret"></a><a name="generateClientSecret"> </a>クライアント シークレットを生成する
Apple では、アプリ開発者がクライアント シークレット値として JWT トークンを作成して署名する必要があります。 このシークレットを生成するには、まず、Apple Developer ポータルから楕円曲線の秘密キーを生成してダウンロードします。 その後、そのキーを使用し、[特定のペイロード](#structure-the-client-secret-jwt)を使って [JWT に署名](#sign-the-client-secret-jwt)します。

### <a name="create-and-download-the-private-key"></a>秘密キーを作成してダウンロードする
1. Apple Developer ポータルの **[Keys]\(キー\)** タブで、 **[Create a key]\(キーの作成\)** を選ぶか、 **[+]** ボタンを選択します。
2. **[Register a New Key]\(新しいキーの登録\)** ページで、キーに名前を付け、 **[Sign in with Apple]** の横にあるチェックボックスをオンにして、 **[Configure]\(構成\)** を選択します。
3. **[Configure Key]\(キーの構成\)** ページで、前に作成したプライマリ アプリ ID にキーをリンクし、 **[Save]\(保存\)** を選択します。
4. 情報を確認し、 **[Continue]\(続行\)** を選択してから、情報を見直し、 **[Register]\(登録\)** を選択してキーの作成を完了します。
5. **[Download Your Key]\(自分のキーのダウンロード\)** ページで、キーをダウンロードします。 これは `.p8` (PKCS # 8) ファイルとしてダウンロードされます。このファイルの内容を使用して、クライアント シークレット JWT に署名します。

### <a name="structure-the-client-secret-jwt"></a>クライアント シークレット JWT を構成する
Apple では、クライアント シークレットが JWT トークンの base64 エンコードである必要があります。 デコードされた JWT トークンには、この例のようなペイロードが構成されているはずです。
```json
{
  "alg": "ES256",
  "kid": "URKEYID001",
}.{
  "sub": "com.yourcompany.app1",
  "nbf": 1560203207,
  "exp": 1560289607,
  "iss": "ABC123DEFG",
  "aud": "https://appleid.apple.com"
}.[Signature]
```
- **sub**: Apple クライアント ID (サービス ID でもある)
- **iss**: ご利用の Apple Developer Team ID
- **aud**: Apple でトークンが受信されるため、それらが対象ユーザーとなります
- **exp**:**nbf** から 6 か月以内

上記のペイロードの base64 エンコード バージョンはこのようになります。```eyJhbGciOiJFUzI1NiIsImtpZCI6IlVSS0VZSUQwMDEifQ.eyJzdWIiOiJjb20ueW91cmNvbXBhbnkuYXBwMSIsIm5iZiI6MTU2MDIwMzIwNywiZXhwIjoxNTYwMjg5NjA3LCJpc3MiOiJBQkMxMjNERUZHIiwiYXVkIjoiaHR0cHM6Ly9hcHBsZWlkLmFwcGxlLmNvbSJ9.ABSXELWuTbgqfrIUz7bLi6nXvkXAz5O8vt0jB2dSHTQTib1x1DSP4__4UrlKI-pdzNg1sgeocolPNTmDKazO8-BHAZCsdeeTNlgFEzBytIpMKFfVEQbEtGRkam5IeclUK7S9oOva4EK4jV4VmgDrr-LGWWO3TaAxAvy3_ZoKohvFFkVG```

_注: Apple では、有効期限が作成 (または nbf) 日から 6 か月を超えるクライアント シークレット JWT は受け入れられません。つまり、クライアント シークレットは、少なくとも 6 か月ごとにローテーションする必要があります。_

トークンの生成と確認の詳細については、[Apple の開発者向けドキュメント](https://developer.apple.com/documentation/sign_in_with_apple/generate_and_validate_tokens)を参照してください。 

### <a name="sign-the-client-secret-jwt"></a>クライアント シークレット JWT に署名する
前にダウンロードした `.p8` ファイルを使用して、クライアント シークレット JWT に署名します。 このファイルは [PCKS#8 ファイル](https://en.wikipedia.org/wiki/PKCS_8)であり、PEM 形式の秘密署名キーが含まれています。 JWT の作成と署名に使用できるライブラリは多数あります。 

JWT トークンの作成と署名のためにオンラインで利用可能なさまざまな種類のオープンソース ライブラリがあります。 JWT トークンの生成の詳細については、jwt.io を参照してください。 たとえば、クライアント シークレットを生成する方法の 1 つとして、[NuGet の Microsoft.IdentityModel.Tokens パッケージ](https://www.nuget.org/packages/Microsoft.IdentityModel.Tokens/)をインポートし、以下に示す少量の C# コードを実行する方法があります。

```csharp
using Microsoft.IdentityModel.Tokens;

public static string GetAppleClientSecret(string teamId, string clientId, string keyId, string p8key)
{
    string audience = "https://appleid.apple.com";

    string issuer = teamId;
    string subject = clientId;
    string kid = keyId;

    IList<Claim> claims = new List<Claim> {
        new Claim ("sub", subject)
    };

    CngKey cngKey = CngKey.Import(Convert.FromBase64String(p8key), CngKeyBlobFormat.Pkcs8PrivateBlob);

    SigningCredentials signingCred = new SigningCredentials(
        new ECDsaSecurityKey(new ECDsaCng(cngKey)),
        SecurityAlgorithms.EcdsaSha256
    );

    JwtSecurityToken token = new JwtSecurityToken(
        issuer,
        audience,
        claims,
        DateTime.Now,
        DateTime.Now.AddDays(180),
        signingCred
    );
    token.Header.Add("kid", kid);
    token.Header.Remove("typ");

    JwtSecurityTokenHandler tokenHandler = new JwtSecurityTokenHandler();

    return tokenHandler.WriteToken(token);
}
```
- **teamId**: ご利用の Apple Developer Team ID
- **clientId**: Apple クライアント ID (サービス ID でもある)
- **p8key**: PEM 形式のキー - テキスト エディターで `.p8` ファイルを開き、改行なしで `-----BEGIN PRIVATE KEY-----` と `-----END PRIVATE KEY-----` の間のすべてをコピーすることで、キーを取得できます。
- **keyId**: ダウンロードされたキーの ID

この返されたトークンが、Apple プロバイダーの構成に使用するクライアント シークレットの値となります。

> [!IMPORTANT]
> クライアント シークレットは、重要なセキュリティ資格情報です。 このシークレットを他のユーザーと共有したり、クライアント アプリケーション内で配信したりしないでください。
>

選択した設定名を使用して、クライアント シークレットをアプリの[アプリケーション設定](./configure-common.md#configure-app-settings)として追加します。 後で使用するためにこの名前をメモしておきます。

## <a name="add-provider-information-to-your-application"></a><a name="configure"> </a>アプリケーションにプロバイダー情報を追加する

> [!NOTE]
> 必要な構成は新しい API 形式ですが、現時点では [ファイルベースの構成 (プレビュー)](.\app-service-authentication-how-to.md#config-file) でのみサポートされています。 このようなファイルを使用して、以下の手順を行う必要があります。

このセクションでは、新しい IDP を含めるように構成を更新する手順について説明します。 構成の例を次に示します。

1. `identityProviders` オブジェクト内に `apple` オブジェクトがまだ存在しない場合は追加します。
2. オブジェクトを `registration` オブジェクトが含まれているそのキーに割り当て、オプションで `login` オブジェクトを割り当てます。
    
    ```json
    "apple" : {
       "registration" : {
            "clientId": "<client id>",
            "clientSecretSettingName": "APP_SETTING_CONTAINING_APPLE_CLIENT_SECRET" 
        },
       "login": {
             "scopes": []
       }
    }
    ```
    a. `registration` オブジェクト内で、収集したクライアント ID に `clientId` を設定します。
    
    b. `registration` オブジェクト内で、クライアント シークレットを格納したアプリケーション設定の名前に `clientSecretSettingName` を設定します。
    
    c. `login` オブジェクト内で、"name" や "email" など、Apple での認証時に使用されるスコープの一覧を含めるように `scopes` 配列を設定することを選択できます。 スコープが構成されている場合は、ユーザーが初めてサインインするときに同意画面でそれらが明示的に要求されます。

この構成が設定されると、アプリで認証に Apple プロバイダーを使用できるようになります。

完全な構成は次の例のようになります (ここで、APPLE_GENERATED_CLIENT_SECRET 設定は、生成された JWT を含むアプリケーション設定を指します)。

```json
{
    "platform": {
        "enabled": true
    },
    "globalValidation": {
        "redirectToProvider": "apple",
        "unauthenticatedClientAction": "RedirectToLoginPage"
    },
    "identityProviders": {
        "apple": {
            "registration": {
                "clientId": "com.contoso.example.client",
                "clientSecretSettingName": "APPLE_GENERATED_CLIENT_SECRET"
            },
            "login": {
                "scopes": []
            }
        }
    },
    "login": {
        "tokenStore": {
            "enabled": true
        }
    }     
}
```

## <a name="next-steps"></a><a name="related-content"> </a>次の手順

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]
