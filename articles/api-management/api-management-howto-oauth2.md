---
title: "Azure API Management の OAuth 2.0 を使用して開発者アカウントを認証する | Microsoft Docs"
description: "API Management で OAuth 2.0 を使用してユーザーを承認する方法について説明します。"
services: api-management
documentationcenter: 
author: steved0x
manager: erikre
editor: 
ms.assetid: 78c48247-64f0-4708-b2d0-98b61a821283
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: apimpm
translationtype: Human Translation
ms.sourcegitcommit: 94e13ac6fec09081484a2f7f5d7bc1871822743f
ms.openlocfilehash: e43027cdea291f34aa60ad123e0de86b385efb30
ms.lasthandoff: 01/31/2017

---
# <a name="how-to-authorize-developer-accounts-using-oauth-20-in-azure-api-management"></a>Azure API Management の OAuth 2.0 を使用して開発者アカウントを認証する方法
多くの API では、[OAuth 2.0](http://oauth.net/2/) がサポートされています。OAuth 2.0 を使用すると、API をセキュリティで保護して、有効なユーザーのみにアクセスが許可されること、および有効なユーザーが許可されたリソースのみにアクセスできることを保証できます。 Azure API Management では、対話型の開発者コンソールでそのような API を使用できるようにするために、OAuth 2.0 に対応する API を使用するサービス インスタンスを構成できます。

## <a name="prerequisites"> </a>前提条件
このガイドでは、開発者アカウントに OAuth 2.0 認証を使用するように API Management サービス インスタンスを構成する方法を説明していますが、OAuth 2.0 プロバイダーを構成する方法は説明していません。 手順は似ていて、API Management サービス インスタンスでの OAuth 2.0 の構成に使用される情報は同じですが、各 OAuth 2.0 プロバイダーの構成は異なっています。 このトピックは、Azure Active Directory を OAuth 2.0 プロバイダーとして使用する例を示しています。

> [!NOTE]
> Azure Active Directory を使用して OAuth 2.0 を構成する方法について詳しくは、[WebApp-GraphAPI-DotNet][WebApp-GraphAPI-DotNet] のサンプルを参照してください。
> 
> 

## <a name="step1"> </a>API Management で OAuth 2.0 認証サーバーを構成する
まず、ご利用の API Management サービスの Azure Portal で **[パブリッシャー ポータル]** をクリックします。

![パブリッシャー ポータル][api-management-management-console]

> [!NOTE]
> まだ API Management サービス インスタンスを作成していない場合は、[Azure API Management の概要][Get started with Azure API Management]に関するチュートリアルの「[API Management インスタンスの作成][Create an API Management service instance]」を参照してください。
> 
> 

左側の **[API Management]** メニューで **[セキュリティ]** をクリックし、**[OAuth 2.0]** をクリックしてから、**[Add authorization server (認証サーバーの追加)]** をクリックします。

![[OAuth 2.0]][api-management-oauth2]

**[認証サーバーの追加 (Add authorization server)]**をクリックした後に、新しい認証サーバーのフォームが表示されます。

![新しいサーバー][api-management-oauth2-server-1]

**[名前]** フィールドと **[説明]** フィールドに、名前とオプションの説明を入力します。 

> [!NOTE]
> これらのフィールドは、OAuth 2.0 認証サーバーを現在の API Management サービス インスタンス内で識別するために使用されるもので、それらの値が OAuth 2.0 サーバーによって自動入力されることはありません。
> 
> 

**クライアント登録ページ URL** を入力します。 このページでは、ユーザーがアカウントを作成して管理できます。このページは使用される OAuth 2.0 プロバイダーによって異なります。 **[クライアント登録ページ URL]** では、ユーザーによるアカウント管理をサポートする OAuth 2.0 プロバイダーについて、ユーザーが自身のアカウントを作成および構成するために使用できるページを指定します。 この機能を OAuth 2.0 プロバイダーがサポートしている場合でも、組織によってはこの機能を構成または使用していない場合があります。 OAuth 2.0 プロバイダーでユーザーによるアカウント管理が構成されていない場合は、会社の URL、`https://placeholder.contoso.com` のような URL などのプレースホルダー URL を入力してください。

フォームの次のセクションには、**[Authorization code grant types (認証コード付与タイプ)]**、**[Authorization endpoint URL (認証エンドポイント URL)]**、および **[Authorization request method (認証要求方式)]** 設定が含まれます。

![新しいサーバー][api-management-oauth2-server-2]

必要なタイプにチェックマークを入れて、 **[認証コード付与タイプ (Authorization code grant types)]** を指定します。 **[認証コード]** が既定で指定されています。

**[Authorization endpoint URL (認証エンドポイント URL)]** を入力します。 Azure Active Directory では、この URL は以下の URL のようになります。ここで、`<client_id>` は、使用するアプリケーションを OAuth 2.0 サーバーが識別するためのクライアント ID に置き換えてください。

`https://login.windows.net/<client_id>/oauth2/authorize`

**[認証要求方式 (Authorization request method)]** は、認証要求が OAuth 2.0 サーバーに送信される方法を指定します。 既定では **[GET]** が選択されています。

次のセクションでは、**[Token endpoint URL (トークン エンドポイント URL)]**、**[Client authentication methods (クライアント認証方式)]**、**[Access token sending method (アクセス トークン送信方式)]**、および **[Default scope (既定のスコープ)]** を指定します。

![新しいサーバー][api-management-oauth2-server-3]

Azure Active Directory OAuth 2.0 サーバーでは、**[Token endpoint URL (トークン エンドポイント URL)]** の形式が以下のようになります。ここで、`<APPID>` の形式は `yourapp.onmicrosoft.com` です。

`https://login.windows.net/<APPID>/oauth2/token`

既定の設定は、**[Client authentication methods (クライアント認証方式)]** が **[Basic (基本)]**、**[Access token sending method (アクセス トークン送信方式)]** が **[Authorization header (承認ヘッダー)]** です。 これらの値は、 **[既定のスコープ (Default scope)]**と共に、フォームのこのセクションで構成されます。

**[クライアントの資格情報]** セクションには **[クライアント ID]** と **[クライアント シークレット]** が含まれます。これらは OAuth 2.0 サーバーの作成と構成のプロセスで取得されます。 **[クライアント ID]** と **[クライアント シークレット]** が指定された後に、**[認証コード]** の **redirect_uri** が生成されます。 この URI は、OAuth 2.0 サーバー構成で応答 URL を構成するために使用されます。

![新しいサーバー][api-management-oauth2-server-4]

**[Authorization code grant types (認証コード付与タイプ)]** が **[Resource owner password (リソース所有者パスワード)]** に設定された場合、**[Resource owner password credentials (リソース所有者パスワードの資格情報)]** セクションがそれらの資格情報の指定に使用されます。そうでない場合は、そのセクションを空白のままにすることができます。

![新しいサーバー][api-management-oauth2-server-5]

このフォームが完了したら、 **[保存]** をクリックして API Management OAuth 2.0 認証サーバーの構成を保存します。 サーバーの構成が保存された後、次のセクションで説明されているように、この構成を使用するように API を構成できます。

## <a name="step2"> </a>OAuth 2.0 ユーザー認証を使用するように API を構成する
左側の **[API Management]** メニューで **[API]** をクリックし、必要な API の名前をクリックし、**[セキュリティ]** をクリックしてから、**[OAuth 2.0]** のボックスにチェックマークを入れます。

![ユーザー認証][api-management-user-authorization]

必要な **[Authorization server (認証サーバー)]** をドロップダウン リストで選択して、**[保存]** をクリックします。

![ユーザー認証][api-management-user-authorization-save]

## <a name="step3"> </a>開発者ポータルで OAuth 2.0 ユーザー認証をテストする
OAuth 2.0 認証サーバーを構成して、そのサーバーを使用するように API を構成した後、開発者ポータルに移動して API を呼び出すことにより、そのサーバーをテストできます。  右上のメニューで、 **[開発者ポータル]** をクリックします。

![[開発者ポータル]][api-management-developer-portal-menu]

上部のメニューで **[API]** をクリックし、**[Echo API]** を選択します。

![[Echo API]][api-management-apis-echo-api]

> [!NOTE]
> アカウントに対して構成されている (またはアカウントから見える) API が&1; つしかない場合、[API] をクリックすると、その API の操作に直接誘導されます。
> 
> 

**[GET Resource]** 操作を選択し、**[コンソールを開く]** をクリックして、ドロップダウンで **[認証コード]** を選択します。

![コンソールを開く][api-management-open-console]

**[認証コード]** が選択されると、OAuth 2.0 プロバイダーのサインイン フォームがあるポップアップ ウィンドウが表示されます。 この例では、サインイン フォームは Azure Active Directory によって提供されています。

> [!NOTE]
> ポップアップが無効になっている場合は、それを有効にするように伝えるプロンプトがブラウザーによって出されます。 ポップアップを有効にした後に、再び **[認証コード]** を選択すると、サインイン フォームが表示されます。
> 
> 

![[サインイン]][api-management-oauth2-signin]

サインインした後、**[要求ヘッダー]** には、要求を認証するための `Authorization : Bearer` ヘッダーが取り込まれます。

![要求ヘッダー トークン][api-management-request-header-token]

これで、残りのパラメーター用に必要な値を構成して、要求を送信できます。 

## <a name="next-steps"></a>次のステップ
OAuth 2.0 と API Management の詳細については、次のビデオとこの [記事](api-management-howto-protect-backend-with-aad.md)をご覧ください。

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Protecting-Web-API-Backend-with-Azure-Active-Directory-and-API-Management/player]
> 
> 

