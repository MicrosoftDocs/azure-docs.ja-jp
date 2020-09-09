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
ms.openlocfilehash: 8e19677adf5fe0f64ad9e1c845f516f81ad89512
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2020
ms.locfileid: "88166061"
---
# <a name="use-microsoft-authenticator-or-intune-company-portal-on-xamarin-applications"></a>Xamarin アプリケーションで Microsoft Authenticator または Intune ポータル サイトを使用する

Android および iOS では、Microsoft Authenticator や Android 固有の Microsoft Intune ポータル サイトなどのブローカーによって、以下が可能になります。

- **シングル サインオン (SSO)** : ユーザーは、各アプリケーションにサインインする必要はありません。
- **デバイスの識別**: ブローカーからデバイス証明書にアクセスします。 この証明書は、デバイスがワークプレースに参加したときに作成されます。
- **アプリケーション ID の検証**: アプリケーションはブローカーを呼び出すとき、自身のリダイレクト URL を渡します。 ブローカーによって URL が確認されます。

これらの機能のいずれかを有効にするには、`PublicClientApplicationBuilder.CreateApplication` メソッドを呼び出すときに `WithBroker()` パラメーターを使用します。 `.WithBroker()` パラメーターは、既定で true に設定されています。 

また、以下のセクションの手順に従って、[iOS](#brokered-authentication-for-ios) アプリケーションまたは [Android](#brokered-authentication-for-android) アプリケーションのブローカー認証を設定します。

## <a name="brokered-authentication-for-ios"></a>iOS のブローカー認証

次の手順に従って、Xamarin.iOS アプリが [Microsoft Authenticator](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458) アプリと通信できるようにします。

### <a name="step-1-enable-broker-support"></a>手順 1:ブローカーのサポートを有効にする
`PublicClientApplication` の個々のインスタンスに対してブローカーのサポートを有効にする必要があります。 既定では、サポートは無効になっています。 `PublicClientApplicationBuilder` を使用して `PublicClientApplication` を作成する場合は、次の例に示すように `WithBroker()` パラメーターを使用します。 `WithBroker()` パラメーターは、既定で true に設定されています。

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
Microsoft Authentication Library for .NET (MSAL.NET) がブローカーを呼び出すと、ブローカーでは `AppDelegate` クラスの `OpenUrl` メソッドを使用してアプリケーションにコールバックされます。 MSAL はブローカーの応答を待つため、アプリケーションを連携させて MSAL.NET を再度呼び出す必要があります。 この連携を可能にするには、`AppDelegate.cs` ファイルを更新して次のメソッドをオーバーライドします。

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
`AppDelegate.cs` ファイルでも、オブジェクト ウィンドウを設定する必要があります。 通常、Xamarin iOS では、オブジェクト ウィンドウを設定する必要はありません。 ただし、ブローカーから応答を送受信するには、オブジェクト ウィンドウが必要です。 

オブジェクト ウィンドウを設定するには、次の手順を実行します。 
1. `AppDelegate.cs` ファイルで、`App.RootViewController` を新しい `UIViewController()` に設定します。 この割り当てにより、ブローカーへの呼び出しに `UIViewController` が含まれるようになります。 この設定が正しく割り当てられていない場合は、次のエラーを受け取ることがあります。

      `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`

1. `AcquireTokenInteractive` の呼び出しで `.WithParentActivityOrWindow(App.RootViewController)` を使用し、使用するオブジェクト ウィンドウへの参照を渡します。

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

### <a name="step-5-register-a-url-scheme"></a>手順 5:URL スキームを登録する
MSAL.NET は、URL を使用してブローカーを呼び出し、ブローカーの応答をアプリに返します。 ラウンド トリップを完了するには、`Info.plist` ファイルにアプリの URL スキームを登録します。

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

次の例のように、`Info.plist` ファイルの `LSApplicationQueriesSchemes` セクションに `msauthv2` を追加します。

```XML
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
      <string>msauthv3</string>
    </array>
```

### <a name="step-7-register-your-redirect-uri-in-the-application-portal"></a>手順 7:アプリケーション ポータルでリダイレクト URI を登録する

ブローカーを使用する場合、リダイレクト URI には追加の要件があります。 リダイレクト URI は次の形式にする _必要_ があります。

```csharp
$"msauth.{BundleId}://auth"
```

次に例を示します。

```csharp
public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth"; 
```

リダイレクト URI が `Info.plist` ファイルに含まれている `CFBundleURLSchemes` の名前と一致することに注意してください。

### <a name="step-8-make-sure-the-redirect-uri-is-registered-with-your-app"></a>手順 8:リダイレクト URI がアプリに登録されていることを確認する

このリダイレクト URI は、[アプリ登録ポータル](https://portal.azure.com)でアプリケーションの有効なリダイレクト URI として登録されている必要があります。 

アプリ登録ポータルには、バンドル ID からのブローカー応答 URI を計算するために役立つ、新しいエクスペリエンスが用意されています。 

リダイレクト URI を計算するには、次の手順を実行します。

1. アプリの登録で、 **[認証]**  >  **[新しいエクスペリエンスを試す]** を選択します。

   ![新しいアプリの登録エクスペリエンスを試す](media/msal-net-use-brokers-with-xamarin-apps/60799285-2d031b00-a173-11e9-9d28-ac07a7ae894a.png)

1. **[プラットフォームの追加]** を選択します。

   ![プラットフォームの追加](media/msal-net-use-brokers-with-xamarin-apps/60799366-4c01ad00-a173-11e9-934f-f02e26c9429e.png)

1. プラットフォームの一覧がサポートされている場合は、 **[iOS]** を選択します。

   ![iOS の構成](media/msal-net-use-brokers-with-xamarin-apps/60799411-60de4080-a173-11e9-9dcc-d39a45826d42.png)

1. 要求されたバンドル ID を入力し、 **[構成]** を選択します。

   ![バンドル ID を入力する](media/msal-net-use-brokers-with-xamarin-apps/60799477-7eaba580-a173-11e9-9f8b-431f5b09344e.png)

手順を完了すると、リダイレクト URI が自動的に計算されます。

![リダイレクト URI のコピー](media/msal-net-use-brokers-with-xamarin-apps/60799538-9e42ce00-a173-11e9-860a-015a1840fd19.png)

## <a name="brokered-authentication-for-android"></a>Android のブローカー認証

### <a name="step-1-enable-broker-support"></a>手順 1:ブローカーのサポートを有効にする

ブローカーのサポートは、PublicClientApplication ベースごとに有効になります。 既定では無効になっています。 `PublicClientApplicationBuilder` によって `IPublicClientApplication` を作成する場合は、`WithBroker()` パラメーター (既定では true に設定) を使用します。

```CSharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithRedirectUri(redirectUriOnAndroid) //(see step 4 below)
                .Build();
```

### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>手順 2:コールバックを処理するように AppDelegate を更新する

MSAL.NET によってブローカーが呼び出されると、次に、ブローカーは OnActivityResult() メソッドを使用してアプリケーションにコールバックします。 MSAL ではブローカーからの応答を待機するので、アプリケーションでは MSAL.NET に結果をルーティングする必要があります。
これを実現するには、次に示すように OnActivityResult () メソッドをオーバーライドして、結果を `SetAuthenticationContinuationEventArgs(int requestCode, Result resultCode, Intent data)` にルーティングします

```CSharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
   base.OnActivityResult(requestCode, resultCode, data);
   AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(requestCode, resultCode, data);
}
```

このメソッドは、ブローカー アプリケーションが起動されるたびに呼び出され、ブローカーからの応答を処理して MSAL.NET によって開始された認証プロセスを完了するために使用されます。

### <a name="step-3-set-an-activity"></a>手順 3:アクティビティを設定する

ブローカー認証が機能するためには、MSAL 上でブローカーからの応答を送受信できるように、アクティビティを設定する必要があります。

これを行うには、アクティビティ (通常は MainActivity) を親オブジェクトとして `WithParentActivityOrWindow(object parent)` に提供する必要があります。 

**例:**

AcquireToken の呼び出しで、次のように記述します。

```CSharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow((Activity)context))
             .ExecuteAsync();
```

### <a name="step-4-register-your-redirecturi-in-the-application-portal"></a>手順 4:アプリケーション ポータルで RedirectUri を登録する

MSAL では URL を使用してブローカーを呼び出して、アプリに戻ります。 ラウンド トリップを完了するには、アプリの URL スキームを登録する必要があります。 このリダイレクト URI は、Azure AD アプリ登録ポータル上でアプリケーションの有効なリダイレクト URI として登録されている必要があります。


アプリケーションに必要なリダイレクト URI は、APK の署名に使用される証明書に依存します。

```
Example: msauth://com.microsoft.xforms.testApp/hgbUYHVBYUTvuvT&Y6tr554365466=
```

URI の最後の部分である `hgbUYHVBYUTvuvT&Y6tr554365466=` は、APK での署名に使用されるシグネチャであり、base64 でエンコードされています。
ただし、Visual Studio を使用したアプリケーションの開発フェーズ期間中に、特定の証明書の APK に署名しないままコードをデバッグすると、デバッグの目的のために Visual Studio によって APK への署名が自動的に行われ、APK にはビルドされているコンピューターに固有のシグネチャが付与されます。 このため、異なるコンピューター上でアプリをビルドするたびに、MSAL による認証を行うために、アプリケーションのコード内および Azure portal 上のアプリケーションの登録にあるリダイレクト URI を更新する必要が生じます。 

デバッグしているときに、指定されたリダイレクト URI が正しくないことを示す MSAL 例外 (ログ メッセージ) が発生する場合があります。 デバッグを行っている現在のコンピューターで**使用していなければならないリダイレクト URI も、この例外によって提示されます**。 当面の間は、このリダイレクト URI を使用して開発を続けることができます。

コードの最終処理を行う準備ができたら、APK に署名する際の証明書のシグネチャを使用するように、コード内と Azure portal 上のアプリケーションの登録にあるリダイレクト URI を確実に更新します。

実際には、チームの各メンバーに対するリダイレクト URI と、実稼働環境での署名済みバージョンの APK に対するリダイレクト URI を登録する必要があることを意味します。

このシグネチャは、MSAL によって行われる方法と同じように、ご自身で計算することもできます。 

```CSharp
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

また、次のコマンドによって keytool を使用して、ご自身のパッケージ用のシグネチャを取得することもできます。

Windows の場合: `keytool.exe -list -v -keystore "%LocalAppData%\Xamarin\Mono for Android\debug.keystore" -alias androiddebugkey -storepass android -keypass android`

Mac の場合: `keytool -exportcert -alias androiddebugkey -keystore ~/.android/debug.keystore | openssl sha1 -binary | openssl base64`

## <a name="next-steps"></a>次のステップ

[MSAL.NET でユニバーサル Windows プラットフォームを使用する場合の考慮事項](msal-net-uwp-considerations.md)に関するページを参照してください。
