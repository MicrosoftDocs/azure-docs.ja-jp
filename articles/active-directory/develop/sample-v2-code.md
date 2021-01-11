---
title: Microsoft ID プラットフォームのコード サンプル
description: シナリオ別に整理された、利用可能な Microsoft ID プラットフォーム (v2.0 エンドポイント) のコード サンプルのインデックスを提供します。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: sample
ms.workload: identity
ms.date: 06/01/2020
ms.author: marsma
ms.reviewer: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 142037366e9309f8087a2e42fa46cd0b4137f6b8
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/11/2020
ms.locfileid: "88119404"
---
# <a name="microsoft-identity-platform-code-samples-v20-endpoint"></a>Microsoft ID プラットフォームのコード サンプル (v2.0 エンドポイント)

Microsoft ID プラットフォームを使用して、次の操作を行うことができます。

- Web アプリケーション Web API に認証と承認を追加する。
- 保護された Web API にアクセスするためにアクセス トークンを要求する。

この記事では、Microsoft ID プラットフォーム エンドポイントについて簡単に説明し、サンプルへのリンクを提供します。 これらのサンプルでは、その実行方法を示し、アプリケーションで使用できるコード スニペットを提供します。 サンプル コードのページには、要件、インストール、設定に役立つ詳細な Readme のトピックが含まれています。 コード内のコメントは、重要なセクションを理解するのに役立ちます。

> [!NOTE]
> v1.0 のサンプルに関心をお持ちの場合は、[Azure AD のサンプル コード (v1.0 エンドポイント)](../azuread-dev/sample-v1-code.md) に関するページをご覧ください。

サンプルの種類ごとの基本的なシナリオを理解するために、[Microsoft ID プラットフォーム エンドポイントのアプリの種類](v2-app-types.md)に関するページを参照してください。

