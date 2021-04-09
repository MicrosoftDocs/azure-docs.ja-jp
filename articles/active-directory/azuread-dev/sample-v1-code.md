---
title: Azure Active Directory v1.0 のコード サンプル | Microsoft Docs
description: シナリオ別に整理された Azure Active Directory (v1.0 エンドポイント) のコード サンプルのインデックスを提供します。
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: sample
ms.workload: identity
ms.date: 07/15/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 662a0f53a5d5e8e02ef3b29640009fd07bf4f1d0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97652146"
---
# <a name="azure-active-directory-code-samples-v10-endpoint"></a>Azure Active Directory のコード サンプル (v1.0 エンドポイント)

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Microsoft Azure Active Directory (Azure AD) を使用すると、Web アプリケーションおよび Web API に認証と承認を追加できます。

このセクションでは、Azure AD v1.0 エンドポイントの詳細を学ぶために使用できるサンプルへのリンクを提供します。 これらのサンプルでは、その実行方法と、アプリケーションで使用できるコード スニペットを示します。 コード サンプルのページには、要件、インストール、設定に関する詳細な Readme トピックが含まれています。 また、重要なセクションの理解に役立つように、コードにはコメントが付けられています。

> [!NOTE]
> Azure AD V2 のコード サンプルに関心がある場合は、「[シナリオ別の v2.0 コード サンプル](../develop/sample-v2-code.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)」をご覧ください。

各サンプル タイプの基本的なシナリオを理解するには、「[Azure AD の認証シナリオ](v1-authentication-scenarios.md)」をご覧ください。

