---
title: MSAL (iOS および macOS) でリダイレクト URI を使用する | Azure
titleSuffix: Microsoft identity platform
description: Microsoft Authentication Library for ObjectiveC (MSAL for iOS および macOS) と Azure AD Authentication Library for ObjectiveC (ADAL.ObjC) の違いと、それらの間の移行方法について説明します。
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/28/2019
ms.author: twhitney
ms.reviewer: jak
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: a401f019f38d00790b1975f7a0a68d6097c21a70
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74920363"
---
# <a name="using-redirect-uris-with-the-microsoft-authentication-library-for-ios-and-macos"></a>iOS 用と macOS 用の Microsoft 認証ライブラリでのリダイレクト URI の使用

ユーザーが認証されるとき、Azure Active Directory (Azure AD) では、Azure AD アプリケーションに登録されているリダイレクト URI を使用して、アプリにトークンが送信されます。

Microsoft Authentication library (MSAL) では、リダイレクト URI が特定の形式で Azure AD アプリに登録されている必要があります。 MSAL では、指定がない場合は、既定のリダイレクト URI が使用されます。 形式は `msauth.[Your_Bundle_Id]://auth` です。

既定のリダイレクト URI 形式は、ブローカー認証やシステム Web ビューを含むほとんどのアプリとシナリオで機能します。 可能な限り、既定の形式を使用してください。

ただし、次に示すように、高度なシナリオでは、リダイレクト URI の変更が必要な場合があります。

## <a name="scenarios-that-require-a-different-redirect-uri"></a>異なるリダイレクト URI が必要なシナリオ

### <a name="cross-app-single-sign-on-sso"></a>クロスアプリ シングル サインオン (SSO)

Microsoft ID プラットフォームでアプリ間でトークンを共有するには、各アプリが同じクライアント ID またはアプリケーション ID を持っている必要があります。 これは、ポータルでアプリを登録したときに提供された一意の識別子です (アプリごとに Apple に登録するアプリケーション バンドル ID ではありません)。

リダイレクト URI は、iOS アプリごとに異なる必要があります。 これにより、Microsoft ID サービスで、アプリケーション ID を共有するさまざまなアプリを一意に識別できます。 各アプリケーションでは、Azure portal で、複数のリダイレクト URI を登録できます。 スイートの各アプリは、異なるリダイレクト URI を持ちます。 例:

Azure portal に次のアプリケーション登録があるとします。

    Client ID: ABCDE-12345 (this is a single client ID)
    RedirectUris: msauth.com.contoso.app1://auth, msauth.com.contoso.app2://auth, msauth.com.contoso.app3://auth

App1 ではリダイレクト `msauth.com.contoso.app1://auth` が、App2 では `msauth.com.contoso.app2://auth` が、App3 では `msauth.com.contoso.app1://auth` が使用されます。

### <a name="migrating-from-adal-to-msal"></a>ADAL から MSAL への移行

Azure AD Authentication ライブラリ (ADAL) を使用していたコードを MSAL に移行する場合、アプリ用に構成されたリダイレクト URI が既に存在している可能性があります。 ADAL アプリがブローカー シナリオをサポートするように構成され、かつリダイレクト URI が MSAL のリダイレクト URI 形式の要件を満たしている限り、同じリダイレクト URI を使用し続けることができます。

## <a name="msal-redirect-uri-format-requirements"></a>MSAL のリダイレクト URI 形式の要件

* MSAL のリダイレクト URI は、`<scheme>://host` 形式である必要があります。

    ここで `<scheme>` は、アプリを識別する一意の文字列です。 これは、一意性を保証するために、主にアプリケーションのバンドル識別子を基にしています。 たとえば、アプリのバンドル ID が `com.contoso.myapp` の場合、リダイレクト URI は `msauth.com.contoso.myapp://auth` 形式になります。

    ADAL から移行する場合、リダイレクト URI の形式は `<scheme>://[Your_Bundle_Id]` になる可能性があります。ここで `scheme` は一意の文字列です。 この形式は、MSAL を使用する場合、引き続き機能します。

* アプリの Info.plist の `CFBundleURLTypes > CFBundleURLSchemes` の下に `<scheme>` を登録する必要があります。  この例では、Info.plist がソース コードとして開かれています。

    ```xml
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
    

MSAL によって、リダイレクト URI が正しく登録されているかどうかが検証され、そうでない場合はエラーが返されます。
    
* ユニバーサル リンクをリダイレクト URI として使用する場合、`<scheme>` を `https` にする必要があり、`CFBundleURLSchemes` 内に宣言する必要はありません。 代わりに、Apple の手順 ([開発者のためのユニバーサル リンク](https://developer.apple.com/ios/universal-links/)) に従ってアプリとドメインを構成し、ユニバーサル リンクを通してアプリケーションが開かれるときに、`MSALPublicClientApplication` の `handleMSALResponse:sourceApplication:` を呼び出します。

## <a name="use-a-custom-redirect-uri"></a>カスタム リダイレクト URI を使用する

カスタム リダイレクト URI を使用するには、`redirectUri` パラメーターを `MSALPublicClientApplicationConfig` に渡し、そのオブジェクトを初期化するときにオブジェクトを `MSALPublicClientApplication` に渡します。 リダイレクト URI が無効な場合は、初期化子によって `nil` が返され、`redirectURIError` と追加情報が設定されます。  例:

Objective-C:

```objc
MSALPublicClientApplicationConfig *config =
        [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"your-client-id"
                                                        redirectUri:@"your-redirect-uri"
                                                        authority:authority];
NSError *redirectURIError;
MSALPublicClientApplication *application =
        [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&redirectURIError];
```

Swift:

```swift
let config = MSALPublicClientApplicationConfig(clientId: "your-client-id",
                                            redirectUri: "your-redirect-uri",
                                              authority: authority)
do {
  let application = try MSALPublicClientApplication(configuration: config)
  // continue on with application          
} catch let error as NSError {
  // handle error here
}       
```



## <a name="handle-the-url-opened-event"></a>URL で開かれたイベントを処理する

アプリケーションでは、URL スキームまたはユニバーサル リンクを通して応答を受信したときに、MSAL を呼び出す必要があります。 アプリケーションを開くときに、`MSALPublicClientApplication` の `handleMSALResponse:sourceApplication:` メソッドを呼び出します。 カスタム スキーマの例を次に示します。

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
    return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
}
```



## <a name="next-steps"></a>次の手順

[認証フローとアプリケーションのシナリオ](authentication-flows-app-scenarios.md)の詳細を確認します