GitHub でサンプルに協力することもできます。 その方法については、[Microsoft Azure Active Directory のサンプルとドキュメント](https://github.com/Azure-Samples?page=3&query=active-directory)をご覧ください。

## <a name="single-page-applications"></a>シングルページ アプリケーション

これらのサンプルでは、Microsoft ID プラットフォームを使用してセキュリティ保護されているシングルページ アプリケーションの作成方法を示します。 これらのサンプルでは、MSAL.js のいずれかの種類を使用します。

| プラットフォーム | 説明 | Link |
| -------- | --------------------- | -------- |
| ![JavaScript のロゴを示す画像](media/sample-v2-code/logo_js.png) [JavaScript (MSAL.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | SPA から Microsoft Graph を呼び出します。 |[javascript-graphapi-v2](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2) |
| ![JavaScript のロゴを示す画像](media/sample-v2-code/logo_js.png) [JavaScript (MSAL.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser) | SPA から認証コード フローと PKCE を使用して Microsoft Graph を呼び出します。 |[javascript-v2](https://github.com/Azure-Samples/ms-identity-javascript-v2) |
| ![JavaScript のロゴを示す画像](media/sample-v2-code/logo_js.png) [JavaScript (MSAL.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | SPA から B2C を呼び出します。 |[b2c-javascript-msal-singlepageapp](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp) |
| ![Angular のロゴを示す画像](media/sample-v2-code/logo_angular.png) [JavaScript (MSAL-Angular)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)| SPA から Microsoft Graph を呼び出します。  | [active-directory-javascript-singlepageapp-angular](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular) |
| ![Angular のロゴを示す画像](media/sample-v2-code/logo_angular.png) [Angular (MSAL-Angular)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)| SPA からカスタム Web API を呼び出します。 | [ms-identity-javascript-angular-spa-aspnetcore-webapi](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnetcore-webapi) |
| ![Angular のロゴを示す画像](media/sample-v2-code/logo_angular.png) [Angular (MSAL-Angular)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular) | SPA から B2C を呼び出します。 |[active-directory-b2c-javascript-angular-spa](https://github.com/Azure-Samples/active-directory-b2c-javascript-angular-spa) |
| ![React のロゴを示す画像](media/sample-v2-code/logo_react.png) [React (MSAL.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core)| SPA からカスタム Web API を呼び出し、そこから Microsoft Graph を呼び出します。  | [ms-identity-javascript-react-spa-dotnetcore-webapi-obo](https://github.com/Azure-Samples/ms-identity-javascript-react-spa-dotnetcore-webapi-obo) |
| ![Angular のロゴを示す画像](media/sample-v2-code/logo_angular.png) [Angular (MSAL-Angular)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular) | SPA からアプリのロールとセキュリティ グループを使用してカスタム Web API を呼び出します。 |[ms-identity-javascript-angular-spa-dotnetcore-webapi-roles-groups](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-dotnetcore-webapi-roles-groups) |

## <a name="web-applications"></a>Web アプリケーション

次のサンプルは、ユーザーがサインインする Web アプリケーションを示しています。 サンプルの中には、Microsoft Graph、またはユーザーの ID を使用してユーザー独自の Web API を呼び出すアプリケーションを示しているものもあります。

| プラットフォーム | ユーザーのサインインのみ | ユーザーのサインインと Microsoft Graph の呼び出し |
| -------- | ------------------- | --------------------------------- |
| ![ASP.NET Core のロゴを示す画像](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core | [ASP.NET Core Web アプリのユーザーのサインインに関するチュートリアル](https://aka.ms/aspnetcore-webapp-sign-in) | [ASP.NET Core Web アプリが Microsoft Graph を呼び出す](https://aka.ms/aspnetcore-webapp-call-msgraph)フェーズと同じサンプル |
| ![ASP.NET のロゴを示す画像](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET | [ASP.NET クイック スタート](https://github.com/AzureAdQuickstarts/AppModelv2-WebApp-OpenIDConnect-DotNet) </p> [dotnet-webapp-openidconnect-v2](https://github.com/azure-samples/active-directory-dotnet-webapp-openidconnect-v2)  |  [dotnet-admin-restricted-scopes-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2) </p> |[msgraph-training-aspnetmvcapp](https://github.com/microsoftgraph/msgraph-training-aspnetmvcapp)
| ![Java のロゴを示す画像](media/sample-v2-code/logo_java.png)  |                   | [ms-identity-java-webapp](https://github.com/Azure-Samples/ms-identity-java-webapp) |
| ![Python のロゴを示す画像](media/sample-v2-code/logo_python.png)  |                   | [ms-identity-python-webapp](https://github.com/Azure-Samples/ms-identity-python-webapp) |
| ![Ruby のロゴを示す画像](media/sample-v2-code/logo_ruby.png) |                   | [msgraph-training-rubyrailsapp](https://github.com/microsoftgraph/msgraph-training-rubyrailsapp) |

## <a name="desktop-and-mobile-public-client-apps"></a>デスクトップおよびモバイルのパブリック クライアント アプリ

次のサンプルは、ユーザーの名前で Microsoft Graph API またはお客様独自の Web API にアクセスするパブリック クライアント アプリケーション (デスクトップまたはモバイル アプリケーション) を示しています。 "*WAM を使用するデスクトップ (コンソール)* " を除き、これらのすべてのクライアント アプリケーションで Microsoft Authentication Library (MSAL) を使用します。

| クライアント アプリケーション | プラットフォーム | フロー/許可 | Microsoft Graph の呼び出し | ASP.NET Core Web API の呼び出し |
| ------------------ | -------- |  ----------| ---------- | ------------------------- |
| デスクトップ (WPF)      | ![.NET/C# のロゴを示す画像](media/sample-v2-code/logo_NET.png) | [承認コード](msal-authentication-flows.md#authorization-code)| [dotnet-desktop-msgraph-v2](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | [dotnet-native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi) |
| デスクトップ (コンソール)   | ![.NET/C# (デスクトップ) のロゴを示す画像](media/sample-v2-code/logo_NET.png) | [統合 Windows 認証](msal-authentication-flows.md#integrated-windows-authentication) | [dotnet-iwa-v2](https://github.com/azure-samples/active-directory-dotnet-iwa-v2) |  |
| デスクトップ (コンソール)   | ![Java のロゴを示す画像](media/sample-v2-code/logo_java.png) | [統合 Windows 認証](msal-authentication-flows.md#integrated-windows-authentication) |[ms-identity-java-desktop](https://github.com/Azure-Samples/ms-identity-java-desktop/) |  |
| デスクトップ (コンソール)   | ![.NET/C# (デスクトップ) のロゴを示す画像](media/sample-v2-code/logo_NETcore.png) | [ユーザー名/パスワード](msal-authentication-flows.md#usernamepassword) |[dotnetcore-up-v2](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2) |  |
| WAM を使用するデスクトップ (コンソール)  | ![.NET/C# (デスクトップ) のロゴを示す画像](media/sample-v2-code/logo_NETcore.png) | [Web アカウント マネージャー](/windows/uwp/security/web-account-manager) (WAM) による対話型 |[dotnet-native-uwp-wam](https://github.com/azure-samples/active-directory-dotnet-native-uwp-wam) |  |
| デスクトップ (コンソール)   | ![Java のロゴを示す画像](media/sample-v2-code/logo_java.png) | [ユーザー名/パスワード](msal-authentication-flows.md#usernamepassword) |[ms-identity-java-desktop](https://github.com/Azure-Samples/ms-identity-java-desktop/) |  |
| デスクトップ (コンソール)   | ![Python のロゴを示す画像](media/sample-v2-code/logo_python.png) | [ユーザー名/パスワード](msal-authentication-flows.md#usernamepassword) |[ms-identity-python-desktop](https://github.com/Azure-Samples/ms-identity-python-desktop) |  |
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
| Web アプリ | ![ASP.NET のロゴを示す画像](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET  | [クライアントの資格情報](msal-authentication-flows.md#client-credentials) | [dotnet-daemon-v2](https://github.com/azure-samples/active-directory-dotnet-daemon-v2) |
| コンソール | ![Java のロゴを示す画像](media/sample-v2-code/logo_java.png) | [クライアントの資格情報](msal-authentication-flows.md#client-credentials) | [ms-identity-java-daemon](https://github.com/Azure-Samples/ms-identity-java-daemon) |
| コンソール | ![Python のロゴを示す画像](media/sample-v2-code/logo_python.png) | [クライアントの資格情報](msal-authentication-flows.md#client-credentials) | [ms-identity-python-daemon](https://github.com/Azure-Samples/ms-identity-python-daemon) |

## <a name="headless-applications"></a>ヘッドレス アプリケーション

次のサンプルは、Web ブラウザーがないデバイスで実行されるパブリック クライアント アプリケーションを示しています。 このアプリは、コマンド ライン ツール、Linux または Mac 上で実行されるアプリ、あるいは IoT アプリケーションにすることもできます。 このサンプルは、別のデバイス (携帯電話など) で対話的にサインインするユーザーの名前で Microsoft Graph API にアクセスするアプリの特徴を示しています。 このクライアント アプリケーションでは、Microsoft Authentication Library (MSAL) を使用します。

| クライアント アプリケーション | プラットフォーム | フロー/許可 | Microsoft Graph の呼び出し |
| ------------------ | -------- |  ----------| ---------- |
| デスクトップ (コンソール)   | ![.NET/C# (デスクトップ) のロゴを示す画像](media/sample-v2-code/logo_NETcore.png) | [デバイス コード フロー](msal-authentication-flows.md#device-code) |[dotnetcore-devicecodeflow-v2](https://github.com/azure-samples/active-directory-dotnetcore-devicecodeflow-v2) |
| デスクトップ (コンソール)   | ![Java のロゴを示す画像](media/sample-v2-code/logo_java.png) | [デバイス コード フロー](msal-authentication-flows.md#device-code) |[ms-identity-java-devicecodeflow](https://github.com/Azure-Samples/ms-identity-java-devicecodeflow) |
| デスクトップ (コンソール)   | ![Python のロゴを示す画像](media/sample-v2-code/logo_python.png) | [デバイス コード フロー](msal-authentication-flows.md#device-code) |[ms-identity-python-devicecodeflow](https://github.com/Azure-Samples/ms-identity-python-devicecodeflow) |

## <a name="multi-tenant-saas-applications"></a>マルチテナント SaaS アプリケーション

次のサンプルでは、任意の Azure Active Directory (Azure AD) テナントからのサインインを受け付けるようにアプリケーションを構成する方法を示します。 アプリケーションを "*マルチテナント*" として構成すると、**サービスとしてのソフトウェア** (SaaS) アプリケーションを多数の組織に提供でき、ユーザーはアカウントの使用に同意した後でアプリケーションにサインインできるようになります。

| プラットフォーム | 説明 | Link |
| -------- | --------------------- | -------- |
| ![JavaScript のロゴを示す画像](media/sample-v2-code/logo_js.png) [JavaScript (MSAL.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser) | マルチテナント SPA で Graph API を呼び出す |[ms-identity-javascript-angular-spa-aspnet-webapi-multitenant](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnet-webapi-multitenant/tree/master/Chapter1) |
| ![Angular のロゴを示す画像](media/sample-v2-code/logo_angular.png) [Angular (MSAL-Angular)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular) | マルチテナント SPA からマルチテナント カスタム Web API を呼び出します。 |[ms-identity-javascript-angular-spa-aspnet-webapi-multitenant](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnet-webapi-multitenant/tree/master/Chapter2) |
| ![ASP.NET Core のロゴを示す画像](media/sample-v2-code/logo_NETcore.png) [.NET Core (MSAL.NET)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | ASP.NET Core MVC Web アプリケーションで Graph API を呼び出す |[active-directory-aspnetcore-webapp-openidconnect-v2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-3-Multi-Tenant) |
| ![ASP.NET Core のロゴを示す画像](media/sample-v2-code/logo_NETcore.png) [.NET Core (MSAL.NET)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | ASP.NET Core MVC Web アプリケーションで ASP.NET Core Web API を呼び出す |[active-directory-aspnetcore-webapp-openidconnect-v2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/4-WebApp-your-API/4-3-AnyOrg) |

## <a name="web-apis"></a>Web API

次のサンプルは、Microsoft ID プラットフォーム エンドポイントで Web API を保護する方法と、その Web API からダウンストリーム API を呼び出す方法を示しています。

| プラットフォーム | サンプル |
| -------- | ------------------- |
| ![ASP.NET Core のロゴを示す画像](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core | [dotnet-native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi-calls-msgraph) の ASP.NET Core Web API (サービス)  |
| ![ASP.NET のロゴを示す画像](media/sample-v2-code/logo_NET.png)</p>ASP.NET MVC | [ms-identity-aspnet-webapi-onbehalfof](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof) の Web API (サービス) |
| ![Java のロゴを示す画像](media/sample-v2-code/logo_java.png) | [ms-identity-java-webapi](https://github.com/Azure-Samples/ms-identity-java-webapi) の Web API (サービス) |
| ![Node.js のロゴを示す画像](media/sample-v2-code/logo_nodejs.png) | [active-directory-javascript-nodejs-webapi-v2](https://github.com/Azure-Samples/active-directory-javascript-nodejs-webapi-v2) の Web API (サービス) |
| ![Node.js のロゴを示す画像](media/sample-v2-code/logo_nodejs.png) | [active-directory-b2c-javascript-nodejs-webapi](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) の B2C Web API (サービス) |

## <a name="azure-functions-as-web-apis"></a>Web API としての Azure Functions

次のサンプルは、HttpTrigger を使用し、Microsoft ID プラットフォーム エンドポイントで Web API を公開して Azure Function を保護する方法と、その Web API からダウンストリーム API を呼び出す方法を示しています。

| プラットフォーム | サンプル |
| -------- | ------------------- |
| ![ASP.NET Core のロゴを示す画像](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core | [dotnet-native-aspnetcore-v2](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions) の ASP.NET Core Web API (サービス) Azure Function  |
| ![Node.js のロゴを示す画像](media/sample-v2-code/logo_nodejs.png)</p>NodeJS | [NodeJS and passport-azure-ad](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions) の Web API (サービス) |
| ![Python のロゴを示す画像](media/sample-v2-code/logo_python.png)</p>Python | [Python](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions) の Web API (サービス) |
| ![Node.js のロゴを示す画像](media/sample-v2-code/logo_nodejs.png)</p>NodeJS | [NodeJS and passport-azure-ad using on behalf of](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions) の Web API (サービス) |

## <a name="other-microsoft-graph-samples"></a>Microsoft Graph のその他のサンプル

Azure AD での認証を含む、Microsoft Graph API のさまざまな使用パターンを示す[サンプル](https://github.com/microsoftgraph/msgraph-community-samples/tree/master/samples#aspnet)とチュートリアルについては、[Microsoft Graph コミュニティのサンプルとチュートリアル](https://github.com/microsoftgraph/msgraph-community-samples)をご覧ください。

## <a name="see-also"></a>関連項目

- [Azure Active Directory (v1.0) 開発者のガイド](../azuread-dev/v1-overview.md)
- [Microsoft Graph API の概念とリファレンス](/graph/use-the-api?context=graph%2fapi%2fbeta&view=graph-rest-beta)