GitHub でサンプルに協力することもできます。 その方法については、[Microsoft Azure Active Directory のサンプルとドキュメント](https://github.com/Azure-Samples?page=3&query=active-directory)をご覧ください。

## <a name="single-page-applications"></a>シングルページ アプリケーション

このサンプルでは、Azure AD を使用してセキュリティ保護されているシングルページ アプリケーションの作成方法を示します。

| プラットフォーム | 独自 API の呼び出し | 別の Web API の呼び出し |
|--|--|--|
| ![JavaScript のロゴを示す画像](media/sample-v2-code/logo-js.png) | [javascript-singlepageapp](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |
| ![Angular JS のロゴを示す画像](media/sample-v2-code/logo-angular.png) | [angularjs-singlepageapp](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp) | [angularjs-singlepageapp-cors](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp-dotnet-webapi) |

## <a name="web-applications"></a>Web アプリケーション

### <a name="web-applications-signing-in-users-calling-microsoft-graph-or-a-web-api-with-the-users-identity"></a>Web アプリケーションでのユーザーのサインインと、ユーザーの ID による Microsoft Graph または Web API の呼び出し

次のサンプルでは、ユーザーがサインインする Web アプリケーションを示しています。 これらのアプリケーションの中には、サインインしたユーザーの名前で Microsoft Graph または独自の Web API も呼び出すものがあります。

| プラットフォーム | ユーザーのサインインのみ | Microsoft Graph の呼び出し | 別の ASP.NET または ASP.NET Core 2.0 Web API の呼び出し |
|--|--|--|--|
| ![ASP.NET Core のロゴを示す画像](media/sample-v2-code/logo-netcore.png)</p>ASP.NET Core 2.0 | [dotnet-webapp-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore) | [webapp-webapi-multitenant-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-webapp-webapi-multitenant-openidconnect-aspnetcore/) </p>(AAD Graph) | [dotnet-webapp-webapi-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-openidconnect-aspnetcore) |
| ![この画像は ASP.NET Framework ロゴを示しています](media/sample-v2-code/logo-netframework.png)</p> ASP.NET 4.5 | </p> [webapp-WSFederation-dotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation) </p> [dotnet-webapp-webapi-oauth2-useridentity](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-useridentity) | [dotnet-webapp-multitenant-openidconnect](https://github.com/Azure-Samples/active-directory-dotnet-webapp-multitenant-openidconnect)</p> (AAD Graph) |
| ![Python のロゴを示す画像](media/sample-v2-code/logo-python.png) |  | [python-webapp-graphapi](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi) |
| ![Java のロゴを示す画像](media/sample-v2-code/logo-java.png) |  | [java-webapp-openidconnect](https://github.com/azure-samples/active-directory-java-webapp-openidconnect) |
| ![PHP のロゴを示す画像](media/sample-v2-code/logo-php.png) |  | [php-graphapi-web](https://github.com/Azure-Samples/active-directory-php-graphapi-web) |

### <a name="web-applications-demonstrating-role-based-access-control-authorization"></a>ロールベースのアクセス制御 (承認) を示す Web アプリケーション

次のサンプルは、ロールベースのアクセス制御 (RBAC) を実装する方法を示しています。 RBAC は、Web アプリケーションの特定の機能のアクセス許可を特定のユーザーに制限するために使用されます。 **Azure AD グループ** に属しているか、特定のアプリケーション **ロール** が割り当てられているかに応じて、ユーザーには権限が与えられます。

| プラットフォーム | サンプル |
|--|--|
| ![この画像は ASP.NET Framework ロゴを示しています](media/sample-v2-code/logo-netframework.png)</p> ASP.NET 4.5 | [dotnet-webapp-groupclaims](https://github.com/Azure-Samples/active-directory-dotnet-webapp-groupclaims) </p>  [dotnet-webapp-roleclaims](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims) | Azure AD の **ロール** を承認に使用する .NET 4.5 MVC Web アプリ |

## <a name="desktop-and-mobile-public-client-applications-calling-microsoft-graph-or-a-web-api"></a>Microsoft Graph または Web API を呼び出すデスクトップおよびモバイルのパブリック クライアント アプリケーション

次のサンプルは、ユーザーの名前で Microsoft Graph または Web API にアクセスするパブリック クライアント アプリケーション (デスクトップ/モバイル アプリケーション) を示しています。 デバイスとプラットフォームに応じて、アプリケーションはさまざまな方法でユーザーにログインできます (フロー/許可)。

- 対話型
- サイレント型 (Windows の統合 Windows 認証、またはユーザー名/パスワードを使用)
- 対話型サインインの別のデバイスへの委任 (Web コントロールを提供しないデバイスで使用されるデバイス コード フロー)

クライアント アプリケーション | プラットフォーム | フロー/許可 | Microsoft Graph の呼び出し | ASP.NET または ASP.NET Core 2.x Web API の呼び出し
------------------ | -------- | ---------- | -------------------- | -------------------------
デスクトップ (WPF)           | ![.NET/C# のロゴを示す画像](media/sample-v2-code/logo-net.png)  | Interactive | [dotnet-native-multitarget](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) の一部 | [Dotnet-native-desktop](https://github.com/Azure-Samples/active-directory-dotnet-native-desktop) </p> [dotnet-native-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore/)</p> [dotnet-webapi-manual-jwt-validation](https://github.com/azure-samples/active-directory-dotnet-webapi-manual-jwt-validation)
モバイル (UWP)            | .![.NET/C#/UWP を示す画像](media/sample-v2-code/logo-windows.png)   | Interactive | [dotnet-native-uwp-wam](https://github.com/azure-samples/active-directory-dotnet-native-uwp-wam) </p> このサンプルでは、[ADAL.NET](https://aka.ms/adalnet) ではなく [WAM](/windows/uwp/security/web-account-manager) を使用します|  [dotnet-windows-store](https://github.com/Azure-Samples/active-directory-dotnet-windows-store) (ADAL.NET を使用してシングル テナント Web API を呼び出す UWP アプリケーション) </p> [dotnet-webapi-multitenant-windows-store](https://github.com/Azure-Samples/active-directory-dotnet-webapi-multitenant-windows-store) (ADAL.NET を使用してマルチテナント Web API を呼び出す UWP アプリケーション)|
モバイル (Android、iOS、UWP)   | ![.NET/C# (Xamarin) を示す画像](media/sample-v2-code/logo-xamarin.png) | Interactive | [dotnet-native-multitarget](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) |
モバイル (Android)           | ![Android のロゴを示す画像](media/sample-v2-code/logo-android.png) | Interactive |   [android](https://github.com/Azure-Samples/active-directory-android) |
モバイル (iOS)           | ![iOS/Objective C または Swift を示す画像](media/sample-v2-code/logo-ios.png) | Interactive |   [nativeClient-iOS](https://github.com/azureadquickstarts/nativeclient-ios) |
デスクトップ (コンソール)          | ![.NET/C# のロゴを示す画像](media/sample-v2-code/logo-net.png) | ユーザー名/パスワード </p>  統合 Windows 認証 | | [dotnet-native-headless](https://github.com/azure-samples/active-directory-dotnet-native-headless)
デスクトップ (コンソール)          | ![Java のロゴを示す画像](media/sample-v2-code/logo-java.png) | ユーザー名/パスワード | | [java-native-headless](https://github.com/Azure-Samples/active-directory-java-native-headless)
デスクトップ (コンソール)           | ![.NET Core/C# のロゴを示す画像](media/sample-v2-code/logo-netcore.png) | デバイス コード フロー | | [dotnet-deviceprofile](https://github.com/Azure-Samples/active-directory-dotnet-deviceprofile)

## <a name="daemon-applications-accessing-web-apis-with-the-applications-identity"></a>デーモン アプリケーション (アプリケーションの ID で Web API にアクセス)

次のサンプルは、ユーザーなしで (アプリケーション ID で) Microsoft Graph または Web API にアクセスするデスクトップまたは Web アプリケーションを示しています。

クライアント アプリケーション | プラットフォーム | フロー/許可 | ASP.NET または ASP.NET Core 2.0 Web API の呼び出し
------------------ | -------- | ---------- | -------------------- 
デーモン アプリ (コンソール)          | ![この画像は .NET Framework ロゴを示しています](media/sample-v2-code/logo-netframework.png) | アプリ シークレットまたは証明書によるクライアント資格情報 | [dotnet-daemon](https://github.com/azure-samples/active-directory-dotnet-daemon)</p> [dotnet-daemon-certificate-credential](https://github.com/azure-samples/active-directory-dotnet-daemon-certificate-credential)
デーモン アプリ (コンソール)         | ![.NET Core のロゴを示す画像](media/sample-v2-code/logo-netcore.png) | 証明書によるクライアント資格情報| [dotnetcore-daemon-certificate-credential](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-certificate-credential)
ASP.NET Web アプリ  | ![この画像は .NET Framework ロゴを示しています](media/sample-v2-code/logo-netframework.png) | クライアントの資格情報 | [dotnet-webapp-webapi-oauth2-appidentity](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-appidentity)

## <a name="web-apis"></a>Web API

### <a name="web-api-protected-by-azure-active-directory"></a>Azure Active Directory によって保護される Web API

次のサンプルは、Azure AD で node.js Web API を保護する方法を示しています。

この記事の前述のセクションには、クライアント アプリケーションから ASP.NET または ASP.NET Core **Web API** を **呼び出す** 方法を示す他のサンプルも掲載されています。 これらのサンプルについては、このセクションでは触れませんが、前述または以下の表の最後の列に記載されています。

| プラットフォーム | サンプル |
|--------|-------------------|
| ![Node.js のロゴを示す画像](media/sample-v2-code/logo-nodejs.png)  | [node-webapi](https://github.com/Azure-Samples/active-directory-node-webapi) |

### <a name="web-api-calling-microsoft-graph-or-another-web-api"></a>Microsoft Graph または別の Web API を呼び出す Web API

次のサンプルでは、別の Web API を呼び出す Web API を示します。 2 番目のサンプルでは、条件付きアクセスを処理する方法を示します。

| プラットフォーム |  Microsoft Graph の呼び出し | 別の ASP.NET または ASP.NET Core 2.0 Web API の呼び出し |
| -------- |  --------------------- | ------------------------- |
| ![この画像は ASP.NET Framework ロゴを示しています](media/sample-v2-code/logo-netframework.png)</p> ASP.NET 4.5 | [dotnet-webapi-onbehalfof](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof) </p> [dotnet-webapi-onbehalfof-ca](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca) | [dotnet-webapi-onbehalfof](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof) </p> [dotnet-webapi-onbehalfof-ca](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca) |

## <a name="other-microsoft-graph-samples"></a>Microsoft Graph のその他のサンプル

Azure AD での認証を含む、Microsoft Graph API のさまざまな使用パターンを示すサンプルとチュートリアルについては、[Microsoft Graph コミュニティのサンプルとチュートリアル](https://github.com/microsoftgraph/msgraph-community-samples)をご覧ください。

## <a name="see-also"></a>参照

- [Azure Active Directory 開発者ガイド](v1-overview.md)
- [Azure Active Directory 認証ライブラリ](active-directory-authentication-libraries.md)
- [Microsoft Graph API の概念とリファレンス](/graph/use-the-api)