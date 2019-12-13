---
title: Azure Active Directory 認証ライブラリ | Microsoft Docs
description: Azure AD 認証ライブラリ (ADAL) を使用すると、クライアント アプリケーション開発者は、クラウドまたはオンプレミスの Active Directory (AD) に対して簡単にユーザーを認証し、API 呼び出しを保護するためのアクセス トークンを取得できます。
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 2e4fc79a-0285-40be-8c77-65edee408a22
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 12/01/2018
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: c391120ccf9fafd4ca51489d7849e838d77a1dbf
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74845503"
---
# <a name="azure-active-directory-authentication-libraries"></a>Azure Active Directory 認証ライブラリ

Azure Active Directory 認証ライブラリ (ADAL) v1.0 を使用すると、アプリケーション開発者は、クラウドまたはオンプレミスの Active Directory (AD) に対してユーザーを認証し、API 呼び出しを保護するためのトークンを取得できます。 ADAL の次の機能を利用すると、開発者にとって認証が簡単になります。

- アクセス トークンと更新トークンを保存する構成可能なトークン キャッシュ
- アクセス トークンの期限が切れ、更新トークンが利用可能になったときの自動トークン更新
- 非同期メソッド呼び出しのサポート

> [!NOTE]
> Azure AD v2.0 ライブラリ (MSAL) をお探しの場合には、 [MSAL ライブラリ ガイド](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries)を参照してください。
>
>

## <a name="microsoft-supported-client-libraries"></a>Microsoft がサポートするクライアント ライブラリ

