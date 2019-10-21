---
title: ブラウザーと WebView のカスタマイズ | Microsoft ID プラットフォーム
description: ユーザーのサインインのために iOS および macOS 用 MSAL で使用されるブラウザー エクスペリエンスをカスタマイズする方法について説明します。
services: active-directory
documentationcenter: dev-center-name
author: tylermsft
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/28/2019
ms.author: twhitney
ms.reviewer: ''
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: c1b7417de8de6fb063de18fe670ef474a3b486d0
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2019
ms.locfileid: "71269393"
---
# <a name="how-to-customize-browsers-and-webviews-for-iosmacos"></a>方法:iOS/macOS のブラウザーと WebView のカスタマイズ

対話型の認証には Web ブラウザーが必要です。 iOS の場合、Microsoft Authentication Library (MSAL) ではシステムの Web ブラウザー (アプリの上部に表示される場合があります) を既定で使用することで、ユーザーのサインインのための対話型認証が行われます。 システム ブラウザーを使用すると、シングル サインオン (SSO) の状態を他のアプリケーションや Web アプリケーションと共有できるという大きな利点があります。

次のような Web コンテンツを表示するためのその他のオプションに対する構成をカスタマイズすることで、エクスペリエンスを変更できます。

iOS の場合のみ:

- [ASWebAuthenticationSession](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession?language=objc)
- [SFAuthenticationSession](https://developer.apple.com/documentation/safariservices/sfauthenticationsession?language=objc) 
- [SFSafariViewController](https://developer.apple.com/documentation/safariservices/sfsafariviewcontroller?language=objc)

iOS と macOS の場合:

- [WKWebView](https://developer.apple.com/documentation/webkit/wkwebview?language=objc).

macOS 用の MSAL では、`WKWebView` のみがサポートされます。

## <a name="system-browsers"></a>システム ブラウザー

iOS の場合、`ASWebAuthenticationSession`、`SFAuthenticationSession`、`SFSafariViewController` がシステムブラウザーと見なされます。 一般に、システム ブラウザーは、Cookie およびその他の Web サイト データを Safari ブラウザー アプリケーションと共有します。

既定では、MSAL では iOS のバージョンが動的に検出され、そのバージョンで使用可能な推奨システムブラウザーが選択されます。 iOS 12 以降では、`ASWebAuthenticationSession` になります。 

| Version | Web ブラウザー |
|:-------------:|:-------------:|
| iOS 12 以降 | ASWebAuthenticationSession |
| iOS 11 | SFAuthenticationSession |
| iOS 10 | SFSafariViewController |

開発者は、MSAL アプリ用に別のシステム ブラウザーを選択することもできます。

- `SFAuthenticationSession` は、`ASWebAuthenticationSession` の iOS 11 バージョンです。
- `SFSafariViewController` は、より汎用的なものであり、Web を閲覧するためのインターフェイスを提供し、ログインの目的でも使用できます。 iOS 9 と 10 では Cookie およびその他の Web サイト データが Safari と共有されますが、iOS 11 以降では共有されません。

## <a name="in-app-browser"></a>アプリ内ブラウザー

[WKWebView](https://developer.apple.com/documentation/webkit/wkwebview) は、Web コンテンツを表示するアプリ内ブラウザーです。 Cookie や Web サイト データは、別の **WKWebView** インスタンスや Safari ブラウザーと共有されません。 WKWebView は、iOS と macOS の両方で使用できるクロスプラットフォームのブラウザーです。

## <a name="cookie-sharing-and-single-sign-on-sso-implications"></a>Cookie 共有とシングル サインオン (SSO) の影響

使用するブラウザーは、Cookie の共有方法によって SSO エクスペリエンスに影響を与えます。 次の表は、ブラウザーごとの SSO エクスペリエンスをまとめたものです。

| テクノロジ    | ブラウザーの種類  | iOS での使用可能性 | macOS での使用可能性 | Cookie およびその他のデータの共有  | MSAL の使用可能性 | SSO |
|:-------------:|:-------------:|:-------------:|:-------------:|:-------------:|:-------------:|-------------:|
| [ASWebAuthenticationSession](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession) | システム | iOS12 以降 | macOS 10.15 以降 | はい | iOS のみ | w/ Safari インスタンス
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

例:

Objective-C
```objc
UIViewController *myParentController = ...;
WKWebView *myCustomWebView = ...;
MSALWebviewParameters *webViewParameters = [[MSALWebviewParameters alloc] initWithParentViewController:myParentController];
webViewParameters.webviewType = MSALWebviewTypeWKWebView;
webViewParameters.customWebview = myCustomWebView;
MSALInteractiveTokenParameters *interactiveParameters = [[MSALInteractiveTokenParameters alloc] initWithScopes:@[@"myscope"] webviewParameters:webViewParameters];
    
[app acquireTokenWithParameters:interactiveParameters completionBlock:completionBlock];
```
Swift
```swift
let myParentController: UIViewController = ...
let myCustomWebView: WKWebView = ...
let webViewParameters = MSALWebviewParameters(parentViewController: myParentController)
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

### <a name="options"></a>オプション

すべての MSAL でサポートされる Web ブラウザーの種類は、[MSALWebviewType 列挙型](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALDefinitions.h#L47)で宣言されます。

```objc
typedef NS_ENUM(NSInteger, MSALWebviewType)
{
#if TARGET_OS_IPHONE
    // For iOS 11 and up, uses AuthenticationSession (ASWebAuthenticationSession
    // or SFAuthenticationSession).
    // For older versions, with AuthenticationSession not being available, uses
    // SafariViewController.
    MSALWebviewTypeDefault,
    
    // Use SFAuthenticationSession/ASWebAuthenticationSession
    MSALWebviewTypeAuthenticationSession,
    
    // Use SFSafariViewController for all versions.
    MSALWebviewTypeSafariViewController,
    
#endif
    // Use WKWebView
    MSALWebviewTypeWKWebView,
};
```

## <a name="next-steps"></a>次の手順

[認証フローとアプリケーションのシナリオ](authentication-flows-app-scenarios.md)の詳細を確認します
