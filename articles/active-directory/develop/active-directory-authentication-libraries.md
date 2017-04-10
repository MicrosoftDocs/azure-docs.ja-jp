---
title: "Azure Active Directory 認証ライブラリ | Microsoft Docs"
description: "Azure AD 認証ライブラリ (ADAL) を使用すると、クライアント アプリケーション開発者は、クラウドまたはオンプレミスの Active Directory (AD) に対して簡単にユーザーを認証し、API 呼び出しを保護するためのアクセス トークンを取得できます。"
services: active-directory
documentationcenter: 
author: bryanla
manager: mbaldwin
editor: mbaldwin
ms.assetid: 2e4fc79a-0285-40be-8c77-65edee408a22
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/13/2017
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 2ad287d383f65ea66382d1f449a76db596963f46
ms.lasthandoff: 03/29/2017


---
# <a name="azure-active-directory-authentication-libraries"></a>Azure Active Directory 認証ライブラリ
Azure AD 認証ライブラリ (ADAL) を使用すると、クライアント アプリケーション開発者は、クラウドまたはオンプレミスの Active Directory (AD) に対して簡単にユーザーを認証し、API 呼び出しを保護するためのアクセス トークンを取得できます。 ADAL には、非同期のサポート、アクセス トークンと更新トークンを格納する構成可能なトークン キャッシュ、アクセス トークンの有効期限が切れたときに更新トークンを使用できる場合のトークンの自動更新など、開発者向けに認証を容易にする多くの機能が用意されています。 複雑な部分のほとんどが ADAL によって処理されるため、開発者はアプリケーションでビジネス ロジックに集中し、セキュリティの専門家でなくても簡単にリソースを保護できます。

ADAL は、さまざまなプラットフォームで使用できます。

### <a name="client-libraries"></a>クライアント ライブラリ

