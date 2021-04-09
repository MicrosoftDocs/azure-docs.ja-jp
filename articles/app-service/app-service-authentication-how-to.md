---
title: 認証/承認の高度な使用方法
description: App Service でさまざまなシナリオに合わせて認証および承認機能をカスタマイズし、ユーザーの要求とさまざまなトークンを取得する方法について説明します。
ms.topic: article
ms.date: 07/08/2020
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 50587feff29e1c02a639d63d0c99156dcec4f68e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102180872"
---
# <a name="advanced-usage-of-authentication-and-authorization-in-azure-app-service"></a>Azure App Service 上での認証と承認の高度な使用方法

この記事では、[App Service 上で組み込みの認証と承認](overview-authentication-authorization.md)をカスタマイズする方法と、アプリケーションから ID を管理する方法について説明します。 

すぐに開始するには、以下のチュートリアルのいずれかをご覧ください。

* [チュートリアル:Azure App Service でユーザーをエンド ツー エンドで認証および承認する](tutorial-auth-aad.md)
* [Azure Active Directory ログインを使用するようにアプリを構成する方法](configure-authentication-provider-aad.md)
* [Facebook ログインを使用するようにアプリを構成する方法](configure-authentication-provider-facebook.md)
* [Google ログインを使用するようにアプリを構成する方法](configure-authentication-provider-google.md)
* [Microsoft アカウント ログインを使用するようにアプリを構成する方法](configure-authentication-provider-microsoft.md)
* [Twitter ログインを使用するようにアプリを構成する方法](configure-authentication-provider-twitter.md)
* [OpenID Connect プロバイダーを使用してログインするようにアプリを構成する方法 (プレビュー)](configure-authentication-provider-openid-connect.md)
* [Sign in with Apple を使用してログインするようにアプリを構成する方法 (プレビュー)](configure-authentication-provider-apple.md)

## <a name="use-multiple-sign-in-providers"></a>複数のサインイン プロバイダーを使用する

ポータル構成では、ユーザーに複数 (Facebook と Twitter の両方など) のサインイン プロバイダーを表示するターンキー手法は提供されません。 ただし、この機能をアプリに追加することは難しくありません。 手順の概要は次のとおりです。

最初に、Azure Portal の **[認証/承認]** ページで、有効にする各 ID プロバイダーを構成します。

**[要求が認証されない場合に実行するアクション]** で、 **[匿名要求を許可する (操作不要)]** を選択します。

サインイン ページ、ナビゲーション バー、またはアプリのその他の任意の場所で、有効にした各プロバイダーへのサインイン リンク (`/.auth/login/<provider>`) を追加します。 次に例を示します。

```html
<a href="/.auth/login/aad">Log in with Azure AD</a>
<a href="/.auth/login/microsoftaccount">Log in with Microsoft Account</a>
<a href="/.auth/login/facebook">Log in with Facebook</a>
<a href="/.auth/login/google">Log in with Google</a>
<a href="/.auth/login/twitter">Log in with Twitter</a>
<a href="/.auth/login/apple">Log in with Apple</a>
```

ユーザーがいずれかのリンクをクリックすると、それぞれのサインイン ページが開き、ユーザーがサインインできます。

サインイン後のユーザーをカスタム URL にリダイレクトさせるには、`post_login_redirect_url` クエリ文字列パラメーターを使用します (ご利用の ID プロバイダーの構成におけるリダイレクト URI と混同しないでください)。 たとえば、サインイン後にユーザーを `/Home/Index` にリダイレクトさせるには、次の HTML コードを使用します。

```html
<a href="/.auth/login/<provider>?post_login_redirect_url=/Home/Index">Log in</a>
```

## <a name="validate-tokens-from-providers"></a>プロバイダーからのトークンを検証する

