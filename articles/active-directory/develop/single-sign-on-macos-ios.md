---
title: macOS と iOS で SSO を構成する
titleSuffix: Microsoft identity platform
description: macOS と iOS でシングル サインオン (SSO) を構成する方法について説明します。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/03/2020
ms.author: marsma
ms.reviewer: ''
ms.custom: aaddev
ms.openlocfilehash: 25389348476552298ddb947ccb59acb8b3d5bc57
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "80881250"
---
# <a name="how-to-configure-sso-on-macos-and-ios"></a>方法: macOS と iOS で SSO を構成する

macOS および iOS 用の Microsoft Authentication Library (MSAL) は、macOS/iOS アプリとブラウザーの間でのシングル サインオン (SSO) をサポートしています。 この記事では、次の SSO シナリオを取り上げます。

- [複数のアプリ間でのサイレント SSO](#silent-sso-between-apps)

この種類の SSO は、同じ Apple Developer によって配布される複数のアプリ間で機能します。 キーチェーンから他のアプリによって書き込まれた更新トークンを読み取って、アクセス トークンとして暗黙的に交換することによってサイレント SSO が提供されます (つまり、ユーザーは資格情報の入力を求められません)。  

- [認証ブローカーを使用した SSO](#sso-through-authentication-broker-on-ios)

> [!IMPORTANT]
> このフローは macOS では使用できません。

Microsoft は、モバイル デバイスが Azure Active Directory (AAD) に登録されている限り、さまざまなベンダーのアプリケーション間での SSO が可能になる、ブローカーと呼ばれるアプリを提供します。 この種類の SSO を使用するには、ユーザーのデバイスにブローカー アプリケーションをインストールする必要があります。

- **MSAL と Safari の間の SSO**

SSO は [ASWebAuthenticationSession](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession?language=objc) クラスを使用して実現されます。 他のアプリや Safari ブラウザーの既存のサインイン状態が使用されます。 同じ Apple Developer から配布されるアプリに限定されるわけではありませんが、ユーザーの操作が必要になります。

ユーザーのサインインにアプリの既定の Web ビューを使用すると、MSAL ベースのアプリケーションと Safari の間で自動 SSO が可能になります。 MSAL でサポートされている Web ビューの詳細については、[ブラウザーと Web ビューのカスタマイズ](customize-webviews.md)に関する記事を参照してください。

> [!IMPORTANT]
> この種類の SSO は現在 macOS では使用できません。 macOS での MSAL は、Safari での SSO をサポートしない WKWebView のみをサポートしています。 

- **ADAL と MSAL の macOS/iOS アプリ間のサイレント SSO**

MSAL の Objective-C では、ADAL Objective-C ベースのアプリでの移行と SSO がサポートされています。 アプリは、同じ Apple Developer によって配布される必要があります。

ADAL と MSAL ベースのアプリ間のクロスアプリ SSO の手順については、[macOS と iOS での ADAL と MSAL アプリ間の SSO](sso-between-adal-msal-apps-macos-ios.md) に関する記事を参照してください。

## <a name="silent-sso-between-apps"></a>アプリ間のサイレント SSO

MSAL は、iOS キーチェーン アクセス グループを介した SSO 共有をサポートしています。

アプリケーション全体で SSO を有効にするには、次の手順を実行する必要があります。詳しくは以下で説明します。

1. すべてのアプリケーションが同じクライアント ID またはアプリケーション ID を使用していることを確認します。
1. キーチェーンを共有できるように、すべてのアプリケーションが Apple の同じ署名証明書を共有していることを確認します。
1. 各アプリケーション用に同じキーチェーン エンタイトルメントを要求します。
1. 既定のものと異なる場合に使用する共有キーチェーンを MSAL SDK に通知します。

### <a name="use-the-same-client-id-and-application-id"></a>同じクライアント ID とアプリケーション ID を使用する

Microsoft ID プラットフォームがトークンを共有できるアプリケーションを把握するには、それらのアプリケーションが同じクライアント ID またはアプリケーション ID を共有する必要があります。 これは、ポータルで最初のアプリケーションを登録したときに提供された一意の識別子です。

Microsoft ID プラットフォームがアプリにそれぞれ同じアプリケーション ID を使用するように伝える場合、**リダイレクト URI** を使用します。 各アプリケーションには、オンボード ポータルで、複数のリダイレクト URI を登録することができます。 スイートの各アプリは、異なるリダイレクト URI を持ちます。 次に例を示します。

App1 のリダイレクト URI: `msauth.com.contoso.mytestapp1://auth`  
App2 のリダイレクト URI: `msauth.com.contoso.mytestapp2://auth`  
App3 のリダイレクト URI: `msauth.com.contoso.mytestapp3://auth`  

> [!IMPORTANT]
> リダイレクト URI の形式は、MSAL がサポートする形式と互換性がある必要があります。これについては「[MSAL リダイレクト URI 形式の要件](redirect-uris-ios.md#msal-redirect-uri-format-requirements)」に記載されています。

### <a name="setup-keychain-sharing-between-applications"></a>アプリケーション間のキーチェーン共有を設定する

キーチェーン共有を有効にするには、Apple の[機能の追加](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html)に関する記事を参照してください。 重要なのは、キーチェーンを何と呼ぶかを決め、SSO に関連するすべてのアプリケーションにわたってその機能を追加することです。

エンタイトルメントを正しく設定すると、プロジェクト ディレクトリに `entitlements.plist` ファイルが表示されます。そのファイルには、次のような内容が含まれています。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>keychain-access-groups</key>
    <array>
        <string>$(AppIdentifierPrefix)com.myapp.mytestapp</string>
        <string>$(AppIdentifierPrefix)com.myapp.mycache</string>
    </array>
</dict>
</plist>
```

#### <a name="add-a-new-keychain-group"></a>新しいキーチェーン グループを追加する

新しいキーチェーン グループをプロジェクトの **[機能]** に追加します。 キーチェーン グループは、次のようになります。
* iOS の場合: `com.microsoft.adalcache` 
* macOS の場合: `com.microsoft.identity.universalstorage`

![キーチェーンの例](media/single-sign-on-macos-ios/keychain-example.png)

詳細については、[キーチェーン グループ](howto-v2-keychain-objc.md)に関する記事をご覧ください。

## <a name="configure-the-application-object"></a>アプリケーション オブジェクトの構成

各アプリケーションでキーチェーンのエンタイトルメントを有効にし、SSO を使用する準備ができたら、次の例のように、キーチェーン アクセス グループを使用して `MSALPublicClientApplication` を構成します。

Objective-C:

```objc
NSError *error = nil;
MSALPublicClientApplicationConfig *configuration = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<my-client-id>"];
configuration.cacheConfig.keychainSharingGroup = @"my.keychain.group";
    
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:configuration error:&error];
```

Swift:

```swift
let config = MSALPublicClientApplicationConfig(clientId: "<my-client-id>")
config.cacheConfig.keychainSharingGroup = "my.keychain.group"

do {
   let application = try MSALPublicClientApplication(configuration: config)
  // continue on with application
} catch let error as NSError {
  // handle error here
}
```

> [!WARNING]
> 複数のアプリケーション間でキーチェーンを共有すると、どのアプリケーションでもユーザーを削除できます。さらに、アプリケーション全体のすべてのトークンを削除することもできてしまいます。
> これは、バックグラウンド作業をトークンに依存するアプリケーションがある場合に、特に影響があります。
> キーチェーンを共有したら、アプリで Microsoft Identity SDK の削除作業を行うときに、十分に注意する必要があります。

これで完了です。 Microsoft Identity SDK が、すべてのアプリケーション間で資格情報を共有するようになりました。 アカウントの一覧も、アプリケーション インスタンス間で共有されます。

## <a name="sso-through-authentication-broker-on-ios"></a>iOS での認証ブローカーを使用した SSO

MSAL では、Microsoft Authenticator による仲介型認証がサポートされています。 Microsoft Authenticator は、AAD に登録されたデバイス用の SSO を提供します。また、アプリケーションが条件付きアクセス ポリシーに従うこともできます。

次の手順は、認証ブローカーを使用してアプリの SSO を有効にする方法です。

1. アプリの Info.plist でアプリケーションのブローカーと互換するリダイレクト URI 形式を登録します。 ブローカーと互換性のあるリダイレクト URI 形式は `msauth.<app.bundle.id>://auth` です。 `<app.bundle.id>` をアプリケーションのバンドル ID に置き換えます。 次に例を示します。

    ```xml
    <key>CFBundleURLSchemes</key>
    <array>
        <string>msauth.<app.bundle.id></string>
    </array>
    ```

1. 次のスキームをアプリの Info.plist の `LSApplicationQueriesSchemes` の下に追加します。

    ```xml
    <key>LSApplicationQueriesSchemes</key>
    <array>
         <string>msauthv2</string>
         <string>msauthv3</string>
    </array>
    ```

1. コールバックが処理されるように、`AppDelegate.m` ファイルに次を追加します。

    Objective-C:
    
    ```objc
    - (BOOL)application:(UIApplication *)app openURL:(NSURL *)url options:(NSDictionary<NSString *,id> *)options
    {
        return [MSALPublicClientApplication handleMSALResponse:url sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
    }
    ```
    
    Swift:
    
    ```swift
    func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
    }
    ```
    
**Xcode 11 を使用している場合は**、代わりに MSAL コールバックを `SceneDelegate` ファイルに配置する必要があります。
以前の iOS との互換性を保持するために UISceneDelegate と UIApplicationDelegate の両方をサポートしている場合は、MSAL コールバックを両方のファイルに配置する必要があります。

Objective-C:

```objc
 - (void)scene:(UIScene *)scene openURLContexts:(NSSet<UIOpenURLContext *> *)URLContexts
 {
     UIOpenURLContext *context = URLContexts.anyObject;
     NSURL *url = context.URL;
     NSString *sourceApplication = context.options.sourceApplication;
     
     [MSALPublicClientApplication handleMSALResponse:url sourceApplication:sourceApplication];
 }
```

Swift:

```swift
func scene(_ scene: UIScene, openURLContexts URLContexts: Set<UIOpenURLContext>) {
        
        guard let urlContext = URLContexts.first else {
            return
        }
        
        let url = urlContext.url
        let sourceApp = urlContext.options.sourceApplication
        
        MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApp)
    }
```

## <a name="next-steps"></a>次のステップ

[認証フローとアプリケーションのシナリオ](authentication-flows-app-scenarios.md)の詳細を確認します