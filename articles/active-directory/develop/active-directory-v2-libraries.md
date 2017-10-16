---
title: "Azure Active Directory v2.0 認証ライブラリ | Microsoft Docs"
description: "Azure Active Directory v2.0 エンドポイントと互換性のあるクライアント ライブラリおよびサーバーのミドルウェア ライブラリ、関連するライブラリ、ソース、サンプル リンク。"
services: active-directory
documentationcenter: 
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 19cec615-e51f-4141-9f8c-aaf38ff9f746
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/22/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: 50a1cc0bf9e00cf5b866b88b3e88c62b06a2376b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-v20-authentication-libraries"></a>Azure Active Directory v2.0 認証ライブラリ
[Azure Active Directory (Azure AD) v2.0 エンドポイント](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-v2-compare)は、業界標準の OAuth 2.0 および OpenID Connect 1.0 プロトコルをサポートします。 v2.0 エンドポイントでは、Microsoft およびその他の組織のさまざまなライブラリを使用することができます。

v2.0 エンドポイントを使用するアプリケーションを構築する場合は、[Microsoft が使用する][Microsoft-SDL]ような Microsoft Security Development Lifecycle (SDL) の手法を採用するプロトコル ドメインの専門家が記述したライブラリを使用することをお勧めします。 プロトコルのサポートをハンド コードする場合は、SDL 手法に従い、各プロトコルの標準仕様におけるセキュリティ上の考慮事項によく注意することをお勧めします。

> [!NOTE]
> Azure AD v1.0 ライブラリ (ADAL) をお探しの場合は、 [ADAL ライブラリ ガイド](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-authentication-libraries)を確認してください。 
> 
> 

## <a name="types-of-libraries"></a>ライブラリの種類
Azure AD v2.0 エンドポイントは、次の 2 種類のライブラリに対応します。

* **クライアント ライブラリ**。 ネイティブ クライアントとサーバーは、クライアント ライブラリを使用して、Microsoft Graph などのリソースを呼び出すためのアクセス トークンを取得します。
* **サーバー ミドルウェア ライブラリ**。 Web アプリは、ユーザーのサインインにサーバー ミドルウェア ライブラリを使用します。 Web API は、サーバー ミドルウェア ライブラリを使用して、ネイティブ クライアントまたは他のサーバーによって送信されるトークンを検証します。

## <a name="library-support"></a>ライブラリのサポート
v2.0 エンドポイントを使用する場合は標準に準拠した任意のライブラリを選択できるため、サポートの入手先を知っておく必要があります。 ライブラリ コードの問題と機能に関する要望については、ライブラリ所有者にお問い合わせください。 サービス側のプロトコルの実装の問題と機能に関する要望については、Microsoft にお問い合わせください。

ライブラリには、次の 2 種類のサポート カテゴリがあります:

* **Microsoft によるサポート**。 Microsoft は、これらのライブラリの修正プログラムを提供するほか、これらのライブラリに対する SDL の適正評価を行っています。
* **互換性**。 Microsoft は基本的なシナリオでこれらのライブラリをテストし、v2.0 エンドポイントで動作することを確認しました。 Microsoft はこれらのライブラリの修正プログラムを提供していません。また、Microsoft はこれらのライブラリのレビューも実施していません。 問題や機能に関する要望については、ライブラリのオープン ソース プロジェクトにお問い合わせください。

v2.0 エンドポイントで動作するライブラリの一覧については、この記事の次のセクションを参照してください。


## <a name="microsoft-supported-client-libraries"></a>Microsoft がサポートするクライアント ライブラリ

> [!IMPORTANT]
> MSAL プレビュー ライブラリは、運用環境での使用に適しています。 これらのライブラリには、現在の運用ライブラリ (ADAL) と同様の運用レベル サポートが提供されています。 プレビュー中に、それらのライブラリの MSAL API、内部キャッシュ フォーマット、およびその他のメカニズムが予告なく変更されることがあり、それらをバグ フィックスや機能改善とともに取り込むことが必要になります。 これは、アプリケーションに影響を与える場合があります。 たとえば、キャッシュ フォーマットの変更は、再度サインインが必要になるなどユーザーに影響を与える場合があります。 API の変更では、コードの更新が必要な場合があります。 リリースの一般提供が開始されると、プレビュー バージョンのライブラリを使用して作成されたアプリケーションが機能しなくなるため、6 か月以内に一般提供バージョンに更新していただく必要があります。

