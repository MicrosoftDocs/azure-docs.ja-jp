---
title: Xamarin、iOS、Android でブローカーを使用する | Azure
titleSuffix: Microsoft identity platform
description: Microsoft Authenticator と Microsoft Authentication Library for .NET (MSAL.NET) を使用できる Xamarin iOS アプリケーションを設定する方法について説明します。 また、Azure AD Authentication Library for .NET (ADAL.NET) から Microsoft Authentication Library for .NET (MSAL.NET) に移行する方法についても説明します。
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
ms.openlocfilehash: 226e94510709b37a7e6b1aae90a7e0ec5b4222b9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103199577"
---
# <a name="use-microsoft-authenticator-or-intune-company-portal-on-xamarin-applications"></a>Xamarin アプリケーションで Microsoft Authenticator または Intune ポータル サイトを使用する

Android および iOS では、Microsoft Authenticator や Android 固有の Microsoft Intune ポータル サイトなどのブローカーによって、以下が可能になります。

- **シングル サインオン (SSO)** : ユーザーは、各アプリケーションにサインインする必要はありません。
- **デバイスの識別**: ブローカーからデバイス証明書にアクセスします。 この証明書は、デバイスがワークプレースに参加したときに作成されます。
- **アプリケーション ID の検証**: アプリケーションはブローカーを呼び出すとき、自身のリダイレクト URL を渡します。 ブローカーによって URL が確認されます。

これらの機能のいずれかを有効にするには、`PublicClientApplicationBuilder.CreateApplication` メソッドを呼び出すときに `WithBroker()` パラメーターを使用します。 `.WithBroker()` パラメーターは、既定で true に設定されています。

Microsoft Authentication Library for .NET (MSAL.NET) でのブローカー認証の設定は、プラットフォームによって異なります。

