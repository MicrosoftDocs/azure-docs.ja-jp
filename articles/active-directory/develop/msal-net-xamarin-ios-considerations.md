---
title: Xamarin iOS に関する考慮事項 (Microsoft Authentication Library for .NET) | Azure
description: Xamarin iOS を Microsoft Authentication Library for .NET (MSAL.NET) と共に使用する場合の固有の考慮事項について説明します。
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
ms.date: 07/16/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b7bb4aab4c217e20245a1f6ee9b2910a4558acad
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2019
ms.locfileid: "68278225"
---
# <a name="xamarin-ios-specific-considerations-with-msalnet"></a>MSAL.NET を使用する Xamarin iOS に固有の考慮事項
Xamarin iOS には、MSAL.NET を使用する場合に考慮する必要がある考慮事項がいくつかあります。

- [iOS 12 と認証に関する既知の問題](#known-issues-with-ios-12-and-authentication)
- [`AppDelegate` での `OpenUrl` 関数のオーバーライドと実装](#implement-openurl)
- [キーチェーン グループの有効化](#enable-keychain-access)
- [トークン キャッシュ共有の有効化](#enable-token-cache-sharing-across-ios-applications)
- [キーチェーン アクセスの有効化](#enable-keychain-access)

## <a name="known-issues-with-ios-12-and-authentication"></a>iOS 12 と認証に関する既知の問題
Microsoft では、iOS12 と一部の種類の認証との間の非互換性について情報を提供する[セキュリティ アドバイザリ](https://github.com/aspnet/AspNetCore/issues/4647)をリリースしました。 その非互換性により、ソーシャル、WSFed、OIDC のログインが中断されます。 このアドバイザリでは、iOS12 と互換性を持たせる目的で ASP.NET によってアプリケーションに追加された現在のセキュリティ制限を取り除くために、開発者ができることについてのガイダンスも提供しています。  

Xamarin iOS 上での MSAL.NET アプリケーション開発時、iOS 12 から Web サイトにサインインしようとすると無限ループが発生することがあります (この [ADAL の問題](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/issues/1329)に似ています)。 

この [WebKit の問題](https://bugs.webkit.org/show_bug.cgi?id=188165)で説明されているように、iOS 12 Safari を使用した ASP.NET Core OIDC 認証でも中断が発生することがあります。

## <a name="implement-openurl"></a>OpenUrl の実装

まず、`FormsApplicationDelegate` 派生クラスの `OpenUrl` メソッドをオーバーライドし、`AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs` を呼び出す必要があります。

```CSharp
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url);
    return true;
}
```

また、URL スキームを定義し、別のアプリを呼び出すために自分のアプリ用にアクセス許可を要求すること、リダイレクト URL 用の特定の形式を作成し、このリダイレクト URL を [Azure portal](https://portal.azure.com) 上で登録することも必要になります。

### <a name="enable-keychain-access"></a>キーチェーン アクセスの有効化

キーチェーン アクセスを有効にするには、アプリケーションにキーチェーン アクセス グループが必要です。
次に示すように、アプリケーションの作成時に `WithIosKeychainSecurityGroup()` API を使用してキーチェーン アクセス グループを設定できます。

シングル サインオンを有効にするには、すべてのアプリケーションで `PublicClientApplication.iOSKeychainSecurityGroup` プロパティを同じ値に設定する必要があります。

MSAL v3.x を使用したこの例は次のようになります。
```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
     .WithIosKeychainSecurityGroup("com.microsoft.msalrocks")
     .Build();
```

entitlements.plist を更新して、次の XML フラグメントのようにする必要があります。

この変更は、`Entitlements.plist` ファイル内でキーチェーン アクセスを有効にすることに "*加えて*"、以下のまたはご自身のアクセス グループを使用して行います。

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
  <key>keychain-access-groups</key>
  <array>
    <string>$(AppIdentifierPrefix)com.microsoft.msalrocks</string>
  </array>
</dict>
</plist>
```

MSAL v4.x を使用したこの例は次のようになります。

```csharp
PublicClientApplication.iOSKeychainSecurityGroup = "com.microsoft.msalrocks";
```

`WithIosKeychainSecurityGroup()` API を使用する場合、MSAL はアプリケーションの "チーム ID" (AppIdentifierPrefix) の末尾にセキュリティ グループを自動的に追加しますが、これは、xcode を使用してアプリケーションをビルドする場合にも同じことを行うからです。 [詳細については iOS エンタイトルメントに関するドキュメントを参照してください](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps)。 そのため、エンタイトルメントを更新して、entitlements.plist でキーチェーン アクセス グループの前に $(AppIdentifierPrefix) を含める必要があります。

### <a name="enable-token-cache-sharing-across-ios-applications"></a>iOS アプリケーション間でのトークン キャッシュ共有の有効化

MSAL 2.x から、複数のアプリケーション間でトークン キャッシュを保持するために使用するキーチェーン アクセス グループを指定できます。 この設定により、[ADAL.NET](https://aka.ms/adal-net) で開発されたアプリケーション、MSAL.NET Xamarin.iOS アプリケーション、[ADAL.objc](https://github.com/AzureAD/azure-activedirectory-library-for-objc) または [MSAL.objc](https://github.com/AzureAD/microsoft-authentication-library-for-objc) で開発されたネイティブ iOS アプリケーションを含め、同じキーチェーン アクセス グループを持つ複数のアプリケーション間でトークン キャッシュを共有できます。

トークン キャッシュを共有すると、同じキーチェーン アクセス グループを使用するすべてのアプリケーション間でのシングル サインオンが可能になります。

このキャッシュ共有を有効にするには、'WithIosKeychainSecurityGroup()' メソッドを使用して、前の例で示しているように、同じキャッシュを共有するすべてのアプリケーションでキーチェーン アクセス グループを同じ値に設定する必要があります。

前に説明したように、`WithIosKeychainSecurityGroup()` API を使用するときは常に、MSAL によって $(AppIdentifierPrefix) が追加されていました。 これは、同じパブリッシャー製のアプリケーションのみがキーチェーン アクセスを共有できることを保証するために AppIdentifierPrefix または "チーム ID" が使用されるからです。

#### <a name="note-keychainsecuritygroup-property-deprecated"></a>注:KeychainSecurityGroup プロパティは非推奨

以前は、MSAL 2.x から、開発者は `KeychainSecurityGroup` プロパティを使用するときに TeamId プレフィックスを使用することを余儀なくされていました。

MSAL 2.7.x からは、新しい `iOSKeychainSecurityGroup` プロパティを使用するとき、MSAL が実行時に TeamId プレフィックスを解決します。 このプロパティを使用するときは、値に TeamId プレフィックスを含めないでください。

以前の `KeychainSecurityGroup` プロパティは古くなっているため、新しい `iOSKeychainSecurityGroup` プロパティを使用します。これを使用すると開発者は TeamId を指定する必要がありません。

### <a name="sample-illustrating-xamarin-ios-specific-properties"></a>Xamarin iOS 固有のプロパティを示すサンプル

詳細については、次に示すサンプルの readme.md ファイルの「[iOS Specific Considerations](https://github.com/azure-samples/active-directory-xamarin-native-v2#ios-specific-considerations)」(iOS に固有の考慮事項) の段落を参照してください。

サンプル | プラットフォーム | 説明
------ | -------- | -----------
[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin iOS、Android、UWP | Azure AD V2.0 エンドポイント経由で MSA と Azure AD を認証し、結果のトークンで Microsoft Graph にアクセスするための MSAL の使用方法を示す、単純な Xamarin Forms アプリ。

<!--- https://github.com/Azure-Samples/active-directory-xamarin-native-v2/blob/master/ReadmeFiles/Topology.png -->
