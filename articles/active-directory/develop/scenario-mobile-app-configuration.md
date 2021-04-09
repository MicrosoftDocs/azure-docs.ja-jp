---
title: Web API を呼び出すモバイル アプリを構成する | Azure
titleSuffix: Microsoft identity platform
description: Web API を呼び出すようにモバイル アプリのコードを構成する方法について説明します
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 06/16/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 6f13d789cd63bb568bb8940ce614ebdb2dbcdb83
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103199745"
---
# <a name="configure-a-mobile-app-that-calls-web-apis"></a>Web API を呼び出すモバイル アプリを構成する

アプリケーションを作成した後、アプリ登録パラメーターを使用してコードを構成する方法を確認します。 モバイル アプリケーションには、作成フレームワークへの適合に関連する複雑な部分が存在します。

## <a name="microsoft-libraries-supporting-mobile-apps"></a>モバイル アプリをサポートする Microsoft ライブラリ

次の Microsoft ライブラリはモバイル アプリをサポートしています。

[!INCLUDE [active-directory-develop-libraries-mobile](../../../includes/active-directory-develop-libraries-mobile.md)]

## <a name="instantiate-the-application"></a>アプリケーションをインスタンス化する

### <a name="android"></a>Android

モバイル アプリケーションでは `PublicClientApplication` クラスが使用されます。 これをインスタンス化する方法を次に示します。

```Java
PublicClientApplication sampleApp = new PublicClientApplication(
                    this.getApplicationContext(),
                    R.raw.auth_config);
```

### <a name="ios"></a>iOS

iOS 上のモバイル アプリケーションは、`MSALPublicClientApplication` クラスをインスタンス化する必要があります。 このクラスをインスタンス化するには、次のコードを使用します。

```objc
NSError *msalError = nil;

MSALPublicClientApplicationConfig *config = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"];
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&msalError];
```

```swift
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>")
if let application = try? MSALPublicClientApplication(configuration: config){ /* Use application */}
```

