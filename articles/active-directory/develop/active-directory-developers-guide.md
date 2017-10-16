---
title: "開発者のための Azure Active Directory | Microsoft Docs"
description: "この記事では、Azure Active Directory を使用した Microsoft の職場および学校アカウントのサインインの概要について説明します。"
services: active-directory
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 5c872c89-ef04-4f4c-98de-bc0c7460c7c2
ms.service: active-directory
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/07/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: dce813b885d492343428428056a2e8aada27b461
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-for-developers"></a>開発者のための Azure Active Directory
Azure Active Directory は、開発者が、Microsoft によってサポートされる職場または学校アカウントでユーザーが安全にサインインできるようにすることができるクラウド ID サービスです。  このドキュメントでは、業界標準の認証プロトコルである OAuth と OpenID Connect を使用して、アプリケーションに Azure AD のサポートを追加する方法を示します。

| | |
| --- | --- |
|[認証の基本](active-directory-authentication-scenarios.md) | Azure AD での認証の概要 |
|[アプリケーションの種類](active-directory-authentication-scenarios.md#application-types-and-scenarios) | Azure AD でサポートされる認証シナリオの概要 |                                
                                                                              
## <a name="get-started"></a>作業開始
これらのガイド付きの手順では、認証ライブラリを使用して、Azure Active Directory ユーザーがサインインできるようにする方法について説明します。

|  |  |  |  |
| --- | --- | --- | --- |
| <center>![モバイル アプリとデスクトップ アプリ](./media/active-directory-developers-guide/NativeApp_Icon.png)<br />モバイル アプリとデスクトップ アプリ</center> | [概要](active-directory-authentication-scenarios.md#native-application-to-web-api)<br /><br />[iOS](active-directory-devquickstarts-ios.md)<br /><br />[Android](active-directory-devquickstarts-android.md) | [.NET](active-directory-devquickstarts-dotnet.md)<br /><br />[Windows](active-directory-devquickstarts-windowsstore.md)<br /><br />[Xamarin](active-directory-devquickstarts-xamarin.md) | [Cordova](active-directory-devquickstarts-cordova.md)<br /><br />[OAuth 2.0](active-directory-protocols-oauth-code.md) |
| <center>![Web Apps](./media/active-directory-developers-guide/Web_app.png)<br />Web Apps</center> | [概要](active-directory-authentication-scenarios.md#web-browser-to-web-application)<br /><br />[ASP.NET](active-directory-devquickstarts-webapp-dotnet.md)<br /><br />[Java](active-directory-devquickstarts-webapp-java.md) | [NodeJS](active-directory-devquickstarts-openidconnect-nodejs.md)<br /><br />[OpenID Connect 1.0](active-directory-protocols-openid-connect-code.md) |  |
| <center>![シングル ページ アプリ](./media/active-directory-developers-guide/SPA.png)<br />シングル ページ アプリ</center> | [概要](active-directory-authentication-scenarios.md#single-page-application-spa)<br /><br />[AngularJS](active-directory-devquickstarts-angular.md)<br /><br />[JavaScript](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |  |  |
| <center>![Web API](./media/active-directory-developers-guide/Web_API.png)<br />Web API</center> | [概要](active-directory-authentication-scenarios.md#web-application-to-web-api)<br /><br />[ASP.NET](active-directory-devquickstarts-webapi-dotnet.md)<br /><br />[NodeJS](active-directory-devquickstarts-webapi-nodejs.md) | &nbsp; |
| <center>![サービス間](./media/active-directory-developers-guide/Service_App.png)<br />サービス間</center> | [概要](active-directory-authentication-scenarios.md#daemon-or-server-application-to-web-api)<br /><br />[.NET](active-directory-code-samples.md#server-or-daemon-application-to-web-api)<br /><br />[OAuth 2.0 クライアント資格情報](active-directory-protocols-oauth-service-to-service.md) |  |

## <a name="guides"></a>ガイド
次の記事では、Azure Active Directory を使用して一般的なタスクを実行する方法について説明しています。

|                                                                           |  |
|---------------------------------------------------------------------------| --- |
|[アプリの登録](active-directory-integrating-applications.md)           | Azure AD にアプリを登録する方法 |
|[マルチテナント アプリ](active-directory-devhowto-multi-tenant-overview.md)    | Microsoft の職場アカウントでサインインできるようにする方法 |
|[OAuth と OpenID Connect](active-directory-protocols-openid-connect-code.md)| 最新の認証プロトコルを使用して、ユーザーがサインインできるようにし、Web API を呼び出す方法 |
|[その他のガイド...](active-directory-developers-guide-index.md#guides)        |     |

## <a name="reference"></a>リファレンス
次の記事には、Azure Active Directory で使用される API、プロトコル メッセージ、および用語に関する詳細な情報が記載されています。

|                                                                                   | |
| ----------------------------------------------------------------------------------| --- |
| [認証ライブラリ (ADAL)](active-directory-authentication-libraries.md)   | Azure AD が提供するライブラリと SDK の概要 |
| [コード サンプル](active-directory-code-samples.md)                                  | Azure AD のコード サンプルの全一覧 |
| [用語集](active-directory-dev-glossary.md)                                      | このドキュメント全体で使用されている用語と語句の定義 |
| [その他の参考資料...](active-directory-developers-guide-index.md#reference)|     |

## <a name="help--support"></a>ヘルプとサポート
これらは、Azure Active Directory での開発に関するサポートを求めるのに最適な場所です。

|  |  
|---|
|[Stack Overflow の `azure-active-directory` タグと `adal` タグ](http://stackoverflow.com/questions/tagged/azure-active-directory+or+adal)      |
|[Azure Active Directory に関するフィードバック](https://feedback.azure.com/forums/169401-azure-active-directory/category/164757-developer-experiences)|
| [Microsoft デベロッパー チャット (無料期間あり) を試す](http://aka.ms/devchat) |

<br />

> [!NOTE]
> Microsoft の個人アカウントでサインインできるようにする必要がある場合は、[Azure AD v2.0 エンドポイント](active-directory-appmodel-v2-overview.md)の使用を検討してください。  Azure AD v2.0 エンドポイントは、(Azure AD から) Microsoft の個人アカウントと Microsoft の職場アカウントを 1 つの認証システムに統合します。
