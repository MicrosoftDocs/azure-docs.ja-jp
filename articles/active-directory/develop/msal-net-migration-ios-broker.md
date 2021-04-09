---
title: ブローカーを使用する Xamarin アプリの MSAL.NET への移行
titleSuffix: Microsoft identity platform
description: Microsoft Authenticator を使用する Xamarin iOS アプリを ADAL.NET から MSAL.NET に移行する方法を説明します。
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/08/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: b4eff5910ff5230902d497b55b2afbe6d605365a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89177433"
---
# <a name="migrate-ios-applications-that-use-microsoft-authenticator-from-adalnet-to-msalnet"></a>Microsoft Authenticator を使用する iOS アプリケーションを ADAL.NET から MSAL.NET に移行する

あなたは Azure Active Directory Authentication Library for .NET (ADAL.NET) と iOS ブローカーを使用しています。 リリース 4.3 以降の iOS 上でブローカーをサポートする [Microsoft Authentication Library](msal-overview.md) for .NET (MSAL.NET) に移行するときが来ました。

どこから始めればよいでしょうか。 この記事では、お使いの Xamarin iOS アプリを ADAL から MSAL に移行する方法について説明します。

## <a name="prerequisites"></a>前提条件
この記事では、iOS ブローカーと統合されている Xamarin iOS アプリが既にあることを前提としています。 そうでない場合は、MSAL.NET に直接移動し、そこでブローカーの実装を開始します。 MSAL.NET で新しいアプリケーションを使用して iOS ブローカーを呼び出す方法については、[こちらのドキュメント](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS#why-use-brokers-on-xamarinios-and-xamarinandroid-applications)を参照してください。

## <a name="background"></a>バックグラウンド

### <a name="what-are-brokers"></a>ブローカーとは

ブローカーは、Android および iOS に Microsoft が提供するアプリケーションです (iOS と Android については [Microsoft Authenticator](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6) アプリに関するページ、Android については Intune ポータル サイト アプリを参照してください)。

これでは、次が可能です。

- シングル サインオン。
- デバイスの識別。これは一部の[条件付きアクセス ポリシー](../conditional-access/overview.md)に必要です。 詳細については、[デバイス管理](../conditional-access/concept-conditional-access-conditions.md#device-platforms)に関する記事を参照してください。
- アプリケーションの識別の検証。これは一部のエンタープライズ シナリオでも必要です。 詳細については、[Intune モバイル アプリケーション管理 (MAM)](/intune/mam-faq) に関する記事を参照してください。

## <a name="migrate-from-adal-to-msal"></a>ADAL から MSAL への移行

### <a name="step-1-enable-the-broker"></a>手順 1:ブローカーを有効にします

<table>
<tr><td>現在の ADAL でのコード:</td><td>MSAL での該当部分:</td></tr>
<tr><td>
ADAL.NET では、ブローカーのサポートは認証ごとのコンテキストで有効になりました。 既定では無効になっています。 ブローカーを呼び出すには、

`PlatformParameters` コンストラクターで `useBroker` フラグを true に設定する必要があります。

```csharp
public PlatformParameters(
        UIViewController callerViewController,
        bool useBroker)
```
また、この例では、プラットフォーム固有の iOS 用のページ レンダラーのコードの `useBroker` フラグを
true に設定する必要があります。
```csharp
page.BrokerParameters = new PlatformParameters(
          this,
          true,
          PromptBehavior.SelectAccount);
```

次に、トークンを取得する呼び出しで、次のパラメーターを含めます。
```csharp
 AuthenticationResult result =
                    await
                        AuthContext.AcquireTokenAsync(
                              Resource,
                              ClientId,
                              new Uri(RedirectURI),
                              platformParameters)
                              .ConfigureAwait(false);
```

</td><td>
MSAL.NET では、ブローカーのサポートは PublicClientApplication ごとに有効になります。 既定では無効になっています。 これを有効にするには、

(既定で true に設定されている) `WithBroker()` パラメーターを使用してブローカーを呼び出します。

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos)
                .Build();
```
トークンの取得の呼び出しで、次のように記述します。
```csharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```
</table>

### <a name="step-2-set-a-uiviewcontroller"></a>手順 2:UIViewController() を設定する
ADAL.NET で、`PlatformParameters` の一部として UIViewController を渡しました (手順 1 の例を参照してください)。MSAL.NET では、開発者に柔軟性を持たせるためにオブジェクト ウィンドウが使用されますが、通常の iOS の使用では必要ありません。 ブローカーを使用するには、ブローカーから応答を送受信するために、オブジェクト ウィンドウを設定します。
<table>
<tr><td>現在の ADAL でのコード:</td><td>MSAL での該当部分:</td></tr>
<tr><td>
UIViewController は

iOS 固有のプラットフォームで `PlatformParameters` に渡されます。

```csharp
page.BrokerParameters = new PlatformParameters(
          this,
          true,
          PromptBehavior.SelectAccount);
```
</td><td>
MSAL.NET で、iOS のオブジェクト ウィンドウを設定するには、次の 2 つの操作を行います。

1. `AppDelegate.cs` で、`App.RootViewController` を新しい `UIViewController()` に設定します。
これを割り当てることにより、UIViewController でのブローカーへの呼び出しが保証されます。 正しく設定されていないと、次のエラーが表示されることがあります。`"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`
1. AcquireTokenInteractive の呼び出しで、`.WithParentActivityOrWindow(App.RootViewController)` を使用し、使用するオブジェクト ウィンドウへの参照を渡します。

**例:**

`App.cs`:
```csharp
   public static object RootViewController { get; set; }
```
`AppDelegate.cs`:
```csharp
   LoadApplication(new App());
   App.RootViewController = new UIViewController();
```
トークンの取得の呼び出しで、次のように記述します。
```csharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```

</table>

### <a name="step-3-update-appdelegate-to-handle-the-callback"></a>手順 3:コールバックを処理するように AppDelegate を更新する
ADAL と MSAL の両方からブローカーが呼び出され、ブローカーでは `AppDelegate` クラスの `OpenUrl` メソッドを使用してアプリケーションにコールバックされます。 詳しくは、[こちらのドキュメント](msal-net-use-brokers-with-xamarin-apps.md#step-3-update-appdelegate-to-handle-the-callback)をご覧ください。

ADAL.NET と MSAL.NET の間に変更はありません。

### <a name="step-4-register-a-url-scheme"></a>手順 4:URL スキームを登録する
ADAL.NET と MSAL.NET では、URL を使用してブローカーを呼び出し、ブローカーの応答をアプリに返します。 次のように、お使いのアプリの `Info.plist` ファイルに URL スキームを登録します。

<table>
<tr><td>現在の ADAL でのコード:</td><td>MSAL での該当部分:</td></tr>
<tr><td>
URL スキームは、お使いのアプリに対して一意です。
</td><td>
その

`CFBundleURLSchemes` の名前には、

`msauth.`

がプレフィックスとして、お使いの `CFBundleURLName` の前に含まれている必要があります。

例: `$"msauth.(BundleId")`

```csharp
 <key>CFBundleURLTypes</key>
    <array>
      <dict>
        <key>CFBundleTypeRole</key>
        <string>Editor</string>
        <key>CFBundleURLName</key>
        <string>com.yourcompany.xforms</string>
        <key>CFBundleURLSchemes</key>
        <array>
          <string>msauth.com.yourcompany.xforms</string>
        </array>
      </dict>
    </array>
```

> [!NOTE]
> この URL スキームは、ブローカーから応答を受け取るときにアプリを一意に識別するために使用されるリダイレクト URI の一部になります。

</table>

### <a name="step-5-add-the-broker-identifier-to-the-lsapplicationqueriesschemes-section"></a>手順 5:LSApplicationQueriesSchemes セクションにブローカー識別子を追加する

ADAL.NET と MSAL.NET は、いずれも `-canOpenURL:` を使用してブローカーがデバイスにインストールされているかどうかを確認します。 info.plist ファイルの LSApplicationQueriesSchemes セクションには、次のように iOS ブローカー用の正しい識別子を追加します。

<table>
<tr><td>現在の ADAL でのコード:</td><td>MSAL での該当部分:</td></tr>
<tr><td>
用途

`msauth`


```csharp
<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauth</string>
</array>
```
</td><td>
用途

`msauthv2`


```csharp
<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauthv2</string>
     <string>msauthv3</string>
</array>
```
</table>

### <a name="step-6-register-your-redirect-uri-in-the-azure-portal"></a>手順 6:Azure portal でリダイレクト URI を登録する

ADAL.NET と MSAL.NET のいずれでも、ブローカーをターゲットとするときにリダイレクト URI に対してさらに要件が追加されます。 Azure portal でお使いのアプリケーションにリダイレクト URI を追加します。
<table>
<tr><td>現在の ADAL でのコード:</td><td>MSAL での該当部分:</td></tr>
<tr><td>

`"<app-scheme>://<your.bundle.id>"`

例:

`mytestiosapp://com.mycompany.myapp`
</td><td>

`$"msauth.{BundleId}://auth"`

例:

`public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth"; `

</table>

Azure portal でリダイレクト URI を登録する方法の詳細については、「[手順 7:アプリの登録にリダイレクト URI を追加する](msal-net-use-brokers-with-xamarin-apps.md#step-7-add-a-redirect-uri-to-your-app-registration)」を参照してください。

### <a name="step-7-set-the-entitlementsplist"></a>**手順 7:Entitlements.plist を設定する**

*Entitlements.plist* ファイルでキーチェーン アクセスを有効にします。

```xml
 <key>keychain-access-groups</key>
    <array>
      <string>$(AppIdentifierPrefix)com.microsoft.adalcache</string>
    </array>
```

キーチェーン アクセスの有効化の詳細については、「[キーチェーン アクセスの有効化](msal-net-xamarin-ios-considerations.md#enable-keychain-access)」を参照してください。

## <a name="next-steps"></a>次のステップ

「[MSAL.NET を使用する Xamarin iOS に固有の考慮事項](msal-net-xamarin-ios-considerations.md)」を参照してください。