---
title: "Azure Active Directory v2.0 認証ライブラリ | Microsoft Docs"
description: "Azure Active Directory v2.0 エンドポイントと互換性のあるクライアント ライブラリおよびサーバーのミドルウェア ライブラリ、関連するライブラリ、ソース、サンプル リンク。"
services: active-directory
documentationcenter: 
author: skwan
manager: mbaldwin
editor: 
ms.assetid: 19cec615-e51f-4141-9f8c-aaf38ff9f746
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/29/2016
ms.author: skwan;bryanla
translationtype: Human Translation
ms.sourcegitcommit: 9710381787cc1fe570443467b6980616879c503f
ms.openlocfilehash: ab5576d09e5a51cd7ae602d57693558715e12d66
ms.lasthandoff: 02/27/2017


---
# <a name="azure-active-directory-v20-authentication-libraries"></a>Azure Active Directory v2.0 認証ライブラリ
Azure Active Directory (Azure AD) v2.0 エンドポイントは、業界標準の OAuth 2.0 や OpenID Connect 1.0 プロトコルをサポートします。 v2.0 エンドポイントでは、Microsoft およびその他の組織のさまざまなライブラリを使用することができます。

v2.0 エンドポイントを使用するアプリケーションを構築する場合は、[Microsoft が使用する][Microsoft-SDL]ような Microsoft Security Development Lifecycle (SDL) の手法を採用するプロトコル ドメインの専門家が記述したライブラリを使用することをお勧めします。 プロトコルのサポートをハンド コードする場合は、SDL 手法に従い、各プロトコルの標準仕様におけるセキュリティ上の考慮事項によく注意することをお勧めします。

## <a name="types-of-libraries"></a>ライブラリの種類
Azure AD v2.0 は、次の&2; 種類のライブラリに対応します。

* **クライアント ライブラリ**。 ネイティブ クライアントとサーバーは、クライアント ライブラリを使用して、Microsoft Graph などのリソースを呼び出すためのアクセス トークンを取得します。
* **サーバー ミドルウェア ライブラリ**。 Web アプリは、ユーザーのサインインにサーバー ミドルウェア ライブラリを使用します。 Web API は、サーバー ミドルウェア ライブラリを使用して、ネイティブ クライアントまたは他のサーバーによって送信されるトークンを検証します。

## <a name="library-support"></a>ライブラリのサポート
v2.0 エンドポイントを使用する場合は標準に準拠した任意のライブラリを選択できるため、サポートの入手先を知っておく必要があります。 ライブラリ コードの問題と機能に関する要望については、ライブラリ所有者にお問い合わせください。 サービス側のプロトコルの実装の問題と機能に関する要望については、Microsoft にお問い合わせください。

ライブラリには、次の&2; 種類のサポート カテゴリがあります:

* **Microsoft によるサポート**。 Microsoft は、これらのライブラリの修正プログラムを提供するほか、これらのライブラリに対する SDL の適正評価を行っています。
* **互換性**。 Microsoft は基本的なシナリオでこれらのライブラリをテストし、v2.0 エンドポイントで動作することを確認しました。 Microsoft はこれらのライブラリの修正プログラムを提供していません。また、Microsoft はこれらのライブラリのレビューも実施していません。 問題や機能に関する要望については、ライブラリのオープン ソース プロジェクトにお問い合わせください。

v2.0 エンドポイントで動作するライブラリの一覧については、この記事の次のセクションを参照してください。

## <a name="microsoft-supported-client-libraries"></a>Microsoft がサポートするクライアント ライブラリ
| プラットフォーム | ライブラリ名 | ダウンロード | ソース コード | サンプル |
|:---:|:---:|:---:|:---:|:---:|
| .NET、Windows ストア、Xamarin |.NET 用 Microsoft 認証ライブラリ (MSAL) |[Microsoft.Identity.Client (NuGet)][ClientLib-NET-Lib] |[MSAL for .NET (GitHub)][ClientLib-NET-Repo] |[Windows デスクトップのネイティブ クライアントのサンプル][ClientLib-NET-Sample] |
| Node.js |Microsoft Azure Active Directory Passport.js プラグイン |[Passport-Azure-AD (npm)][ClientLib-Node-Lib] |[Passport-Azure-AD (GitHub)][ClientLib-Node-Repo] |近日対応予定 |

<!--- COMMENTING OUT UNTIL THEY ARE READY
| iOS, Mac | Microsoft Authentication Library (MSAL) for ObjC | In development | In development | In development |
| Android | Microsoft Authentication Library (MSAL) for Android | In development | In development | In development |
| JavaScript | Microsoft Authentication Library (MSAL) for JavaScript | In development | In development | In development |
 -->

