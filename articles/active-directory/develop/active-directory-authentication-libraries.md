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
ms.date: 08/25/2017
ms.author: bryanla
ms.custom: aaddev
ms.openlocfilehash: 1b79fb5b280b0cb4e087c2acde07796fd51e81fb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-authentication-libraries"></a>Azure Active Directory 認証ライブラリ
Azure Active Directory 認証ライブラリ (ADAL) を使用すると、アプリケーション開発者は、クラウドまたはオンプレミスの Active Directory (AD) に対してユーザーを認証し、API 呼び出しを保護するためのトークンを取得できます。 ADAL の次の機能を利用すると、開発者にとって認証が簡単になります。
 - アクセス トークンと更新トークンを保存する構成可能なトークン キャッシュ
 - アクセス トークンの期限が切れ、更新トークンが利用可能になったときの自動トークン更新
 - 非同期メソッド呼び出しのサポート
 - その他

> [!NOTE]
> Azure AD v2.0 ライブラリ (MSAL) をお探しの場合には、 [MSAL ライブラリ ガイド](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-v2-libraries)を参照してください。 
> 
> 

### <a name="client-libraries"></a>クライアント ライブラリ

| プラットフォーム | ライブラリ | ダウンロード | ソース コード | サンプル | リファレンス
| --- | --- | --- | --- | --- | --- |
| .NET クライアント、Windows ストア、UWP、Xamarin iOS、Android |ADAL .NET v3 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) |[Github](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet) | [デスクトップ アプリ](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-dotnet) |[リファレンス](https://docs.microsoft.com/dotnet/api/?view=identitymodelclientsad-3.13.9) | 
| .NET クライアント、Windows ストア、Windows Phone 8.1 |ADAL .NET v2 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/2.28.4) |[Github](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/releases/tag/v2.28.4) | [デスクトップ アプリ](https://github.com/AzureADQuickStarts/NativeClient-DotNet/releases/tag/v2.X) | | 
| JavaScript |ADAL.js |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[シングル ページ アプリケーション](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) | |
| iOS、macOS |ADAL |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-objc/releases) |[Github](https://github.com/AzureAD/azure-activedirectory-library-for-objc) |[iOS アプリ](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-ios) | [リファレンス](https://cocoapods.org/pods/ADAL)|
| Android |ADAL |[中央リポジトリ](http://search.maven.org/remotecontent?filepath=com/microsoft/aad/adal/) |[Github](https://github.com/AzureAD/azure-activedirectory-library-for-android) |[Android アプリ](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-android) | [JavaDocs](http://javadoc.io/doc/com.microsoft.aad/adal/)|
| Node.js |ADAL |[npm](https://www.npmjs.com/package/adal-node) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-nodejs) | | |
| Java |ADAL4J |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-java) |[Github](https://github.com/AzureAD/azure-activedirectory-library-for-java) |[Java Web アプリ](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-webapp-java) | |
| Python |ADAL |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-python) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-python) | | |

### <a name="server-libraries"></a>サーバー ライブラリ 

| プラットフォーム | ライブラリ | ダウンロード | ソース コード | サンプル | リファレンス
| --- | --- | --- | --- | --- | --- |
| .NET |OWIN for AzureAD|[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.ActiveDirectory/) |[CodePlex](http://katanaproject.codeplex.com) |[MVC アプリ](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-webapp-dotnet) | |
| .NET |OWIN for OpenIDConnect |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect) |[CodePlex](http://katanaproject.codeplex.com) |[Web アプリ](https://github.com/AzureADSamples/WebApp-OpenIDConnect-DotNet) | |
| Node.js |Azure AD Passport |[npm](https://www.npmjs.com/package/passport-azure-ad) |[GitHub](https://github.com/AzureAD/passport-azure-ad) | [Web API](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-webapi-nodejs)| |
| .NET |OWIN for WS-Federation |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) |[CodePlex](http://katanaproject.codeplex.com) |[MVC Web アプリ](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet) | |
| .NET |Identity Protocol Extensions for .NET 4.5 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Protocol.Extensions) |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| .NET |JWT Handler for .NET 4.5 |[NuGet](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt) |[Github](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |

### <a name="v20-client-libraries-msal"></a>v2.0 クライアント ライブラリ (MSAL)

[Azure AD v2.0 エンドポイント](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-v2-compare)は、1 つのエンドポイントの裏で Azure AD アカウントと Microsoft アカウントを組み合わせたものです。 開発者がこのエンドポイントにアクセスする際は、ADAL の代わりに [運用環境対応のプレビュー MSAL ライブラリ](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-v2-libraries)を利用できます。

| プラットフォーム | ライブラリ | ダウンロード | ソース コード | サンプル | リファレンス
| --- | --- | --- | --- | --- | --- |
| .NET クライアント、Windows ストア、UWP、Xamarin iOS、Android |MSAL for .NET (プレビュー) |[NuGet](https://www.nuget.org/packages/Microsoft.Identity.Client/1.1.0-preview) |[Github](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | [デスクトップ アプリ](~/articles/active-directory/develop/guidedsetups/active-directory-windesktop.md) |[リファレンス](https://docs.microsoft.com/dotnet/api/?view=identityclient-1.1.0-preview) | 
| JavaScript |MSAL for JavaScript (プレビュー) |[Github](https://github.com/AzureAD/microsoft-authentication-library-for-js) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js) | [シングル ページ アプリケーション](~/articles/active-directory/develop/GuidedSetups/active-directory-javascriptspa.md) | [リファレンス](https://htmlpreview.github.io/?https://raw.githubusercontent.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/docs/classes/_useragentapplication_.msal.useragentapplication.html) | 
| iOS |MSAL for iOS (プレビュー) |[Github](https://github.com/AzureAD/microsoft-authentication-library-for-objc) |[Github](https://github.com/AzureAD/microsoft-authentication-library-for-objc) | [iOS アプリ](~/articles/active-directory/develop/GuidedSetups/active-directory-ios.md) | [リファレンス](https://azuread.github.io/docs/objc/) |
| Android |MSAL for Android (プレビュー) |[Github](https://github.com/AzureAD/microsoft-authentication-library-for-android) |[Github](https://github.com/AzureAD/microsoft-authentication-library-for-android) | [Android アプリ](~/articles/active-directory/develop/GuidedSetups/active-directory-android.md) | [リファレンス](http://javadoc.io/doc/com.microsoft.identity.client/msal/0.1.1) |

## <a name="scenarios"></a>シナリオ

リモート リソースにアクセスするクライアントの認証に ADAL を使用できる 3 つの一般的なシナリオを以下に示します。  

### <a name="authenticating-users-of-a-native-client-application-running-on-a-device"></a>デバイスで実行しているネイティブ クライアント アプリケーションのユーザーを認証する 

このシナリオでは、開発者は Azure AD によって保護されたリモート リソース (Web API など) にアクセスする必要がある WPF クライアントを使用しています。 また、Azure サブスクリプションを持ち、ダウンストリーム Web API を呼び出す方法と、Web API が使用する Azure AD テナントを知っています。 そのため、ADAL を使用して、ADAL に認証エクスペリエンスを完全に委任するか、ユーザー資格情報を明示的に処理することで、Azure AD による認証を容易にすることができます。 ADAL により、ユーザーを認証し、Azure AD からアクセス トークンと更新トークンを取得し、そのアクセス トークンを使用して Web API への要求を行うことが容易になります。

Azure AD に対する認証を使用してこのシナリオを実証するコード サンプルについては、 [ネイティブ クライアント WPF アプリケーションと Web API](https://github.com/azureadsamples/nativeclient-dotnet)に関するページをご覧ください。

### <a name="authenticating-a-confidential-client-application-running-on-a-web-server"></a>Web サーバーで実行している機密クライアント アプリケーションを認証する

このシナリオでは、開発者は Azure AD によって保護されたリモート リソース (Web API など) にアクセスする必要がある、サーバー上で実行されるアプリケーションを使用しています。 また、Azure サブスクリプションを持ち、ダウンストリーム サービスを呼び出す方法と、Web API が使用する Azure AD テナントを知っています。 そのため、ADAL を使用して、アプリケーションの資格情報を明示的に処理することで、Azure AD による認証を容易にすることができます。 ADAL により、アプリケーションのクライアント資格情報を使用して Azure AD からトークンを取得し、そのトークンを使用して Web API への要求を行うことが容易になります。 また、ADAL はアクセス トークンをキャッシュしてその有効期間を管理し、必要に応じて更新します。 このシナリオを実証するコード サンプルについては、[デーモン コンソール アプリケーションと Web API](https://github.com/AzureADSamples/Daemon-DotNet) に関するページをご覧ください。

### <a name="authenticating-a-confidential-client-application-running-on-a-server-on-behalf-of-a-user"></a>ユーザーの代わりにサーバーで実行している機密クライアント アプリケーションを認証する 

このシナリオでは、開発者は Azure AD によって保護されたリモート リソース (Web API など) にアクセスする必要がある、サーバー上で実行されるアプリケーションを使用しています。 また、Azure AD ユーザーに代わって要求を行う必要があります。 開発者は Azure サブスクリプションを持ち、ダウンストリーム Web API を呼び出す方法と、サービスが使用する Azure AD テナントを知っています。 ユーザーが Web アプリケーションに対して認証されると、アプリケーションは Azure AD からユーザーの認証コードを取得できます。 その後、Web アプリケーションは、ADAL を使用して、アプリケーションに関連付けられた認証コードとクライアント資格情報を使って、ユーザーの代わりに Azure AD からアクセス トークンと更新トークンを取得できます。 アクセス トークンを所有している Web アプリケーションは、トークンの有効期限が切れるまで Web API を呼び出すことができます。 トークンの有効期限が切れたら、Web アプリケーションは ADAL を使用して、以前に取得した更新トークンを使って新しいアクセス トークンを取得できます。 このシナリオを実証するコード サンプルについては、[ネイティブ クライアントが Web API を呼び出し、その Web API がさらに別の Web API を呼び出すしくみ](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof)に関するページをご覧ください。

## <a name="see-also"></a>関連項目

- [Azure Active Directory 開発者ガイド](active-directory-developers-guide.md)
- [Azure AD の認証シナリオ](active-directory-authentication-scenarios.md)
- [Azure Active Directory のコード例](active-directory-code-samples.md)
