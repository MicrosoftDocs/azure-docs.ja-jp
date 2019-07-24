---
title: Azure Active Directory v2.0 認証ライブラリ | Microsoft Docs
description: Azure Active Directory v2.0 エンドポイントと互換性のあるクライアント ライブラリおよびサーバーのミドルウェア ライブラリ、関連するライブラリ、ソース、サンプル リンク。
services: active-directory
documentationcenter: ''
author: negoe
manager: mtillman
editor: ''
ms.assetid: 19cec615-e51f-4141-9f8c-aaf38ff9f746
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2018
ms.author: negoe
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf5821bdfc9dfc284334b2b8b4950c3a54fb73b2
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2019
ms.locfileid: "60010746"
---
# <a name="azure-active-directory-v20-authentication-libraries"></a>Azure Active Directory v2.0 認証ライブラリ

[Azure Active Directory (Azure AD) v2.0 エンドポイント](active-directory-v2-compare.md)は、業界標準の OAuth 2.0 および OpenID Connect 1.0 プロトコルをサポートします。 Microsoft Authentication Library (MSAL) は Azure AD v2.0 エンドポイントと連動するように設計されています。 OAuth 2.0 と OpenID Connect 1.0 をサポートするオープンソース ライブラリを使用することもできます。

[Microsoft が使用する][Microsoft-SDL]ような Microsoft Security Development Lifecycle (SDL) の手法を採用するプロトコル ドメインの専門家が記述したライブラリを使用することをお勧めします。 各プロトコル向けにハンド コードする場合は、Microsoft の SDL のような手法に従い、各プロトコルの標準仕様におけるセキュリティ上の考慮事項によく注意する必要があります。

> [!NOTE]
> Azure AD v1.0 ライブラリ (ADAL) をお探しの場合は、 [ADAL ライブラリ ガイド](active-directory-authentication-libraries.md)を確認してください。

## <a name="types-of-libraries"></a>ライブラリの種類

Azure AD v2.0 エンドポイントは、次の 2 種類のライブラリに対応します。

* **クライアント ライブラリ**:ネイティブ クライアントとサーバーは、クライアント ライブラリを使用して、Microsoft Graph などのリソースを呼び出すためのアクセス トークンを取得します。
* **サーバー ミドルウェア ライブラリ**:Web アプリは、ユーザーのサインインにサーバー ミドルウェア ライブラリを使用します。 Web API は、サーバー ミドルウェア ライブラリを使用して、ネイティブ クライアントまたは他のサーバーによって送信されるトークンを検証します。

## <a name="library-support"></a>ライブラリのサポート

ライブラリには、次の 2 種類のサポート カテゴリがあります:

* **Microsoft によるサポート**:Microsoft は、これらのライブラリの修正プログラムを提供するほか、これらのライブラリに対する SDL の適正評価を行っています。
* **互換性**:Microsoft は基本的なシナリオでこれらのライブラリをテストし、v2.0 エンドポイントで動作することを確認しました。 Microsoft はこれらのライブラリの修正プログラムを提供していません。また、Microsoft はこれらのライブラリのレビューも実施していません。 問題や機能に関する要望については、ライブラリのオープン ソース プロジェクトにお問い合わせください。

v2.0 エンドポイントで動作するライブラリの一覧については、この記事の次のセクションを参照してください。

## <a name="microsoft-supported-client-libraries"></a>Microsoft がサポートするクライアント ライブラリ

クライアントの認証ライブラリは、保護された Web API を呼び出すトークンを取得するために使用します。