クライアント主導のサインインでは、アプリケーションはユーザーをプロバイダーに手動でサインインさせ、検証のために認証トークンを App Service に送信します (「[Authentication flow](overview-authentication-authorization.md#authentication-flow)」をご覧ください)。 この検証自体では、必要なアプリ リソースへのアクセス権が実際には付与されませんが、検証に成功すると、アプリ リソースへのアクセスに使用できるセッション トークンが付与されます。 

プロバイダーのトークンを検証するには、最初に目的のプロバイダーを使用して App Service のアプリが構成されている必要があります。 実行時に、プロバイダーから認証トークンを取得した後、検証のためにトークンを `/.auth/login/<provider>` にポストします。 次に例を示します。 

```
POST https://<appname>.azurewebsites.net/.auth/login/aad HTTP/1.1
Content-Type: application/json

{"id_token":"<token>","access_token":"<token>"}
```

トークンの形式は、プロバイダーによって若干異なります。 詳しくは、以下の表をご覧ください。

| プロバイダーの値 | 要求本文に必要 | 説明 |
|-|-|-|
| `aad` | `{"access_token":"<access_token>"}` | |
| `microsoftaccount` | `{"access_token":"<token>"}` | `expires_in` プロパティは省略可能です。 <br/>ライブ サービスからトークンを要求する場合は、常に `wl.basic` スコープを要求します。 |
| `google` | `{"id_token":"<id_token>"}` | `authorization_code` プロパティは省略可能です。 指定した場合、必要に応じて `redirect_uri` プロパティを添付することもできます。 |
| `facebook`| `{"access_token":"<user_access_token>"}` | Facebook からの有効な[ユーザー アクセス トークン](https://developers.facebook.com/docs/facebook-login/access-tokens)を使用します。 |
| `twitter` | `{"access_token":"<access_token>", "access_token_secret":"<acces_token_secret>"}` | |
| | | |

プロバイダー トークンが正常に検証された場合、API は、セッション トークンである `authenticationToken` を応答本文に入れて戻ります。 

```json
{
    "authenticationToken": "...",
    "user": {
        "userId": "sid:..."
    }
}
```

このセッション トークンを入手したら、`X-ZUMO-AUTH` ヘッダーを HTTP 要求に追加することで、保護対象のアプリ リソースにアクセスすることができます。 次に例を示します。 

```
GET https://<appname>.azurewebsites.net/api/products/1
X-ZUMO-AUTH: <authenticationToken_value>
```

## <a name="sign-out-of-a-session"></a>セッションからサインアウトする

ユーザーは、アプリの `/.auth/logout` エンドポイントに `GET` 要求を送信することでサインアウトを開始できます。 `GET` 要求は次の処理を行います。

- 現在のセッションから認証 Cookie をクリアします。
- トークン ストアから現在のユーザーのトークンを削除します。
- Azure Active Directory と Google の場合、ID プロバイダーでサーバー側のサインアウトを実行します。

Web ページの簡単なサインアウト リンクを次に示します。

```html
<a href="/.auth/logout">Sign out</a>
```

既定では、サインアウトに成功すると、クライアントは URL `/.auth/logout/done` にリダイレクトされます。 `post_logout_redirect_uri` クエリ パラメーターを追加して、サインアウト後のリダイレクト ページを変更できます。 次に例を示します。

```
GET /.auth/logout?post_logout_redirect_uri=/index.html
```

`post_logout_redirect_uri` の値を[エンコード](https://wikipedia.org/wiki/Percent-encoding)することをお勧めします。

完全修飾 URL を使用している場合、URL は同じドメインでホストされているか、アプリの許可された外部リダイレクト URL として構成されている必要があります。 次の例では、同じドメインにホストされていない `https://myexternalurl.com` にリダイレクトします。

```
GET /.auth/logout?post_logout_redirect_uri=https%3A%2F%2Fmyexternalurl.com
```

[Azure Cloud Shell](../cloud-shell/quickstart.md) で次のコマンドを実行します。

```azurecli-interactive
az webapp auth update --name <app_name> --resource-group <group_name> --allowed-external-redirect-urls "https://myexternalurl.com"
```

## <a name="preserve-url-fragments"></a>URL フラグメントを保持する

ユーザーは、アプリにサインインすると、通常は `/wiki/Main_Page#SectionZ` のように同じページの同じセクションにリダイレクトされたいと考えます。 ただし、[URL フラグメント](https://wikipedia.org/wiki/Fragment_identifier) (たとえば `#SectionZ`) はサーバーに送信されないため、OAuth のサインインが完了してアプリにリダイレクトされた後は既定で保持されません。 目的のアンカーに再度移動する必要がある場合、ユーザー エクスペリエンスは最適ではありません。 この制限は、すべてのサーバー側認証ソリューションに適用されます。

App Service 認証では、OAuth サインイン全体で URL フラグメントを保存できます。 これを行うには、`WEBSITE_AUTH_PRESERVE_URL_FRAGMENT` というアプリ設定を `true` に設定します。 [Azure portal](https://portal.azure.com) で行うか、[Azure Cloud Shell](../cloud-shell/quickstart.md) で次のコマンドを実行するだけです。

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group <group_name> --settings WEBSITE_AUTH_PRESERVE_URL_FRAGMENT="true"
```

## <a name="access-user-claims"></a>ユーザー要求へのアクセス

App Service では、特殊なヘッダーを使用して、アプリケーションにユーザー要求を渡します。 外部要求ではこれらのヘッダーの設定が許可されないので、これらのヘッダーは App Service によって設定されている場合にのみ存在します。 いくつかのヘッダーの例は次のとおりです。

* X-MS-CLIENT-PRINCIPAL-NAME
* X-MS-CLIENT-PRINCIPAL-ID

任意の言語またはフレームワークで記述されたコードで、これらのヘッダーから必要な情報を取得できます。 ASP.NET 4.6 アプリの場合は、 **ClaimsPrincipal** が自動的に適切な値に設定されます。 ただし、ASP.NET Core では、App Service のユーザー要求と統合する認証ミドルウェアが提供されません。 回避策については、「[MaximeRouiller.Azure.AppService.EasyAuth](https://github.com/MaximRouiller/MaximeRouiller.Azure.AppService.EasyAuth)」を参照してください。

アプリで[トークン ストア](overview-authentication-authorization.md#token-store)が有効になっている場合は、`/.auth/me` を呼び出して認証されたユーザーの追加の詳細を取得することもできます。 Mobile Apps サーバー SDK には、このデータを操作するためのヘルパー メソッドが用意されています。 詳細については、「[Azure Mobile Apps Node.js SDK の使用方法](/previous-versions/azure/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk#howto-tables-getidentity)」と「[Azure Mobile Apps 用 .NET バックエンド サーバー SDK の操作](/previous-versions/azure/app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk#user-info)」を参照してください。

## <a name="retrieve-tokens-in-app-code"></a>アプリ コードでのトークンの取得

サーバー コードからプロバイダー固有のトークンが要求ヘッダーに挿入されるので、これらのトークンに簡単にアクセスできます。 次の表は、可能なトークン ヘッダー名を示しています。

| プロバイダー | ヘッダー名 |
|-|-|
| Azure Active Directory | `X-MS-TOKEN-AAD-ID-TOKEN` <br/> `X-MS-TOKEN-AAD-ACCESS-TOKEN` <br/> `X-MS-TOKEN-AAD-EXPIRES-ON`  <br/> `X-MS-TOKEN-AAD-REFRESH-TOKEN` |
| Facebook トークン | `X-MS-TOKEN-FACEBOOK-ACCESS-TOKEN` <br/> `X-MS-TOKEN-FACEBOOK-EXPIRES-ON` |
| Google | `X-MS-TOKEN-GOOGLE-ID-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-ACCESS-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-EXPIRES-ON` <br/> `X-MS-TOKEN-GOOGLE-REFRESH-TOKEN` |
| Microsoft アカウント | `X-MS-TOKEN-MICROSOFTACCOUNT-ACCESS-TOKEN` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-EXPIRES-ON` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-AUTHENTICATION-TOKEN` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-REFRESH-TOKEN` |
| Twitter | `X-MS-TOKEN-TWITTER-ACCESS-TOKEN` <br/> `X-MS-TOKEN-TWITTER-ACCESS-TOKEN-SECRET` |
|||

クライアント コード (モバイル アプリやブラウザー内の JavaScript など) から、HTTP `GET` 要求を `/.auth/me` に送信します ([トークン ストア](overview-authentication-authorization.md#token-store)を有効にする必要があります)。 返される JSON にはプロバイダー固有のトークンがあります。

> [!NOTE]
> アクセス トークンはプロバイダー リソースへのアクセス用であるため、クライアント シークレットを使用してプロバイダーを構成する場合にのみ存在します。 更新トークンを取得する方法を確認するには、「更新アクセス トークン」を参照してください。

## <a name="refresh-identity-provider-tokens"></a>ID プロバイダー トークンの更新

プロバイダーのアクセス トークン ([セッション トークン](#extend-session-token-expiration-grace-period)ではなく) が期限切れになった場合は、そのトークンを再度使用する前に、ユーザーを再認証する必要があります。 アプリケーションの `/.auth/refresh` エンドポイントに `GET` 呼び出しを行って、トークンの期限切れを回避することができます。 呼び出されると、App Service は認証されたユーザーの[トークン ストア](overview-authentication-authorization.md#token-store)内のアクセス トークンを自動的に更新します。 アプリ コードによる後続のトークン要求で、更新トークンを取得します。 ただし、トークンの更新が動作するためには、トークン ストアにプロバイダーの[更新トークン](https://auth0.com/learn/refresh-tokens/)が含まれている必要があります。 更新トークンの取得方法は各プロバイダーによって文書化されていますが、次の一覧に概要を示します。

- **Google**: `access_type=offline` クエリ文字列パラメーターを `/.auth/login/google` API 呼び出しに追加します。 Mobile Apps SDK を使用している場合は、`LogicAsync` オーバーロードの 1 つにパラメーターを追加できます ([Google 更新トークン](https://developers.google.com/identity/protocols/OpenIDConnect#refresh-tokens)に関するページをご覧ください)。
- **Facebook**: 更新トークンを提供しません。 長期間維持されるトークンの有効期限は 60 日間です ([Facebook のアクセス トークンの有効期限と延長](https://developers.facebook.com/docs/facebook-login/access-tokens/expiration-and-extension)に関するページをご覧ください)。
- **Twitter**: アクセス トークンに有効期限はありません ([Twitter OAuth の FAQ](https://developer.twitter.com/en/docs/authentication/faq) に関するページを参照してください)。
- **Microsoft アカウント**: [Microsoft アカウント認証設定を構成する](configure-authentication-provider-microsoft.md)場合は、`wl.offline_access` スコープを選択します。
- **Azure Active Directory**: [https://resources.azure.com](https://resources.azure.com) で、次の手順を実行します。
    1. ページの上部にある **[Read/Write]** を選択します。
    2. 左側のブラウザーで、**subscriptions** > ** _\<subscription\_name_** > **resourceGroups** > **_ \<resource\_group\_name> _** > **providers** > **Microsoft.Web** > **sites** > **_ \<app\_name>_** > **config** > **authsettings** に移動します。 
    3. **[編集]** をクリックします。
    4. 次のプロパティを変更します。 _\<app\_id>_ を、アクセスするサービスの Azure Active Directory アプリケーション ID に置き換えます。

        ```json
        "additionalLoginParams": ["response_type=code id_token", "resource=<app_id>"]
        ```

    5. **[Put]** をクリックします。 

プロバイダーが構成されたら、トークン ストアで[更新トークンを見つけ、そのアクセス トークンの有効期限を確認](#retrieve-tokens-in-app-code)できます。 

任意の時点でアクセス トークンを更新するには、任意の言語で `/.auth/refresh` を呼び出します。 次のスニペットでは、jQuery を使用して、JavaScript クライアントからアクセス トークンを更新します。

```javascript
function refreshTokens() {
  let refreshUrl = "/.auth/refresh";
  $.ajax(refreshUrl) .done(function() {
    console.log("Token refresh completed successfully.");
  }) .fail(function() {
    console.log("Token refresh failed. See application logs for details.");
  });
}
```

ユーザーがアプリに許可されている権限を取り消すと、`/.auth/me` の呼び出しは `403 Forbidden` 応答で失敗する可能性があります。 エラーを診断するには、アプリケーション ログで詳細を確認します。

## <a name="extend-session-token-expiration-grace-period"></a>セッション トークンの有効期限の猶予期間の延長

認証されたセッションは、8 時間後に期限切れになります。 認証されたセッションの期限が切れた後、既定で 72 時間の猶予期間があります。 この猶予期間内は、ユーザーを再認証せずにセッション トークンを App Service で更新できます。 セッション トークンが無効になったときに `/.auth/refresh` を呼び出すことができ、トークンの有効期限を自分で追跡する必要はありません。 72 時間の猶予期間が経過した後、ユーザーはもう一度サインインして有効なセッション トークンを取得する必要があります。

72 時間が十分な時間でない場合は、この有効期間を延長することができます。 有効期限を長期に延長すると、セキュリティに大きく影響する可能性があります (認証トークンが漏洩または盗難にあった場合など)。 したがって、既定の 72 時間のままにするか、延長期間を最小限の値に設定する必要があります。

既定の有効期間を延長するには、[Cloud Shell](../cloud-shell/overview.md) で次のコマンドを実行します。

```azurecli-interactive
az webapp auth update --resource-group <group_name> --name <app_name> --token-refresh-extension-hours <hours>
```

> [!NOTE]
> 猶予期間は、ID プロバイダーからのトークンではなく、App Service で認証されたセッションにのみ適用されます。 有効期限が切れたプロバイダー トークンの猶予期間はありません。 
>

## <a name="limit-the-domain-of-sign-in-accounts"></a>サインイン アカウントのドメインの制限

Microsoft アカウントと Azure Active Directory の両方に複数のドメインからサインインできます。 たとえば、Microsoft アカウントでは _outlook.com_、_live.com_、_hotmail.com_ アカウントが許可されます。 Azure AD では、サインイン アカウントに任意の数のカスタム ドメインが許可されます。 ただし、ユーザーを独自のブランドの Azure AD サインインページ (`contoso.com`など) に直接誘導することもできます。 サインイン アカウントのドメイン名を提示するには、以下の手順に従います。

[https://resources.azure.com](https://resources.azure.com) で、**subscriptions** > ** _\<subscription\_name_** > **resourceGroups** > **_ \<resource\_group\_name> _** > **providers** > **Microsoft.Web** > **sites** > **_ \<app\_name> _** > **config** > **authsettings** に移動します。 

**[Edit]** をクリックし、次のプロパティを変更し、 **[Put]** をクリックします。 _\<domain\_name>_ は使用するドメインで置き換えてください。

```json
"additionalLoginParams": ["domain_hint=<domain_name>"]
```

この設定で、`domain_hint` クエリ文字列パラメーターがログイン リダイレクト URL に追加されます。 

> [!IMPORTANT]
> クライアントは、リダイレクト URL を受け取った後で `domain_hint` パラメーターを削除し、別のドメインでログインできます。 そのため、この機能は便利ですが、セキュリティ機能ではありません。
>

## <a name="authorize-or-deny-users"></a>ユーザーを承認または拒否する

App Service は最も単純な承認ケース (つまり、認証されていない要求の拒否) を処理しますが、アプリでは、特定のユーザー グループだけにアクセスを制限するなど、よりきめ細かな承認動作が必要になる場合があります。 場合によっては、サインインしたユーザーに対してアクセスを許可または拒否するためのカスタム アプリケーション コードの記述が必要になります。 また、App Service やお使いの ID プロバイダーが、コードの変更を必要とせずにサポートできる場合もあります。

- [サーバー レベル](#server-level-windows-apps-only)
- [ID プロバイダー レベル](#identity-provider-level)
- [アプリケーション レベル](#application-level)

### <a name="server-level-windows-apps-only"></a>サーバー レベル (Windows アプリのみ)

Windows アプリでは、*Web.config* ファイルを編集して IIS Web サーバーの承認動作を定義できます。 Linux アプリは、IIS を使用しないため、*Web.config* を使用して構成することはできません。

1. `https://<app-name>.scm.azurewebsites.net/DebugConsole` に移動します

1. App Service ファイルのブラウザー エクスプローラーで、*site/wwwroot* に移動します。 *Web.config* が存在しない場合は、 **+**  >  **[新しいファイル]** を選択して作成します。 

1. *Web.config* の鉛筆を選択して編集します。 次の構成コードを追加し、 **[保存]** をクリックします。 *Web.config* が既に存在する場合は、すべての内容を含めた `<authorization>` 要素を追加するだけです。 許可するアカウントを `<allow>` 要素に追加します。

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <configuration>
       <system.web>
          <authorization>
            <allow users="user1@contoso.com,user2@contoso.com"/>
            <deny users="*"/>
          </authorization>
       </system.web>
    </configuration>
    ```

### <a name="identity-provider-level"></a>ID プロバイダー レベル

ID プロバイダーによって特定のターンキー承認が提供される場合があります。 次に例を示します。

- [Azure App Service](configure-authentication-provider-aad.md) の場合、Azure AD で直接、[エンタープライズ レベルのアクセスを管理](../active-directory/manage-apps/what-is-access-management.md)できます。 説明については、「[アプリケーションへのユーザー アクセスの削除方法](../active-directory/manage-apps/methods-for-removing-user-access.md)」をご覧ください。
- [Google](configure-authentication-provider-google.md) の場合、特定の [組織](https://cloud.google.com/resource-manager/docs/cloud-platform-resource-hierarchy#organizations)に属する Google API プロジェクトを、その組織内のユーザーだけにアクセスを許可するように構成できます ([Google の「**Setting up OAuth 2.0**」サポート ページ](https://support.google.com/cloud/answer/6158849?hl=en)をご覧ください)。

### <a name="application-level"></a>アプリケーション レベル

他のいずれのレベルでも必要な承認が提供されない場合、またはお使いのプラットフォームまたは ID プロバイダーがサポートされていない場合、[ユーザーの要求](#access-user-claims)に基づいてユーザーを承認するカスタム コードを記述する必要があります。

## <a name="updating-the-configuration-version-preview"></a>構成バージョンの更新 (プレビュー)

認証および承認機能用の管理 API には、2 つのバージョンがあります。 Azure portal での "認証 (プレビュー)" エクスペリエンスには、プレビュー V2 バージョンが必要です。 V1 の API が既に使用されているアプリは、いくつかの変更が行われた後に、V2 バージョンにアップグレードできます。 具体的には、シークレット構成を、スロット固定のアプリケーション設定に移動する必要があります。 Microsoft アカウント プロバイダーの構成も、V2 では現在サポートされていません。

> [!WARNING]
> V2 プレビューに移行すると、Azure portal、Azure CLI、Azure PowerShell の既存のエクスペリエンスなど、一部のクライアントを介したアプリケーションに対する App Service の認証または承認機能の管理が無効になります。 これを元に戻すことはできません。 プレビュー期間中は、運用ワークロードの移行は推奨されず、サポートもされていません。 このセクションの手順は、テスト アプリケーションのみを対象に実行してください。

### <a name="moving-secrets-to-application-settings"></a>シークレットをアプリケーション設定に移動

1. V1 API を使用して既存の構成を取得します。

   ```azurecli
   # For Web Apps
   az webapp auth show -g <group_name> -n <site_name>

   # For Azure Functions
   az functionapp auth show -g <group_name> -n <site_name>
   ```

   結果として取得した JSON ペイロードで、構成した各プロバイダーに使用されているシークレット値をメモします。

   * AAD: `clientSecret`
   * Google: `googleClientSecret`
   * Facebook: `facebookAppSecret`
   * Twitter: `twitterConsumerSecret`
   * Microsoft アカウント: `microsoftAccountClientSecret`

   > [!IMPORTANT]
   > シークレット値は重要なセキュリティ資格情報であり、慎重に扱う必要があります。 これらの値を共有したり、ローカル マシン上に保持したりしないでください。

1. 各シークレット値のスロット固定アプリケーション設定を作成します。 各アプリケーション設定の名前を選択できます。 この値は、前の手順で取得した値と一致しているか、その値を使用して作成した [Key Vault シークレットを参照](./app-service-key-vault-references.md?toc=/azure/azure-functions/toc.json)している必要があります。

   この設定を作成するには、Azure portal を使用するか、プロバイダーごとに次の方法を実行できます。

   ```azurecli
   # For Web Apps, Google example    
   az webapp config appsettings set -g <group_name> -n <site_name> --slot-settings GOOGLE_PROVIDER_AUTHENTICATION_SECRET=<value_from_previous_step>

   # For Azure Functions, Twitter example
   az functionapp config appsettings set -g <group_name> -n <site_name> --slot-settings TWITTER_PROVIDER_AUTHENTICATION_SECRET=<value_from_previous_step>
   ```

   > [!NOTE]
   > この構成のアプリケーション設定はスロット固定としてマークする必要があります。つまり、それらは、[スロット スワップ操作](./deploy-staging-slots.md)中に環境間を移動しません。 これは、認証構成自体が環境に関連付けられているからです。 

1. `authsettings.json` という名前の新しい JSON ファイルを作成します。前に受け取った出力から各シークレット値を削除します。 シークレットが含まれていないことを確認して、残りの出力をファイルに書き込みます。 場合によっては、空の文字列を含む配列が構成に含まれていることがあります。 `microsoftAccountOAuthScopes` がそうでないことを確認し、そうである場合は、その値を `null` に切り替えます。

1. 前の手順で作成した各プロバイダーのアプリケーション設定名を指すプロパティを `authsettings.json` に追加します。
 
   * AAD: `clientSecretSettingName`
   * Google: `googleClientSecretSettingName`
   * Facebook: `facebookAppSecretSettingName`
   * Twitter: `twitterConsumerSecretSettingName`
   * Microsoft アカウント: `microsoftAccountClientSecretSettingName`

   この操作後のサンプル ファイルは、次のようになります。この場合は、AAD 用にのみ構成されています。

   ```json
   {
       "id": "/subscriptions/00d563f8-5b89-4c6a-bcec-c1b9f6d607e0/resourceGroups/myresourcegroup/providers/Microsoft.Web/sites/mywebapp/config/authsettings",
       "name": "authsettings",
       "type": "Microsoft.Web/sites/config",
       "location": "Central US",
       "properties": {
           "enabled": true,
           "runtimeVersion": "~1",
           "unauthenticatedClientAction": "AllowAnonymous",
           "tokenStoreEnabled": true,
           "allowedExternalRedirectUrls": null,
           "defaultProvider": "AzureActiveDirectory",
           "clientId": "3197c8ed-2470-480a-8fae-58c25558ac9b",
           "clientSecret": null,
           "clientSecretSettingName": "MICROSOFT_IDENTITY_AUTHENTICATION_SECRET",
           "clientSecretCertificateThumbprint": null,
           "issuer": "https://sts.windows.net/0b2ef922-672a-4707-9643-9a5726eec524/",
           "allowedAudiences": [
               "https://mywebapp.azurewebsites.net"
           ],
           "additionalLoginParams": null,
           "isAadAutoProvisioned": true,
           "aadClaimsAuthorization": null,
           "googleClientId": null,
           "googleClientSecret": null,
           "googleClientSecretSettingName": null,
           "googleOAuthScopes": null,
           "facebookAppId": null,
           "facebookAppSecret": null,
           "facebookAppSecretSettingName": null,
           "facebookOAuthScopes": null,
           "gitHubClientId": null,
           "gitHubClientSecret": null,
           "gitHubClientSecretSettingName": null,
           "gitHubOAuthScopes": null,
           "twitterConsumerKey": null,
           "twitterConsumerSecret": null,
           "twitterConsumerSecretSettingName": null,
           "microsoftAccountClientId": null,
           "microsoftAccountClientSecret": null,
           "microsoftAccountClientSecretSettingName": null,
           "microsoftAccountOAuthScopes": null,
           "isAuthFromFile": "false"
       }   
   }
   ```

1. このファイルをアプリの新しい認証および承認構成として送信します。

   ```azurecli
   az rest --method PUT --url "/subscriptions/<subscription_id>/resourceGroups/<group_name>/providers/Microsoft.Web/sites/<site_name>/config/authsettings?api-version=2020-06-01" --body @./authsettings.json
   ```

1. この後もアプリが想定どおりに動作していることを確認します。

1. 前の手順で使用したファイルを削除します。

これで、ID プロバイダーのシークレットをアプリケーション設定として格納するためにアプリが移行されました。

### <a name="support-for-microsoft-account-registrations"></a>Microsoft アカウント登録のサポート

V2 API では、現在、Microsoft アカウントは個別のプロバイダーとしてサポートされていません。 むしろ、集中型 [Microsoft ID プラットフォーム](../active-directory/develop/v2-overview.md)を活用し、個人の Microsoft アカウントを使用してユーザーをサインインさせます。 V2 API に切り替えるときは、V1 の Azure Active Directory 構成を使用して Microsoft ID プラットフォーム プロバイダーが構成されます。

既存の構成に Microsoft アカウント プロバイダーが含まれており、Azure Active Directory プロバイダーが含まれていない場合は、構成を Azure Active Directory プロバイダーに切り替えてから、移行を実行できます。 これを行うには、次の手順を実行します。

1. Azure portal で [ **[アプリの登録]**](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) に移動して、お使いの Microsoft アカウント プロバイダーに関連付けられている登録を見つけます。 [個人用アカウントからのアプリケーション] という見出しの下に表示されている場合があります。
1. その登録の [認証] ページに移動します。 [リダイレクト URI] の下に、末尾が `/.auth/login/microsoftaccount/callback` のエントリが表示されます。 この URI をコピーします。
1. コピーしたものと一致する新しい URI を追加します。ただし、末尾は `/.auth/login/aad/callback` にする必要があります。 これにより、App Service の認証および承認構成でこの登録を使用できるようになります。
1. アプリの App Service 認証および承認構成に移動します。
1. Microsoft アカウント プロバイダーの構成を収集します。
1. 前の手順で収集したクライアント ID とクライアント シークレットの値を指定し、"詳細" 管理モードを使用して Azure Active Directory プロバイダーを構成します。 発行者の URL については、`<authentication-endpoint>/<tenant-id>/v2.0` を使用し、 *\<authentication-endpoint>* を [クラウド環境の認証エンドポイント](../active-directory/develop/authentication-national-cloud.md#azure-ad-authentication-endpoints) (たとえば、グローバル Azure の場合、"https://login.microsoftonline.com") に置き換え、さらに、 *\<tenant-id>* を **ディレクトリ (テナント) ID** に置き換えます。
1. 構成を保存したら、ブラウザーでサイトの `/.auth/login/aad` エンドポイントに移動し、サインイン フローを完了して、ログイン フローをテストします。
1. この時点で、構成は正常にコピーされましたが、既存の Microsoft アカウント プロバイダーの構成はそのまま残っています。 それを削除する前に、アプリのすべての部分で、ログイン リンクなどを使用して Azure Active Directory プロバイダーが参照されていることを確認してください。アプリのすべての部分が想定どおりに動作することを確認します。
1. AAD Azure Active Directory プロバイダーに対して機能することを検証したら、Microsoft アカウント プロバイダーの構成を削除できます。

一部のアプリでは、Azure Active Directory と Microsoft アカウント用に別々の登録が既に存在している場合があります。 現時点では、これらのアプリを移行することはできません。 

> [!WARNING]
> AAD アプリの登録で[サポートされているアカウントの種類](../active-directory/develop/supported-accounts-validation.md)を変更することにより、この 2 つの登録を集中させることができます。 ただし、これにより、Microsoft アカウント ユーザーに対して新しい同意プロンプトが強制され、それらのユーザーの ID 要求の構造が異なるものになる場合があります。特に、新しいアプリ ID が使用されてから、`sub` で値が変化します。 この方法は、十分に理解している場合を除き、推奨されません。 代わりに、V2 API でこの 2 つの登録に対するサポートが提供されるのを待つことをお勧めします。

### <a name="switching-to-v2"></a>V2 への切り替え

上記の手順を実行したら、Azure portal でアプリに移動します。 [認証 (プレビュー)] セクションを選択します。 

または、サイト リソースの下にある `config/authsettingsv2` リソースに対して PUT 要求を行うこともできます。 ペイロードのスキーマは、「[ファイルを使用して構成する](#config-file)」セクションで取り込まれるものと同じです。

## <a name="configure-using-a-file-preview"></a><a name="config-file"> </a>ファイルを使用して構成する (プレビュー)

認証設定は、必要に応じて、デプロイによって提供されるファイルを使用して構成できます。 これは、App Service の認証または承認の特定のプレビュー機能で必要になる場合があります。

> [!IMPORTANT]
> アプリのペイロード、つまりこのファイルは、[スロット](./deploy-staging-slots.md)と同様に環境間を移動する場合があることに注意してください。 各スロットに異なるアプリ登録を固定したい場合はよくありますが、このような場合は、構成ファイルを使用する代わりに、標準の構成方法を使用し続ける必要があります。

### <a name="enabling-file-based-configuration"></a>ファイルベースの構成の有効化

> [!CAUTION]
> プレビュー期間中、ファイルベースの構成を有効にすると、Azure portal、Azure CLI、Azure PowerShell などの一部のクライアントを介したアプリケーションの App Service の認証または承認機能の管理が無効になります。

1. 構成用の新しい JSON ファイルをプロジェクトのルートに作成します (Web/関数アプリでは D:\home\site\wwwroot にデプロイされます)。 [ファイルベースの構成リファレンス](#configuration-file-reference)に従って、必要な構成を入力します。 既存の Azure Resource Manager 構成を変更する場合は、必ず、`authsettings` コレクションにキャプチャされたプロパティを構成ファイルに変換してください。

2. [Azure Resource Manager](../azure-resource-manager/management/overview.md) API で `Microsoft.Web/sites/<siteName>/config/authsettings` にキャプチャされた既存の構成を変更します。 これを変更するには、[Azure Resource Manager テンプレート](../azure-resource-manager/templates/overview.md)や、[Azure Resource Explorer](https://resources.azure.com/) などのツールを使用できます。 authsettings コレクション内で、3 つのプロパティを設定する必要があります (他のプロパティは削除してもかまいません)。

    1.  `enabled` を "true" に設定します
    2.  `isAuthFromFile` を "true" に設定します
    3.  `authFilePath` をファイルの名前に設定します ("auth.json" など)

> [!NOTE]
> `authFilePath` の形式は、プラットフォームによって異なります。 Windows では、相対パスと絶対パスの両方がサポートされます。 相対パスを使用することをお勧めします。 Linux では、絶対パスのみが現在サポートされているため、設定の値は "/home/site/wwwroot/auth.json" または同様の値にする必要があります。

この構成の更新を行うと、ファイルの内容を使用して、そのサイトでの App Service の認証または承認の動作が定義されます。 Azure Resource Manager 構成に戻りたい場合、`isAuthFromFile` を "false" に設定し直せば戻ることができます。

### <a name="configuration-file-reference"></a>構成ファイル リファレンス

構成ファイルから参照されるシークレットがある場合、[アプリケーション設定](./configure-common.md#configure-app-settings)として保存する必要があります。 設定には任意の名前を付けることができます。 構成ファイルからの参照で同じキーを使用していることを確認してください。

次の例では、ファイル内の可能な構成オプションを使い果たしています。

```json
{
    "platform": {
        "enabled": <true|false>
    },
    "globalValidation": {
        "unauthenticatedClientAction": "RedirectToLoginPage|AllowAnonymous|Return401|Return403",
        "redirectToProvider": "<default provider alias>",
        "excludedPaths": [
            "/path1",
            "/path2"
        ]
    },
    "httpSettings": {
        "requireHttps": <true|false>,
        "routes": {
            "apiPrefix": "<api prefix>"
        },
        "forwardProxy": {
            "convention": "NoProxy|Standard|Custom",
            "customHostHeaderName": "<host header value>",
            "customProtoHeaderName": "<proto header value>"
        }
    },
    "login": {
        "routes": {
            "logoutEndpoint": "<logout endpoint>"
        },
        "tokenStore": {
            "enabled": <true|false>,
            "tokenRefreshExtensionHours": "<double>",
            "fileSystem": {
                "directory": "<directory to store the tokens in if using a file system token store (default)>"
            },
            "azureBlobStorage": {
                "sasUrlSettingName": "<app setting name containing the sas url for the Azure Blob Storage if opting to use that for a token store>"
            }
        },
        "preserveUrlFragmentsForLogins": <true|false>,
        "allowedExternalRedirectUrls": [
            "https://uri1.azurewebsites.net/",
            "https://uri2.azurewebsites.net/",
            "url_scheme_of_your_app://easyauth.callback"
        ],
        "cookieExpiration": {
            "convention": "FixedTime|IdentityDerived",
            "timeToExpiration": "<timespan>"
        },
        "nonce": {
            "validateNonce": <true|false>,
            "nonceExpirationInterval": "<timespan>"
        }
    },
    "identityProviders": {
        "azureActiveDirectory": {
            "enabled": <true|false>,
            "registration": {
                "openIdIssuer": "<issuer url>",
                "clientId": "<app id>",
                "clientSecretSettingName": "APP_SETTING_CONTAINING_AAD_SECRET",
            },
            "login": {
                "loginParameters": [
                    "paramName1=value1",
                    "paramName2=value2"
                ]
            },
            "validation": {
                "allowedAudiences": [
                    "audience1",
                    "audience2"
                ]
            }
        },
        "facebook": {
            "enabled": <true|false>,
            "registration": {
                "appId": "<app id>",
                "appSecretSettingName": "APP_SETTING_CONTAINING_FACEBOOK_SECRET"
            },
            "graphApiVersion": "v3.3",
            "login": {
                "scopes": [
                    "public_profile",
                    "email"
                ]
            },
        },
        "gitHub": {
            "enabled": <true|false>,
            "registration": {
                "clientId": "<client id>",
                "clientSecretSettingName": "APP_SETTING_CONTAINING_GITHUB_SECRET"
            },
            "login": {
                "scopes": [
                    "profile",
                    "email"
                ]
            }
        },
        "google": {
            "enabled": true,
            "registration": {
                "clientId": "<client id>",
                "clientSecretSettingName": "APP_SETTING_CONTAINING_GOOGLE_SECRET"
            },
            "login": {
                "scopes": [
                    "profile",
                    "email"
                ]
            },
            "validation": {
                "allowedAudiences": [
                    "audience1",
                    "audience2"
                ]
            }
        },
        "twitter": {
            "enabled": <true|false>,
            "registration": {
                "consumerKey": "<consumer key>",
                "consumerSecretSettingName": "APP_SETTING_CONTAINING TWITTER_CONSUMER_SECRET"
            }
        },
        "apple": {
            "enabled": <true|false>,
            "registration": {
                "clientId": "<client id>",
                "clientSecretSettingName": "APP_SETTING_CONTAINING_APPLE_SECRET"
            },
            "login": {
                "scopes": [
                    "profile",
                    "email"
                ]
            }
        },
        "openIdConnectProviders": {
            "<providerName>": {
                "enabled": <true|false>,
                "registration": {
                    "clientId": "<client id>",
                    "clientCredential": {
                        "clientSecretSettingName": "<name of app setting containing client secret>"
                    },
                    "openIdConnectConfiguration": {
                        "authorizationEndpoint": "<url specifying authorization endpoint>",
                        "tokenEndpoint": "<url specifying token endpoint>",
                        "issuer": "<url specifying issuer>",
                        "certificationUri": "<url specifying jwks endpoint>",
                        "wellKnownOpenIdConfiguration": "<url specifying .well-known/open-id-configuration endpoint - if this property is set, the other properties of this object are ignored, and authorizationEndpoint, tokenEndpoint, issuer, and certificationUri are set to the corresponding values listed at this endpoint>"
                    }
                },
                "login": {
                    "nameClaimType": "<name of claim containing name>",
                    "scopes": [
                        "openid",
                        "profile",
                        "email"
                    ],
                    "loginParameterNames": [
                        "paramName1=value1",
                        "paramName2=value2"
                    ],
                }
            },
            //...
        }
    }
}
```

## <a name="pin-your-app-to-a-specific-authentication-runtime-version"></a>特定の認証ランタイム バージョンにアプリをピン留めする

認証/承認を有効にすると、[機能の概要](overview-authentication-authorization.md#how-it-works)で説明されているように、プラットフォーム ミドルウェアが HTTP 要求パイプラインに挿入されます。 このプラットフォーム ミドルウェアは、定期的なプラットフォームの更新の一環として、新機能と機能強化で定期的に更新されます。 既定では、Web アプリまたは関数アプリは、このプラットフォーム ミドルウェアの最新バージョンで実行されます。 これらの自動更新は、常に下位互換性があります。 ただし、まれに、この自動更新によって Web アプリまたは関数アプリにランタイムの問題が発生することがあります。その場合は、以前のミドルウェア バージョンに一時的にロールバックすることができます。 この記事では、特定のバージョンの認証ミドルウェアにアプリを一時的にピン留めする方法について説明します。

### <a name="automatic-and-manual-version-updates"></a>自動および手動でのバージョンの更新 

アプリの `runtimeVersion` 設定を設定することで、プラットフォーム ミドルウェアの特定のバージョンにアプリをピン留めすることができます。 特定のバージョンに明示的にピン留めし直さない限り、アプリは常に最新バージョンで実行されます。 一度にサポートされるバージョンはいくつかあります。 サポートされなくなった無効なバージョンにピン留めすると、アプリでは代わりに最新バージョンが使用されます。 常に最新バージョンを実行するには、`runtimeVersion` を ~1 に設定します。 

### <a name="view-and-update-the-current-runtime-version"></a>現在のランタイム バージョンの表示と更新

アプリで使用されるランタイム バージョンを変更できます。 新しいランタイム バージョンは、アプリを再起動した後に有効になります。 

#### <a name="view-the-current-runtime-version"></a>現在のランタイム バージョンの表示

プラットフォーム認証ミドルウェアの現在のバージョンを表示するには、Azure CLI を使用するか、アプリ内にある組み込みバージョン HTTP エンドポイントのいずれか 1 つを経由します。

##### <a name="from-the-azure-cli"></a>Azure CLI から

Azure CLI を使用して、[az webapp auth show](/cli/azure/webapp/auth#az-webapp-auth-show) コマンドで現在のミドルウェア バージョンを表示します。

```azurecli-interactive
az webapp auth show --name <my_app_name> \
--resource-group <my_resource_group>
```

このコードでは、`<my_app_name>` をアプリの名前に置き換えます。 また、`<my_resource_group>` をアプリのリソース グループの名前に置き換えます。

CLI 出力に `runtimeVersion` フィールドが表示されます。 次の出力例のようになります。ここでは、わかりやすくするために抜粋されています。 
```output
{
  "additionalLoginParams": null,
  "allowedAudiences": null,
    ...
  "runtimeVersion": "1.3.2",
    ...
}
```

##### <a name="from-the-version-endpoint"></a>バージョン エンドポイントから

アプリで /.auth/version エンドポイントをクリックして、アプリが実行されている現在のミドルウェア バージョンを表示することもできます。 次の出力例のようになります。
```output
{
"version": "1.3.2"
}
```

#### <a name="update-the-current-runtime-version"></a>現在のランタイム バージョンの更新

Azure CLI を使用すると、[az webapp auth update](/cli/azure/webapp/auth#az-webapp-auth-update) コマンドでアプリの `runtimeVersion` 設定を更新できます。

```azurecli-interactive
az webapp auth update --name <my_app_name> \
--resource-group <my_resource_group> \
--runtime-version <version>
```

`<my_app_name>` をご自分のアプリの名前に置き換えます。 また、`<my_resource_group>` をアプリのリソース グループの名前に置き換えます。 また、`<version>` を 1.x ランタイムの有効なバージョン、または最新バージョンの `~1` に置き換えます。 さまざまなランタイム バージョンのリリース ノートを [こちら] (https://github.com/Azure/app-service-announcements) ) で検索して、ピン留め先となるバージョンを特定することができます。

このコマンドは、上記のコード サンプルの **[テスト]** をクリックすることで、[Azure Cloud Shell](../cloud-shell/overview.md) から実行できます。 また、[Azure CLI をローカルに](/cli/azure/install-azure-cli)使用して、[az ログイン](/cli/azure/reference-index#az-login)を実行してサインインした後に、このコマンドを実行することもできます。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [チュートリアル:エンドツーエンドでのユーザーの認証と承認](tutorial-auth-aad.md)