[api-management-management-console]: ./media/api-management-howto-oauth2/api-management-management-console.png
[api-management-oauth2]: ./media/api-management-howto-oauth2/api-management-oauth2.png
[api-management-user-authorization]: ./media/api-management-howto-oauth2/api-management-user-authorization.png
[api-management-user-authorization-save]: ./media/api-management-howto-oauth2/api-management-user-authorization-save.png
[api-management-oauth2-signin]: ./media/api-management-howto-oauth2/api-management-oauth2-signin.png
[api-management-request-header-token]: ./media/api-management-howto-oauth2/api-management-request-header-token.png
[api-management-developer-portal-menu]: ./media/api-management-howto-oauth2/api-management-developer-portal-menu.png
[api-management-open-console]: ./media/api-management-howto-oauth2/api-management-open-console.png
[api-management-oauth2-server-1]: ./media/api-management-howto-oauth2/api-management-oauth2-server-1.png
[api-management-oauth2-server-2]: ./media/api-management-howto-oauth2/api-management-oauth2-server-2.png
[api-management-oauth2-server-3]: ./media/api-management-howto-oauth2/api-management-oauth2-server-3.png
[api-management-oauth2-server-4]: ./media/api-management-howto-oauth2/api-management-oauth2-server-4.png
[api-management-oauth2-server-5]: ./media/api-management-howto-oauth2/api-management-oauth2-server-5.png
[api-management-apis-echo-api]: ./media/api-management-howto-oauth2/api-management-apis-echo-api.png


[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: api-management-get-started.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Create an API Management service instance]: api-management-get-started.md#create-service-instance

[http://oauth.net/2/]: http://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps


