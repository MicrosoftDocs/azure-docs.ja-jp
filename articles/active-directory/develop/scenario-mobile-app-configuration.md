---
title: Web API を呼び出すモバイル アプリを構成する - Microsoft ID プラットフォーム | Azure
description: Web API を呼び出すモバイル アプリを構築する方法 (アプリのコード構成) について説明します
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/23/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 44392882a7d3e1816b952969dbadb518e2762142
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74919955"
---
# <a name="mobile-app-that-calls-web-apis---code-configuration"></a>Web API を呼び出すモバイル アプリ - コード構成

アプリケーションを作成した後、アプリ登録パラメーターを使用してコードを構成する方法を確認します。 モバイル アプリケーションには、その構築に使用されるフレームワークへの適合に関係する複雑な仕様もあります。

## <a name="msal-libraries-supporting-mobile-apps"></a>モバイル アプリをサポートする MSAL ライブラリ

モバイル アプリをサポートする Microsoft ライブラリは次のとおりです。

  MSAL ライブラリ | 説明
  ------------ | ----------
  ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | ポータブル アプリケーションを開発するため。 モバイル アプリケーションを構築するために MSAL.NET でサポートされているプラットフォームは、UWP、Xamarin.iOS、Xamarin.Android です。
  ![MSAL.iOS](media/sample-v2-code/logo_iOS.png) <br/> MSAL.iOS | Objective-C または Swift でネイティブの iOS アプリケーションを開発するため
  ![MSAL.Android](media/sample-v2-code/logo_android.png) <br/> MSAL.Android | Java for Android でネイティブの Android アプリケーションを開発するため

## <a name="instantiating-the-application"></a>アプリケーションをインスタンス化する

### <a name="android"></a>Android

モバイル アプリケーションでは `PublicClientApplication` クラスが使用されます。 これをインスタンス化する方法を次に示します。

```Java
PublicClientApplication sampleApp = new PublicClientApplication(
                    this.getApplicationContext(),
                    R.raw.auth_config);
```

### <a name="ios"></a>iOS

iOS 上のモバイル アプリケーションは、`MSALPublicClientApplication` クラスをインスタンス化する必要があります。

Objective-C:

```objc
NSError *msalError = nil;
     
MSALPublicClientApplicationConfig *config = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"];    
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&msalError];
```

Swift:
```swift
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>")
if let application = try? MSALPublicClientApplication(configuration: config){ /* Use application */}
```

