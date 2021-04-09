---
title: セッション管理 - Microsoft Threat Modeling Tool - Azure | Microsoft Docs
description: Threat Modeling Tool で公開されている脅威に対するセッション管理の軽減策について説明します。 軽減策の情報を参照し、コード例を確認します。
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.custom: has-adal-ref, devx-track-js, devx-track-csharp
ms.openlocfilehash: a1f4d4a3bb78da82753d651e1a73cf244096d5df
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94518103"
---
# <a name="security-frame-session-management"></a>セキュリティ フレーム:セッションの管理
| 製品/サービス | [アーティクル] |
| --------------- | ------- |
| **Azure AD**    | <ul><li>[Azure AD を使うときは、ADAL のメソッドを使って適切なログアウトを実装する](#logout-adal)</li></ul> |
| **IoT デバイス** | <ul><li>[生成される SaS トークンに対して有限の有効期間を使う](#finite-tokens)</li></ul> |
| **Azure Document DB** | <ul><li>[生成されるリソース トークンに対して最小限のトークン有効期間を使う](#resource-tokens)</li></ul> |
| **ADFS** | <ul><li>[ADFS を使うときは、WsFederation のメソッドを使って適切なログアウトを実装する](#wsfederation-logout)</li></ul> |
| **Identity Server** | <ul><li>[Identity Server を使うときは、適切なログアウトを実装する](#proper-logout)</li></ul> |
| **Web アプリケーション** | <ul><li>[HTTPS 経由で使用可能なアプリケーションは、セキュリティで保護された cookie を使う必要がある](#https-secure-cookies)</li><li>[すべての http ベースのアプリケーションは、クッキーの定義に対してのみ http を指定する必要がある](#cookie-definition)</li><li>[ASP.NET Web ページでクロスサイト リクエスト フォージェリ (CSRF) 攻撃を軽減する](#csrf-asp)</li><li>[非アクティブ状態の有効期間対応にセッションを設定する](#inactivity-lifetime)</li><li>[アプリケーションから適切なログアウトを実装する](#proper-app-logout)</li></ul> |
| **Web API** | <ul><li>[ASP.NET Web API でクロスサイト リクエスト フォージェリ (CSRF) 攻撃を軽減する](#csrf-api)</li></ul> |

## <a name="implement-proper-logout-using-adal-methods-when-using-azure-ad"></a><a id="logout-adal"></a>Azure AD を使うときは、ADAL のメソッドを使って適切なログアウトを実装する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Azure AD | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | 該当なし  |
| **手順** | アプリケーションが Azure AD によって発行されたアクセス トークンに依存する場合、ログアウト イベント ハンドラーを呼び出す必要があります。 |

### <a name="example"></a>例
```csharp
HttpContext.GetOwinContext().Authentication.SignOut(OpenIdConnectAuthenticationDefaults.AuthenticationType, CookieAuthenticationDefaults.AuthenticationType)
```

### <a name="example"></a>例
Session.Abandon() メソッドを呼び出して、ユーザーのセッションを破棄する必要もあります。 次のメソッドでは、ユーザー ログアウトの安全な実装を示します。
```csharp
    [HttpPost]
        [ValidateAntiForgeryToken]
        public void LogOff()
        {
            string userObjectID = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
            AuthenticationContext authContext = new AuthenticationContext(Authority + TenantId, new NaiveSessionCache(userObjectID));
            authContext.TokenCache.Clear();
            Session.Clear();
            Session.Abandon();
            Response.SetCookie(new HttpCookie("ASP.NET_SessionId", string.Empty));
            HttpContext.GetOwinContext().Authentication.SignOut(
                OpenIdConnectAuthenticationDefaults.AuthenticationType,
                CookieAuthenticationDefaults.AuthenticationType);
        } 
```

## <a name="use-finite-lifetimes-for-generated-sas-tokens"></a><a id="finite-tokens"></a>生成される SaS トークンに対して有限の有効期間を使う

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | IoT デバイス | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | 該当なし  |
| **手順** | Azure IoT Hub に対する認証用に生成される SaS トークンには、有限の有効期間が必要です。 SaS トークンの有効期間を最小限に抑え、トークンが侵害された場合に再生できる時間を制限します。|

## <a name="use-minimum-token-lifetimes-for-generated-resource-tokens"></a><a id="resource-tokens"></a>生成されるリソース トークンに対して最小限のトークン有効期間を使う

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Azure Document DB | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | 該当なし  |
| **手順** | リソース トークンの期間を、必要な最小値に減らします。 リソース トークンの既定の有効期間は 1 時間です。|

## <a name="implement-proper-logout-using-wsfederation-methods-when-using-adfs"></a><a id="wsfederation-logout"></a>ADFS を使うときは、WsFederation のメソッドを使って適切なログアウトを実装する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | ADFS | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | 該当なし  |
| **手順** | アプリケーションが ADFS によって発行された STS トークンに依存する場合、ログアウト イベント ハンドラーは WSFederationAuthenticationModule.FederatedSignOut() メソッドを呼び出してユーザーをログアウトさせる必要があります。 また、現在のセッションを破棄し、セッション トークンの値をリセットして無効にする必要があります。|

### <a name="example"></a>例
```csharp
        [HttpPost, ValidateAntiForgeryToken]
        [Authorization]
        public ActionResult SignOut(string redirectUrl)
        {
            if (!this.User.Identity.IsAuthenticated)
            {
                return this.View("LogOff", null);
            }

            // Removes the user profile.
            this.Session.Clear();
            this.Session.Abandon();
            HttpContext.Current.Response.Cookies.Add(new System.Web.HttpCookie("ASP.NET_SessionId", string.Empty)
                {
                    Expires = DateTime.Now.AddDays(-1D),
                    Secure = true,
                    HttpOnly = true
                });

            // Signs out at the specified security token service (STS) by using the WS-Federation protocol.
            Uri signOutUrl = new Uri(FederatedAuthentication.WSFederationAuthenticationModule.Issuer);
            Uri replyUrl = new Uri(FederatedAuthentication.WSFederationAuthenticationModule.Realm);
            if (!string.IsNullOrEmpty(redirectUrl))
            {
                replyUrl = new Uri(FederatedAuthentication.WSFederationAuthenticationModule.Realm + redirectUrl);
            }
           //     Signs out of the current session and raises the appropriate events.
            var authModule = FederatedAuthentication.WSFederationAuthenticationModule;
            authModule.SignOut(false);
        //     Signs out at the specified security token service (STS) by using the WS-Federation
        //     protocol.            
            WSFederationAuthenticationModule.FederatedSignOut(signOutUrl, replyUrl);
            return new RedirectResult(redirectUrl);
        }
```

## <a name="implement-proper-logout-when-using-identity-server"></a><a id="proper-logout"></a>Identity Server を使うときは、適切なログアウトを実装する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Identity Server | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | [IdentityServer3 - フェデレーション サインアウト](https://identityserver.github.io/Documentation/docsv2/advanced/federated-signout.html) |
| **手順** | IdentityServer は、外部 ID プロバイダーとフェデレーションする機能をサポートします。 ユーザーがアップストリームの ID プロバイダーからサインアウトするとき、使われているプロトコルによっては、ユーザーのサインアウト時に通知を受け取ることができる場合があります。これにより、IdentityServer は、クライアントもユーザーをサインアウトできるように、クライアントに通知することができます。実装について詳しくは、「参照」セクションのドキュメントをご覧ください。|

## <a name="applications-available-over-https-must-use-secure-cookies"></a><a id="https-secure-cookies"></a>HTTPS 経由で使用可能なアプリケーションは、セキュリティで保護された cookie を使う必要がある

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Web アプリケーション | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | EnvironmentType - OnPrem |
| **参照**              | [httpCookies 要素 (ASP.NET 設定スキーマ)](/previous-versions/dotnet/netframework-4.0/ms228262(v=vs.100))、 [HttpCookie.Secure プロパティ](/dotnet/api/system.web.httpcookie.secure) |
| **手順** | 通常、cookie はそれがスコープ指定されたドメインからのみアクセスできます。 残念ながら、"domain" の定義にはプロトコルが含まれないため、HTTPS 経由で作成された cookie に HTTP 経由でアクセスできます。 "secure" 属性は、cookie を HTTPS 経由でのみ使用できるようにする必要があることをブラウザーに示します。 HTTPS で設定されるすべての cookie が、**secure** 属性を使うようにする必要があります。 この要件は、web.config ファイルで requireSSL 属性を true に設定することによって適用できます。 これは、コードを変更することなく、現在および将来のすべての cookie に **secure** 属性を強制できるので、推奨される方法です。|

### <a name="example"></a>例
```csharp
<configuration>
  <system.web>
    <httpCookies requireSSL="true"/>
  </system.web>
</configuration>
```
この設定は、アプリケーションへのアクセスに HTTP が使われている場合でも適用されます。 アプリケーションへのアクセスに HTTP が使われている場合、cookie には secure 属性が設定されていて、ブラウザーはアプリケーションに cookie を返送しないので、この設定によりアプリケーションは中断されます。

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Web アプリケーション | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | Web フォーム、MVC5 |
| **属性**              | EnvironmentType - OnPrem |
| **参照**              | 該当なし  |
| **手順** | Web アプリケーションが証明書利用者で、IdP が ADFS サーバーである場合、web.config の `system.identityModel.services` セクションで requireSSL を True に設定することにより、FedAuth トークンの secure 属性を構成できます。|

### <a name="example"></a>例
```csharp
  <system.identityModel.services>
    <federationConfiguration>
      <!-- Set requireSsl=true; domain=application domain name used by FedAuth cookies (Ex: .gdinfra.com); -->
      <cookieHandler requireSsl="true" persistentSessionLifetime="0.0:20:0" />
    ....  
    </federationConfiguration>
  </system.identityModel.services>
```

## <a name="all-http-based-application-should-specify-http-only-for-cookie-definition"></a><a id="cookie-definition"></a>すべての http ベースのアプリケーションは、クッキーの定義に対してのみ http を指定する必要がある

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Web アプリケーション | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | [cookie の secure 属性](https://en.wikipedia.org/wiki/HTTP_cookie#Secure_cookie) |
| **手順** | クロスサイト スクリプティング (XSS) 攻撃による情報漏えいのリスクを軽減するため、新しい属性 httpOnly が cookie に導入され、すべての主要ブラウザーでサポートされています。 この属性は、cookie がスクリプトからアクセスできないことを指定します。 HttpOnly が設定された cookie を使うことで、Web アプリケーションは、cookie に含まれる機密情報がスクリプトによって盗まれて攻撃者の Web サイトに送信される可能性を、減らすことができます。 |

### <a name="example"></a>例
cookie を使うすべての HTTP ベースのアプリケーションでは、web.config に次の構成を実装することにより、cookie の定義で HttpOnly を指定する必要があります。
```XML
<system.web>
.
.
   <httpCookies requireSSL="false" httpOnlyCookies="true"/>
.
.
</system.web>
```

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Web アプリケーション | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | Web フォーム |
| **属性**              | 該当なし  |
| **参照**              | [FormsAuthentication.RequireSSL プロパティ](/dotnet/api/system.web.security.formsauthentication.requiressl) |
| **手順** | RequireSSL プロパティの値は、ASP.NET アプリケーション用の構成ファイルで、構成要素の requireSSL 属性を使うことにより設定します。 ASP.NET アプリケーション用の Web.config ファイルで、requireSSL 属性を設定することにより、トランスポート層セキュリティ (TLS) (以前の SSL (Secure Sockets Layer)) が forms-authentication cookie をサーバーに返す必要があるかどうかを指定できます。|

### <a name="example"></a>例 
次のコード例は、Web.config ファイルで requireSSL 属性を設定しています。
```XML
<authentication mode="Forms">
  <forms loginUrl="member_login.aspx" cookieless="UseCookies" requireSSL="true"/>
</authentication>
```

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Web アプリケーション | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | MVC5 |
| **属性**              | EnvironmentType - OnPrem |
| **参照**              | [Windows Identity Foundation (WIF) の構成 – 第 II 部](/archive/blogs/alikl/windows-identity-foundation-wif-configuration-part-ii-cookiehandler-chunkedcookiehandler-customcookiehandler) |
| **手順** | FedAuth cookie の httpOnly 属性を設定するには、hideFromCsript 属性の値を True に設定する必要があります。 |

### <a name="example"></a>例
正しい構成を次に示します。
```XML
<federatedAuthentication>
<cookieHandler mode="Custom"
                       hideFromScript="true"
                       name="FedAuth"
                       path="/"
                       requireSsl="true"
                       persistentSessionLifetime="25">
</cookieHandler>
</federatedAuthentication>
```

## <a name="mitigate-against-cross-site-request-forgery-csrf-attacks-on-aspnet-web-pages"></a><a id="csrf-asp"></a>ASP.NET Web ページでクロスサイト リクエスト フォージェリ (CSRF) 攻撃を軽減する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Web アプリケーション | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | 該当なし  |
| **手順** | クロスサイト リクエスト フォージェリ (CSRF または XSRF) とは、Web サイトで別のユーザーが確立したセッションのセキュリティ コンテキストで、攻撃者が何らかのアクションを実行することができる攻撃のことです。 攻撃者の目標は、対象の Web サイトが受信した要求の認証をセッション cookie のみに依存している場合に、コンテンツを変更または削除することです。 攻撃者は、別のユーザーのブラウザーを取得し、そのユーザーが既にログインしてる脆弱なサイトからのコマンドで URL を読み込むことによって、この脆弱性を悪用する可能性があります。 脆弱なサーバーからリソースを読み込む別の Web サイトをホストしたり、ユーザーにリンクをクリックさせるなど、攻撃者がこれ行うには多くの方法があります。 この攻撃は、ASP.NET の AntiForgeryToken や ViewState を使うなどして、サーバーが追加トークンをクライアントに送信し、以降のすべての要求にそのトークンを含めるようクライアントに要求し、以降のすべてのトークンに現在のセッションに関連するトークンが含まれることを確認することによって、防ぐことができます。 |

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Web アプリケーション | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | MVC5、MVC6 |
| **属性**              | 該当なし  |
| **参照**              | [ASP.NET MVC と Web ページでの XSRF/CSRF の防止](https://www.asp.net/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) |
| **手順** | CSRF 対策フォームと ASP.NET MVC フォーム - ビューの `AntiForgeryToken` ヘルパー メソッドを使う、`Html.AntiForgeryToken()` をフォームに設定する、など。|

### <a name="example"></a>例
```csharp
@using (Html.BeginForm("UserProfile", "SubmitUpdate")) { 
    @Html.ValidationSummary(true) 
    @Html.AntiForgeryToken()
    <fieldset> 
```

### <a name="example"></a>例
```csharp
<form action="/UserProfile/SubmitUpdate" method="post">
    <input name="__RequestVerificationToken" type="hidden" value="saTFWpkKN0BYazFtN6c4YbZAmsEwG0srqlUqqloi/fVgeV2ciIFVmelvzwRZpArs" />
    <!-- rest of form goes here -->
</form>
```

### <a name="example"></a>例
同時に、Html.AntiForgeryToken() は、上で示したランダムな非表示値と同じ値を含む __RequestVerificationToken という名前の cookie を訪問者に渡します。 次に、受信したフォーム投稿を検証するため、[ValidateAntiForgeryToken] フィルターを対象のアクション メソッドに追加します。 次に例を示します。
```
[ValidateAntiForgeryToken]
public ViewResult SubmitUpdate()
{
// ... etc.
}
```
以下のことを確認する承認フィルターです。
* 受信した要求が、__RequestVerificationToken という名前の cookie を持っている
* 受信した要求が、__RequestVerificationToken という名前の `Request.Form` エントリを持っている
* これらの cookie と `Request.Form` の値が一致する。すべてに問題がない場合、要求は通常どおり通過します。 問題がある場合は、"必要な偽造防止トークンが指定されていないか、または無効です" というメッセージで承認が失敗します。 

### <a name="example"></a>例
CSRF 対策と AJAX: AJAX 要求は HTML フォーム データではなく JSON データを送信する場合があるため、フォーム トークンは AJAX 要求に対して問題である可能性があります。 1 つの解決策は、カスタム HTTP ヘッダーでトークンを送信することです。 次のコードでは、Razor 構文を使ってトークンを生成した後、AJAX 要求にトークンを追加しています。 
```csharp
<script>
    @functions{
        public string TokenHeaderValue()
        {
            string cookieToken, formToken;
            AntiForgery.GetTokens(null, out cookieToken, out formToken);
            return cookieToken + ":" + formToken;                
        }
    }

    $.ajax("api/values", {
        type: "post",
        contentType: "application/json",
        data: {  }, // JSON data goes here
        dataType: "json",
        headers: {
            'RequestVerificationToken': '@TokenHeaderValue()'
        }
    });
</script>
```

### <a name="example"></a>例
要求を処理するときは、要求ヘッダーからトークンを抽出します。 その後、AntiForgery.Validate メソッドを呼び出してトークンを検証します。 トークンが有効ではない場合、Validate メソッドは例外をスローします。
```csharp
void ValidateRequestHeader(HttpRequestMessage request)
{
    string cookieToken = "";
    string formToken = "";

    IEnumerable<string> tokenHeaders;
    if (request.Headers.TryGetValues("RequestVerificationToken", out tokenHeaders))
    {
        string[] tokens = tokenHeaders.First().Split(':');
        if (tokens.Length == 2)
        {
            cookieToken = tokens[0].Trim();
            formToken = tokens[1].Trim();
        }
    }
    AntiForgery.Validate(cookieToken, formToken);
}
```

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Web アプリケーション | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | Web フォーム |
| **属性**              | 該当なし  |
| **参照**              | [ASP.NET の組み込み機能を活用し、Web 攻撃を回避する](/previous-versions/dotnet/articles/ms972969(v=msdn.10)#securitybarriers_topic2) |
| **手順** | WebForm ベースのアプリケーションでの CSRF 攻撃は、ユーザーごとに異なるランダムな文字列 (ユーザー ID、またはもっとよいのはセッション ID) を ViewStateUserKey に設定することによって軽減できます。 さまざまな技術的および社会的な理由により、セッション ID の方がはるかに適しています。セッション ID は予測できず、タイムアウトし、ユーザーごとに異なるためです。|

### <a name="example"></a>例
次に示すコードを、すべてのページに組み込む必要があります。
```csharp
void Page_Init (object sender, EventArgs e) {
   ViewStateUserKey = Session.SessionID;
   :
}
```

## <a name="set-up-session-for-inactivity-lifetime"></a><a id="inactivity-lifetime"></a>非アクティブ状態の有効期間対応にセッションを設定する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Web アプリケーション | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | [HttpSessionState.Timeout プロパティ](/dotnet/api/system.web.sessionstate.httpsessionstate.timeout) |
| **手順** | セッションのタイムアウトは、Web サーバーで定義されている期間中にユーザーが Web サイト上でアクションを何も実行しないと発生するイベントを表します。 サーバー側では、イベントはユーザー セッションの状態を "無効" (たとえば、"もう使われない") に変更し、Web サーバーにそれを破棄する (そこに含まれるすべてのデータを削除する) ように指定します。 次のコード例では、Web.config ファイルでタイムアウト セッション属性を 15 分に設定しています。|

### <a name="example"></a>例
```XML 
<configuration>
  <system.web>
    <sessionState mode="InProc" cookieless="true" timeout="15" />
  </system.web>
</configuration>
```

## <a name="enable-threat-detection-on-azure-sql"></a><a id="threat-detection"></a>Azure SQL での脅威の検出を有効にする

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Web アプリケーション | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | Web フォーム |
| **属性**              | 該当なし  |
| **参照**              | [認証の forms 要素 (ASP.NET 設定スキーマ)](/previous-versions/dotnet/netframework-4.0/1d3t3c61(v=vs.100)) |
| **手順** | フォーム認証チケット cookie のタイムアウトを 15 分に設定します。|

### <a name="example"></a>例
```XML
<forms  name=".ASPXAUTH" loginUrl="login.aspx"  defaultUrl="default.aspx" protection="All" timeout="15" path="/" requireSSL="true" slidingExpiration="true"/>
</forms>
```

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Web アプリケーション | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | Web フォーム、MVC5 |
| **属性**              | EnvironmentType - OnPrem |
| **参照**              | [asdeqa](https://skf.azurewebsites.net/Mitigations/Details/wefr) |
| **手順** | Webアプリケーションが証明書利用者であり、ADFS が STS の場合、認証 Cookie (FedAuth トークン) の有効期間は web.config の次の構成で設定できます。|

### <a name="example"></a>例
```XML
  <system.identityModel.services>
    <federationConfiguration>
      <!-- Set requireSsl=true; domain=application domain name used by FedAuth cookies (Ex: .gdinfra.com); -->
      <cookieHandler requireSsl="true" persistentSessionLifetime="0.0:15:0" />
      <!-- Set requireHttps=true; -->
      <wsFederation passiveRedirectEnabled="true" issuer="http://localhost:39529/" realm="https://localhost:44302/" reply="https://localhost:44302/" requireHttps="true"/>
      <!--
      Use the code below to enable encryption-decryption of claims received from ADFS. Thumbprint value varies based on the certificate being used.
      <serviceCertificate>
        <certificateReference findValue="4FBBBA33A1D11A9022A5BF3492FF83320007686A" storeLocation="LocalMachine" storeName="My" x509FindType="FindByThumbprint" />
      </serviceCertificate>
      -->
    </federationConfiguration>
  </system.identityModel.services>
```

### <a name="example"></a>例
また、ADFS サーバーで次の PowerShell コマンドを実行して、ADFS が発行した SAML 要求トークンの有効期間を 15 分に設定する必要があります。
```csharp
Set-ADFSRelyingPartyTrust -TargetName "<RelyingPartyWebApp>" -ClaimsProviderName @("Active Directory") -TokenLifetime 15 -AlwaysRequireAuthentication $true
```

## <a name="implement-proper-logout-from-the-application"></a><a id="proper-app-logout"></a>アプリケーションから適切なログアウトを実装する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Web アプリケーション | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | 該当なし  |
| **手順** | ユーザーがログアウト ボタンをクリックしたときに、アプリケーションからの適切なサインアウトを実行します。 ログアウト時に、アプリケーションは、ユーザーのセッションを破棄し、セッション cookie の値をリセットして無効にし、さらに認証 cookie の値もリセットして無効にする必要があります。 また、複数のセッションが 1 つのユーザー ID に結び付けられている場合は、タイムアウトまたはログアウト時にサーバー側でまとめて終了する必要があります。 最後に、すべてのページにログアウト機能があることを確認します。 |

## <a name="mitigate-against-cross-site-request-forgery-csrf-attacks-on-aspnet-web-apis"></a><a id="csrf-api"></a>ASP.NET Web API でクロスサイト リクエスト フォージェリ (CSRF) 攻撃を軽減する

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Web API | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | ジェネリック |
| **属性**              | 該当なし  |
| **参照**              | 該当なし  |
| **手順** | クロスサイト リクエスト フォージェリ (CSRF または XSRF) とは、Web サイトで別のユーザーが確立したセッションのセキュリティ コンテキストで、攻撃者が何らかのアクションを実行することができる攻撃のことです。 攻撃者の目標は、対象の Web サイトが受信した要求の認証をセッション cookie のみに依存している場合に、コンテンツを変更または削除することです。 攻撃者は、別のユーザーのブラウザーを取得し、そのユーザーが既にログインしてる脆弱なサイトからのコマンドで URL を読み込むことによって、この脆弱性を悪用する可能性があります。 脆弱なサーバーからリソースを読み込む別の Web サイトをホストしたり、ユーザーにリンクをクリックさせるなど、攻撃者がこれ行うには多くの方法があります。 この攻撃は、ASP.NET の AntiForgeryToken や ViewState を使うなどして、サーバーが追加トークンをクライアントに送信し、以降のすべての要求にそのトークンを含めるようクライアントに要求し、以降のすべてのトークンに現在のセッションに関連するトークンが含まれることを確認することによって、防ぐことができます。 |

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Web API | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | MVC5、MVC6 |
| **属性**              | 該当なし  |
| **参照**              | [ASP.NET Web API でクロスサイト リクエスト フォージェリ (CSRF) 攻撃を防止する](https://www.asp.net/web-api/overview/security/preventing-cross-site-request-forgery-csrf-attacks) |
| **手順** | CSRF 対策と AJAX: AJAX 要求は HTML フォーム データではなく JSON データを送信する場合があるため、フォーム トークンは AJAX 要求に対して問題である可能性があります。 1 つの解決策は、カスタム HTTP ヘッダーでトークンを送信することです。 次のコードでは、Razor 構文を使ってトークンを生成した後、AJAX 要求にトークンを追加しています。 |

### <a name="example"></a>例
```Javascript
<script>
    @functions{
        public string TokenHeaderValue()
        {
            string cookieToken, formToken;
            AntiForgery.GetTokens(null, out cookieToken, out formToken);
            return cookieToken + ":" + formToken;                
        }
    }
    $.ajax("api/values", {
        type: "post",
        contentType: "application/json",
        data: {  }, // JSON data goes here
        dataType: "json",
        headers: {
            'RequestVerificationToken': '@TokenHeaderValue()'
        }
    });
</script>
```

### <a name="example"></a>例
要求を処理するときは、要求ヘッダーからトークンを抽出します。 その後、AntiForgery.Validate メソッドを呼び出してトークンを検証します。 トークンが有効ではない場合、Validate メソッドは例外をスローします。
```csharp
void ValidateRequestHeader(HttpRequestMessage request)
{
    string cookieToken = "";
    string formToken = "";

    IEnumerable<string> tokenHeaders;
    if (request.Headers.TryGetValues("RequestVerificationToken", out tokenHeaders))
    {
        string[] tokens = tokenHeaders.First().Split(':');
        if (tokens.Length == 2)
        {
            cookieToken = tokens[0].Trim();
            formToken = tokens[1].Trim();
        }
    }
    AntiForgery.Validate(cookieToken, formToken);
}
```

### <a name="example"></a>例
CSRF 対策フォームと ASP.NET MVC フォーム - ビューの AntiForgeryToken ヘルパー メソッドを使う、Html.AntiForgeryToken() をフォームに設定する、など。
```csharp
@using (Html.BeginForm("UserProfile", "SubmitUpdate")) { 
    @Html.ValidationSummary(true) 
    @Html.AntiForgeryToken()
    <fieldset> 
}
```

### <a name="example"></a>例
上記の例の出力は、次のようになります。
```csharp
<form action="/UserProfile/SubmitUpdate" method="post">
    <input name="__RequestVerificationToken" type="hidden" value="saTFWpkKN0BYazFtN6c4YbZAmsEwG0srqlUqqloi/fVgeV2ciIFVmelvzwRZpArs" />
    <!-- rest of form goes here -->
</form>
```

### <a name="example"></a>例
同時に、Html.AntiForgeryToken() は、上で示したランダムな非表示値と同じ値を含む __RequestVerificationToken という名前の cookie を訪問者に渡します。 次に、受信したフォーム投稿を検証するため、[ValidateAntiForgeryToken] フィルターを対象のアクション メソッドに追加します。 次に例を示します。
```
[ValidateAntiForgeryToken]
public ViewResult SubmitUpdate()
{
// ... etc.
}
```
以下のことを確認する承認フィルターです。
* 受信した要求が、__RequestVerificationToken という名前の cookie を持っている
* 受信した要求が、__RequestVerificationToken という名前の `Request.Form` エントリを持っている
* これらの cookie と `Request.Form` の値が一致する。すべてに問題がない場合、要求は通常どおり通過します。 問題がある場合は、"必要な偽造防止トークンが指定されていないか、または無効です" というメッセージで承認が失敗します。

| タイトル                   | 詳細      |
| ----------------------- | ------------ |
| **コンポーネント**               | Web API | 
| **SDL フェーズ**               | Build |  
| **適用できるテクノロジ** | MVC5、MVC6 |
| **属性**              | ID プロバイダー - ADFS、ID プロバイダー - Azure AD |
| **参照**              | [ASP.NET Web API 2.2 において個別のアカウントおよびローカル ログインで Web API を保護する](https://www.asp.net/web-api/overview/security/individual-accounts-in-web-api) |
| **手順** | Web API は、OAuth 2.0 を使ってセキュリティ保護されている場合、承認要求ヘッダーにベアラー トークンが含まれることを想定し、トークンが有効な場合にのみ要求にアクセスを許可します。 cookie ベースの認証とは異なり、ブラウザーは要求にベアラー トークンを添付しません。 要求元のクライアントは、要求ヘッダーにベアラー トークンを明示的に追加する必要があります。 したがって、OAuth 2.0 を使って保護されている ASP.NET Web API では、ベアラー トークンは CSRF 攻撃に対する防御と見なされます。 アプリケーションの MVC 部分がフォーム認証を使っている (つまり、cookie を使っている) 場合は、MVC Web アプリで偽造防止トークンを使う必要があることに注意してください。 |

### <a name="example"></a>例
ベアラー トークンのみを利用し、cookie は利用しないように、Web API に通知する必要があります。 この処理は、`WebApiConfig.Register` メソッドの次の構成で実行できます。

```csharp
config.SuppressDefaultHostAuthentication();
config.Filters.Add(new HostAuthenticationFilter(OAuthDefaults.AuthenticationType));
```

SuppressDefaultHostAuthentication メソッドは、IIS または OWIN ミドルウェアから送信された要求が Web API パイプラインに到達する前に発生したすべての認証を無視するように Web API に指示します。 そのため、ベアラー トークンを使用する場合にのみ認証するように Web API を制限することができます。