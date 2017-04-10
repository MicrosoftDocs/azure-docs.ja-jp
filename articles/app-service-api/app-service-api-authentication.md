---
title: "Azure App Service での API Apps の認証と承認 | Microsoft Docs"
description: "Azure App Service が API Apps 向けに提供している認証サービスと承認サービスについて説明します。"
services: app-service\api
documentationcenter: .net
author: alexkarcher-msft
manager: erikre
editor: 
ms.assetid: d620b53a-5a6f-41c9-84c7-f7ef5ff02ae7
ms.service: app-service-api
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/23/2016
ms.author: alkarche
translationtype: Human Translation
ms.sourcegitcommit: b75f7aa757679a29a42cdfc04799873ee30bab2e
ms.openlocfilehash: cd66296718d8ae3cd2bcd69c66f54684f57d7ece
ms.lasthandoff: 02/16/2017


---
# <a name="authentication-and-authorization-for-api-apps-in-azure-app-service"></a>Azure App Service での API Apps の認証と承認
## <a name="overview"></a>概要
> [!NOTE]
> このトピックは、Web、Mobile、および API Apps に関する説明が記載された [App Service の認証/承認](../app-service/app-service-authentication-overview.md) に関するトピックに統合される予定です。
> 
> 

Azure App Service は、[OAuth 2.0](#oauth) と [OpenID Connect](#oauth) を実装する、組み込みの認証および承認サービスを提供します。 この記事では、Azure App Service の API Apps に使用可能なサービスとオプションについて説明します。

次の図は、App Service の認証の鍵となるいくつかの特性を示しています。

* 受信した API 要求の前処理を行います。つまり、App Service でサポートされる言語またはフレームワークが処理されます。
* 認証処理をどこまで独自のコードで行うかに関して、いくつかの選択肢が提供されています。
* エンド ユーザーとサービス アカウントの両方の認証に対応します。 
* ID プロバイダーとして、Azure Active Directory、Facebook、Google、Twitter、Microsoft アカウントの&5; つがサポートされます。
* API Apps、Web Apps、Mobile Apps のいずれについても同じ処理が行われます。

![](./media/app-service-api-authentication/api-apps-overview.png)

## <a name="language-agnostic"></a>言語を選ばない
App Service 認証処理は、要求が API アプリに到達する前の時点で行われます。つまり、認証機能を利用する側の API アプリは、どのような言語、どのようなフレームワークで作成されていてもかまいません。  API の開発には、ASP.NET、Java、Node.js のほか、App Service でサポートされるあらゆるフレームワークを使用できます。

App Service は、HTTP 要求の Authorization ヘッダーで JSON Web トークン (JWT) を受け渡しします。コードは、その作成に使用された言語やフレームワークに関係なく、必要な情報をトークンから取得することができます。 また、App Service では、いくつかの特殊なヘッダーを設定すると、ごく一般的に使用される要求にも簡単にアクセスすることができます。その例を次に示します。

* X-MS-CLIENT-PRINCIPAL-NAME
* X-MS-CLIENT-PRINCIPAL-ID
* X-MS-TOKEN-FACEBOOK-ACCESS-TOKEN
* X-MS-TOKEN-FACEBOOK-EXPIRES-ON

.NET API では `Authorize` 属性を使用できます。要求の情報は .NET のクラスで自動的に設定されるため、要求に基づくコードをすぐに記述して、きめ細かな承認処理を行うことができます。

## <a name="multiple-protection-options"></a>複数の保護オプション
App Service は、API アプリに対する匿名 HTTP 要求の到達を禁止するか、すべての要求を通過させて匿名 HTTP 要求を含む要求のトークンを検証するか、または、要求に対して操作を行うことなく、すべての要求を通過させることができます。

1. 認証済みの要求のみ API アプリへの到達を許可する。
   
    ブラウザーから匿名要求を受信した場合、App Service は、選択した認証プロバイダー (Azure AD、Google、Twitter など) のログオン ページにリダイレクトされます。 
   
    この選択肢をアプリで採用した場合、認証コードを自分で記述する必要は一切ありません。また、最も重要な要求は HTTP ヘッダー内に指定されているため承認コードも単純です。
2. すべての要求に API アプリへの到達を許可したうえで、認証済みの要求を検証し、HTTP ヘッダー内の認証情報を受け渡しする。
   
    この選択肢では、匿名要求をより柔軟に処理することができますが、匿名ユーザーに API の利用を禁止する場合は、独自にコードを記述する必要があります。 最も一般的な要求は HTTP 要求のヘッダーで渡されるため、承認コードは比較的単純です。
3. すべての要求に API への到達を許可し、要求に含まれる認証情報に対して何も実行しない。
   
    この選択肢を採用した場合、認証と承認に伴う一切の処理をアプリケーション コードに委ねることになります。

どの選択肢を採用するかは、 [Azure ポータル](https://portal.azure.com/)の **[認証/承認]** ブレードで選びます。

![](./media/app-service-api-authentication/authblade.png)

1 つ目と 2 つ目の選択肢については、**[App Service 認証]** をオンにし、**[要求が認証されなかったときに実行する処理]** ボックスの一覧の **[ログイン]** または **[要求の許可 (操作不要)]** を選択します。  **[ログイン]**を選択した場合は、認証プロバイダーを選んで、その構成を行う必要があります。

![](./media/app-service-api-authentication/actiontotake.png)

認証を構成する方法の詳細については、「 [Azure Active Directory ログインを使用するように App Service アプリケーションを構成する方法](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md)」を参照してください。 この記事は API アプリだけでなくモバイル アプリにも適用され、他の認証プロバイダーに関する他の記事へのリンクが記載されています。

## <a id="internal"></a> サービス アカウントの認証
App Service 認証では、内部的なシナリオ (API アプリから別の API アプリを呼び出すなど) を処理します。 このシナリオでは、エンド ユーザーの資格情報ではなく、サービス アカウントの資格情報を使用して、トークンを取得します。 サービス アカウントは、Azure Active Directory では *サービス プリンシパル* とも呼ばれ、このようなアカウントでの認証は、サービス間シナリオとも呼ばれます。 

サービス間シナリオでは、呼び出し先の API アプリを Azure Active Directory で保護し、その API アプリを呼び出すときに AAD サービス プリンシパル承認トークンを提供します。 クライアント ID とクライアント シークレットを AAD アプリケーションから提供して、トークンを取得します。 Mobile Services Zumo トークンの処理で使用されていたような Azure 専用の特殊なコードは不要です。 このシナリオについて、ASP.NET API アプリを使った例が、 [API Apps のサービス プリンシパル認証](app-service-api-dotnet-service-principal-auth.md)に関するチュートリアルで紹介されています。

App Service 認証を使わずにサービス間の認証を行う場合、クライアント証明書または基本認証を利用することができます。 Azure のクライアント証明書の詳細については、「 [Web Apps の TLS 相互認証を構成する方法](../app-service-web/app-service-web-configure-tls-mutual-auth.md)」を参照してください。 ASP.NET での基本認証の詳細については、「 [ASP.NET Web API 2 での認証フィルター](http://www.asp.net/web-api/overview/security/authentication-filters)」を参照してください。

App Service ロジック アプリから API アプリへのサービス アカウント認証は特殊なケースであり、「 [App Service でホストされたカスタム API のロジック アプリでの使用](../logic-apps/logic-apps-custom-hosted-api.md)」で説明されています。

## <a name="mobile-client-authentication"></a>モバイル クライアント認証
モバイル クライアントから認証を処理する方法の詳細については、「 [モバイル アプリの認証に関するドキュメント](../app-service-mobile/app-service-mobile-ios-get-started-users.md)」を参照してください。 App Service 認証は、モバイル アプリと API アプリと同じ方法で処理されます。

## <a name="more-information"></a>詳細情報
Azure App Service での認証と承認の詳細については、以下のリソースを参照してください。

* [Expanding App Service authentication / authorization (App Service の認証/承認の展開)](https://azure.microsoft.com/blog/announcing-app-service-authentication-authorization/)
* [Azure Active Directory ログインを使用するように App Service アプリケーションを構成する方法](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md) (ページの先頭に、他の認証プロバイダーのリンクがあります) 

OAuth 2.0、OpenID Connect、JSON Web トークン (JWT) の詳細については、次のリソースを参照してください。

* [OAuth 2.0 の概要](http://shop.oreilly.com/product/0636920021810.do "Getting Started with OAuth 2.0") 
* [OAuth2、OpenID Connect と JSON Web トークン (JWT) の概要 - PluralSight コース](http://www.pluralsight.com/courses/oauth2-json-web-tokens-openid-connect-introduction) 
* [ASP.NET での複数のクライアント用 RESTful API の構築とセキュリティ保護 - PluralSight コース](http://www.pluralsight.com/courses/building-securing-restful-api-aspdotnet)

Azure Active Directory の詳細については、次のリソースを参照してください。

* [Azure AD のシナリオ](http://aka.ms/aadscenarios)
* [Azure AD 開発者ガイド](http://aka.ms/aaddev)
* [Azure AD のサンプル](http://aka.ms/aadsamples)

## <a name="next-steps"></a>次のステップ
この記事では、API アプリに使用できる App Service の認証と承認の機能について説明しました。 入門シリーズの次のチュートリアルでは、 [App Service API Apps にユーザー認証](app-service-api-dotnet-user-principal-auth.md)を実装する方法について説明します。


