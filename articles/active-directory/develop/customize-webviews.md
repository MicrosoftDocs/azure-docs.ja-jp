---
title: ブラウザーと WebView (MSAL iOS/macOS) のカスタマイズ | Azure
titleSuffix: Microsoft identity platform
description: ユーザーのサインインのために MASL iOS/macOS ブラウザー エクスペリエンスをカスタマイズする方法について説明します。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev
ms.openlocfilehash: a8486ec87b5198231a33b1dab382ba457c8c8066
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "85478129"
---
# <a name="how-to-customize-browsers-and-webviews-for-iosmacos"></a>方法:iOS/macOS のブラウザーと WebView のカスタマイズ

対話型の認証には Web ブラウザーが必要です。 iOS および macOS 10.15 以降の場合、Microsoft Authentication Library (MSAL) ではシステムの Web ブラウザー (アプリの上部に表示される場合があります) を既定で使用することで、ユーザーのサインインのための対話型認証が行われます。 システム ブラウザーを使用すると、シングル サインオン (SSO) の状態を他のアプリケーションや Web アプリケーションと共有できるという利点があります。

次のような Web コンテンツを表示するためのその他のオプションに対する構成をカスタマイズすることで、エクスペリエンスを変更できます。

iOS の場合のみ:

- [SFAuthenticationSession](https://developer.apple.com/documentation/safariservices/sfauthenticationsession?language=objc) 
- [SFSafariViewController](https://developer.apple.com/documentation/safariservices/sfsafariviewcontroller?language=objc)

iOS と macOS の場合:

- [ASWebAuthenticationSession](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession?language=objc)
- [WKWebView](https://developer.apple.com/documentation/webkit/wkwebview?language=objc).

macOS 用の MSAL の場合、以前のバージョンの OS では `WKWebView` のみサポートされています。 `ASWebAuthenticationSession` は、macOS 10.15 以降でのみサポートされます。 

## <a name="system-browsers"></a>システム ブラウザー

iOS の場合、`ASWebAuthenticationSession`、`SFAuthenticationSession`、`SFSafariViewController` がシステムブラウザーと見なされます。 MacOS の場合は、`ASWebAuthenticationSession` のみ使用できます。 一般に、システム ブラウザーは、Cookie およびその他の Web サイト データを Safari ブラウザー アプリケーションと共有します。

既定では、MSAL では iOS のバージョンが動的に検出され、そのバージョンで使用可能な推奨システムブラウザーが選択されます。 iOS 12 以降では、`ASWebAuthenticationSession` になります。 

### <a name="default-configuration-for-ios"></a>iOS の既定の構成

| Version | Web ブラウザー |
|:-------------:|:-------------:|
| iOS 12 以降 | ASWebAuthenticationSession |
| iOS 11 | SFAuthenticationSession |
| iOS 10 | SFSafariViewController |

### <a name="default-configuration-for-macos"></a>macOS の既定の構成

| Version | Web ブラウザー |
|:-------------:|:-------------:|
| macOS 10.15 以降 | ASWebAuthenticationSession |
| その他のバージョン | WKWebView |

開発者は、MSAL アプリ用に別のシステム ブラウザーを選択することもできます。

- `SFAuthenticationSession` は、`ASWebAuthenticationSession` の iOS 11 バージョンです。
- `SFSafariViewController` は、より汎用的なものであり、Web を閲覧するためのインターフェイスを提供し、ログインの目的でも使用できます。 iOS 9 と 10 では Cookie およびその他の Web サイト データが Safari と共有されますが、iOS 11 以降では共有されません。

## <a name="in-app-browser"></a>アプリ内ブラウザー

[WKWebView](https://developer.apple.com/documentation/webkit/wkwebview) は、Web コンテンツを表示するアプリ内ブラウザーです。 Cookie や Web サイト データは、別の **WKWebView** インスタンスや Safari ブラウザーと共有されません。 WKWebView は、iOS と macOS の両方で使用できるクロスプラットフォームのブラウザーです。

## <a name="cookie-sharing-and-single-sign-on-sso-implications"></a>Cookie 共有とシングル サインオン (SSO) の影響

使用するブラウザーは、Cookie の共有方法によって SSO エクスペリエンスに影響を与えます。 次の表は、ブラウザーごとの SSO エクスペリエンスをまとめたものです。

| テクノロジ    | ブラウザーの種類  | iOS での使用可能性 | macOS での使用可能性 | Cookie およびその他のデータの共有  | MSAL の使用可能性 | SSO |
|:-------------:|:-------------:|:-------------:|:-------------:|:-------------:|:-------------:|-------------:|
| [ASWebAuthenticationSession](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession) | システム | iOS12 以降 | macOS 10.15 以降 | はい | iOS および macOS 10.15 以降 | w/ Safari インスタンス
| [SFAuthenticationSession](https://developer.apple.com/documentation/safariservices/sfauthenticationsession) | システム | iOS11 以降 | 該当なし | はい | iOS のみ |  w/ Safari インスタンス
| [SFSafariViewController](https://developer.apple.com/documentation/safariservices/sfsafariviewcontroller) | システム | iOS11 以降 | 該当なし | いいえ | iOS のみ | いいえ**
| **SFSafariViewController** | システム | iOS10 | 該当なし | はい | iOS のみ |  w/ Safari インスタンス
| **WKWebView**  | アプリ内 | iOS8 以降 | macOS 10.10 以降 | いいえ | iOS と macOS | いいえ**

** SSO が機能するには、アプリ間でトークンを共有する必要があります。 これには、トークン キャッシュ、または iOS 用の Microsoft Authenticator などのブローカー アプリケーションが必要です。

## <a name="change-the-default-browser-for-the-request"></a>要求の既定のブラウザーを変更する

`MSALWebviewParameters` で次のプロパティを変更することによって、UX の要件に応じてアプリ内ブラウザーまたは特定のシステム ブラウザーを使用できます。

```objc
@property (nonatomic) MSALWebviewType webviewType;
```

## <a name="change-per-interactive-request"></a>対話型要求ごとに変更する

各要求は、既定のブラウザーをオーバーライドするように構成できます。そのためには、`MSALInteractiveTokenParameters.webviewParameters.webviewType` プロパティを変更してから `acquireTokenWithParameters:completionBlock:` API に渡します。

さらに、MSAL では、`MSALInteractiveTokenParameters.webviewParameters.customWebView` プロパティを設定することによってカスタム `WKWebView` に渡すことがサポートされています。

次に例を示します。

Objective-C
```objc
UIViewController *myParentController = ...;
WKWebView *myCustomWebView = ...;
MSALWebviewParameters *webViewParameters = [[MSALWebviewParameters alloc] initWithAuthPresentationViewController:myParentController];
webViewParameters.webviewType = MSALWebviewTypeWKWebView;
webViewParameters.customWebview = myCustomWebView;
MSALInteractiveTokenParameters *interactiveParameters = [[MSALInteractiveTokenParameters alloc] initWithScopes:@[@"myscope"] webviewParameters:webViewParameters];
    
[app acquireTokenWithParameters:interactiveParameters completionBlock:completionBlock];
```
Swift
```swift
let myParentController: UIViewController = ...
let myCustomWebView: WKWebView = ...
let webViewParameters = MSALWebviewParameters(authPresentationViewController: myParentController)
webViewParameters.webviewType = MSALWebviewType.wkWebView
webViewParameters.customWebview = myCustomWebView
let interactiveParameters = MSALInteractiveTokenParameters(scopes: ["myscope"], webviewParameters: webViewParameters)

app.acquireToken(with: interactiveParameters, completionBlock: completionBlock)
```

カスタム Web ビューを使用する場合、表示されている Web コンテンツの状態を示すために通知が使用されます。次に例を示します。

```objc
/*! Fired at the start of a resource load in the webview. The URL of the load, if available, will be in the @"url" key in the userInfo dictionary */
extern NSString *MSALWebAuthDidStartLoadNotification;

/*! Fired when a resource finishes loading in the webview. */
extern NSString *MSALWebAuthDidFinishLoadNotification;

/*! Fired when web authentication fails due to reasons originating from the network. Look at the @"error" key in the userInfo dictionary for more details.*/
extern NSString *MSALWebAuthDidFailNotification;

/*! Fired when authentication finishes */
extern NSString *MSALWebAuthDidCompleteNotification;

/*! Fired before ADAL invokes the broker app */
extern NSString *MSALWebAuthWillSwitchToBrokerApp;
```

### <a name="options"></a>Options

すべての MSAL でサポートされる Web ブラウザーの種類は、[MSALWebviewType 列挙型](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALDefinitions.h#L47)で宣言されます。

```objc
typedef NS_ENUM(NSInteger, MSALWebviewType)
{
    /**
     For iOS 11 and up, uses AuthenticationSession (ASWebAuthenticationSession or SFAuthenticationSession).
     For older versions, with AuthenticationSession not being available, uses SafariViewController.
     For macOS 10.15 and above uses ASWebAuthenticationSession
     For older macOS versions uses WKWebView
     */
    MSALWebviewTypeDefault,
    
    /** Use ASWebAuthenticationSession where available.
     On older iOS versions uses SFAuthenticationSession
     Doesn't allow any other webview type, so if either of these are not present, fails the request*/
    MSALWebviewTypeAuthenticationSession,
    
#if TARGET_OS_IPHONE
    
    /** Use SFSafariViewController for all versions. */
    MSALWebviewTypeSafariViewController,
    
#endif
    /** Use WKWebView */
    MSALWebviewTypeWKWebView,
};
```

## <a name="next-steps"></a>次のステップ

[認証フローとアプリケーションのシナリオ](authentication-flows-app-scenarios.md)の詳細を確認します