| プラットフォーム | ライブラリ | ダウンロード | ソース コード | サンプル | リファレンス
| --- | --- | --- | --- | --- | --- |
| .NET クライアント、Windows ストア、UWP、Xamarin iOS、Android |ADAL .NET v3 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet) | [デスクトップ アプリ](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-devquickstarts-dotnet) |[リファレンス](https://docs.microsoft.com/active-directory/adal/microsoft.identitymodel.clients.activedirectory) | 
| .NET クライアント、Windows ストア、Windows Phone 8.1 |ADAL .NET v2 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/2.28.2) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/releases/tag/v2.28.2) | [デスクトップ アプリ](https://github.com/AzureADQuickStarts/NativeClient-DotNet/releases/tag/v2.X) |[リファレンス](https://docs.microsoft.com/en-us/active-directory/adal//v2/microsoft.identitymodel.clients.activedirectory) | 
| JavaScript |ADAL.js |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[シングル ページ アプリケーション](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) | |
| iOS、macOS |ADAL |[CocoaPods](http://cocoadocs.org/docsets/ADAL/) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-objc) |[iOS アプリ](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-devquickstarts-ios) | [リファレンス](http://cocoadocs.org/docsets/ADAL/)|
| Android |ADAL |[中央リポジトリ](http://search.maven.org/remotecontent?filepath=com/microsoft/aad/adal/) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-android) |[Android アプリ](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-devquickstarts-android) | [JavaDocs](http://javadoc.io/doc/com.microsoft.aad/adal/)|
| Node.js |ADAL |[npm](https://www.npmjs.com/package/adal-node) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-nodejs) | | |
| Java |ADAL4J |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-java) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-java) |[Java Web アプリ](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-devquickstarts-webapp-java) | |

### <a name="server-libraries"></a>サーバー ライブラリ 

| プラットフォーム | ライブラリ | ダウンロード | ソース コード | サンプル | リファレンス
| --- | --- | --- | --- | --- | --- |
| .NET |OWIN for AzureAD|[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.ActiveDirectory/) |[CodePlex](http://katanaproject.codeplex.com) |[MVC アプリ](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-devquickstarts-webapp-dotnet) | |
| .NET |OWIN for OpenIDConnect |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect) |[CodePlex](http://katanaproject.codeplex.com) |[Web アプリ](https://github.com/AzureADSamples/WebApp-OpenIDConnect-DotNet) | |
| Node.js |Azure AD Passport |[npm](https://www.npmjs.com/package/passport-azure-ad) |[GitHub](https://github.com/AzureAD/passport-azure-ad) | [Web API](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-devquickstarts-webapi-nodejs)| |
| .NET |OWIN for WS-Federation |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) |[CodePlex](http://katanaproject.codeplex.com) |[MVC Web アプリ](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet) | |
| .NET |Identity Protocol Extensions for .NET 4.5 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Protocol.Extensions) |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| .NET |JWT Handler for .NET 4.5 |[NuGet](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt) |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |



## <a name="scenarios"></a>シナリオ
ADAL を認証に使用できる 3 つの一般的なシナリオを以下に示します。  

### <a name="authenticating-users-of-a-client-application-to-a-remote-resource"></a>リモート リソースに対してクライアント アプリケーションのユーザーを認証する
このシナリオでは、開発者は Azure AD によって保護されたリモート リソース (Web API など) にアクセスする必要があるクライアント (WPF アプリケーションなど) を使用しています。 また、Azure サブスクリプションを持ち、ダウンストリーム Web API を呼び出す方法と、Web API が使用する Azure AD テナントを知っています。 そのため、ADAL を使用して、ADAL に認証エクスペリエンスを完全に委任するか、ユーザー資格情報を明示的に処理することで、Azure AD による認証を容易にすることができます。 ADAL により、ユーザーを認証し、Azure AD からアクセス トークンと更新トークンを取得し、そのアクセス トークンを使用して Web API への要求を行うことが容易になります。

Azure AD に対する認証を使用してこのシナリオを実証するコード サンプルについては、 [ネイティブ クライアント WPF アプリケーションと Web API](https://github.com/azureadsamples/nativeclient-dotnet)に関するページをご覧ください。

### <a name="authenticating-a-server-application-to-a-remote-resource"></a>リモート リソースに対してサーバー アプリケーションを認証する
このシナリオでは、開発者は Azure AD によって保護されたリモート リソース (Web API など) にアクセスする必要がある、サーバー上で実行されるアプリケーションを使用しています。 また、Azure サブスクリプションを持ち、ダウンストリーム サービスを呼び出す方法と、Web API が使用する Azure AD テナントを知っています。 そのため、ADAL を使用して、アプリケーションの資格情報を明示的に処理することで、Azure AD による認証を容易にすることができます。 ADAL により、アプリケーションのクライアント資格情報を使用して Azure AD からトークンを取得し、そのトークンを使用して Web API への要求を行うことが容易になります。 また、ADAL はアクセス トークンをキャッシュしてその有効期間を管理し、必要に応じて更新します。 このシナリオを実証するコード サンプルについては、 [コンソール アプリケーションと Web API](https://github.com/AzureADSamples/Daemon-DotNet)に関するページをご覧ください。

### <a name="authenticating-a-server-application-on-behalf-of-a-user-to-access-a-remote-resource"></a>リモート リソースにアクセスするために、ユーザーに代わってサーバー アプリケーションを認証する
このシナリオでは、開発者は Azure AD によって保護されたリモート リソース (Web API など) にアクセスする必要がある、サーバー上で実行されるアプリケーションを使用しています。 また、Azure AD でユーザーに代わって要求を行う必要があります。 開発者は Azure サブスクリプションを持ち、ダウンストリーム Web API を呼び出す方法と、サービスが使用する Azure AD テナントを知っています。 ユーザーが Web アプリケーションに対して認証されると、アプリケーションは Azure AD からユーザーの認証コードを取得できます。 その後、Web アプリケーションは、ADAL を使用して、アプリケーションに関連付けられた認証コードとクライアント資格情報を使って、ユーザーの代わりに Azure AD からアクセス トークンと更新トークンを取得できます。 アクセス トークンを所有している Web アプリケーションは、トークンの有効期限が切れるまで Web API を呼び出すことができます。 トークンの有効期限が切れたら、Web アプリケーションは ADAL を使用して、以前に取得した更新トークンを使って新しいアクセス トークンを取得できます。

## <a name="see-also"></a>関連項目
[Azure Active Directory 開発者ガイド](active-directory-developers-guide.md)

[Azure AD の認証シナリオ](active-directory-authentication-scenarios.md)

[Azure Active Directory のコード例](active-directory-code-samples.md)

