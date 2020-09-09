---
title: Xamarin iOS に関する考慮事項 (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Xamarin iOS を Microsoft Authentication Library for .NET (MSAL.NET) と共に使用する際の考慮事項について説明します。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 73303d64d7ebeb94290819226e5e5944f95f6e42
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2020
ms.locfileid: "88165704"
---
# <a name="considerations-for-using-xamarin-ios-with-msalnet"></a>MSAL.NET と共に Xamarin iOS を使用する際の考慮事項
Xamarin iOS で Microsoft Authentication Library for .NET (MSAL.NET) を使用する場合は、次のことを行う必要があります。 

- `AppDelegate` での `OpenUrl` 関数のオーバーライドと実装。
- キーチェーン グループの有効化。
- トークン キャッシュ共有の有効化。
- キーチェーン アクセスの有効化。
- iOS 12 と認証に関する既知の問題の理解。

## <a name="implement-openurl"></a>OpenUrl の実装

`FormsApplicationDelegate` 派生クラスの `OpenUrl` メソッドをオーバーライドし、`AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs` を呼び出します。 次に例を示します。

```csharp
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url);
    return true;
}
```

また、次のタスクを実行します。 
* URL スキームを定義する。
* アプリが別のアプリを呼び出すためのアクセス許可を要求する。
* リダイレクト URL 用の特定の形式を用意する。
* [Azure portal](https://portal.azure.com) でリダイレクト URI を登録する。

### <a name="enable-keychain-access"></a>キーチェーン アクセスの有効化

キーチェーン アクセスを有効にするには、アプリケーションにキーチェーン アクセス グループがあることを確認します。 `WithIosKeychainSecurityGroup()` API を使用してアプリケーションを作成するとき、キーチェーン アクセス グループを設定できます。

キャッシュとシングル サインオン (SSO) を活用するには、ご利用のすべてのアプリケーションで、キーチェーン アクセス グループを同じ値に設定する必要があります。

このセットアップの例では、MSAL 4.x を使用します。
```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
     .WithIosKeychainSecurityGroup("com.microsoft.adalcache")
     .Build();
```

また、`Entitlements.plist` ファイルでキーチェーン アクセスを有効にします。 次のアクセス グループまたは独自のアクセス グループを使用します。

```xml
<dict>
  <key>keychain-access-groups</key>
  <array>
    <string>$(AppIdentifierPrefix)com.microsoft.adalcache</string>
  </array>
</dict>
```

`WithIosKeychainSecurityGroup()` API を使用すると、MSAL によって、アプリケーションの *チーム ID* (`AppIdentifierPrefix`) の最後にセキュリティ グループが自動的に追加されます。 MSAL によってセキュリティ グループが追加されるのは、Xcode でアプリケーションをビルドすると、同じ動作が実行されるためです。 そのため、`Entitlements.plist` ファイル内のエンタイトルメントには、キーチェーン アクセス グループの前に `$(AppIdentifierPrefix)` を含める必要があります。

詳細については、[iOS エンタイトルメントのドキュメント](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps)を参照してください。 

### <a name="enable-token-cache-sharing-across-ios-applications"></a>iOS アプリケーション間でのトークン キャッシュ共有の有効化

MSAL 2.x 以降では、複数のアプリケーション間でトークン キャッシュを保持するキーチェーン アクセス グループを指定できます。 この設定を使用すると、同じキーチェーン アクセス グループを持つ複数のアプリケーション間でトークン キャッシュを共有できます。 トークン キャッシュは、[ADAL.NET](https://aka.ms/adal-net) アプリケーション、MSAL.NET Xamarin.iOS アプリケーション、[ADAL.objc](https://github.com/AzureAD/azure-activedirectory-library-for-objc) または [MSAL.objc](https://github.com/AzureAD/microsoft-authentication-library-for-objc) で開発されたネイティブ iOS アプリケーションの間で共有できます。

トークン キャッシュを共有すると、同じキーチェーン アクセス グループを使用するすべてのアプリケーション間でのシングル サインオン (SSO) が可能になります。

このキャッシュ共有を有効にするには、`WithIosKeychainSecurityGroup()` メソッドを使用して、同じキャッシュを共有するすべてのアプリケーションでキーチェーン アクセス グループを同じ値に設定します。 この記事の最初のコード例では、メソッドの使用方法を示しています。

この記事の前半では、`WithIosKeychainSecurityGroup()` API を使用すると、MSAL によって `$(AppIdentifierPrefix)` が追加されることについて説明しました。 MSAL によってこの要素が追加されるのは、チーム ID `AppIdentifierPrefix` によって、同じパブリッシャーによって作成されるアプリケーションのみがキーチェーン アクセスを共有できるようになるためです。

> [!NOTE]
> `KeychainSecurityGroup` プロパティは非推奨となっています。
> 
> MSAL 2.x 以降では、開発者は `KeychainSecurityGroup` プロパティを使用するときに `TeamId` プレフィックスを使用することを余儀なくされていました。 ただし、MSAL 2.7. x 以降では、新しい `iOSKeychainSecurityGroup` プロパティを使用すると、MSAL は実行時に `TeamId` プレフィックスを解決します。 このプロパティを使用する場合は、値に `TeamId` プレフィックスを含めないでください。 このプレフィックスは必要ありません。
>
> `KeychainSecurityGroup` プロパティは互換性のために残されているため、`iOSKeychainSecurityGroup` プロパティを使用してください。

### <a name="use-microsoft-authenticator"></a>Microsoft Authenticator を使用する

アプリケーションで Microsoft Authenticator をブローカーとして使用すると、以下を有効にすることができます。

- **SSO**:SSO を有効にすると、ユーザーはアプリケーションごとにサインインする必要がなくなります。
- **デバイスの識別**:デバイスの識別を使用して、デバイス証明書にアクセスして認証します。 この証明書は、デバイスがワークプレースに参加したときに作成されます。 テナント管理者がデバイスに関連する条件付きアクセスを有効にすると、アプリケーションの準備が整います。
- **アプリケーションの識別の検証**:アプリケーションはブローカーを呼び出すとき、自身のリダイレクト URL を渡します。 ブローカーはリダイレクト URL を検証します。

ブローカーを有効にする方法の詳細については、「[Xamarin iOS および Android アプリケーションで Microsoft Authenticator または Microsoft Intune ポータル サイトを使用する](msal-net-use-brokers-with-xamarin-apps.md)」を参照してください。

## <a name="known-issues-with-ios-12-and-authentication"></a>iOS 12 と認証に関する既知の問題
Microsoft では、iOS 12 と一部の種類の認証との間の非互換性に関する[セキュリティ アドバイザリ](https://github.com/aspnet/AspNetCore/issues/4647)をリリースしました。 その非互換性により、ソーシャル、WSFed、OIDC のサインインが中断されます。セキュリティ アドバイザリは、開発者が ASP.NET セキュリティ制限をアプリケーションから除去してアプリケーションに iOS 12 との互換性を持たせる方法を理解するのに役立ちます。  

Xamarin iOS 上での MSAL.NET アプリケーション開発時、iOS 12 から Web サイトにサインインしようとすると無限ループが発生することがあります。 この動作は、この [ADAL の問題](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/issues/1329)に似ています。 

iOS 12 Safari を使用した ASP.NET Core OIDC 認証でも中断が発生することがあります。 詳細については、こちらの [WebKit の問題](https://bugs.webkit.org/show_bug.cgi?id=188165)のページを参照してください。

## <a name="next-steps"></a>次のステップ

Xamarin iOS のプロパティについては、次に示すサンプルの README.md ファイルの「[iOS Specific Considerations](https://github.com/Azure-Samples/active-directory-xamarin-native-v2/tree/master/1-Basic#ios-specific-considerations)」(iOS に固有の考慮事項) の段落を参照してください。

サンプル | プラットフォーム | 説明
------ | -------- | -----------
[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin iOS、Android、ユニバーサル Windows プラットフォーム (UWP) | MSAL を使用して、Azure AD 2.0 エンドポイントを介して Microsoft の個人用アカウントおよび Azure AD を認証する方法を示す簡単な Xamarin Forms アプリ。 このアプリは、結果として得られたトークンを使用して Microsoft Graph にアクセスする方法も示しています。

<!--- https://github.com/Azure-Samples/active-directory-xamarin-native-v2/blob/master/ReadmeFiles/Topology.png -->