既定の機関のオーバーライド、リダイレクト URI の指定、または MSAL トークンのキャッシュ動作の変更を行うことができる[追加の MSALPublicClientApplicationConfig プロパティ](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALPublicClientApplicationConfig.html#/Configuration%20options)があります。 

### <a name="xamarin-or-uwp"></a>Xamarin または UWP

次の段落では、Xamarin.iOS、Xamarin.Android、UWP の各アプリ向けにアプリケーションをインスタンス化する方法について説明します。

#### <a name="instantiating-the-application"></a>アプリケーションをインスタンス化する

Xamarin または UWP で最も簡単にアプリケーションをインスタンス化する方法は次のとおりです。ここでの `ClientId` は、登録されたアプリの GUID です。

```CSharp
var app = PublicClientApplicationBuilder.Create(clientId)
                                        .Build();
```

この他に、親 UI の設定、既定の機関のオーバーライド、クライアント名とバージョンの指定 (テレメトリ用)、リダイレクト URI の指定、使用する HTTP ファクトリの指定 (プロキシを処理するインスタンスにはテレメトリとログを指定) を行う With*parameter* メソッドがあります。 これについては次の段落で説明します。

##### <a name="specifying-the-parent-uiwindowactivity"></a>親 UI/ウィンドウ/アクティビティの指定

Android では、対話型認証を行う前に親アクティビティを渡す必要があります。 iOS では、ブローカーを使用する場合に ViewController を渡す必要があります。 UWP の場合と同じように、親ウィンドウを渡すことができます。 これはトークンを取得するときに実行できますが、アプリ作成時のコールバックに UIParent を返すデリゲートを指定することもできます。

```CSharp
IPublicClientApplication application = PublicClientApplicationBuilder.Create(clientId)
  .ParentActivityOrWindowFunc(() => parentUi)
  .Build();
```

Android では、[こちら](https://github.com/jamesmontemagno/CurrentActivityPlugin)にある `CurrentActivityPlugin` をお勧めします。  この場合、`PublicClientApplication` ビルダー コードは次のようになります。

```CSharp
// Requires MSAL.NET 4.2 or above
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```

##### <a name="more-app-building-parameters"></a>モバイル アプリ構築パラメーター

- `PublicClientApplicationBuilder` で使用可能なすべての修飾子の一覧については、リファレンス ドキュメント [PublicClientApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods) を参照してください
- `PublicClientApplicationOptions` に公開されているすべてのオプションの説明については、リファレンス ドキュメント内の [PublicClientApplicationOptions](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationoptions) を参照してください

## <a name="xamarin-ios-specific-considerations"></a>Xamarin iOS 固有の考慮事項

Xamarin iOS には、MSAL.NET を使用する場合に留意する必要がある考慮事項があります。

1. [`AppDelegate` での `OpenUrl` 関数のオーバーライドと実装](msal-net-xamarin-ios-considerations.md#implement-openurl)
1. [キーチェーン グループの有効化](msal-net-xamarin-ios-considerations.md#enable-keychain-access)
1. [トークン キャッシュ共有の有効化](msal-net-xamarin-ios-considerations.md#enable-token-cache-sharing-across-ios-applications)
1. [キーチェーン アクセスの有効化](msal-net-xamarin-ios-considerations.md#enable-keychain-access)

詳細については、[Xamarin iOS の考慮事項](msal-net-xamarin-ios-considerations.md)に関するページを参照してください。

## <a name="msal-for-ios-and-macos-specific-considerations"></a>iOS および macOS 用の MSAL 固有の考慮事項

iOS および macOS 用の MSAL を使用する場合も同様の考慮事項が適用されます。

1. [`openURL` コールバックの実装](#brokered-authentication-for-msal-for-ios-and-macos)
2. [キーチェーン アクセス グループの有効化](howto-v2-keychain-objc.md)
3. [ブラウザーと WebView のカスタマイズ](customize-webviews.md)

## <a name="xamarin-android-specific-considerations"></a>Xamarin Android 固有の考慮事項

Xamarin Android には次の仕様があります。

- [認証フローの対話部分が終了したら確実に制御が MSAL に戻るようにする](msal-net-xamarin-android-considerations.md#ensuring-control-goes-back-to-msal-once-the-interactive-portion-of-the-authentication-flow-ends)
- [Android マニフェストを更新する](msal-net-xamarin-android-considerations.md#update-the-android-manifest)
- [埋め込み Web ビューを使用する (省略可能)](msal-net-xamarin-android-considerations.md#use-the-embedded-web-view-optional)
- [トラブルシューティング](msal-net-xamarin-android-considerations.md#troubleshooting)

詳細については、[Xamarin Android の考慮事項](msal-net-xamarin-android-considerations.md)に関するページを参照してください。

最後に、Android のブラウザーには知っておくべき特異性があります。 これについては、「[MSAL.NET での Xamarin Android に固有の考慮事項](msal-net-system-browser-android-considerations.md)」で説明されています。

#### <a name="uwp-specific-considerations"></a>UWP 固有の考慮事項

UWP では企業ネットワークを使用できます。 UWP での MSAL ライブラリの使用に関する追加情報については、「[MSAL.NET でのユニバーサル Windows プラットフォームに固有の考慮事項](msal-net-uwp-considerations.md)」を参照してください。

## <a name="configuring-the-application-to-use-the-broker"></a>ブローカーを使用する場合のアプリケーションの構成

### <a name="why-use-brokers-in-ios-and-android-applications"></a>iOS および Android アプリケーションでブローカーを使用する理由

Android と iOS では、ブローカーによって次のことが可能になります。

- デバイスが AAD に登録されたときのシングル サインオン (SSO)。 ユーザーがアプリケーションごとにサインインする必要がなくなります。
- デバイスの識別。 Azure AD デバイスに関連する条件付きアクセス ポリシーを有効にします。これを行うには、デバイスをワークプレースに追加するときにデバイスで作成したデバイス証明書にアクセスします。
- アプリケーション ID の検証。 アプリケーションはブローカーを呼び出してリダイレクト URL を渡し、それがブローカーによって検証されます。

### <a name="enable-the-broker-on-xamarin"></a>Xamarin でのブローカーの有効化

これらの機能のいずれかを有効にするには、`PublicClientApplicationBuilder.CreateApplication` メソッドを呼び出すときに `WithBroker()` パラメーターを使用します。 `.WithBroker()` は既定で true に設定されます。 [Xamarin.iOS](#brokered-authentication-for-xamarinios) の場合は次の手順に従ってください。

### <a name="enable-the-broker-for-msal-for-android"></a>Android 向け MSAL に対するブローカーの有効化

Android でブローカーを有効にする方法の詳細については、「[Android での仲介型認証](brokered-auth.md)」を参照してください。 

### <a name="enable-the-broker-for-msal-for-ios-and-macos"></a>iOS および macOS 用の MSAL に対するブローカーの有効化

iOS および macOS 用の MSAL の AAD シナリオでは、ブローカー認証が既定で有効化されています。 以下のステップに従い、[iOS および macOS 用の MSAL](#brokered-authentication-for-msal-for-ios-and-macos) に対するブローカー認証サポートのためにアプリケーションを構成します。 [Xamarin.iOS 用の MSAL](#brokered-authentication-for-xamarinios) と [iOS および macOS 用の MSAL](#brokered-authentication-for-msal-for-ios-and-macos) で一部のステップが異なることに注意してください。

### <a name="brokered-authentication-for-xamarinios"></a>Xamarin.iOS でのブローカー認証

次の手順に従って、Xamarin.iOS アプリが [Microsoft Authenticator](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458) アプリと通信できるようにします。

#### <a name="step-1-enable-broker-support"></a>手順 1:ブローカーのサポートを有効にする

ブローカーのサポートは `PublicClientApplication` ごとに有効にします。 既定では無効になっています。 `PublicClientApplicationBuilder` によって `PublicClientApplication` を作成する場合は、`WithBroker()` パラメーター (既定では true に設定) を使用する必要があります。

```CSharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

#### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>手順 2:コールバックを処理するように AppDelegate を更新する

MSAL.NET がブローカーを呼び出すと、ブローカーは `AppDelegate.OpenUrl` メソッドを通じてアプリケーションにコールバックします。 MSAL はブローカーからの応答を待つため、アプリケーションが協力して MSAL.NET を再度呼び出す必要があります。 これを行うには、`AppDelegate.cs` ファイルを更新して次のメソッドをオーバーライドします。

```CSharp
public override bool OpenUrl(UIApplication app, NSUrl url,
                             string sourceApplication,
                             NSObject annotation)
{
 if (AuthenticationContinuationHelper.IsBrokerResponse(sourceApplication))
 {
  AuthenticationContinuationHelper.SetBrokerContinuationEventArgs(url);
  return true;
 }
 else if (!AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url))
 {
  return false;
 }
 return true;
}
```

このメソッドは、アプリケーションが起動されるたびに呼び出され、ブローカーからの応答を処理して MSAL.NET が開始した認証プロセスを完了するために使用されます。

#### <a name="step-3-set-a-uiviewcontroller"></a>手順 3:UIViewController() を設定する

Xamarin.iOS では、通常はオブジェクト ウィンドウを設定する必要はありませんが、ここではブローカーからの応答を送受信するために行います。 引き続き `AppDelegate.cs` で、ViewController を設定します。

次の手順でオブジェクト ウィンドウを設定します。

1) `AppDelegate.cs` で `App.RootViewController` を新しい `UIViewController()` に設定します。 これにより、ブローカーの呼び出しで `UIViewController` が確実に存在するようになります。 正しく設定されていないと、次のエラーが表示されることがあります。`"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`
2) AcquireTokenInteractive の呼び出しで、`.WithParentActivityOrWindow(App.RootViewController)` を使用し、使用するオブジェクト ウィンドウへの参照を渡します。

**例:**

`App.cs`で、次のように記述します。
```CSharp
   public static object RootViewController { get; set; }
```
`AppDelegate.cs`で、次のように記述します。
```CSharp
   LoadApplication(new App());
   App.RootViewController = new UIViewController();
```
AcquireToken の呼び出しで、次のように記述します。
```CSharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```

#### <a name="step-4-register-a-url-scheme"></a>手順 4:URL スキームを登録する

MSAL.NET は、URL を使用してブローカーを呼び出し、ブローカーの応答をアプリに返します。 ラウンド トリップを終了するには、`Info.plist` ファイルにアプリの URL スキームを登録する必要があります。

`CFBundleURLSchemes` の前に `msauth` を付加します。 次に `CFBundleURLName` を末尾に追加します。

`$"msauth.(BundleId)"`

**例:** 
`msauth.com.yourcompany.xforms`

> [!NOTE]
> この URL スキームは、ブローカーから応答を受信したときにアプリを一意に識別するために使用される RedirectUri の一部になります。

```XML
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

#### <a name="step-5-lsapplicationqueriesschemes"></a>手順 5:LSApplicationQueriesSchemes

MSAL は、`–canOpenURL:` を使用してブローカーがデバイスにインストールされているかどうかを確認します。 iOS 9 では、アプリケーションがクエリを実行できるスキームが Apple によってロックダウンされました。

`Info.plist` ファイルの `LSApplicationQueriesSchemes` セクションに **`msauthv2`** を**追加**します。

```XML 
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
    </array>
```

### <a name="brokered-authentication-for-msal-for-ios-and-macos"></a>iOS および macOS 用の MSAL のブローカー認証

AAD シナリオでは、ブローカー認証が既定で有効化されています。

#### <a name="step-1-update-appdelegate-to-handle-the-callback"></a>手順 1:コールバックを処理するように AppDelegate を更新する

iOS および macOS 用の MSAL がブローカーを呼び出すと、ブローカーは `openURL` メソッドを使用してアプリケーションにコールバックします。 MSAL はブローカーからの応答を待機するため、アプリケーションが協力して MSAL を再度呼び出す必要があります。 これを行うには、`AppDelegate.m` ファイルを更新して次のメソッドをオーバーライドします。

Objective-C:

```objc
- (BOOL)application:(UIApplication *)app
            openURL:(NSURL *)url
            options:(NSDictionary<UIApplicationOpenURLOptionsKey,id> *)options
{
    return [MSALPublicClientApplication handleMSALResponse:url 
                                         sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
}
```

Swift:

```swift
    func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        
        guard let sourceApplication = options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String else {
            return false
        }
        
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApplication)
    }
```

iOS 13 以降で UISceneDelegate を導入した場合、MSAL のコールバックは、代わりに UISceneDelegate の `scene:openURLContexts:` に配置する必要があります ([Apple のドキュメント](https://developer.apple.com/documentation/uikit/uiscenedelegate/3238059-scene?language=objc)を参照)。 MSAL `handleMSALResponse:sourceApplication:` の呼び出しは URL ごとに 1 回のみにする必要があります。

#### <a name="step-2-register-a-url-scheme"></a>手順 2:URL スキームを登録する

iOS および macOS 用の MSAL は、URL を使用してブローカーを呼び出し、ブローカーの応答をアプリに返します。 ラウンド トリップを終了するには、`Info.plist` ファイルにアプリの URL スキームを登録する必要があります。

カスタム URL スキームの前に `msauth` を付けます。 次に、**バンドル ID** を末尾に追加します。

`msauth.(BundleId)`

**例:** 
`msauth.com.yourcompany.xforms`

> [!NOTE]
> この URL スキームは、ブローカーから応答を受信したときにアプリを一意に識別するために使用される RedirectUri の一部になります。 `msauth.(BundleId)://auth` の形式の RedirectUri が、[Azure portal](https://portal.azure.com) でアプリケーションに対して登録されていることを確認してください。

```XML
<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>msauth.[BUNDLE_ID]</string>
        </array>
    </dict>
</array>
```

#### <a name="step-3-lsapplicationqueriesschemes"></a>手順 3:LSApplicationQueriesSchemes

**`LSApplicationQueriesSchemes`** を追加して、Microsoft Authenticator がインストールされている場合にこれを呼び出せるようにします。
Xcode 11 以降でアプリをコンパイルする場合、"msauthv3" スキームが必要であることに注意してください。 

```XML 
<key>LSApplicationQueriesSchemes</key>
<array>
  <string>msauthv2</string>
  <string>msauthv3</string>
</array>
```

### <a name="brokered-authentication-for-xamarinandroid"></a>Xamarin.Android でのブローカー認証

MSAL.NET では、Android 用のブローカーがまだサポートされていません。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [トークンの取得](scenario-mobile-acquire-token.md)
