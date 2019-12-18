---
title: MSAL.NET で Web ブラウザーを使用する | Azure
titleSuffix: Microsoft identity platform
description: .NET 用 Microsoft 認証ライブラリ (MSAL.NET) で Xamarin Android を使用する場合の固有の考慮事項について説明します。
services: active-directory
author: TylerMSFT
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: d5b8c8e78c554994b71f9e246f8bacc39828b17f
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74921601"
---
# <a name="using-web-browsers-in-msalnet"></a>MSAL.NET で Web ブラウザーを使用する
対話型の認証には Web ブラウザー が必要です。 MSAL.NET では、Xamarin.iOS と Xamarin.Android での[システム Web ブラウザー](#system-web-browser-on-xamarinios-xamarinandroid)が既定でサポートされています。 ただし、[Xamarin.iOS](#choosing-between-embedded-web-browser-or-system-browser-on-xamarinios) および [Xamarin.Android](#detecting-the-presence-of-custom-tabs-on-xamarinandroid) のアプリでは、要件に応じて (UX、シングル サインオン (SSO) の必要性、セキュリティ)、[埋め込み Web ブラウザーを有効にすることもできます](#enable-embedded-webviews-on-ios-and-android)。 さらに、Android に Chrome または Chrome カスタム タブをサポートするブラウザーが存在するかどうかに基づいて、使用する Web ブラウザーを[動的に選択する](#detecting-the-presence-of-custom-tabs-on-xamarinandroid)こともできます。 MSAL.NET では、.NET Core デスクトップアプリケーションでシステム ブラウザーがサポートされるのみとなります。

## <a name="web-browsers-in-msalnet"></a>MSAL.NET での Web ブラウザー

### <a name="interaction-happens-in-a-web-browser"></a>Web ブラウザーで行われるやりとり

トークンを対話形式で取得するときは、ダイアログ ボックスの内容はライブラリではなく STS (セキュリティ トークン サービス) によって提供されることを理解しておくことが重要です。 認証エンドポイントによって、対話を制御する HTML および JavaScript が返送され、それが Web ブラウザーまたは Web コントロールにレンダリングされます。 STS で HTML の対話を処理できるようにすると、多くの利点があります。

- パスワード (入力された場合) がアプリケーションまたは認証ライブラリによって格納されることはありません。
- 他の ID プロバイダーへのリダイレクトが有効になります (たとえば、MSAL での職場または学校アカウントや個人アカウントでのログイン、または Azure AD B2C でのソーシャル アカウントでのログイン)。
- たとえば、ユーザーに認証フェーズの間に多要素認証 (MFA) を行わせることで (Windows Hello PIN の入力、電話での呼び出し、または携帯電話での認証アプリ)、STS で条件付きアクセスを制御します。 必要な多要素認証がまだ設定されていない場合、ユーザーは同じダイアログで必要な時に必要な分だけで設定できます。  ユーザーは、携帯電話番号を入力すると、認証アプリケーションをインストールし、QR タグをスキャンして自分のアカウントを追加するよう指示されます。 このサーバー主導の対話は優れたエクスペリエンスです。
- パスワードが期限切れのとき、ユーザーがこの同じダイアログでパスワードを変更できるようにします (古いパスワードと新しいパスワードのための追加フィールドを提供します)。
- Azure AD テナント管理者/アプリケーション所有者によって制御されるテナントまたはアプリケーション (イメージ) のブランド化を有効にします。
- ユーザーが認証の直後に自分の名前でアプリケーションがリソース/スコープにアクセスすることに同意できるようにします。

### <a name="embedded-vs-system-web-ui"></a>埋め込み Web UI とシステム Web UI の比較

MSAL.NET はマルチフレームワーク ライブラリであり、UI コントロールでブラウザーをホストできるようにフレームワーク固有のコードが用意されています (たとえば、.Net Classic では、WinForms が使用され、Xamarin ではネイティブのモバイル コントロールなどが使用されます)。 このコントロールは、`embedded` Web UI と呼ばれます。 また、MSAL.NET ではシステム OS ブラウザーを開始することもできます。

通常は、プラットフォームの既定値を使用することをお勧めします。これは、一般的にはシステム ブラウザーです。 システム ブラウザーは、以前にログインしたことのあるユーザーを記憶するのに適しています。 この動作を変更する必要がある場合は、`WithUseEmbeddedWebView(bool)` を使用します

### <a name="at-a-glance"></a>概略

| フレームワーク        | 埋め込み | システム | Default |
| ------------- |-------------| -----| ----- |
| .NET Classic     | はい | はい^ | 埋め込み |
| .NET Core     | いいえ | はい^ | システム |
| .NET Standard | いいえ | はい^ | システム |
| UWP | はい | いいえ | 埋め込み |
| Xamarin.Android | はい | はい  | システム |
| Xamarin.iOS | はい | はい  | システム |
| Xamarin.Mac| はい | いいえ | 埋め込み |

^ では、"http://localhost" リダイレクト URI が必須です

## <a name="system-web-browser-on-xamarinios-xamarinandroid"></a>Xamarin.iOS および Xamarin.Android でのシステム Web ブラウザー

MSAL.NET では、Xamarin.iOS、Xamarin.Android、および .NET Core に対するシステム Web ブラウザーが既定でサポートされています。 UI を提供するすべてのプラットフォームでは (つまり、.NET Core 以外)、Web ブラウザー コントロールを埋め込むダイアログがライブラリによって提供されます。 また、MSAL.NET では、.NET デスクトップ用の埋め込み Web ビューおよび UWP プラットフォーム用の WAB も使用されます。 ただし、Xamarin iOS および Xamarin.Android アプリケーションに対しては既定で**システム Web ブラウザー**が利用されます。 iOS では、オペレーティング システムのバージョン (iOS12、iOS11、それより前) に応じて、使用する Web ビューさえ選択されます。

システム ブラウザーを使うと、ブローカー (会社ポータル/Authenticator) の必要なしに、SSO の状態を他のアプリケーションや Web アプリケーションと共有できるという大きな利点があります。 Xamarin.iOS および Xamarin.Android プラットフォーム用の MSAL.NET では、既定でシステム ブラウザーが使用されていました。その理由は、これらのプラットフォーム上で、システム Web ブラウザーは画面全体を占め、ユーザー エクスペリエンスがよくなることにありました。 システム Web ビューはダイアログと区別できません。 ただし、iOS では、ユーザーはブラウザーがアプリケーションに対してコールバックするのに同意することが必要な場合があり、面倒なことがあります。

## <a name="system-browser-experience-on-net-core"></a>.NET Core でのシステム ブラウザー エクスペリエンス

.NET Core の場合、MSAL.NET ではシステム ブラウザーが個別のプロセスとして起動されます。 MSAL.NET ではこのブラウザーを制御できません。しかし、ユーザーが認証を完了すると、MSAL.NET が URI をインターセプトできるように Web ページはリダイレクトされます。

次のように指定することで、このブラウザーを使用するように .NET Classic 向けに作成されたアプリを構成することもできます

```csharp
await pca.AcquireTokenInteractive(s_scopes)
         .WithUseEmbeddedWebView(false)
```

MSAL.NET では、ユーザーが離れた場所に移動したのか、ブラウザーを閉じただけなのかを検出できません。 この手法を使用するアプリでは、(`CancellationToken` 経由で) タイムアウトを定義することをお勧めします。 ユーザーがパスワードの変更または多要素認証の実行を求められた場合を考慮に入れて、少なくとも数分のタイムアウトをお勧めします。

### <a name="how-to-use-the-default-os-browser"></a>既定の OS ブラウザーを使用する方法

MSAL.NET では、`http://localhost:port` をリッスンすることで、ユーザーの認証が完了したときに AAD から送信されるコードをインターセプトする必要があります (詳細については、[認証コード](v2-oauth2-auth-code-flow.md)に関するページを参照してください)

システム ブラウザーを有効にするには:

1. アプリの登録時に、`http://localhost` をリダイレクト URI として構成します (B2C では現在サポートされていません)
2. PublicClientApplication を構築するとき、次のリダイレクト URI を指定します。

```csharp
IPublicClientApplication pca = PublicClientApplicationBuilder
                            .Create("<CLIENT_ID>")
                             // or use a known port if you wish "http://localhost:1234"
                            .WithRedirectUri("http://localhost")  
                            .Build();
```

> [!Note]
> `http://localhost` を構成する場合は、ランダムに開いているポートが MSAL.NET によって内部的に検出され、使用されます。

### <a name="linux-and-mac"></a>Linux および MAC

Linux の場合、MSAL.NET では xdg-open ツールを使用して既定の OS ブラウザーが開きます。 トラブルシューティングを行うには、ターミナル (たとえば、`xdg-open "https://www.bing.com"`) からツールを実行します  
Mac の場合、`open <url>` を呼び出すとブラウザーが開かれます

### <a name="customizing-the-experience"></a>エクスペリエンスのカスタマイズ

> [!NOTE]
> カスタマイズは MSAL.NET 4.1.0 以降で使用できます。

MSAL.NET では、トークンを受信したとき、またはエラーが発生したとき、HTTP メッセージを使用して応答できます。 HTML メッセージを表示することも、任意の URL にリダイレクトすることもできます。

```csharp
var options = new SystemWebViewOptions() 
{
    HtmlMessageError = "<p> An error occured: {0}. Details {1}</p>",
    BrowserRedirectSuccess = new Uri("https://www.microsoft.com");
}

await pca.AcquireTokenInteractive(s_scopes)
         .WithUseEmbeddedWebView(false)
         .WithSystemWebViewOptions(options)
         .ExecuteAsync();
```

### <a name="opening-a-specific-browser-experimental"></a>特定のブラウザーを開く (試験段階)

MSAL.NET がブラウザーを開く方法をカスタマイズできます。 たとえば、既定のブラウザーを使用するのではなく、特定のブラウザーを強制的に開くことができます。

```csharp
var options = new SystemWebViewOptions() 
{
    OpenBrowserAsync = SystemWebViewOptions.OpenWithEdgeBrowserAsync
}
```

### <a name="uwp-doesnt-use-the-system-webview"></a>UWP では System WebView は使用されない

一方、デスクトップ アプリケーションでは、System WebView を起動すると、既に他のタブを開いている場合でもブラウザーが表示されるので、ユーザー エクスペリエンスが悪くなることがあります。 また、認証が発生した場合、このウィンドウを閉じるようユーザーに要求するページが表示されます。 ユーザーは、注意していないと、プロセス全体を閉じてしまう可能性があります (認証に関係のない他のタブも含めて)。 また、デスクトップでシステム ブラウザーを利用すると、ローカル ポートを開いてそれでリッスンする必要もあり、アプリケーションに対して高度なアクセスを許可が必要になる場合があります。 開発者、ユーザー、管理者にとって、この要件は気が進まないものである可能性があります。

## <a name="enable-embedded-webviews-on-ios-and-android"></a>iOS と Android で埋め込みの WebView を有効にする

Xamarin.iOS および Xamarin.Android アプリで、埋め込みの WebView を有効にすることもできます。 MSAL.NET 2.0.0 プレビュー以降では、MSAL.NET でも**埋め込み** WebView オプションの使用がサポートされています。 ADAL.NET では、埋め込み WebView がサポートされる唯一のオプションです。

Xamarin を対象として MSAL.NET を使用する開発者は、埋め込み WebView またはシステム ブラウザーどちらかの使用を選択できます。 これは、目標とするユーザー エクスペリエンスとセキュリティの懸念事項に応じて選択します。

現在、MSAL.NET では、Android および iOS のブローカーはまだサポートされていません。 そのため、シングル サインオン (SSO) を提供する必要がある場合は、システム ブラウザーの方がまだより適切なオプションである可能性があります。 埋め込み Web ブラウザーでのブローカーのサポートは、MSAL.NET のバックログになっています。

### <a name="differences-between-embedded-webview-and-system-browser"></a>埋め込み WebView とシステム ブラウザーの違い
MSAL.NET での埋め込み WebView とシステム ブラウザーの間には、ビジュアルに関していくつか違いがあります。

**埋め込み WebView を使用する MSAL.NET での対話型サインイン:**

![埋め込み](media/msal-net-web-browsers/embedded-webview.png)

**システム ブラウザーを使用する MSAL.NET での対話型サインイン:**

![システム ブラウザー](media/msal-net-web-browsers/system-browser.png)

### <a name="developer-options"></a>開発者のオプション

MSAL.NET を使用する開発者には、STS からの対話型ダイアログの表示に関していくつかのオプションがあります。

- **システム ブラウザー。** ライブラリではシステム ブラウザーが既定で設定されます。 Android を使用している場合、認証に対してサポートされるブラウザーについての具体的な情報は、[システム ブラウザー](msal-net-system-browser-android-considerations.md)に関する記事をご覧ください。 Android でシステム ブラウザーを使用するときは、デバイスに Chrome のカスタム タブをサポートするブラウザーがあることが推奨されます。  それ以外の場合、認証が失敗する可能性があります。
- **埋め込み WebView。** MSAL.NET で埋め込み WebView のみを使用するときのために、`AcquireTokenInteractively` パラメーター ビルダーには `WithUseEmbeddedWebView()` メソッドが含まれます。

    iOS

    ```csharp
    AuthenticationResult authResult;
    authResult = app.AcquireTokenInteractively(scopes)
                    .WithUseEmbeddedWebView(useEmbeddedWebview)
                    .ExecuteAsync();
    ```

    Android:

    ```csharp
    authResult = app.AcquireTokenInteractively(scopes)
                .WithParentActivityOrWindow(activity)
                .WithUseEmbeddedWebView(useEmbeddedWebview)
                .ExecuteAsync();
    ```

#### <a name="choosing-between-embedded-web-browser-or-system-browser-on-xamarinios"></a>Xamarin.iOS での埋め込み Web ブラウザーまたはシステム ブラウザーの選択

iOS アプリでは、`AppDelegate.cs` 内で `ParentWindow` を `null` に初期化できます。 iOS では使用されません

```csharp
App.ParentWindow = null; // no UI parent on iOS
```

#### <a name="choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid"></a>Xamarin.Android での埋め込み Web ブラウザーまたはシステム ブラウザーの選択

Android アプリでは、`MainActivity.cs` で親アクティビティを設定して、認証の結果が返されるようにすることができます。

```csharp
 App.ParentWindow = this;
```

その後、`MainPage.xaml.cs` で次のようにします。

```csharp
authResult = await App.PCA.AcquireTokenInteractive(App.Scopes)
                      .WithParentActivityOrWindow(App.ParentWindow)
                      .WithUseEmbeddedWebView(true)
                      .ExecuteAsync();
```

#### <a name="detecting-the-presence-of-custom-tabs-on-xamarinandroid"></a>Xamarin.Android でのカスタム タブの存在の検出

ブラウザーで実行されるアプリでシステム Web ブラウザーを使用して SSO を有効にしようとして、Android デバイスのユーザー エクスペリエンスにカスタム タブをサポートするブラウザーがあるかどうかわからない場合は、`IPublicClientApplication` の `IsSystemWebViewAvailable()` メソッドを呼び出して確認できます。 このメソッドでは、PackageManager がカスタム タブを検出した場合は `true` が返され、デバイスで検出されない場合は `false` が返されます。

このメソッドによって返される値と要件に基づいて、決定することができます。

- ユーザーにカスタム エラー メッセージを返すことができます。 例: "認証を続けるには Chrome をインストールしてください"
- 埋め込み WebView オプションにフォールバックし、埋め込み WebView として UI を起動できます。

埋め込み WebView オプションのコードを次に示します。

```csharp
bool useSystemBrowser = app.IsSystemWebviewAvailable();

authResult = await App.PCA.AcquireTokenInteractive(App.Scopes)
                      .WithParentActivityOrWindow(App.ParentWindow)
                      .WithUseEmbeddedWebView(!useSystemBrowser)
                      .ExecuteAsync();
```

#### <a name="net-core-doesnt-support-interactive-authentication-with-an-embedded-browser"></a>.NET Core では、埋め込みブラウザーを使用した対話型認証はサポートされていません

.NET Core において、対話形式でトークンを取得できるのはシステム Web ブラウザーを介した場合のみであり、埋め込み Web ビューでそれを行うことはできません。 実際には、.NET Core では UI はまだ提供されていません。
システム Web ブラウザーを使用してブラウジング エクスペリエンスをカスタマイズする場合は、[IWithCustomUI](scenario-desktop-acquire-token.md#withcustomwebui) インターフェイスを実装し、さらに独自のブラウザーを用意します。
