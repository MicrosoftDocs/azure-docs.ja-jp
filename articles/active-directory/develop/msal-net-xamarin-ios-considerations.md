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
ms.date: 09/09/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 62eb4ab9eb6e4b0e7be0f7aadae1173950d21615
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98064489"
---
# <a name="considerations-for-using-xamarin-ios-with-msalnet"></a>MSAL.NET と共に Xamarin iOS を使用する際の考慮事項

Xamarin iOS で Microsoft Authentication Library for .NET (MSAL.NET) を使用する場合は、次のことを行う必要があります。

- `AppDelegate` での `OpenUrl` 関数のオーバーライドと実装。
- キーチェーン グループの有効化。
- トークン キャッシュ共有の有効化。
- キーチェーン アクセスの有効化。
- iOS 12 および iOS 13 と認証に関する既知の問題の理解。

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

* リダイレクト URI スキームを定義する。
* アプリが別のアプリを呼び出すためのアクセス許可を要求する。
* リダイレクト URI 用の特定の形式を用意する。
* Azure portal で[リダイレクト URI を登録する](quickstart-register-app.md#add-a-redirect-uri)。

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
> `KeychainSecurityGroup` プロパティは非推奨となっています。 代わりに、`iOSKeychainSecurityGroup` プロパティを使用してください。 `iOSKeychainSecurityGroup` を使用する場合、`TeamId` プレフィックスは必要ありません。

### <a name="use-microsoft-authenticator"></a>Microsoft Authenticator を使用する

アプリケーションで Microsoft Authenticator をブローカーとして使用すると、以下を有効にすることができます。

- **SSO**:SSO を有効にすると、ユーザーはアプリケーションごとにサインインする必要がなくなります。
- **デバイスの識別**:デバイスの識別を使用して、デバイス証明書にアクセスして認証します。 この証明書は、デバイスがワークプレースに参加したときに作成されます。 テナント管理者がデバイスに関連する条件付きアクセスを有効にすると、アプリケーションの準備が整います。
- **アプリケーション ID の検証**: アプリケーションはブローカーを呼び出すとき、自身のリダイレクト URL を渡します。 ブローカーはリダイレクト URL を検証します。

ブローカーを有効にする方法の詳細については、「[Xamarin iOS および Android アプリケーションで Microsoft Authenticator または Microsoft Intune ポータル サイトを使用する](msal-net-use-brokers-with-xamarin-apps.md)」を参照してください。

## <a name="known-issues-with-ios-12-and-authentication"></a>iOS 12 と認証に関する既知の問題

Microsoft では、iOS 12 と一部の種類の認証との間の非互換性に関する[セキュリティ アドバイザリ](https://github.com/aspnet/AspNetCore/issues/4647)をリリースしました。 その非互換性により、ソーシャル、WSFed、OIDC のサインインが中断されます。セキュリティ アドバイザリは、ASP.NET セキュリティ制限をアプリケーションから除去してアプリケーションに iOS 12 との互換性を持たせる方法を理解するのに役立ちます。

Xamarin iOS 上での MSAL.NET アプリケーション開発時、iOS 12 から Web サイトにサインインしようとすると無限ループが発生することがあります。 このような動作は、GitHub 上の次の ADAL に関する問題と似ています。「[iOS 12 から Web サイトにログインしようとすると無限ループが発生する #1329](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/issues/1329)」。

iOS 12 Safari を使用した ASP.NET Core OIDC 認証でも中断が発生することがあります。 詳細については、こちらの [WebKit の問題](https://bugs.webkit.org/show_bug.cgi?id=188165)のページを参照してください。

## <a name="known-issues-with-ios-13-and-authentication"></a>iOS 13 と認証に関する既知の問題

アプリに条件付きアクセスまたは証明書認証のサポートが必要な場合は、アプリが Microsoft Authenticator ブローカー アプリと通信できるようにします。 その後のアプリケーションと Microsoft Authenticator 間の要求と応答の処理は MSAL が担当します。

iOS 13 では、Apple によって、カスタム URL スキームを介して外部アプリケーションから応答を受け取ったときにソース アプリケーションを読み取るアプリケーションの機能を削除するという重大な変更が API に加えられました。

[UIApplicationOpenURLOptionsSourceApplicationKey](https://developer.apple.com/documentation/uikit/uiapplicationopenurloptionssourceapplicationkey?language=objc) に関する Apple のドキュメントには、次のように記載されています。

> *自分のチームに属する別のアプリから送信された要求の場合、UIKit によって、このキーの値がそのアプリの ID に設定されます。元のアプリのチーム識別子が現在のアプリのチーム識別子と異なる場合、キーの値は nil になります。*

MSAL と Microsoft Authenticator アプリの間の通信を確認するために `UIApplication.SharedApplication.OpenUrl` に依存していたため、この変更は MSAL にとって重大です。

さらに、iOS 13 では、`ASWebAuthenticationSession` を使用する場合、開発者はプレゼンテーション コントローラーを用意する必要があります。

Xcode 11 を使用してビルドし、iOS ブローカーまたは `ASWebAuthenticationSession` を使用している場合は、アプリに影響があります。

このような場合は、[MSAL.NET 4.4.0 以降](https://www.nuget.org/packages/Microsoft.Identity.Client/)を使用して認証を成功させます。

### <a name="additional-requirements"></a>その他の要件

- 最新の MSAL ライブラリを使用する場合は、**Microsoft Authenticator バージョン 6.3.19 以降** がデバイスにインストールされていることを確認します。
- MSAL.NET 4.4.0 以降に更新する場合は、*Info.plist* ファイルの `LSApplicationQueriesSchemes` を更新し、`msauthv3` を追加します。

    ```xml
    <key>LSApplicationQueriesSchemes</key>
    <array>
         <string>msauthv2</string>
         <string>msauthv3</string>
    </array>
    ```

    iOS 13 をサポートするデバイス上の最新の Microsoft Authenticator アプリの存在を検出するには、`msauthv3` を *Info.plist* に追加する必要があります。

## <a name="report-an-issue"></a>問題の報告

不明な点がある場合、または MSAL.NET で見つけた問題を報告する場合は、GitHub の [AzureAD/microsoft-authentication-library-for-dotnet](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues) リポジトリでイシューを開いてください。

## <a name="next-steps"></a>次の手順

Xamarin iOS のプロパティについては、次に示すサンプルの README.md ファイルの「[iOS Specific Considerations](https://github.com/Azure-Samples/active-directory-xamarin-native-v2/tree/master/1-Basic#ios-specific-considerations)」(iOS に固有の考慮事項) の段落を参照してください。

サンプル | プラットフォーム | 説明
------ | -------- | -----------
[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin iOS、Android、ユニバーサル Windows プラットフォーム (UWP) | MSAL を使用して、Azure AD 2.0 エンドポイントを介して Microsoft の個人用アカウントおよび Azure AD を認証する方法を示す簡単な Xamarin Forms アプリ。 このアプリは、結果として得られたトークンを使用して Microsoft Graph にアクセスする方法も示しています。

<!--- https://github.com/Azure-Samples/active-directory-xamarin-native-v2/blob/master/ReadmeFiles/Topology.png -->