| プラットフォーム | ライブラリ | ダウンロード | ソース コード | サンプル | リファレンス
| --- | --- | --- | --- | --- | --- |
| .NET クライアント、Windows ストア、UWP、Xamarin iOS、Android | MSAL .NET (プレビュー) |[NuGet](https://www.nuget.org/packages/Microsoft.Identity.Client) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | [デスクトップ アプリ](guidedsetups/active-directory-mobileanddesktopapp-windowsdesktop-intro.md) |  |
| JavaScript | MSAL.js (プレビュー) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js) | [シングル ページ アプリケーション](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) |  |
| iOS、macOS | MSAL (プレビュー) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) | [iOS アプリ](https://github.com/Azure-Samples/active-directory-msal-ios-swift) |  |
| Android | MSAL (プレビュー) | [中央リポジトリ](https://repo1.maven.org/maven2/com/microsoft/identity/client/msal/) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-android) | [Android アプリ](guidedsetups/active-directory-mobileanddesktopapp-android-intro.md) | [JavaDocs](http://javadoc.io/doc/com.microsoft.identity.client/msal) |

## <a name="microsoft-supported-server-middleware-libraries"></a>Microsoft がサポートするサーバー ミドルウェア ライブラリ

| プラットフォーム | ライブラリ | ダウンロード | ソース コード | サンプル | リファレンス
| --- | --- | --- | --- | --- | --- |
| .NET 4.x | OWIN OpenID Connect ミドルウェア |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect) |[CodePlex](http://katanaproject.codeplex.com) |[MVC アプリ](guidedsetups/active-directory-serversidewebapp-aspnetwebappowin-intro.md) | |
| .NET 4.x | AzureAD 用 OWIN OAuth Bearer ミドルウェア |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.ActiveDirectory/) |[CodePlex](http://katanaproject.codeplex.com) |  | |
| .NET 4.x | JWT Handler for .NET 4.5 | [NuGet](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt/4.0.4.403061554) | [GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| .NET Core | ASP.NET OpenID Connect ミドルウェア |[Microsoft.AspNetCore.Authentication.OpenIdConnect (NuGet)][ServerLib-NetCore-Owin-Oidc-Lib] |[ASP.Net のセキュリティ (GitHub)][ServerLib-NetCore-Owin-Oidc-Repo] |[MVC アプリ](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore-v2) |
| .NET Core | ASP.NET OAuth Bearer ミドルウェア |[Microsoft.AspNetCore.Authentication.OAuth (NuGet)][ServerLib-NetCore-Owin-Oauth-Lib] |[ASP.Net のセキュリティ (GitHub)][ServerLib-NetCore-Owin-Oauth-Repo] |  |
| .NET Core | JWT Handler for .NET Core  |[NuGet](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt) |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| Node.js |Azure AD Passport |[npm](https://www.npmjs.com/package/passport-azure-ad) |[GitHub](https://github.com/AzureAD/passport-azure-ad) | [Web アプリ](active-directory-v2-devquickstarts-node-web.md)| |

## <a name="compatible-client-libraries"></a>互換性のあるクライアント ライブラリ
| プラットフォーム | ライブラリ名 | テスト済みのバージョン | ソース コード | サンプル |
|:---:|:---:|:---:|:---:|:---:|
| Android |[OIDCAndroidLib](https://github.com/kalemontes/OIDCAndroidLib/wiki) |0.2.1 |[OIDCAndroidLib](https://github.com/kalemontes/OIDCAndroidLib) |[ネイティブ アプリのサンプル](active-directory-v2-devquickstarts-android.md) |
| iOS |[NXOAuth2Client](https://github.com/nxtbgthng/OAuth2Client) |1.2.8 |[NXOAuth2Client](https://github.com/nxtbgthng/OAuth2Client) |[ネイティブ アプリのサンプル](active-directory-v2-devquickstarts-ios.md) |
| JavaScript |[Hello.js](https://adodson.com/hello.js/) |1.13.5 |[Hello.js](https://github.com/MrSwitch/hello.js) |[SPA](https://github.com/Azure-Samples/active-directory-javascript-graphapi-web-v2) |

## <a name="compatible-server-middleware-libraries"></a>互換性のあるサーバー ミドルウェア ライブラリ
| プラットフォーム | ライブラリ名 | テスト済みのバージョン | ソース コード | サンプル |
|:---:|:---:|:---:|:---:|:---:|
| Java | [Scribe Java scribejava](https://github.com/scribejava/scribejava) | [バージョン 3.2.0](https://github.com/scribejava/scribejava/releases/tag/scribejava-3.2.0) | [ScribeJava](https://github.com/scribejava/scribejava/archive/scribejava-3.2.0.zip) | |
| PHP | [PHP League oauth2-client](https://github.com/thephpleague/oauth2-client) | [バージョン 1.4.2](https://github.com/thephpleague/oauth2-client/releases/tag/1.4.2) | [oauth2-client](https://github.com/thephpleague/oauth2-client/archive/1.4.2.zip) | |
| Python-Flask |[Flask-OAuthlib](https://github.com/lepture/flask-oauthlib) |0.9.3 |[Flask-OAuthlib](https://github.com/lepture/flask-oauthlib) |[Web アプリ](https://github.com/Azure-Samples/active-directory-python-flask-graphapi-web-v2) |
| Ruby |[OmniAuth](https://github.com/omniauth/omniauth/wiki) |omniauth:1.3.1</br>omniauth-oauth2:1.4.0 |[OmniAuth](https://github.com/omniauth/omniauth)</br>[OmniAuth OAuth2](https://github.com/intridea/omniauth-oauth2) |  |

## <a name="related-content"></a>関連コンテンツ
Azure AD v2.0 エンドポイントの詳細については、[Azure AD アプリ モデル v2.0 の概要][AAD-App-Model-V2-Overview]に関するページを参照してください。

<!--Image references-->

<!--Reference style links -->
[AAD-App-Model-V2-Overview]: ../active-directory-appmodel-v2-overview.md
[ClientLib-NET-Lib]: http://www.nuget.org/packages/Microsoft.Identity.Client
[ClientLib-NET-Repo]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet
[ClientLib-NET-Sample]: active-directory-v2-devquickstarts-wpf.md
[ClientLib-Node-Lib]: https://www.npmjs.com/package/passport-azure-ad
[ClientLib-Node-Repo]: https://github.com/AzureAD/passport-azure-ad
[ClientLib-Node-Sample]:/
[ClientLib-Iosmac-Lib]:/
[ClientLib-Iosmac-Repo]:/
[ClientLib-Iosmac-Sample]:/
[ClientLib-Android-Lib]:/
[ClientLib-Android-Repo]:/
[ClientLib-Android-Sample]:/
[ClientLib-Js-Lib]:/
[ClientLib-Js-Repo]:/
[ClientLib-Js-Sample]:/

[Microsoft-SDL]: http://www.microsoft.com/sdl/default.aspx
[ServerLib-Net4-Owin-Oidc-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/
[ServerLib-Net4-Owin-Oidc-Repo]: http://katanaproject.codeplex.com/
[ServerLib-Net4-Owin-Oidc-Sample]: active-directory-v2-devquickstarts-dotnet-web.md
[ServerLib-Net4-Owin-Oauth-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OAuth/
[ServerLib-Net4-Owin-Oauth-Repo]: http://katanaproject.codeplex.com/
[ServerLib-Net4-Owin-Oauth-Sample]: https://azure.microsoft.com/en-us/documentation/articles/active-directory-v2-devquickstarts-dotnet-api/
[ServerLib-Net-Jwt-Lib]: https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt
[ServerLib-Net-Jwt-Repo]: https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet
[ServerLib-Net-Jwt-Sample]:/
[ServerLib-NetCore-Owin-Oidc-Lib]: https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.OpenIdConnect/
[ServerLib-NetCore-Owin-Oidc-Repo]: https://github.com/aspnet/Security
[ServerLib-NetCore-Owin-Oidc-Sample]: https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore-v2
[ServerLib-NetCore-Owin-Oauth-Lib]: https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.OAuth/
[ServerLib-NetCore-Owin-Oauth-Repo]: https://github.com/aspnet/Security
[ServerLib-NetCore-Owin-Oauth-Sample]:/
[ServerLib-Node-Lib]: https://www.npmjs.com/package/passport-azure-ad
[ServerLib-Node-Repo]: https://github.com/AzureAD/passport-azure-ad/
[ServerLib-Node-Sample]: https://azure.microsoft.com/en-us/documentation/articles/active-directory-v2-devquickstarts-node-web/