* [iOS アプリケーション](#brokered-authentication-for-ios)
* [Android アプリケーション](#brokered-authentication-for-android)

## <a name="brokered-authentication-for-ios"></a>iOS のブローカー認証

Xamarin.iOS アプリで [Microsoft Authenticator](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458) アプリと通信できるようにするには、次の手順のようにします。 対象が iOS 13 の場合は、[Apple の API の破壊的変更](./msal-net-xamarin-ios-considerations.md)に関する記事を参照してください。

### <a name="step-1-enable-broker-support"></a>手順 1:ブローカーのサポートを有効にする

`PublicClientApplication` の個々のインスタンスに対してブローカーのサポートを有効にする必要があります。 既定では、サポートは無効になっています。 `PublicClientApplicationBuilder` を使用して `PublicClientApplication` を作成する場合は、次の例に示すように `WithBroker()` パラメーターを使用します。 `WithBroker()` パラメーターは、既定で true に設定されます。

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

### <a name="step-2-enable-keychain-access"></a>手順 2:キーチェーン アクセスの有効化

キーチェーン アクセスを有効にするには、アプリケーションにキーチェーン アクセス グループが必要です。 アプリケーションを作成するときに、`WithIosKeychainSecurityGroup()` API を使用してキーチェーン アクセス グループを設定できます。

```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
     .WithIosKeychainSecurityGroup("com.microsoft.adalcache")
     .Build();
```

詳しくは、「[キーチェーン アクセスの有効化](msal-net-xamarin-ios-considerations.md#enable-keychain-access)」を参照してください。

### <a name="step-3-update-appdelegate-to-handle-the-callback"></a>手順 3:コールバックを処理するように AppDelegate を更新する

MSAL.NET によってブローカーが呼び出されると、ブローカーでは `AppDelegate` クラスの `OpenUrl` メソッドを使用してアプリケーションへのコールバックが行われます。 MSAL はブローカーの応答を待つため、アプリケーションを連携させて MSAL.NET を再度呼び出す必要があります。 この連携を可能にするには、*AppDelegate.cs* ファイルを更新して次のメソッドをオーバーライドします。

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

このメソッドは、アプリケーションが起動されるたびに呼び出されます。 これは、ブローカーからの応答を処理し、MSAL.NET によって開始される認証プロセスを完了する機会として使用されます。

### <a name="step-4-set-uiviewcontroller"></a>手順 4:Set UIViewController()

引き続き *AppDelegate.cs* ファイルで、オブジェクト ウィンドウを設定します。 通常、Xamarin iOS ではオブジェクト ウィンドウを設定する必要はありませんが、ブローカーとの間で応答を送受信するには、オブジェクト ウィンドウが必要です。

オブジェクト ウィンドウを設定するには、次の手順を実行します。

1. *AppDelegate.cs* ファイルで、`App.RootViewController` を新しい `UIViewController()` に設定します。 この割り当てにより、ブローカーへの呼び出しに `UIViewController` が含まれるようになります。 この設定が正しく割り当てられていない場合は、次のエラーを受け取ることがあります。

      `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`

1. `AcquireTokenInteractive` の呼び出しで `.WithParentActivityOrWindow(App.RootViewController)` を使用し、使用するオブジェクト ウィンドウへの参照を渡します。

    *App.cs* の場合:

    ```csharp
       public static object RootViewController { get; set; }
    ```

    *AppDelegate.cs* の場合:

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

### <a name="step-5-register-a-url-scheme"></a>手順 5:URL スキームを登録する

MSAL.NET は、URL を使用してブローカーを呼び出し、ブローカーの応答をアプリに返します。 ラウンド トリップを完了するには、*Info.plist* ファイルでアプリの URL スキームを登録します。

`CFBundleURLSchemes` の名前には、プレフィックスとして `msauth.` を含める必要があります。 プレフィックスに `CFBundleURLName` を続けます。

URL スキームでは、`BundleId` によってアプリが一意に識別されます (`$"msauth.(BundleId)"`)。 `BundleId` が `com.yourcompany.xforms` の場合、URL スキームは `msauth.com.yourcompany.xforms` になります。

> [!NOTE]
> この URL スキームは、ブローカーから応答を受け取るときにアプリを一意に識別するリダイレクト URI の一部になります。

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

### <a name="step-6-add-the-broker-identifier-to-the-lsapplicationqueriesschemes-section"></a>手順 6:LSApplicationQueriesSchemes セクションにブローカー識別子を追加する

MSAL では、`–canOpenURL:` を使用してブローカーがデバイスにインストールされているかどうかを確認します。 iOS 9 では、アプリケーションが照会できるスキームが Apple によってロックされています。

次の例のように、*Info.plist* ファイルの `LSApplicationQueriesSchemes` セクションに `msauthv2` を追加します。

```XML
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
      <string>msauthv3</string>
    </array>
```

### <a name="step-7-add-a-redirect-uri-to-your-app-registration"></a>手順 7:アプリの登録にリダイレクト URI を追加する

ブローカーを使用する場合、リダイレクト URI には追加の要件があります。 リダイレクト URI は次の形式にする _必要_ があります。

```csharp
$"msauth.{BundleId}://auth"
```

次に例を示します。

```csharp
public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth";
```

リダイレクト URI が *Info.plist* ファイルに追加した `CFBundleURLSchemes` の名前と一致することにご注意ください。

[Azure portal](https://portal.azure.com) で、アプリの登録にリダイレクト URI を追加します。 適切な形式のリダイレクト URI を生成するには、Azure portal の **[アプリの登録]** を使用して、バンドル ID からブローカー リダイレクト URI を生成します。

**リダイレクト URI を生成するには:**

1. <a href="https://portal.azure.com/" target="_blank">Azure portal</a> にサインインします。
1. **[Azure Active Directory]**  >  **[アプリの登録]** を選択し、登録したアプリを選択します
1. **[認証]**  >  **[プラットフォームを追加]**  >  **[iOS/macOS]** を選択します
1. バンドル ID を入力した後、 **[構成]** を選択します。

    コードに含めるため、 **[リダイレクト URI]** テキスト ボックスに表示される生成されたリダイレクト URI をコピーします。

    :::image type="content" source="media/msal-net-use-brokers-with-xamarin-apps/portal-01-ios-platform-settings.png" alt-text="Azure portal で生成されたリダイレクト URI による iOS プラットフォームの設定":::
1. **[完了]** を選択して、リダイレクト URI の生成を完了します。

## <a name="brokered-authentication-for-android"></a>Android のブローカー認証

### <a name="step-1-enable-broker-support"></a>手順 1:ブローカーのサポートを有効にする

ブローカーのサポートは `PublicClientApplication` ごとに有効にします。 既定では無効になっています。 `PublicClientApplicationBuilder` によって `IPublicClientApplication` を作成する場合は、`WithBroker()` パラメーター (既定では true に設定) を使用します。

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithRedirectUri(redirectUriOnAndroid) // See step #4
                .Build();
```

### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>手順 2:コールバックを処理するように AppDelegate を更新する

MSAL.NET によってブローカーが呼び出されると、今度は、ブローカーにより `OnActivityResult()` メソッドを使用してアプリケーションがコールバックされます。 MSAL ではブローカーからの応答を待機するので、アプリケーションでは MSAL.NET に結果をルーティングする必要があります。

次に示すように、`OnActivityResult()` メソッドをオーバーライドすることで、結果を `SetAuthenticationContinuationEventArgs(int requestCode, Result resultCode, Intent data)` メソッドにルーティングします。

```csharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
   base.OnActivityResult(requestCode, resultCode, data);
   AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(requestCode, resultCode, data);
}
```

このメソッドは、ブローカー アプリケーションが起動されるたびに呼び出され、ブローカーからの応答を処理して MSAL.NET によって開始された認証プロセスを完了するために使用されます。

### <a name="step-3-set-an-activity"></a>手順 3:アクティビティを設定する

ブローカー認証を有効にするには、MSAL がブローカーとの間で応答を送受信できるように、アクティビティを設定します。 そのためには、アクティビティ (通常は `MainActivity`) を親オブジェクト `WithParentActivityOrWindow(object parent)` に提供します。

たとえば、`AcquireTokenInteractive()` への呼び出しで次のようにします。

```csharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow((Activity)context))
             .ExecuteAsync();
```

### <a name="step-4-add-a-redirect-uri-to-your-app-registration"></a>手順 4:アプリの登録にリダイレクト URI を追加する

MSAL では、URL を使用してブローカーが呼び出され、アプリに戻されます。 そのラウンド トリップを完了するには、[Azure portal](https://portal.azure.com) を使用して、アプリに対する **リダイレクト URI** を登録します。

アプリケーションのリダイレクト URI の形式は、APK の署名に使用された証明書によって異なります。 次に例を示します。

```
msauth://com.microsoft.xforms.testApp/hgbUYHVBYUTvuvT&Y6tr554365466=
```

URI の最後の部分 `hgbUYHVBYUTvuvT&Y6tr554365466=` は、APK の署名に使用されている署名の Base64 エンコード バージョンです。 Visual Studio でアプリを開発するときに、特定の証明書を使用して APK に署名せずにコードをデバッグすると、Visual Studio によってデバッグのために自動的に APK が署名されます。 このように Visual Studio によって APK の署名が自動的に行われるときは、ビルドに使用されたコンピューターに対する一意の署名が提供されます。 このため、異なるコンピューター上でアプリをビルドするたびに、MSAL による認証を行うために、アプリケーションのコード内および Azure portal 上のアプリケーションの登録にあるリダイレクト URI を更新する必要が生じます。

デバッグしているときに、指定されたリダイレクト URI が正しくないことを示す MSAL 例外 (ログ メッセージ) が発生する場合があります。 現在デバッグを行っているコンピューターで **使用する必要があるリダイレクト URI は、例外またはログ メッセージでも示されます**。 コードでリダイレクト URI を更新し、提供されたリダイレクト URI を Azure portal のアプリの登録に追加すれば、提供されたリダイレクト URI を使用してアプリの開発を続けることができます。

コードの最終処理を行う準備ができたら、APK に署名するときの証明書の署名を使用するように、コード内と Azure portal 上のアプリケーションの登録にあるリダイレクト URI を更新します。

実際には、これは、開発チームの各メンバーに対するリダイレクト URI に "*加えて*"、運用環境の署名済みバージョンの APK に対するリダイレクト URI を追加することを、考慮する必要があることを意味します。

MSAL で行われるのと同じように、自分で署名を計算することもできます。

```csharp
   private string GetRedirectUriForBroker()
   {
      string packageName = Application.Context.PackageName;
      string signatureDigest = this.GetCurrentSignatureForPackage(packageName);
      if (!string.IsNullOrEmpty(signatureDigest))
      {
            return string.Format(CultureInfo.InvariantCulture, "{0}://{1}/{2}", RedirectUriScheme,
               packageName.ToLowerInvariant(), signatureDigest);
      }

      return string.Empty;
   }

   private string GetCurrentSignatureForPackage(string packageName)
   {
            PackageInfo info = Application.Context.PackageManager.GetPackageInfo(packageName,
               PackageInfoFlags.Signatures);
            if (info != null && info.Signatures != null && info.Signatures.Count > 0)
            {
               // First available signature. Applications can be signed with multiple signatures.
               // The order of Signatures is not guaranteed.
               Signature signature = info.Signatures[0];
               MessageDigest md = MessageDigest.GetInstance("SHA");
               md.Update(signature.ToByteArray());
               return Convert.ToBase64String(md.Digest(), Base64FormattingOptions.None);
               // Server side needs to register all other tags. ADAL will
               // send one of them.
            }
   }
```

また、**keytool** で次のコマンドを使用して、パッケージに対する署名を取得することもできます。

* Windows:
    ```console
    keytool.exe -list -v -keystore "%LocalAppData%\Xamarin\Mono for Android\debug.keystore" -alias androiddebugkey -storepass android -keypass android
    ````
* macOS:
    ```console
    keytool -exportcert -alias androiddebugkey -keystore ~/.android/debug.keystore | openssl sha1 -binary | openssl base64
    ````

### <a name="step-5-optional-fall-back-to-the-system-browser"></a>手順 5 (省略可能):システム ブラウザーにフォールバックする

MSAL がブローカーを使用するように構成されているのに、ブローカーがインストールされていない場合、MSAL は Web ビュー (ブラウザー) を使用するようにフォールバックします。 MSAL によりデバイスの既定のシステム ブラウザーを使用して認証が試みられますが、リダイレクト URI はブローカー用に構成されていて、システム ブラウザーではそれを使用して MSAL に戻る方法がわからないため、これは失敗します。 この失敗を回避するには、手順 4 で使用したブローカー リダイレクト URI を使用して、"*インテント フィルター*" を構成できます。

アプリケーションのマニフェストを変更して、インテント フィルターを追加します。

```xml
<!-- NOTE the SLASH (required) that prefixes the signature value in the path attribute.
     The signature value is the Base64-encoded signature discussed above. -->
<intent-filter>
      <data android:scheme="msauth"
                    android:host="Package Name"
                    android:path="/Package Signature"/>
```

たとえば、`msauth://com.microsoft.xforms.testApp/hgbUYHVBYUTvuvT&Y6tr554365466=` のリダイレクト URI がある場合、次の XML スニペットのようなマニフェストにする必要があります。

`android:path` の値の署名の前にあるスラッシュ (`/`) は **必要** です。

```xml
<!-- NOTE the SLASH (required) that prefixes the signature value in the path attribute.
     The signature value is the Base64-encoded signature discussed above. -->
<intent-filter>
      <data android:scheme="msauth"
                    android:host="com.microsoft.xforms.testApp"
                    android:path="/hgbUYHVBYUTvuvT&Y6tr554365466="/>
```

システム ブラウザーおよび Android 11 のサポート用にアプリケーションを構成する方法の詳細については、[システム ブラウザーのサポート用の Android マニフェストの更新](msal-net-xamarin-android-considerations.md#update-the-android-manifest-for-system-webview-support)に関するページを参照してください。

代わりに、リダイレクト URI に依存しない埋め込みブラウザーにフォールバックするように、MSAL を構成することもできます。

```csharp
.WithUseEmbeddedWebUi(true)
```

## <a name="troubleshooting-tips-for-android-brokered-authentication"></a>Android ブローカー認証のトラブルシューティングのヒント

Android でブローカー認証を実装するときの問題を回避するためのヒントを次に示します。

- **リダイレクト URI** - [Azure portal](https://portal.azure.com/) でアプリケーションの登録にリダイレクト URI を追加します。 リダイレクト URI がなかったり、正しくなかったりするのは、開発時によくある問題です。
- **ブローカーのバージョン** - 最低限必要なバージョンのブローカー アプリをインストールします。 Android でのブローカー認証には、次の 2 つのアプリのいずれかを使用できます。
  - [Intune ポータル サイト](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal) (バージョン 5.0.4689.0 以降)
  - [Microsoft Authenticator](https://play.google.com/store/apps/details?id=com.azure.authenticator) (バージョン 6.2001.0140 以降)。
- **ブローカーの優先順位** - 複数のブローカーがインストールされている場合、MSAL では、デバイスに "*インストールされた最初のブローカー*" が通信に使用されます。

    例:Microsoft Authenticator を最初にインストールしてから Intune ポータル サイトをインストールした場合、ブローカー認証は Microsoft Authenticator "*だけで*" 行われます。
- **ログ** - ブローカー認証で問題が発生した場合、ブローカーのログを見ると、原因の診断に役立つ場合があります。
  - Microsoft Authenticator のログの取得:

    1. アプリの右上隅にあるメニュー ボタンを選択します。
    1. **[フィードバックの送信]**  >  **[何かお困りですか? ]** を選択します。
    1. **[何を実行しようとしていますか?]** で、オプションを選択して説明を追加します。
    1. ログを送信するには、アプリの右上隅にある矢印を選択します。

    ログを送信すると、ダイアログ ボックスにインシデント ID が表示されます。 インシデント ID を記録し、サポートを要求するときに記載してください。

  - Intune ポータル サイトのログの取得:

    1. アプリの左上隅にあるメニュー ボタンを選択します。
    1. **[ヘルプ]**  >  **[電子メールでのサポート]** を選択します。
    1. ログを送信するには、 **[Upload Logs Only]\(ログのみをアップロード)\** を選択します。

    ログを送信すると、ダイアログ ボックスにインシデント ID が表示されます。 インシデント ID を記録し、サポートを要求するときに記載してください。

## <a name="next-steps"></a>次のステップ

[MSAL.NET でユニバーサル Windows プラットフォームを使用する場合の考慮事項](msal-net-uwp-considerations.md)に関するページを参照してください。