| プラットフォーム | ライブラリ | ダウンロード | ソース コード | サンプル | リファレンス | 概念に関するドキュメント | ロードマップ |
| --- | --- | --- | --- | --- | --- | --- | ---|
| ![JavaScript](media/sample-v2-code/logo_js.png) | MSAL.js (プレビュー) | [NPM](https://www.npmjs.com/package/msal) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angularjs/README.md) |  [シングルページ アプリ](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) |  | [wiki](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki)|
|![Angular JS](media/sample-v2-code/logo_angular.png) | MSAL Angular JS | [NPM](https://www.npmjs.com/package/@azure/msal-angularjs) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angularjs/README.md) |  |  | |
![Angular](media/sample-v2-code/logo_angular.png) | MSAL Angular (プレビュー) | [NPM](https://www.npmjs.com/package/@azure/msal-angular) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | | | |
| ![.NET Framework](media/sample-v2-code/logo_NET.png) ![UWP](media/sample-v2-code/logo_windows.png) ![Xamarin](media/sample-v2-code/logo_xamarin.png) | MSAL .NET (プレビュー) |[NuGet](https://www.nuget.org/packages/Microsoft.Identity.Client) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | [デスクトップ アプリ](guidedsetups/active-directory-mobileanddesktopapp-windowsdesktop-intro.md) | [MSAL.NET](https://docs.microsoft.com/dotnet/api/microsoft.identity.client?view=azure-dotnet-preview) |[wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki#conceptual-documentation) | [ロードマップ](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki#roadmap)
| ![iOS / Objective C または Swift](media/sample-v2-code/logo_iOS.png) | MSAL obj_c (プレビュー) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) | [iOS アプリ](https://github.com/Azure-Samples/active-directory-msal-ios-swift) |  |
|![Android/Java](media/sample-v2-code/logo_Android.png) | MSAL (プレビュー) | [中央リポジトリ](https://repo1.maven.org/maven2/com/microsoft/identity/client/msal/) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-android) | [Android アプリ](quickstart-v2-android.md) | [JavaDocs](https://javadoc.io/doc/com.microsoft.identity.client/msal) | | |

## <a name="microsoft-supported-server-middleware-libraries"></a>Microsoft がサポートするサーバー ミドルウェア ライブラリ

ミドルウェア ライブラリは、Web アプリケーションと Web API を保護するために使用します。 ASP.NET または ASP.NET Core で記述された Web アプリまたは Web API については、このミドルウェア ライブラリが ASP.NET/ASP.NET Core によって使用されます

| プラットフォーム | ライブラリ | ダウンロード | ソース コード | サンプル | リファレンス
| --- | --- | --- | --- | --- | --- |
| ![.NET](media/sample-v2-code/logo_NET.png) ![.NET Core](media/sample-v2-code/logo_NETcore.png) | ASP.NET のセキュリティ |[NuGet](https://www.nuget.org/packages/Microsoft.AspNet.Mvc/) |[ASP.NET のセキュリティ (GitHub)](https://github.com/aspnet/Security) |[MVC アプリ](quickstart-v2-aspnet-webapp.md) |[ASP.NET API リファレンス](https://docs.microsoft.com/dotnet/api/?view=aspnetcore-2.0) |
| ![.NET](media/sample-v2-code/logo_NET.png)| IdentityModel Extensions for .NET| |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | [MVC アプリ](quickstart-v2-aspnet-webapp.md) |[リファレンス](https://docs.microsoft.com/dotnet/api/overview/azure/activedirectory/client?view=azure-dotnet) |
| ![Node.js](media/sample-v2-code/logo_nodejs.png) | Azure AD Passport |[NPM](https://www.npmjs.com/package/passport-azure-ad) |[GitHub](https://github.com/AzureAD/passport-azure-ad) | [Web アプリ](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs) | |

## <a name="compatible-client-libraries"></a>互換性のあるクライアント ライブラリ

| プラットフォーム | ライブラリ名 | テスト済みのバージョン | ソース コード | サンプル |
|:---:|:---:|:---:|:---:|:---:|
|![JavaScript](media/sample-v2-code/logo_js.png)|[Hello.js](https://adodson.com/hello.js/) | 1.13.5 |[Hello.js](https://github.com/MrSwitch/hello.js) |[SPA](https://github.com/Azure-Samples/active-directory-javascript-graphapi-web-v2) |
| ![Java](media/sample-v2-code/logo_java.png) | [Scribe Java](https://github.com/scribejava/scribejava) | [バージョン 3.2.0](https://github.com/scribejava/scribejava/releases/tag/scribejava-3.2.0) | [ScribeJava](https://github.com/scribejava/scribejava/) | |
| ![Java](media/sample-v2-code/logo_java.png) | [Gluu OpenID Connect ライブラリ](https://github.com/GluuFederation/oxAuth) | [バージョン 3.0.2](https://github.com/GluuFederation/oxAuth/releases/tag/3.0.2) | [Gluu OpenID Connect ライブラリ](https://github.com/GluuFederation/oxAuth) | |
| ![Python](media/sample-v2-code/logo_python.png) | [Requests-OAuthlib](https://github.com/requests/requests-oauthlib) | [バージョン 1.2.0](https://github.com/requests/requests-oauthlib/releases/tag/v1.2.0) | [Requests-OAuthlib](https://github.com/requests/requests-oauthlib) | |
| ![Node.js](media/sample-v2-code/logo_nodejs.png) | [openid-client](https://github.com/panva/node-openid-client) | [バージョン 2.4.5](https://github.com/panva/node-openid-client/releases/tag/v2.4.5) | [openid-client](https://github.com/panva/node-openid-client) | |
| ![PHP](media/sample-v2-code/logo_php.png) | [PHP League oauth2-client](https://github.com/thephpleague/oauth2-client) | [バージョン 1.4.2](https://github.com/thephpleague/oauth2-client/releases/tag/1.4.2) | [oauth2-client](https://github.com/thephpleague/oauth2-client/) | |
| ![Ruby](media/sample-v2-code/logo_ruby.png) |[OmniAuth](https://github.com/omniauth/omniauth/wiki) |omniauth:1.3.1<br />omniauth-oauth2:1.4.0 |[OmniAuth](https://github.com/omniauth/omniauth)<br />[OmniAuth OAuth2](https://github.com/intridea/omniauth-oauth2) |  |
| ![iOS](media/sample-v2-code/logo_iOS.png) ![Android](media/sample-v2-code/logo_Android.png) | [React Native App 認証](https://github.com/FormidableLabs/react-native-app-auth) | [バージョン 4.2.0](https://github.com/FormidableLabs/react-native-app-auth/releases/tag/v4.2.0) | [React Native App 認証](https://github.com/FormidableLabs/react-native-app-auth) | |

標準に準拠しているライブラリの場合は v2.0 エンドポイントを使用できるため、サポートの入手先を知っておく必要があります。

* ライブラリ コードの問題と新機能に関する要望については、ライブラリ所有者にお問い合わせください。
* サービス側のプロトコルの実装の問題と新機能に関する要望については、Microsoft にお問い合わせください。
* プロトコルに追加機能を求める場合、[機能要求を提出](https://feedback.azure.com/forums/169401-azure-active-directory)してください。
* Azure AD v2.0 エンドポイントが OAuth 2.0 や OpenID Connect 1.0 に準拠しない問題があれば、[サポート依頼を作成](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)してください。

## <a name="related-content"></a>関連コンテンツ

Azure AD v2.0 エンドポイントの詳細については、[Azure AD アプリ モデル v2.0 の概要][AAD-App-Model-V2-Overview]に関するページを参照してください。

<!--Image references-->

<!--Reference style links -->
[AAD-App-Model-V2-Overview]: v2-overview.md
[ClientLib-NET-Lib]: https://www.nuget.org/packages/Microsoft.Identity.Client
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

[Microsoft-SDL]: https://www.microsoft.com/sdl/default.aspx
[ServerLib-Net4-Owin-Oidc-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/
[ServerLib-Net4-Owin-Oidc-Repo]: https://katanaproject.codeplex.com/
[ServerLib-Net4-Owin-Oidc-Sample]: active-directory-v2-devquickstarts-dotnet-web.md
[ServerLib-Net4-Owin-Oauth-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OAuth/
[ServerLib-Net4-Owin-Oauth-Repo]: https://katanaproject.codeplex.com/
[ServerLib-Net4-Owin-Oauth-Sample]: https://azure.microsoft.com/documentation/articles/active-directory-v2-devquickstarts-dotnet-api/
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
[ServerLib-Node-Sample]: https://azure.microsoft.com/documentation/articles/active-directory-v2-devquickstarts-node-web/
