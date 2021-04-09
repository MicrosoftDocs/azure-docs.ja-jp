---
title: Microsoft ID プラットフォームのコード サンプル
description: シナリオ別に整理された、利用可能な Microsoft ID プラットフォーム コード サンプルのインデックスを提供します。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: sample
ms.workload: identity
ms.date: 11/04/2020
ms.author: marsma
ms.reviewer: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: dc740acb366212640015ca0fb527d34fade1097d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103010764"
---
# <a name="microsoft-identity-platform-code-samples-v20-endpoint"></a>Microsoft ID プラットフォームのコード サンプル (v2.0 エンドポイント)

Microsoft ID プラットフォームを使用して、次の操作を行うことができます。

- Web アプリケーション Web API に認証と承認を追加する。
- 保護された Web API にアクセスするためにアクセス トークンを要求する。

この記事では、Microsoft ID プラットフォームのサンプルについて簡単に説明し、サンプルへのリンクを提供します。 これらのサンプルでは、その実行方法を示し、アプリケーションで使用できるコード スニペットを提供します。 サンプル コードのページには、要件、インストール、設定に役立つ詳細な Readme のトピックが含まれています。 コード内のコメントは、重要なセクションを理解するのに役立ちます。

サンプルの種類ごとの基本的なシナリオについては、[Microsoft ID プラットフォームのアプリの種類](v2-app-types.md)に関する記事をご覧ください。