| プラットフォーム | ライブラリ | ダウンロード | ソース コード | サンプル | リファレンス
| --- | --- | --- | --- | --- | --- |
| .NET クライアント、Windows ストア、UWP、Xamarin iOS、Android |ADAL .NET v3 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet) | [デスクトップ アプリ](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-dotnet) |[リファレンス](https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.clients.activedirectory?view=azure-dotnet) |
| .NET クライアント、Windows ストア、Windows Phone 8.1 |ADAL .NET v2 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/2.28.4) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/releases/tag/v2.28.4) | [デスクトップ アプリ](https://github.com/AzureADQuickStarts/NativeClient-DotNet/releases/tag/v2.X) | |
| JavaScript |ADAL.js |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[シングルページ アプリ](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) | |
| iOS、macOS |ADAL |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-objc/releases) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-objc) |[iOS アプリ](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-ios) | [リファレンス](http://cocoadocs.org/docsets/ADAL/2.5.1/)|
| Android |ADAL |[Maven](https://search.maven.org/search?q=g:com.microsoft.aad+AND+a:adal&core=gav) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-android) |[Android アプリ](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-android) | [JavaDocs](https://javadoc.io/doc/com.microsoft.aad/adal/)|
| Node.js |ADAL |[npm](https://www.npmjs.com/package/adal-node) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-nodejs) | [Node.js Web アプリ](https://github.com/Azure-Samples/active-directory-node-webapp-openidconnect)|[リファレンス](https://docs.microsoft.com/javascript/api/overview/azure/activedirectory) |
| Java |ADAL4J |[Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3Aadal4j%20g%3Acom.microsoft.azure) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-java) |[Java Web アプリ](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect) |[リファレンス](https://javadoc.io/doc/com.microsoft.azure/adal4j) |
| Python |ADAL |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-python) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-python) |[Python Web アプリ](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi) |[リファレンス](https://adal-python.readthedocs.io/) |

## <a name="microsoft-supported-server-libraries"></a>Microsoft がサポートするサーバー ライブラリ

| プラットフォーム | ライブラリ | ダウンロード | ソース コード | サンプル | リファレンス
| --- | --- | --- | --- | --- | --- |
| .NET |OWIN for AzureAD|[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.ActiveDirectory/) |[GitHub](https://github.com/aspnet/AspNetKatana/tree/dev/src/Microsoft.Owin.Security.ActiveDirectory) |[MVC アプリ](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-webapp-dotnet) | |
| .NET |OWIN for OpenIDConnect |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect) |[GitHub](https://github.com/aspnet/AspNetKatana/tree/dev/src/Microsoft.Owin.Security.OpenIdConnect) |[Web アプリ](https://github.com/AzureADSamples/WebApp-OpenIDConnect-DotNet) | |
| .NET |OWIN for WS-Federation |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) |[GitHub](https://github.com/aspnet/AspNetKatana/tree/dev/src/Microsoft.Owin.Security.WsFederation) |[MVC Web アプリ](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet) | |
| .NET |Identity Protocol Extensions for .NET 4.5 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Protocol.Extensions) |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| .NET |JWT Handler for .NET 4.5 |[NuGet](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt) |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| Node.js |Azure AD Passport |[npm](https://www.npmjs.com/package/passport-azure-ad) |[GitHub](https://github.com/AzureAD/passport-azure-ad) | [Web API](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-webapi-nodejs)| |

## <a name="scenarios"></a>シナリオ

リモート リソースにアクセスするクライアントで ADAL を使用する 3 つの一般的なシナリオを次に示します。

### <a name="authenticating-users-of-a-native-client-application-running-on-a-device"></a>デバイスで実行しているネイティブ クライアント アプリケーションのユーザーを認証する

このシナリオでは、開発者はリモート リソース (Web API など) にアクセスする必要があるモバイル クライアントまたはデスクトップ アプリケーションを使用しています。 Web API は、匿名の呼び出しを許可しないので、認証されたユーザーのコンテキストで呼び出す必要があります。 Web API は、指定の Azure AD テナントによって発行されたアクセス トークンを信頼するように事前構成されています。 Azure AD は、そのリソースに対するアクセス トークンを発行するように事前構成されています。 クライアントから Web API を呼び出すのに、開発者は Azure AD での認証を容易にするために ADAL を使用します。 ADAL を使用する最も安全な方法は、ユーザーの資格情報を収集するためのユーザー インターフェイスをレンダリング (ブラウザー ウィンドウとしてレンダリング) することです。

ADAL により、ユーザーを認証し、Azure AD からアクセス トークンと更新トークンを取得し、そのアクセス トークンを使用して Web API を呼び出すのが容易になります。

Azure AD に対する認証を使用してこのシナリオを実証するコード サンプルについては、 [ネイティブ クライアント WPF アプリケーションと Web API](https://github.com/azureadsamples/nativeclient-dotnet)に関するページをご覧ください。

### <a name="authenticating-a-confidential-client-application-running-on-a-web-server"></a>Web サーバーで実行している機密クライアント アプリケーションを認証する

このシナリオでは、開発者はリモート リソース (Web API など) にアクセスする必要がある、サーバー上で実行されるアプリケーションを使用しています。 Web API は、匿名の呼び出しを許可しないので、承認されたサービスから呼び出す必要があります。 Web API は、指定の Azure AD テナントによって発行されたアクセス トークンを信頼するように事前構成されています。 Azure AD は、クライアントの資格情報 (クライアント ID とシークレット) を持つサービスに対するリソースへのアクセス トークンを発行するように事前構成されています。 ADAL では、Azure AD がWeb API を呼び出すのに使用されるアクセス トークンを返すことで、そのサービスの認証を容易にします。 また、ADAL はアクセス トークンをキャッシュしてその有効期間を管理し、必要に応じて更新します。 このシナリオを実証するコード サンプルについては、[デーモン コンソール アプリケーションと Web API](https://github.com/AzureADSamples/Daemon-DotNet) に関するページをご覧ください。

### <a name="authenticating-a-confidential-client-application-running-on-a-server-on-behalf-of-a-user"></a>ユーザーの代わりにサーバーで実行している機密クライアント アプリケーションを認証する

このシナリオでは、開発者はリモート リソース (Web API など) にアクセスする必要がある、サーバー上で実行される Web アプリケーションを使用しています。 Web API は、匿名の呼び出しを許可しないので、認証されたユーザーに代わって承認されたサービスから呼び出す必要があります。 Web API は、指定の Azure AD テナントによって発行されたアクセス トークンを信頼するように事前構成されています。そして、Azure AD は、クライアントの資格情報を持つサービスに対するリソースへのアクセス トークンを発行するように事前構成されています。 ユーザーが Web アプリケーションで認証されると、アプリケーションは Azure AD からユーザーの認証コードを取得できます。 その後、Web アプリケーションは、ADAL を使用して、アプリケーションに関連付けられた認証コードとクライアント資格情報を使って、ユーザーの代わりに Azure AD からアクセス トークンと更新トークンを取得できます。 アクセス トークンを所有している Web アプリケーションは、トークンの有効期限が切れるまで Web API を呼び出すことができます。 トークンの有効期限が切れたら、Web アプリケーションは ADAL を使用して、以前に取得した更新トークンを使って新しいアクセス トークンを取得できます。 このシナリオを実証するコード サンプルについては、[ネイティブ クライアントが Web API を呼び出し、その Web API がさらに別の Web API を呼び出すしくみ](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof)に関するページをご覧ください。

## <a name="see-also"></a>関連項目

- [Azure Active Directory 開発者ガイド](v1-overview.md)
- [Azure AD の認証シナリオ](v1-authentication-scenarios.md)
- [Azure Active Directory のコード例](sample-v1-code.md)
