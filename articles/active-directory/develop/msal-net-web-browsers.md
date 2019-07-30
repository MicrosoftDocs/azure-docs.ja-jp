---
title: .NET 用 Microsoft Authentication Library での Web ブラウザー | Azure
description: .NET 用 Microsoft 認証ライブラリ (MSAL.NET) で Xamarin Android を使用する場合の固有の考慮事項について説明します。
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/06/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 061b8a9f16396841c3f0d650ccc2f2c4a907aab3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67111320"
---
# <a name="using-web-browsers-in-msalnet"></a>MSAL.NET で Web ブラウザーを使用する
対話型の認証には Web ブラウザー が必要です。 MSAL.NET では、Xamarin.iOS と [Xamarin.Android](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/system-browser) での[システム Web ブラウザー](#system-web-browser-on-xamarinios-and-xamarinandroid)が既定でサポートされています。 ただし、[Xamarin.iOS](#choosing-between-embedded-web-browser-or-system-browser-on-xamarinios) および [Xamarin.Android](#choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid) のアプリでは、要件に応じて (UX、シングル サインオン (SSO) の必要性、セキュリティ)、[埋め込み Web ブラウザーを有効にすることもできます](#enable-embedded-webviews)。 さらに、Android に Chrome または Chrome カスタム タブをサポートするブラウザーが存在するかどうかに基づいて、使用する Web ブラウザーを[動的に選択する](#detecting-the-presence-of-custom-tabs-on-xamarinandroid)こともできます。

## <a name="web-browsers-in-msalnet"></a>MSAL.NET での Web ブラウザー

トークンを対話形式で取得するときは、ダイアログ ボックスの内容はライブラリではなく STS (セキュリティ トークン サービス) によって提供されることを理解しておくことが重要です。 認証エンドポイントによって、対話を制御する HTML および JavaScript が返送され、それが Web ブラウザーまたは Web コントロールにレンダリングされます。 STS で HTML の対話を処理できるようにすると、多くの利点があります。

- パスワード (入力された場合) がアプリケーションまたは認証ライブラリによって格納されることはありません。
- 他の ID プロバイダーへのリダイレクトが有効になります (たとえば、MSAL での職場または学校アカウントや個人アカウントでのログイン、または Azure AD B2C でのソーシャル アカウントでのログイン)。
- たとえば、ユーザーに認証フェーズの間に多要素認証 (MFA) を行わせることで (Windows Hello PIN の入力、電話での呼び出し、または携帯電話での認証アプリ)、STS で条件付きアクセスを制御します。 必要な多要素認証がまだ設定されていない場合、ユーザーは同じダイアログですぐに設定できます。  ユーザーは、携帯電話番号を入力すると、認証アプリケーションをインストールし、QR タグをスキャンして自分のアカウントを追加するよう指示されます。 このサーバー主導の対話は優れたエクスペリエンスです。
- パスワードが期限切れのとき、ユーザーがこの同じダイアログでパスワードを変更できるようにします (古いパスワードと新しいパスワードのための追加フィールドを提供します)。
- Azure AD テナント管理者/アプリケーション所有者によって制御されるテナントまたはアプリケーション (イメージ) のブランド化を有効にします。
- ユーザーが認証の直後に自分の名前でアプリケーションがリソース/スコープにアクセスすることに同意できるようにします。

## <a name="system-web-browser-on-xamarinios-and-xamarinandroid"></a>Xamarin.iOS および Xamarin.Android でのシステム Web ブラウザー

MSAL.NET では、Xamarin.iOS と Xamarin.Android でのシステム Web ブラウザーが既定でサポートされています。 UI を提供するすべてのプラットフォームでは (つまり、.NET Core 以外)、Web ブラウザー コントロールを埋め込むダイアログがライブラリによって提供されます。 また、MSAL.NET では、.NET デスクトップ用の埋め込み Web ビューおよび UWP プラットフォーム用の WAB も使用されます。 ただし、Xamarin iOS および Xamarin.Android アプリケーションに対しては既定で**システム Web ブラウザー**が利用されます。 iOS では、オペレーティング システムのバージョン (iOS12、iOS11、それより前) に応じて、使用する Web ビューさえ選択されます。

システム ブラウザーを使うと、ブローカー (会社ポータル/Authenticator) の必要なしに、SSO の状態を他のアプリケーションや Web アプリケーションと共有できるという大きな利点があります。 Xamarin.iOS および Xamarin.Android プラットフォーム用の MSAL.NET においてシステム ブラウザーが既定で使用されるのは、これらのプラットフォームでは、システム Web ブラウザーは画面全体を占め、ユーザー エクスペリエンスがよくなるためです。 システム Web ビューはダイアログと区別できません。 ただし、iOS では、ユーザーはブラウザーがアプリケーションに対してコールバックするのに同意することが必要な場合があり、面倒なことがあります。

### <a name="uwp-does-not-use-the-system-webview"></a>UWP では System WebView は使用されない

一方、デスクトップ アプリケーションでは、System WebView を起動すると、既に他のタブを開いている場合でもブラウザーが表示されるので、ユーザー エクスペリエンスが悪くなることがあります。 また、認証が発生した場合、このウィンドウを閉じるようユーザーに要求するページが表示されます。 ユーザーは、注意していないと、プロセス全体を閉じる可能性があります (認証に関係のない他のタブも含めて)。 また、デスクトップでシステム ブラウザーを利用すると、ローカル ポートを開いてそれでリッスンする必要もあり、アプリケーションに対して高度なアクセスを許可が必要になる場合があります。 開発者、ユーザー、管理者にとって、この要件は気が進まないものである可能性があります。

## <a name="enable-embedded-webviews"></a>埋め込みの WebView を有効にする 
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

## <a name="net-core-does-not-support-interactive-authentication-out-of-the-box"></a>.NET Core では対話型認証は標準ではサポートされていない

.NET Core では、トークンを対話形式で取得することはできません。 実際には、.NET Core では UI はまだ提供されていません。 .NET Core アプリケーションで対話型サインインを提供する場合は、対話形式でサインインを行うためのコードと URL をアプリケーションでユーザーに提示できます ([デバイス コード フロー](msal-authentication-flows.md#device-code)に関する記事をご覧ください)。

または、[IWithCustomUI](scenario-desktop-acquire-token.md#withcustomwebui) インターフェイスを実装し、独自のブラウザーを提供することもできます