GitHub でサンプルに協力することもできます。 その方法については、[Microsoft Azure Active Directory のサンプルとドキュメント](https://github.com/Azure-Samples?page=3&query=active-directory)をご覧ください。

## <a name="single-page-applications"></a>シングルページ アプリケーション

これらのサンプルでは、Microsoft ID プラットフォームを使用してセキュリティ保護されているシングルページ アプリケーションの作成方法を示します。 これらのサンプルでは、MSAL.js のいずれかの種類を使用します。

| プラットフォーム | 説明 | Link |
| -------- | --------------------- | -------- |
| ![JavaScript のロゴを示す画像](media/sample-v2-code/logo_js.png) [JavaScript (MSAL.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | SPA から Microsoft Graph を呼び出します。 |[javascript-graphapi-v2](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2) |
| ![JavaScript のロゴを示す画像](media/sample-v2-code/logo_js.png) [JavaScript (MSAL.js 2.0)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser) | SPA から認証コード フローと PKCE を使用して Microsoft Graph を呼び出します。 |[javascript-v2](https://github.com/Azure-Samples/ms-identity-javascript-v2) |
| ![JavaScript のロゴを示す画像](media/sample-v2-code/logo_js.png) [JavaScript (MSAL.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | SPA から B2C を呼び出します。 |[b2c-javascript-msal-singlepageapp](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp) |
| ![JavaScript のロゴを示す画像](media/sample-v2-code/logo_js.png) [JavaScript (MSAL.js 2.0)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser) | SPA から認証コード フローと PKCE を使用して B2C を呼び出します |[b2c-javascript-spa](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa) |
| ![JavaScript のロゴを示す画像](media/sample-v2-code/logo_js.png) [JavaScript (MSAL.js 2.0)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser) | SPA からカスタム Web API を呼び出し、そこから Microsoft Graph を呼び出します  | [ms-identity-javascript-tutorial-chapter4-obo](https://github.com/Azure-Samples/ms-identity-javascript-tutorial/tree/main/4-AdvancedGrants/1-call-api-graph) |
| ![Angular のロゴを示す画像](media/sample-v2-code/logo_angular.png) [Angular (MSAL Angular)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)| SPA から Microsoft Graph を呼び出します。  | [active-directory-javascript-singlepageapp-angular](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular) |
| ![Angular のロゴを示す画像](media/sample-v2-code/logo_angular.png) [Angular (MSAL Angular 2.0)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)| SPA から認証コード フローと PKCE を使用して Microsoft Graph を呼び出します。 | [ms-identity-javascript-angular-spa](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa) |
| ![Angular のロゴを示す画像](media/sample-v2-code/logo_angular.png) [Angular (MSAL Angular 2.0)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)| SPA からカスタム Web API を呼び出します。 | [ms-identity-javascript-angular-spa-aspnetcore-webapi](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnetcore-webapi) |
| ![Angular のロゴを示す画像](media/sample-v2-code/logo_angular.png) [Angular (MSAL Angular)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular) | SPA から B2C を呼び出します。 |[active-directory-b2c-javascript-angular-spa](https://github.com/Azure-Samples/active-directory-b2c-javascript-angular-spa) |
| ![Angular のロゴを示す画像](media/sample-v2-code/logo_angular.png) [Angular (MSAL Angular 2.0)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular) | SPA からアプリのロールとセキュリティ グループを使用してカスタム Web API を呼び出します。 |[ms-identity-javascript-angular-spa-dotnetcore-webapi-roles-groups](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-dotnetcore-webapi-roles-groups) |
| ![React のロゴを示す画像](media/sample-v2-code/logo_react.png) [React (MSAL React)](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-react)| SPA から認証コード フローと PKCE を使用して Microsoft Graph を呼び出します。 | [ms-identity-javascript-react-spa](https://github.com/Azure-Samples/ms-identity-javascript-react-spa) |
| ![React のロゴを示す画像](media/sample-v2-code/logo_react.png) [React (MSAL React)](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-react)| SPA からカスタム Web API を呼び出します。 | [ms-identity-javascript-react-tutorial](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/tree/main/3-Authorization-II/1-call-api) |
| ![React のロゴを示す画像](media/sample-v2-code/logo_react.png) [React (MSAL.js 2.0)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core)| SPA からカスタム Web API を呼び出し、そこから Microsoft Graph を呼び出します。  | [ms-identity-javascript-react-spa-dotnetcore-webapi-obo](https://github.com/Azure-Samples/ms-identity-javascript-react-spa-dotnetcore-webapi-obo) |
| ![Blazor のロゴを示す画像](media/sample-v2-code/logo-blazor.png) [Blazor WebAssembly (MSAL.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser) | Azure Active Directory を使用してユーザーをサインインし、API を呼び出す Blazor WebAssembly チュートリアル |[ms-identity-blazor-wasm](https://github.com/Azure-Samples/ms-identity-blazor-wasm) |

## <a name="web-applications"></a>Web アプリケーション

次のサンプルは、ユーザーがサインインする Web アプリケーションを示しています。 サンプルの中には、Microsoft Graph、またはユーザーの ID を使用してユーザー独自の Web API を呼び出すアプリケーションを示しているものもあります。

| プラットフォーム | ユーザーのサインインのみ | ユーザーのサインインと Microsoft Graph の呼び出し |
| -------- | ------------------- | --------------------------------- |
| ![ASP.NET Core のロゴを示す画像](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core | [ASP.NET Core Web アプリのユーザーのサインインに関するチュートリアル](https://aka.ms/aspnetcore-webapp-sign-in) | [ASP.NET Core Web アプリが Microsoft Graph を呼び出す](https://aka.ms/aspnetcore-webapp-call-msgraph)フェーズと同じサンプル</p>高度なサンプル「[バックグラウンド アプリ、API、サービスから、ログインしているユーザーのトークン キャッシュへのアクセス](https://github.com/Azure-Samples/ms-identity-dotnet-advanced-token-cache)」 |
| ![この画像は ASP.NET Framework ロゴを示しています](media/sample-v2-code/logo_NETframework.png)</p>ASP.NET Core | [開発者向けの、AD FS から Azure AD へのアプリケーション移行プレイブック](https://github.com/Azure-Samples/ms-identity-dotnet-adfs-to-aad)。Active Directory フェデレーション サービス (AD FS) と統合されているアプリケーションを Azure Active Directory (Azure AD) に安全に移行する方法を学習 | |
| ![この画像は ASP.NET Framework ロゴを示しています](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET | [ASP.NET クイック スタート](https://github.com/AzureAdQuickstarts/AppModelv2-WebApp-OpenIDConnect-DotNet) </p> [dotnet-webapp-openidconnect-v2](https://github.com/azure-samples/active-directory-dotnet-webapp-openidconnect-v2)  |  [dotnet-admin-restricted-scopes-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2) </p> |[msgraph-training-aspnetmvcapp](https://github.com/microsoftgraph/msgraph-training-aspnetmvcapp)
| ![Java のロゴを示す画像](media/sample-v2-code/logo_java.png)  |[Java サーブレットのチュートリアル - 第 1.1 章](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/blob/main/README.md) AAD でのサインイン| |
| ![Java のロゴを示す画像](media/sample-v2-code/logo_java.png)  |[Java サーブレットのチュートリアル - 第 1.2 章](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/blob/main/README.md) B2C でのサインイン |
| ![Java のロゴを示す画像](media/sample-v2-code/logo_java.png)  | | [Java サーブレットのチュートリアル - 第 2.1 章](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/blob/main/README.md) AAD でのサインインと Graph の呼び出し|
| ![Java のロゴを示す画像](media/sample-v2-code/logo_java.png)  |[Java サーブレットのチュートリアル - 第 3.1 章](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/blob/main/README.md) AAD でのサインインと Roles 要求でのアクセス制御| |
| ![Java のロゴを示す画像](media/sample-v2-code/logo_java.png)  | | [Java サーブレットのチュートリアル - 第 3.2 章](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/blob/main/README.md) AAD でのサインインと Groups 要求でのアクセス制御|
| ![Java のロゴを示す画像](media/sample-v2-code/logo_java.png) | |[Java サーブレットのチュートリアル - 第 4.1 章](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/blob/main/README.md) Azure App Service へのデプロイ|
| ![Java のロゴを示す画像](media/sample-v2-code/logo_java.png)  | | [ms-identity-java-webapp](https://github.com/Azure-Samples/ms-identity-java-webapp) |
| ![Java のロゴを示す画像](media/sample-v2-code/logo_java.png)  | [ms-identity-b2c-java-servlet-webapp-authentication](https://github.com/Azure-Samples/ms-identity-b2c-java-servlet-webapp-authentication)|  |
| ![Node.js のロゴを示す画像](media/sample-v2-code/logo_nodejs.png)</p>Node.js (MSAL Node) | [Express Web アプリのユーザーのサインインに関するチュートリアル](https://github.com/Azure-Samples/ms-identity-node) | |
| ![Python のロゴを示す画像](media/sample-v2-code/logo_python.png)  | [Python Flask のチュートリアル - 第 1.1 章](https://github.com/Azure-Samples/ms-identity-python-flask-tutorial/blob/main/README.md) AAD でのサインイン  |  |
| ![Python のロゴを示す画像](media/sample-v2-code/logo_python.png)  | [Python Flask のチュートリアル - 第 1.2 章](https://github.com/Azure-Samples/ms-identity-python-flask-tutorial/blob/main/README.md) B2C でのサインイン                    |  |
| ![Python のロゴを示す画像](media/sample-v2-code/logo_python.png)  | | [Python Flask のチュートリアル - 第 2.1 章](https://github.com/Azure-Samples/ms-identity-python-flask-tutorial/blob/main/README.md) AAD でのサインインと Graph の呼び出し |
| ![Python のロゴを示す画像](media/sample-v2-code/logo_python.png)  | |[Python Flask のチュートリアル - 第 3.1 章](https://github.com/Azure-Samples/ms-identity-python-flask-tutorial/blob/main/README.md) Azure App Service へのデプロイ  |
| ![Python のロゴを示す画像](media/sample-v2-code/logo_python.png)  | [Python Django のチュートリアル - 第 1.1 章](https://github.com/Azure-Samples/ms-identity-python-django-tutorial/blob/main/README.md) AAD でのサインイン  | |
| ![Python のロゴを示す画像](media/sample-v2-code/logo_python.png)  | [Python Django のチュートリアル - 第 1.2 章](https://github.com/Azure-Samples/ms-identity-python-django-tutorial/blob/main/README.md) B2C でのサインイン                    |  |
| ![Python のロゴを示す画像](media/sample-v2-code/logo_python.png)  | | [Python Django のチュートリアル - 第 2.1 章](https://github.com/Azure-Samples/ms-identity-python-django-tutorial/blob/main/README.md) AAD でのサインインと Graph の呼び出し|
| ![Python のロゴを示す画像](media/sample-v2-code/logo_python.png)  | | [Python Django のチュートリアル - 第 3.1 章](https://github.com/Azure-Samples/ms-identity-python-django-tutorial/blob/main/README.md) Azure App Service へのデプロイ                    |
| ![Python のロゴを示す画像](media/sample-v2-code/logo_python.png)  | | [Python Flask Web アプリ](https://github.com/Azure-Samples/ms-identity-python-webapp) |
| ![Ruby のロゴを示す画像](media/sample-v2-code/logo_ruby.png) |                   | [msgraph-training-rubyrailsapp](https://github.com/microsoftgraph/msgraph-training-rubyrailsapp) |
| ![この画像は、Blazor ロゴを示しています](media/sample-v2-code/logo-blazor.png)</p>Blazor サーバー | [Blazor Server アプリのユーザーのサインインに関するチュートリアル](https://github.com/Azure-Samples/ms-identity-blazor-server/tree/main/WebApp-OIDC) | [Blazor Server app calls Microsoft Graph (Blazor Server アプリが Microsoft Graph を呼び出す)](https://github.com/Azure-Samples/ms-identity-blazor-server/tree/main/WebApp-graph-user/Call-MSGraph)</p>チャプター形式のチュートリアル:[Blazor Server app to sign-in users and call APIs with Azure Active Directory (Azure Active Directory を使用してユーザーをサインインさせ API を呼び出す Blazor Server アプリ)](https://github.com/Azure-Samples/ms-identity-blazor-server) |

## <a name="desktop-and-mobile-public-client-apps"></a>デスクトップおよびモバイルのパブリック クライアント アプリ

次のサンプルは、ユーザーの名前で Microsoft Graph API またはお客様独自の Web API にアクセスするパブリック クライアント アプリケーション (デスクトップまたはモバイル アプリケーション) を示しています。 "*WAM を使用するデスクトップ (コンソール)* " を除き、これらのすべてのクライアント アプリケーションで Microsoft Authentication Library (MSAL) を使用します。

| クライアント アプリケーション | プラットフォーム | フロー/許可 | Microsoft Graph の呼び出し | ASP.NET Core Web API の呼び出し |
| ------------------ | -------- |  ----------| ---------- | ------------------------- |
| デスクトップ チュートリアル (.NET Core) - 必要に応じて以下を使用</p>- クロス プラットフォーム トークン キャッシュ</p>- カスタム Web UI | ![.NET/C# のロゴを示す画像](media/sample-v2-code/logo_NETcore.png) | [承認コード](msal-authentication-flows.md#authorization-code)| [ms-identity-dotnet-desktop-tutorial](https://github.com/azure-samples/ms-identity-dotnet-desktop-tutorial) | |
| デスクトップ (WPF)      | ![.NET デスクトップ/C# のロゴを示す画像](media/sample-v2-code/logo_NET.png) | [承認コード](msal-authentication-flows.md#authorization-code)| [dotnet-desktop-msgraph-v2](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | [dotnet-native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi) |
| デスクトップ (コンソール)   | ![.NET/C# (デスクトップ) のロゴを示す画像](media/sample-v2-code/logo_NET.png) | [統合 Windows 認証](msal-authentication-flows.md#integrated-windows-authentication) | [dotnet-iwa-v2](https://github.com/azure-samples/active-directory-dotnet-iwa-v2) |  |
| デスクトップ (コンソール)   | ![Java のロゴを示す画像](media/sample-v2-code/logo_java.png) | [統合 Windows 認証](msal-authentication-flows.md#integrated-windows-authentication) |[ms-identity-java-desktop](https://github.com/Azure-Samples/ms-identity-java-desktop/) |  |
| デスクトップ (コンソール)   | ![.NET/C# (デスクトップ) のロゴ](media/sample-v2-code/logo_NETcore.png) | [ユーザー名/パスワード](msal-authentication-flows.md#usernamepassword) |[dotnetcore-up-v2](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2) |  |
| WAM を使用するデスクトップ (コンソール)  | ![.NET/C# (デスクトップ) のロゴ](media/sample-v2-code/logo_NETcore.png) | [Web アカウント マネージャー](/windows/uwp/security/web-account-manager) (WAM) による対話型 |[dotnet-native-uwp-wam](https://github.com/azure-samples/active-directory-dotnet-native-uwp-wam) |  |
| デスクトップ (コンソール)   | ![Java のロゴを示す画像](media/sample-v2-code/logo_java.png) | [ユーザー名/パスワード](msal-authentication-flows.md#usernamepassword) |[ms-identity-java-desktop](https://github.com/Azure-Samples/ms-identity-java-desktop/) |  |
| デスクトップ (コンソール)   | ![Python のロゴを示す画像](media/sample-v2-code/logo_python.png) | [ユーザー名/パスワード](msal-authentication-flows.md#usernamepassword) |[ms-identity-python-desktop](https://github.com/Azure-Samples/ms-identity-python-desktop) |  |
| デスクトップ (Electron)   | ![Node.js のロゴを示す画像](media/sample-v2-code/logo_nodejs.png)</p>Node.js (MSAL Node) | [承認コード (PKCE)](msal-authentication-flows.md#authorization-code) |[ms-identity-javascript-nodejs-desktop](https://github.com/Azure-Samples/ms-identity-javascript-nodejs-desktop) |  |
| モバイル (Android、iOS、UWP)   | ![.NET/C# (Xamarin) のロゴを示す画像](media/sample-v2-code/logo_xamarin.png) | [承認コード](msal-authentication-flows.md#authorization-code) |[xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) |  |
| モバイル (iOS)       | ![iOS/Objective C または Swift を示す画像](media/sample-v2-code/logo_iOS.png) | [承認コード](msal-authentication-flows.md#authorization-code) |[ios-swift-objc-native-v2](https://github.com/azure-samples/active-directory-ios-swift-native-v2) </p> [ios-native-nxoauth2-v2](https://github.com/azure-samples/active-directory-ios-native-nxoauth2-v2) |  |
| デスクトップ (macOS)       | macOS | [承認コード](msal-authentication-flows.md#authorization-code) |[macOS-swift-objc-native-v2](https://github.com/Azure-Samples/ms-identity-macOS-swift-objc) |  |
| Mobile (Android-Java)   | ![Android のロゴを示す画像](media/sample-v2-code/logo_Android.png) | [承認コード](msal-authentication-flows.md#authorization-code) |  [android-Java](https://github.com/Azure-Samples/ms-identity-android-java) |  |
| Mobile (Android-Kotlin)   | ![Android のロゴを示す画像](media/sample-v2-code/logo_Android.png) | [承認コード](msal-authentication-flows.md#authorization-code) |  [android-Kotlin](https://github.com/Azure-Samples/ms-identity-android-kotlin) |  |

## <a name="daemon-applications"></a>デーモン アプリケーション

以下に、アプリケーション独自の ID (ユーザーなしで) で Microsoft Graph API にアクセスするサンプル アプリケーションを示します。

| クライアント アプリケーション | プラットフォーム | フロー/許可 | Microsoft Graph の呼び出し |
| ------------------ | -------- | ---------- | -------------------- |
| コンソール | ![.NET Core のロゴを示す画像](media/sample-v2-code/logo_NETcore.png)</p> ASP.NET  | [クライアントの資格情報](msal-authentication-flows.md#client-credentials) | [dotnetcore-daemon-v2](https://github.com/azure-samples/active-directory-dotnetcore-daemon-v2) |
| Web アプリ | ![ASP.NET のロゴを示すスクリーンショット。](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET  | [クライアントの資格情報](msal-authentication-flows.md#client-credentials) | [dotnet-daemon-v2](https://github.com/azure-samples/active-directory-dotnet-daemon-v2) |
| コンソール | ![Java のロゴを示す画像](media/sample-v2-code/logo_java.png) | [クライアントの資格情報](msal-authentication-flows.md#client-credentials) | [ms-identity-java-daemon](https://github.com/Azure-Samples/ms-identity-java-daemon) |
| コンソール | ![Node.js のロゴを示す画像](media/sample-v2-code/logo_nodejs.png)</p>Node.js (MSAL Node)| [クライアントの資格情報](msal-authentication-flows.md#client-credentials) | [ms-identity-javascript-nodejs-console](https://github.com/Azure-Samples/ms-identity-javascript-nodejs-console) |
| コンソール | ![Python のロゴを示す画像](media/sample-v2-code/logo_python.png) | [クライアントの資格情報](msal-authentication-flows.md#client-credentials) | [ms-identity-python-daemon](https://github.com/Azure-Samples/ms-identity-python-daemon) |

## <a name="headless-applications"></a>ヘッドレス アプリケーション

次のサンプルは、Web ブラウザーがないデバイスで実行されるパブリック クライアント アプリケーションを示しています。 このアプリは、コマンド ライン ツール、Linux または Mac 上で実行されるアプリ、あるいは IoT アプリケーションにすることもできます。 このサンプルは、別のデバイス (携帯電話など) で対話的にサインインするユーザーの名前で Microsoft Graph API にアクセスするアプリの特徴を示しています。 このクライアント アプリケーションでは、Microsoft Authentication Library (MSAL) を使用します。

| クライアント アプリケーション | プラットフォーム | フロー/許可 | Microsoft Graph の呼び出し |
| ------------------ | -------- |  ----------| ---------- |
| デスクトップ (コンソール)   | ![.NET/C# (デスクトップ) のロゴを示す画像](media/sample-v2-code/logo_NETcore.png) | [デバイス コード フロー](msal-authentication-flows.md#device-code) |[dotnetcore-devicecodeflow-v2](https://github.com/azure-samples/active-directory-dotnetcore-devicecodeflow-v2) |
| デスクトップ (コンソール)   | ![Java のロゴを示す画像](media/sample-v2-code/logo_java.png) | [デバイス コード フロー](msal-authentication-flows.md#device-code) |[ms-identity-java-devicecodeflow](https://github.com/Azure-Samples/ms-identity-java-devicecodeflow) |
| デスクトップ (コンソール)   | ![Python のロゴを示す画像](media/sample-v2-code/logo_python.png) | [デバイス コード フロー](msal-authentication-flows.md#device-code) |[ms-identity-python-devicecodeflow](https://github.com/Azure-Samples/ms-identity-python-devicecodeflow) |

## <a name="multi-tenant-saas-applications"></a>マルチテナント SaaS アプリケーション

次のサンプルでは、任意の Azure Active Directory (Azure AD) テナントからのサインインを受け付けるようにアプリケーションを構成する方法を示します。 アプリケーションを "*マルチテナント*" として構成すると、**サービスとしてのソフトウェア** (SaaS) アプリケーションを多数の組織に提供でき、ユーザーは同意を与えた後でアプリケーションにサインインできるようになります。

| プラットフォーム | 説明 | Link |
| -------- | --------------------- | -------- |
| ![Angular のロゴを示す画像](media/sample-v2-code/logo_angular.png) [Angular (MSAL Angular 2.0)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular) | マルチテナント SPA で Graph API を呼び出す |[ms-identity-javascript-angular-spa-aspnet-webapi-multitenant](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnet-webapi-multitenant/tree/master/Chapter1) |
| ![Angular のロゴを示す画像](media/sample-v2-code/logo_angular.png) [Angular (MSAL Angular 2.0)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular) | マルチテナント SPA からマルチテナント カスタム Web API を呼び出します。 |[ms-identity-javascript-angular-spa-aspnet-webapi-multitenant](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnet-webapi-multitenant/tree/master/Chapter2) |
| ![ASP.NET Core のロゴを示す画像](media/sample-v2-code/logo_NETcore.png) [.NET Core (MSAL.NET)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | ASP.NET Core MVC Web アプリケーションで Graph API を呼び出す |[active-directory-aspnetcore-webapp-openidconnect-v2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-3-Multi-Tenant) |
| ![ASP.NET Core のロゴを示す画像](media/sample-v2-code/logo_NETcore.png) [.NET Core (MSAL.NET)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | ASP.NET Core MVC Web アプリケーションで ASP.NET Core Web API を呼び出す |[active-directory-aspnetcore-webapp-openidconnect-v2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/4-WebApp-your-API/4-3-AnyOrg) |

## <a name="web-apis"></a>Web API

次のサンプルは、Microsoft ID プラットフォームで Web API を保護する方法と、その Web API からダウンストリーム API を呼び出す方法を示しています。

| プラットフォーム | サンプル |
| -------- | ------------------- |
| ![ASP.NET Core のロゴを示す画像](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core | [dotnet-native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi-calls-msgraph) の ASP.NET Core Web API (サービス)  |
| ![ASP.NET のロゴを示す画像](media/sample-v2-code/logo_NET.png)</p>ASP.NET MVC | [ms-identity-aspnet-webapi-onbehalfof](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof) の Web API (サービス) |
| ![Java のロゴを示す画像](media/sample-v2-code/logo_java.png) | [ms-identity-java-webapi](https://github.com/Azure-Samples/ms-identity-java-webapi) の Web API (サービス) |
| ![Node.js のロゴを示す画像](media/sample-v2-code/logo_nodejs.png)</p>Node.js (Passport.js)| [active-directory-javascript-nodejs-webapi-v2](https://github.com/Azure-Samples/active-directory-javascript-nodejs-webapi-v2) の Web API (サービス) |
| ![Node.js のロゴを示す画像](media/sample-v2-code/logo_nodejs.png)</p>Node.js (Passport.js)| [active-directory-b2c-javascript-nodejs-webapi](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) の B2C Web API (サービス) |

## <a name="azure-functions-as-web-apis"></a>Web API としての Azure Functions

次のサンプルは、HttpTrigger を使用し、Microsoft ID プラットフォームで Web API を公開して Azure Function を保護する方法と、その Web API からダウンストリーム API を呼び出す方法を示しています。

| プラットフォーム | サンプル |
| -------- | ------------------- |
| ![ASP.NET Core のロゴを示す画像](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core | [dotnet-native-aspnetcore-v2](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions) の ASP.NET Core Web API (サービス) Azure Function  |
| ![Python のロゴを示す画像](media/sample-v2-code/logo_python.png)</p>Python | [Python](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions) の Web API (サービス) |
| ![Node.js のロゴを示す画像](media/sample-v2-code/logo_nodejs.png)</p>Node.js (Passport.js)| [Node.js および passport-azure-ad](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions) の Web API (サービス) |
| ![Node.js のロゴを示す画像](media/sample-v2-code/logo_nodejs.png)</p>Node.js (Passport.js)| [Node.js and passport-azure-ad using on behalf of](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions) の Web API (サービス) |

## <a name="other-microsoft-graph-samples"></a>Microsoft Graph のその他のサンプル

Azure AD での認証を含む、Microsoft Graph API のさまざまな使用パターンを示す[サンプル](https://github.com/microsoftgraph/msgraph-community-samples/tree/master/samples#aspnet)とチュートリアルについては、[Microsoft Graph コミュニティのサンプルとチュートリアル](https://github.com/microsoftgraph/msgraph-community-samples)をご覧ください。

## <a name="see-also"></a>関連項目

[Microsoft Graph API の概念とリファレンス](/graph/use-the-api?context=graph%2fapi%2fbeta&view=graph-rest-beta&preserve-view=true)