[追加の MSALPublicClientApplicationConfig プロパティ](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALPublicClientApplicationConfig.html#/Configuration%20options)で、既定の機関のオーバーライド、リダイレクト URI の指定、または MSAL トークンのキャッシュ動作の変更を行うことができます。

### <a name="xamarin-or-uwp"></a>Xamarin または UWP

このセクションでは、Xamarin.iOS、Xamarin.Android、および UWP の各アプリ向けにアプリケーションをインスタンス化する方法について説明します。

#### <a name="instantiate-the-application"></a>アプリケーションをインスタンス化する

Xamarin または UWP では、アプリケーションをインスタンス化する最も簡単な方法は、次のコードを使用することです。 このコードの `ClientId` は、登録済みアプリの GUID です。

```csharp
var app = PublicClientApplicationBuilder.Create(clientId)
                                        .Build();
```

追加の `With<Parameter>` メソッドを使用して、親 UI の設定、既定の機関のオーバーライド、クライアント名とバージョンの指定 (テレメトリ用)、リダイレクト URI の指定、および使用する HTTP ファクトリの指定を実行します。 たとえば、HTTP ファクトリを使用して、プロキシの処理とテレメトリとログの指定を行います。

次のセクションで、アプリケーションのインスタンス化について詳しく説明します。

##### <a name="specify-the-parent-ui-window-or-activity"></a>親 UI、ウィンドウ、またはアクティビティを指定する

Android では、対話型認証を行う前に親アクティビティを渡します。 iOS でブローカーを使用する場合は、`ViewController` を渡します。 UWP の場合と同じように、親ウィンドウを渡すことができます。 トークンを取得するときに、それを渡します。 ただし、アプリを作成するときに、コールバックを `UIParent` を返すデリゲートとして指定することもできます。

```csharp
IPublicClientApplication application = PublicClientApplicationBuilder.Create(clientId)
  .ParentActivityOrWindowFunc(() => parentUi)
  .Build();
```

Android では、[`CurrentActivityPlugin`](https://github.com/jamesmontemagno/CurrentActivityPlugin) の使用をお勧めします。 結果の `PublicClientApplication` ビルダー コードは、次の例のようになります。

```csharp
// Requires MSAL.NET 4.2 or above
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```

##### <a name="find-more-app-building-parameters"></a>他のアプリ ビルド パラメーターを見つける

`PublicClientApplicationBuilder` で使用できるすべてのメソッドの一覧については、[メソッドの一覧](/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods)を参照してください。

`PublicClientApplicationOptions` で公開されるすべてのオプションの説明については、[リファレンス ドキュメント](/dotnet/api/microsoft.identity.client.publicclientapplicationoptions)を参照してください。

## <a name="tasks-for-xamarin-ios"></a>Xamarin iOS のタスク

Xamarin iOS で MSAL.NET を使用する場合は、次のタスクを実行します。

* [`AppDelegate` での `OpenUrl` 関数のオーバーライドと実装](msal-net-xamarin-ios-considerations.md#implement-openurl)
* [キーチェーン グループの有効化](msal-net-xamarin-ios-considerations.md#enable-keychain-access)
* [トークン キャッシュ共有の有効化](msal-net-xamarin-ios-considerations.md#enable-token-cache-sharing-across-ios-applications)
* [キーチェーン アクセスの有効化](msal-net-xamarin-ios-considerations.md#enable-keychain-access)

詳細については、[Xamarin iOS の考慮事項](msal-net-xamarin-ios-considerations.md)に関する記事を参照してください。

## <a name="tasks-for-msal-for-ios-and-macos"></a>iOS と macOS 用の MSAL のタスク

iOS と macOS 用の MSAL を使用する場合は、次のタスクが必要です。

* [`openURL` コールバックの実装](#brokered-authentication-for-msal-for-ios-and-macos)
* [キーチェーン アクセス グループの有効化](howto-v2-keychain-objc.md)
* [ブラウザーと WebView のカスタマイズ](customize-webviews.md)

## <a name="tasks-for-xamarinandroid"></a>Xamarin.Android のタスク

Xamarin.Android を使用する場合は、次のタスクを実行します。

- [認証フローの対話部分が終了したら確実に制御が MSAL に戻るようにする](msal-net-xamarin-android-considerations.md#ensure-that-control-returns-to-msal)
- [Android マニフェストを更新する](msal-net-xamarin-android-considerations.md#update-the-android-manifest-for-system-webview-support)
- [埋め込み Web ビューを使用する (省略可能)](msal-net-xamarin-android-considerations.md#use-the-embedded-web-view-optional)
- [必要に応じてトラブルシューティングを行う](msal-net-xamarin-android-considerations.md#troubleshooting)

詳細については、[Xamarin.Android の考慮事項](msal-net-xamarin-android-considerations.md)に関する記事を参照してください。

Android でのブラウザーに関する考慮事項については、[MSAL.NET での Xamarin.Android 固有の考慮事項](msal-net-system-browser-android-considerations.md)に関する記事を参照してください。

#### <a name="tasks-for-uwp"></a>UWP のタスク

UWP では企業ネットワークを使用できます。 次のセクションで、企業のシナリオで完了する必要があるタスクについて説明します。

詳細については、[MSAL.NET での UWP 固有の考慮事項](msal-net-uwp-considerations.md)に関する記事を参照してください。

## <a name="configure-the-application-to-use-the-broker"></a>ブローカーを使用するようにアプリケーションを構成する

Android と iOS では、ブローカーによって次のことが可能になります。

- **シングル サインオン (SSO)** :Azure Active Directory (Azure AD) に登録されているデバイスで SSO を使用できます。 SSO を使用すると、ユーザーはアプリケーションごとにサインインする必要がなくなります。
- **デバイスの識別**:この設定により、Azure AD デバイスに関連する条件付きアクセス ポリシーが有効になります。 認証プロセスでは、デバイスがワークプレースに参加したときに作成されたデバイス証明書が使用されます。
- **アプリケーション ID の検証**:アプリケーションでは、ブローカーを呼び出すときにそのリダイレクト URL が渡されます。 ブローカーによってそれが検証されます。

### <a name="enable-the-broker-on-xamarin"></a>Xamarin でのブローカーの有効化

Xamarin でブローカーを有効にするには、`PublicClientApplicationBuilder.CreateApplication` メソッドを呼び出すときに `WithBroker()` パラメーターを使用します。 既定では、`.WithBroker()` は true に設定されます。

Xamarin.iOS のブローカー認証を有効にするには、この記事の [Xamarin.iOS に関するセクション](#enable-brokered-authentication-for-xamarin-ios)に記載されている手順に従います。

### <a name="enable-the-broker-for-msal-for-android"></a>Android 向け MSAL に対するブローカーの有効化

Android でブローカーを有効にする方法の詳細については、「[Android のブローカー認証](msal-android-single-sign-on.md)」を参照してください。

### <a name="enable-the-broker-for-msal-for-ios-and-macos"></a>iOS および macOS 用の MSAL に対するブローカーの有効化

iOS および macOS 用の MSAL を使用する Azure AD シナリオでは、ブローカー認証が既定で有効化されています。

次のセクションで、Xamarin.iOS 用の MSAL、または iOS および macOS 用の MSAL のいずれかでブローカー認証をサポートするようにアプリケーションを構成するための手順について説明します。 この 2 つの手順セットでは、一部の手順が異なります。

### <a name="enable-brokered-authentication-for-xamarin-ios"></a>Xamarin iOS 用のブローカー認証を有効にする

このセクションの手順に従って、Xamarin.iOS アプリが [Microsoft Authenticator](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458) アプリと通信できるようにします。

#### <a name="step-1-enable-broker-support"></a>手順 1:ブローカーのサポートを有効にする

ブローカー サポートは、既定では無効になっています。 個々の `PublicClientApplication` クラスに対して有効にすることができます。 `PublicClientApplicationBuilder` を使用して `PublicClientApplication` クラスを作成するときに、`WithBroker()` パラメーターを使用します。 `WithBroker()` パラメーターは、既定で true に設定されます。

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

#### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>手順 2:コールバックを処理するように AppDelegate を更新する

MSAL.NET でブローカーが呼び出されると、ブローカーがアプリケーションにコールバックします。 それは、`AppDelegate.OpenUrl` メソッドを使用してコールバックします。 MSAL はブローカーからの応答を待つため、アプリケーションが協力して MSAL.NET を再度呼び出す必要があります。 次のコードに示すように、メソッドをオーバーライドするように `AppDelegate.cs` ファイルを更新することで、この動作を設定します。

```csharp
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

このメソッドは、アプリケーションが起動されるたびに呼び出されます。 これは、ブローカーからの応答を処理し、MSAL.NET によって開始された認証プロセスを完了する機会です。

#### <a name="step-3-set-a-uiviewcontroller"></a>手順 3:UIViewController() を設定する

Xamarin iOS では、通常は、オブジェクト ウィンドウを設定する必要はありません。 ただし、ここでは、ブローカーに対する応答を送受信できるようにそれを設定する必要があります。 `AppDelegate.cs` にオブジェクト ウィンドウを設定するには、`ViewController` を設定します。

オブジェクト ウィンドウを設定するには、次の手順に従います。

1. `AppDelegate.cs` で `App.RootViewController` を新しい `UIViewController()` に設定します。 この設定により、ブローカーへの呼び出しに `UIViewController` が含まれるようになります。 正しく設定されていないと、次のエラーが表示されることがあります。

    `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker."`

1. `AcquireTokenInteractive` 呼び出しで、`.WithParentActivityOrWindow(App.RootViewController)` を使用します。 使用するオブジェクト ウィンドウに参照を渡します。 次に例を示します。

    `App.cs`:
    ```csharp
       public static object RootViewController { get; set; }
    ```
    `AppDelegate.cs`:
    ```csharp
       LoadApplication(new App());
       App.RootViewController = new UIViewController();
    ```
    `AcquireToken` の呼び出しの場合:
    ```csharp
    result = await app.AcquireTokenInteractive(scopes)
                 .WithParentActivityOrWindow(App.RootViewController)
                 .ExecuteAsync();
    ```

#### <a name="step-4-register-a-url-scheme"></a>手順 4:URL スキームを登録する

MSAL.NET は、URL を使用してブローカーを呼び出し、ブローカーの応答をアプリに返します。 ラウンド トリップを終了するには、`Info.plist` ファイルにアプリの URL スキームを登録します。

アプリの URL スキームを登録するには、次の手順に従います。

1. `CFBundleURLSchemes` に `msauth` プレフィックスを追加します。
1. `CFBundleURLName` を末尾に追加します。 次のパターンに従います。

   `$"msauth.(BundleId)"`

   ここでは、`BundleId` によってデバイスが一意に識別されます。 たとえば、`BundleId` が `yourcompany.xforms` の場合、URL スキームは `msauth.com.yourcompany.xforms` になります。

  
      この URL スキームは、ブローカーから応答を受け取るときにアプリを一意に識別するリダイレクト URI の一部になります。

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

#### <a name="step-5-add-to-the-lsapplicationqueriesschemes-section"></a>手順 5:LSApplicationQueriesSchemes セクションに追加する

MSAL は、`–canOpenURL:` を使用してブローカーがデバイスにインストールされているかどうかを確認します。 iOS 9 では、アプリケーションが照会できるスキームが Apple によってロックされています。

次のコード例のように、`Info.plist` ファイルの `LSApplicationQueriesSchemes` セクションに `msauthv2` を追加します。

```XML
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
    </array>
```

### <a name="brokered-authentication-for-msal-for-ios-and-macos"></a>iOS および macOS 用の MSAL のブローカー認証

Azure AD シナリオでは、ブローカー認証が既定で有効化されています。

#### <a name="step-1-update-appdelegate-to-handle-the-callback"></a>手順 1:コールバックを処理するように AppDelegate を更新する

iOS および macOS 用の MSAL でブローカーが呼び出されると、ブローカーは `openURL` メソッドを使用してアプリケーションにコールバックします。 MSAL がブローカーからの応答を待っているため、アプリケーションが協力して MSAL をコールバックする必要があります。 次のコード例に示すように、メソッドをオーバーライドするように `AppDelegate.m` ファイルを更新することで、この機能を設定します。

```objc
- (BOOL)application:(UIApplication *)app
            openURL:(NSURL *)url
            options:(NSDictionary<UIApplicationOpenURLOptionsKey,id> *)options
{
    return [MSALPublicClientApplication handleMSALResponse:url
                                         sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
}
```

```swift
    func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {

        guard let sourceApplication = options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String else {
            return false
        }

        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApplication)
    }
```

iOS 13 以降で `UISceneDelegate` を採用した場合は、代わりに `UISceneDelegate` の `scene:openURLContexts:` に MSAL のコールバックを配置します。 MSAL `handleMSALResponse:sourceApplication:` の呼び出しは URL ごとに 1 回のみにする必要があります。

詳しくは、[Apple のドキュメント](https://developer.apple.com/documentation/uikit/uiscenedelegate/3238059-scene?language=objc)をご覧ください。

#### <a name="step-2-register-a-url-scheme"></a>手順 2:URL スキームを登録する

iOS および macOS 用の MSAL では、URL を使用してブローカーが呼び出され、ブローカーの応答がアプリに返されます。 ラウンド トリップを終了するには、`Info.plist` ファイルにアプリの URL スキームを登録します。

アプリのスキームを登録するには:

1. カスタム URL スキームの前に `msauth` を付けます。

1. バンドル ID をスキーマの末尾に追加します。 次のパターンに従います。

   `$"msauth.(BundleId)"`

   ここでは、`BundleId` によってデバイスが一意に識別されます。 たとえば、`BundleId` が `yourcompany.xforms` の場合、URL スキームは `msauth.com.yourcompany.xforms` になります。

    この URL スキームは、ブローカーから応答を受け取るときにアプリを一意に識別するリダイレクト URI の一部になります。 [Azure portal](https://portal.azure.com) で、`msauth.(BundleId)://auth` 形式のリダイレクト URI がアプリケーションに対して登録されていることを確認してください。

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

#### <a name="step-3-add-lsapplicationqueriesschemes"></a>手順 3:LSApplicationQueriesSchemes を追加する

Microsoft Authenticator アプリがインストールされている場合にその呼び出しを許可するために、`LSApplicationQueriesSchemes` を追加します。

> [!NOTE]
> アプリが Xcode 11 以降を使用してコンパイルされている場合は、`msauthv3` スキームが必要です。

`LSApplicationQueriesSchemes` を追加する方法の例を次に示します。

```XML
<key>LSApplicationQueriesSchemes</key>
<array>
  <string>msauthv2</string>
  <string>msauthv3</string>
</array>
```

### <a name="brokered-authentication-for-xamarinandroid"></a>Xamarin.Android でのブローカー認証

Android でブローカーを有効にする方法の詳細については、[Xamarin.Android のブローカー認証](msal-net-use-brokers-with-xamarin-apps.md#brokered-authentication-for-android)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

このシナリオの次の[トークンを取得する](scenario-mobile-acquire-token.md)に関する記事に進みます。