## <a name="microsoft-supported-server-middleware-libraries"></a>Microsoft がサポートするサーバー ミドルウェア ライブラリ
| プラットフォーム | ライブラリ名 | ダウンロード | ソース コード | サンプル |
|:---:|:---:|:---:|:---:|:---:|
| .NET 4.x |OWIN の ASP.NET 用 OpenID Connect ミドルウェア |[Microsoft.Owin.Security.OpenIdConnect (NuGet)][ServerLib-Net4-Owin-Oidc-Lib] |[Katana プロジェクト (CodePlex)][ServerLib-Net4-Owin-Oidc-Repo] |[Web アプリのサンプル][ServerLib-Net4-Owin-Oidc-Sample] |
| .NET 4.x |OWIN の ASP.NET 用 OAuth Bearer ミドルウェア |[Microsoft.Owin.Security.OAuth (NuGet)][ServerLib-Net4-Owin-Oauth-Lib] |[Katana プロジェクト (CodePlex)][ServerLib-Net4-Owin-Oauth-Repo] |[Web API のサンプル][ServerLib-Net4-Owin-Oauth-Sample] |
| .NET Core |OWIN の .NET Core 用 OpenID Connect ミドルウェア |[Microsoft.AspNetCore.Authentication.OpenIdConnect (NuGet)][ServerLib-NetCore-Owin-Oidc-Lib] |[ASP.NET のセキュリティ (GitHub)][ServerLib-NetCore-Owin-Oidc-Repo] |[Web アプリのサンプル][ServerLib-NetCore-Owin-Oidc-Sample] |
| .NET Core |OWIN の .NET Core 用 OAuth Bearer ミドルウェア |[Microsoft.AspNetCore.Authentication.OAuth (NuGet)][ServerLib-NetCore-Owin-Oauth-Lib] |[ASP.Net のセキュリティ (GitHub)][ServerLib-NetCore-Owin-Oauth-Repo] |近日対応予定 |
| Node.js |Microsoft Azure Active Directory Passport.js プラグイン |[Passport-Azure-AD (npm)][ServerLib-Node-Lib] |[Passport-Azure-AD (GitHub)][ServerLib-Node-Repo] |[Web アプリのサンプル][ServerLib-Node-Sample] |

<!--- COMMENTING UNTIL SAMPLE IS AVAILABLE
| .NET 4.x, .NET Core | JSON Web Token Handler for .NET | [System.IdentityModel.Tokens.Jwt (NuGet)][ServerLib-Net-Jwt-Lib] | [Azure AD identity model extensions for .NET (GitHub)][ServerLib-Net-Jwt-Repo] | Coming soon |
--->
## <a name="compatible-client-libraries"></a>互換性のあるクライアント ライブラリ
| プラットフォーム | ライブラリ名 | テスト済みのバージョン | ソース コード | サンプル |
|:---:|:---:|:---:|:---:|:---:|
| Android |[OIDCAndroidLib](https://github.com/kalemontes/OIDCAndroidLib/wiki) |0.2.1 |[OIDCAndroidLib](https://github.com/kalemontes/OIDCAndroidLib) |[ネイティブ アプリのサンプル](active-directory-v2-devquickstarts-android.md) |
| iOS |[NXOAuth2Client](https://github.com/nxtbgthng/OAuth2Client) |1.2.8 |[NXOAuth2Client](https://github.com/nxtbgthng/OAuth2Client) |[ネイティブ アプリのサンプル](active-directory-v2-devquickstarts-ios.md) |
| Java | [Scribe Java scribejava](https://github.com/scribejava/scribejava) | [バージョン 3.2.0](https://github.com/scribejava/scribejava/releases/tag/scribejava-3.2.0) | [ScribeJava](https://github.com/scribejava/scribejava/archive/scribejava-3.2.0.zip) | 近日対応予定 |
| JavaScript |[Hello.js](https://adodson.com/hello.js/) |1.13.5 |[Hello.js](https://github.com/MrSwitch/hello.js) |[SPA](https://github.com/Azure-Samples/active-directory-javascript-graphapi-web-v2) |
| PHP | [PHP League oauth2-client](https://github.com/thephpleague/oauth2-client) | [バージョン 1.4.2](https://github.com/thephpleague/oauth2-client/releases/tag/1.4.2) | [oauth2-client](https://github.com/thephpleague/oauth2-client/archive/1.4.2.zip) | 近日対応予定 |
| Python-Flask |[Flask-OAuthlib](https://github.com/lepture/flask-oauthlib) |0.9.3 |[Flask-OAuthlib](https://github.com/lepture/flask-oauthlib) |[Web アプリ](https://github.com/Azure-Samples/active-directory-python-flask-graphapi-web-v2) |
| Ruby |[OmniAuth](https://github.com/omniauth/omniauth/wiki) |omniauth:1.3.1</br>omniauth-oauth2:1.4.0 |[OmniAuth](https://github.com/omniauth/omniauth)</br>[OmniAuth OAuth2](https://github.com/intridea/omniauth-oauth2) |近日対応予定 |

<!--- REMOVING BRANDON'S FOR NOW
|  |  |  |  |  |
| Android | [OAuth2 Client](https://github.com/wuman/android-oauth-client) |   | [OAuth2 Client](https://github.com/wuman/android-oauth-client)  | Coming soon  |
| Java | [WSO2 Identity Server](https://docs.wso2.com/display/IS500/Introducing+the+Identity+Server) | [Version 5.2.0](http://wso2.com/products/identity-server/) | [Source](https://docs.wso2.com/display/IS500/Building+from+Source) | [Samples index](https://docs.wso2.com/display/IS500/Samples)  |
| Java | [Java Gluu Server](https://gluu.org/docs/) |   | [oxAuth](https://github.com/GluuFederation/oxAuth)  | Coming soon |
| Node.js | [NPM passport-openidconnect](https://www.npmjs.com/package/passport-openidconnect) | 0.0.1  | [Passport-OpenID Connect](https://github.com/jaredhanson/passport-openidconnect) | Coming soon  |
| PHP | [OpenID Connect Basic Client](https://github.com/jumbojett/OpenID-Connect-PHP) |   | [OpenID Connect Basic Client](https://github.com/jumbojett/OpenID-Connect-PHP)  | Coming soon  |
-->

## <a name="compatible-server-middleware-libraries"></a>互換性のあるサーバー ミドルウェア ライブラリ
近日対応予定

## <a name="related-content"></a>関連コンテンツ
Azure AD v2.0 エンドポイントの詳細については、[Azure AD アプリ モデル v2.0 の概要][AAD-App-Model-V2-Overview]に関するページを参照してください。

コンテンツ改善のため、この記事の後の Disqus コメント機能を使用してご意見をお寄せください。

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

