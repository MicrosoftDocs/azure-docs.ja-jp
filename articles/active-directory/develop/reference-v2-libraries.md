---
title: Microsoft ID プラットフォームの認証ライブラリ | Azure
description: Microsoft ID プラットフォームと互換性のあるクライアント ライブラリとミドルウェアの一覧。 これらのライブラリを使用して、ユーザーのサインイン (認証) と保護された Web API アクセス (認可) のサポートをアプリケーションに追加します。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: reference
ms.workload: identity
ms.date: 01/29/2021
ms.author: marsma
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.openlocfilehash: 9549ebab687400e32bbc68a2c76cf8efc8c106c8
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/31/2021
ms.locfileid: "99218286"
---
# <a name="microsoft-identity-platform-authentication-libraries"></a>Microsoft ID プラットフォームの認証ライブラリ

次の表は、いくつかのアプリケーションの種類に対する Microsoft 認証ライブラリのサポートを示しています。 これには、ライブラリ ソース コードへのリンク、アプリのプロジェクトのパッケージを取得する場所、ライブラリでユーザーのサインイン (認証)、保護された Web API へのアクセス (認可)、またはその両方がサポートされるかどうかが含まれます。

Microsoft ID プラットフォームは、[認定 OpenID プロバイダ](https://openid.net/certification/)として OpenID Foundation に認定されています。 Microsoft Authentication Library (MSAL) や Microsoft がサポートしている別のライブラリではないライブラリを使用する場合は、[認定 OpenID Connect 実装](https://openid.net/developers/certified/)のものを選択します。

[OAuth 2.0 または OpenID Connect 1.0](active-directory-v2-protocols.md) の独自のプロトコルレベル実装を手作業でコーディングする場合は、各標準の仕様におけるセキュリティの考慮事項に細心の注意を払い、[Microsoft SDL][Microsoft-SDL] などのソフトウェア開発ライフサイクル (SDL) 手法に従ってください。

## <a name="single-page-application-spa"></a>シングルページ アプリケーション (SPA)

シングルページ アプリケーションは、ブラウザー画面上で完全に実行され、ページ データ (HTML、CSS、JavaScript) を動的に、またはアプリケーションの読み込み時にフェッチします。 Web API を呼び出して、バックエンド データ ソースとやり取りできます。

SPA のコードは完全にブラウザーで実行されるため、シークレットを安全に格納できない *パブリック クライアント* と見なされます。

| 言語/フレームワーク | プロジェクト<br/>GitHub                                                                                                    | Package                                                                      | 取得<br/>started                             | ユーザーのサインイン                                         | Web API へのアクセス                                                 | 一般提供 (GA) *または*<br/>パブリック プレビュー<sup>1</sup> |
|----------------------|--------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------|:-----------------------------------------------:|:-----------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| Angular              | [MSAL Angular 2.0](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)         | [@azure/msal-angular](https://www.npmjs.com/package/@azure/msal-angular)     | —                                               | ![ライブラリでは、ユーザー サインインの ID トークンを要求できます。][y] | ![ライブラリでは、保護された Web API のアクセス トークンを要求できます。][y] | パブリック プレビュー                                               |
| Angular              | [MSAL Angular](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/msal-angular-v1/lib/msal-angular) | [@azure/msal-angular](https://www.npmjs.com/package/@azure/msal-angular)     | [チュートリアル](tutorial-v2-angular.md)              | ![ライブラリでは、ユーザー サインインの ID トークンを要求できます。][y] | ![ライブラリでは、保護された Web API のアクセス トークンを要求できます。][y] | GA                                                           |
| AngularJS            | [MSAL AngularJS](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angularjs)         | [@azure/msal-angularjs](https://www.npmjs.com/package/@azure/msal-angularjs) | —                                               | ![ライブラリでは、ユーザー サインインの ID トークンを要求できます。][y] | ![ライブラリでは、保護された Web API のアクセス トークンを要求できます。][y] | パブリック プレビュー                                               |
| JavaScript           | [MSAL.js 2.0](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser)              | [@azure/msal-browser](https://www.npmjs.com/package/@azure/msal-browser)     | [チュートリアル](tutorial-v2-javascript-auth-code.md) | ![ライブラリでは、ユーザー サインインの ID トークンを要求できます。][y] | ![ライブラリでは、保護された Web API のアクセス トークンを要求できます。][y] | GA                                                           |
| React                | [MSAL React](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-react)                 | [@azure/msal-react](https://www.npmjs.com/package/@azure/msal-react)         | —                                               | ![ライブラリでは、ユーザー サインインの ID トークンを要求できます。][y] | ![ライブラリでは、保護された Web API のアクセス トークンを要求できます。][y] | パブリック プレビュー                                               |
<!--
| Vue | [Vue MSAL]( https://github.com/mvertopoulos/vue-msal) | [vue-msal]( https://www.npmjs.com/package/vue-msal) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
-->

<sup>1</sup>「[Microsoft Azure プレビューの追加利用規約][preview-tos]」は、*パブリック プレビュー* のライブラリにも適用されます。

## <a name="web-application"></a>Web アプリケーション

Web アプリケーションでは、HTML、CSS、および JavaScript を生成し、ユーザーの Web ブラウザーに送信してレンダリングするサーバーでコードを実行します。 ユーザーの ID は、ユーザーのブラウザー (フロントエンド) と Web サーバー (バックエンド) の間のセッションとして保持されます。

Web アプリケーションのコードは Web サーバー上で実行されるため、シークレットを安全に格納できる *機密クライアント* と見なされます。

| 言語/フレームワーク | プロジェクト<br/>GitHub                                                                                     | Package                                                                                                    | 取得<br/>started                               | ユーザーのサインイン                                            | Web API へのアクセス                                                    | 一般提供 (GA) *または*<br/>パブリック プレビュー<sup>1</sup> |
|----------------------|-----------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------|:-------------------------------------------------:|:--------------------------------------------------------:|:------------------------------------------------------------------:|:------------------------------------------------------------:|
| .NET                 | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)                        | [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)                      | —                                                 | ![ライブラリでは、ユーザー サインインの ID トークンを要求できません。][n] | ![ライブラリでは、保護された Web API のアクセス トークンを要求できます。][y]    | GA                                                           |
| ASP.NET Core         | [ASP.NET のセキュリティ](/aspnet/core/security/)                                                                | [Microsoft.AspNetCore.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) | —                                                 | ![ライブラリでは、ユーザー サインインの ID トークンを要求できます。][y]    | ![ライブラリでは、保護された Web API のアクセス トークンを要求できません。][n] | GA                                                           |
| ASP.NET Core         | [Microsoft.Identity.Web](https://github.com/AzureAD/microsoft-identity-web)                               | [Microsoft.Identity.Web](https://www.nuget.org/packages/Microsoft.Identity.Web)                            | —                                                 | ![ライブラリでは、ユーザー サインインの ID トークンを要求できます。][y]    | ![ライブラリでは、保護された Web API のアクセス トークンを要求できます。][y]    | GA                                                           |
| Java                 | [MSAL4J](https://github.com/AzureAD/microsoft-authentication-library-for-java)                            | [msal4j](https://search.maven.org/artifact/com.microsoft.azure/msal4j)                                     | [クイックスタート](quickstart-v2-java-webapp.md)        | ![ライブラリでは、ユーザー サインインの ID トークンを要求できます。][y]    | ![ライブラリでは、保護された Web API のアクセス トークンを要求できます。][y]    | GA                                                           |
| Node.js              | [MSAL Node.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) | [msal-node](https://www.npmjs.com/package/@azure/msal-node)                                                | [クイックスタート](quickstart-v2-nodejs-webapp-msal.md) | ![ライブラリでは、ユーザー サインインの ID トークンを要求できます。][y]    | ![ライブラリでは、保護された Web API のアクセス トークンを要求できます。][y]    | パブリック プレビュー                                               |
| Node.js              | [Azure AD Passport](https://github.com/AzureAD/passport-azure-ad)                                         | [passport-azure-ad](https://www.npmjs.com/package/passport-azure-ad)                                       | [クイックスタート](quickstart-v2-nodejs-webapp.md)      | ![ライブラリでは、ユーザー サインインの ID トークンを要求できます。][y]    | ![ライブラリでは、保護された Web API のアクセス トークンを要求できません。][n] | GA                                                           |
| Python               | [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python)                     | [msal](https://pypi.org/project/msal)                                                                      | [クイックスタート](quickstart-v2-python-webapp.md)      | ![ライブラリでは、ユーザー サインインの ID トークンを要求できます。][y]    | ![ライブラリでは、保護された Web API のアクセス トークンを要求できます。][y]    | GA                                                           |
<!--
| Java | [ScribeJava](https://github.com/scribejava/scribejava) | [ScribeJava 3.2.0](https://github.com/scribejava/scribejava/releases/tag/scribejava-3.2.0) | ![X indicating no.][n] | ![X indicating no.][n] | ![Green check mark.][y] | -- |
| Java | [Gluu oxAuth](https://github.com/GluuFederation/oxAuth) | [oxAuth 3.0.2](https://github.com/GluuFederation/oxAuth/releases/tag/3.0.2) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
| Node.js | [openid-client](https://github.com/panva/node-openid-client/) | [openid-client 2.4.5](https://github.com/panva/node-openid-client/releases/tag/v2.4.5) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
| PHP | [PHP League oauth2-client](https://github.com/thephpleague/oauth2-client) | [oauth2-client 1.4.2](https://github.com/thephpleague/oauth2-client/releases/tag/1.4.2) | ![X indicating no.][n] | ![X indicating no.][n] | ![Green check mark.][y] | -- |
| Ruby | [OmniAuth](https://github.com/omniauth/omniauth) | [omniauth 1.3.1](https://github.com/omniauth/omniauth/releases/tag/v1.3.1)<br/>[omniauth-oauth2 1.4.0](https://github.com/intridea/omniauth-oauth2) | ![X indicating no.][n] | ![X indicating no.][n] | ![Green check mark.][y] | -- |
-->

<sup>1</sup>「[Microsoft Azure プレビューの追加利用規約][preview-tos]」は、*パブリック プレビュー* のライブラリにも適用されます。

## <a name="desktop-application"></a>デスクトップ アプリケーション

デスクトップ アプリケーションは、通常、ユーザー インターフェイスを表面に出し、ユーザーのデスクトップで実行されることを意図した、バイナリ (コンパイル済み) コードです。

デスクトップ アプリケーションはユーザーのデスクトップで実行されるため、シークレットを安全に保存できない *パブリック クライアント* と見なされます。

| 言語/フレームワーク | プロジェクト<br/>GitHub                                                                                     | Package                                                                               | 取得<br/>started                        | ユーザーのサインイン                                         | Web API へのアクセス                                                 | 一般提供 (GA) *または*<br/>パブリック プレビュー<sup>1</sup> |
|----------------------|-----------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------|:------------------------------------------:|:-----------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| Electron             | [MSAL Node.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) | [@azure/msal-node](https://www.npmjs.com/package/@azure/msal-node)                    | —                                          | ![ライブラリでは、ユーザー サインインの ID トークンを要求できます。][y] | ![ライブラリでは、保護された Web API のアクセス トークンを要求できます。][y] | パブリック プレビュー                                               |
| Java                 | [MSAL4J](https://github.com/AzureAD/microsoft-authentication-library-for-java)                            | [msal4j](https://mvnrepository.com/artifact/com.microsoft.azure/msal4j)               | —                                          | ![ライブラリでは、ユーザー サインインの ID トークンを要求できます。][y] | ![ライブラリでは、保護された Web API のアクセス トークンを要求できます。][y] | GA                                                           |
| macOS (Swift/Obj-C)  | [iOS および macOS 用の MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-objc)            | [MSAL](https://cocoapods.org/pods/MSAL)                                               | [チュートリアル](tutorial-v2-ios.md)             | ![ライブラリでは、ユーザー サインインの ID トークンを要求できます。][y] | ![ライブラリでは、保護された Web API のアクセス トークンを要求できます。][y] | GA                                                           |
| UWP                  | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)                        | [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client) | [チュートリアル](tutorial-v2-windows-uwp.md)     | ![ライブラリでは、ユーザー サインインの ID トークンを要求できます。][y] | ![ライブラリでは、保護された Web API のアクセス トークンを要求できます。][y] | GA                                                           |
| WPF                  | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)                        | [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client) | [チュートリアル](tutorial-v2-windows-desktop.md) | ![ライブラリでは、ユーザー サインインの ID トークンを要求できます。][y] | ![ライブラリでは、保護された Web API のアクセス トークンを要求できます。][y] | GA                                                           |
<!--
| Java | Scribe | [Scribe Java](https://mvnrepository.com/artifact/org.scribe/scribe) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
| React Native | [React Native App Auth](https://github.com/FormidableLabs/react-native-app-auth/blob/main/docs/config-examples/azure-active-directory.md) | [react-native-app-auth](https://www.npmjs.com/package/react-native-app-auth) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
-->

<sup>1</sup>「[Microsoft Azure プレビューの追加利用規約][preview-tos]」は、*パブリック プレビュー* のライブラリにも適用されます。

## <a name="mobile-application"></a>モバイル アプリケーション

モバイル アプリケーションは、通常、ユーザー インターフェイスを表面に出し、ユーザーのモバイル デバイスで実行されることを意図した、バイナリ (コンパイル済み) コードです。

モバイル アプリケーションはユーザーのモバイル デバイスで実行されるため、シークレットを安全に保存できない *パブリック クライアント* と見なされます。

| プラットフォーム          | プロジェクト<br/>GitHub                                                                          | Package                                                                               | 取得<br/>started                    | ユーザーのサインイン                                         | Web API へのアクセス                                                 | 一般提供 (GA) *または*<br/>パブリック プレビュー<sup>1</sup> |
|-------------------|------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------|:--------------------------------------:|:-----------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| Android (Java)    | [MSAL Android](https://github.com/AzureAD/microsoft-authentication-library-for-android)        | [MSAL](https://mvnrepository.com/artifact/com.microsoft.identity.client/msal)         | [クイックスタート](quickstart-v2-android.md) | ![ライブラリでは、ユーザー サインインの ID トークンを要求できます。][y] | ![ライブラリでは、保護された Web API のアクセス トークンを要求できます。][y] | GA                                                           |
| Android (Kotlin)  | [MSAL Android](https://github.com/AzureAD/microsoft-authentication-library-for-android)        | [MSAL](https://mvnrepository.com/artifact/com.microsoft.identity.client/msal)         | —                                      | ![ライブラリでは、ユーザー サインインの ID トークンを要求できます。][y] | ![ライブラリでは、保護された Web API のアクセス トークンを要求できます。][y] | GA                                                           |
| iOS (Swift/Obj-C) | [iOS および macOS 用の MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-objc) | [MSAL](https://cocoapods.org/pods/MSAL)                                               | [チュートリアル](tutorial-v2-ios.md)         | ![ライブラリでは、ユーザー サインインの ID トークンを要求できます。][y] | ![ライブラリでは、保護された Web API のアクセス トークンを要求できます。][y] | GA                                                           |
| Xamarin (.NET)    | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)             | [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client) | —                                      | ![ライブラリでは、ユーザー サインインの ID トークンを要求できます。][y] | ![ライブラリでは、保護された Web API のアクセス トークンを要求できます。][y] | GA                                                           |
<!--
| React Native |[React Native App Auth](https://github.com/FormidableLabs/react-native-app-auth/blob/main/docs/config-examples/azure-active-directory.md) | [react-native-app-auth](https://www.npmjs.com/package/react-native-app-auth) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
-->

<sup>1</sup>「[Microsoft Azure プレビューの追加利用規約][preview-tos]」は、*パブリック プレビュー* のライブラリにも適用されます。

## <a name="service--daemon"></a>サービス/デーモン

サービスとデーモンは、サーバー間およびその他の無人 (*ヘッドレス* とも呼ばれます) 通信でよく使用されます。 キーボードの前にユーザーがいて資格情報を入力したりリソースへのアクセスを同意したりすることがないため、これらのアプリケーションでは、Web API のリソースへの認可済みアクセスを要求するときに、ユーザーではなくそれ自身として認証されます。

サーバーで実行されるサービスまたはデーモンは、シークレットを安全に格納できる *機密クライアント* と見なされます。

| 言語/フレームワーク | プロジェクト<br/>GitHub                                                                 | Package                                                                                | 取得<br/>started                           | ユーザーのサインイン                                            | Web API へのアクセス                                                 | 一般提供 (GA) *または*<br/>パブリック プレビュー<sup>1</sup> |
|----------------------|---------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------|:---------------------------------------------:|:--------------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| .NET                 | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)    | [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client/) | [クイックスタート](quickstart-v2-netcore-daemon.md) | ![ライブラリでは、ユーザー サインインの ID トークンを要求できません。][n] | ![ライブラリでは、保護された Web API のアクセス トークンを要求できます。][y] | GA                                                           |
| Java                 | [MSAL4J](https://github.com/AzureAD/microsoft-authentication-library-for-java)        | [msal4j](https://javadoc.io/doc/com.microsoft.azure/msal4j/latest/index.html)          | —                                             | ![ライブラリでは、ユーザー サインインの ID トークンを要求できません。][n] | ![ライブラリでは、保護された Web API のアクセス トークンを要求できます。][y] | GA                                                           |
| Python               | [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python) | [msal-python](https://github.com/AzureAD/microsoft-authentication-library-for-python)  | [クイックスタート](quickstart-v2-python-daemon.md)  | ![ライブラリでは、ユーザー サインインの ID トークンを要求できません。][n] | ![ライブラリでは、保護された Web API のアクセス トークンを要求できます。][y] | GA                                                           |
<!--
|PHP| [The PHP League oauth2-client](https://oauth2-client.thephpleague.com/usage/) | [League\OAuth2](https://oauth2-client.thephpleague.com/) | ![Green check mark.][n] | ![X indicating no.][n] | ![Green check mark.][y] | -- |
-->

<sup>1</sup>「[Microsoft Azure プレビューの追加利用規約][preview-tos]」は、*パブリック プレビュー* のライブラリにも適用されます。

## <a name="next-steps"></a>次のステップ

Microsoft Authentication Library の詳細については、「[Microsoft Authentication Library (MSAL)の概要](msal-overview.md)」を参照してください。

<!--Image references-->
[y]: ./media/common/yes.png
[n]: ./media/common/no.png

<!--Reference-style links -->
[AAD-App-Model-V2-Overview]: v2-overview.md
[Microsoft-SDL]: https://www.microsoft.com/securityengineering/sdl/
[preview-tos]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
