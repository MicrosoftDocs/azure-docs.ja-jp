---
title: API Management の OAuth 2.0 を使用して開発者アカウントを承認する
titleSuffix: Azure API Management
description: API Management で OAuth 2.0 を使用してユーザーを承認する方法について説明します。 OAuth 2.0 では、ユーザーが利用資格のあるリソースにアクセスできるように、API をセキュリティで保護します。
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/14/2020
ms.author: apimpm
ms.openlocfilehash: fae4e349d46425c0c2b2b923d6a61e2e588708c1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "93077253"
---
# <a name="how-to-authorize-developer-accounts-using-oauth-20-in-azure-api-management"></a>Azure API Management の OAuth 2.0 を使用して開発者アカウントを認証する方法

多くの API では、[OAuth 2.0](https://oauth.net/2/) がサポートされています。OAuth 2.0 を使用すると、API をセキュリティで保護して、有効なユーザーのみにアクセスが許可されること、および有効なユーザーが許可されたリソースのみにアクセスできることを保証できます。 Azure API Management では、対話型の開発者コンソールでそのような API を使用できるようにするために、OAuth 2.0 に対応する API を使用するサービス インスタンスを構成できます。

## <a name="prerequisites"></a><a name="prerequisites"> </a>前提条件

このガイドでは、開発者アカウントに OAuth 2.0 認証を使用するように API Management サービス インスタンスを構成する方法を説明していますが、OAuth 2.0 プロバイダーを構成する方法は説明していません。 手順は似ていて、API Management サービス インスタンスでの OAuth 2.0 の構成に使用される情報は同じですが、各 OAuth 2.0 プロバイダーの構成は異なっています。 このトピックは、Azure Active Directory を OAuth 2.0 プロバイダーとして使用する例を示しています。

> [!NOTE]
> Azure Active Directory を使用して OAuth 2.0 を構成する方法について詳しくは、 [WebApp-GraphAPI-DotNet][WebApp-GraphAPI-DotNet] のサンプルを参照してください。

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="configure-an-oauth-20-authorization-server-in-api-management"></a><a name="step1"> </a>API Management で OAuth 2.0 承認サーバーを構成する

> [!NOTE]
> API Management サービス インスタンスをまだ作成していない場合は、[API Management サービス インスタンスの作成][Create an API Management service instance]に関するページを参照してください。

1. 左側のメニューの [OAuth 2.0] タブをクリックし、 **[+ 追加]** をクリックします。

    ![[OAuth 2.0] メニュー](./media/api-management-howto-oauth2/oauth-01.png)

2. **[名前]** フィールドと **[説明]** フィールドに、名前とオプションの説明を入力します。

    > [!NOTE]
    > これらのフィールドは、OAuth 2.0 認証サーバーを現在の API Management サービス インスタンス内で識別するために使用されるもので、それらの値が OAuth 2.0 サーバーによって自動入力されることはありません。

3. **クライアント登録ページ URL** を入力します。 このページでは、ユーザーがアカウントを作成して管理できます。このページは使用される OAuth 2.0 プロバイダーによって異なります。 **[クライアント登録ページ URL]** では、ユーザーによるアカウント管理をサポートする OAuth 2.0 プロバイダーについて、ユーザーが自身のアカウントを作成および構成するために使用できるページを指定します (`https://contoso.com/login` など)。 この機能を OAuth 2.0 プロバイダーがサポートしている場合でも、組織によってはこの機能を構成または使用していない場合があります。 OAuth 2.0 プロバイダーでユーザーによるアカウント管理が構成されていない場合は、会社の URL、`https://placeholder.contoso.com` のような URL などのプレースホルダー URL を入力してください。

    ![OAuth 2.0 の新しいサーバー](./media/api-management-howto-oauth2/oauth-02.png)

4. フォームの次のセクションには、 **[承認許可の種類]** 、 **[承認エンドポイントの URL]** 、および **[承認要求方法]** の設定が含まれます。

    必要な種類をオンにして、 **[承認許可の種類]** を指定します。 **[認証コード]** が既定で指定されています。

    **[Authorization endpoint URL (認証エンドポイント URL)]** を入力します。 Azure Active Directory では、この URL は以下の URL のようになります。ここで、`<tenant_id>` は、Azure AD テナントの ID に置き換えてください。

    `https://login.microsoftonline.com/<tenant_id>/oauth2/authorize`

    **[認証要求方式 (Authorization request method)]** は、認証要求が OAuth 2.0 サーバーに送信される方法を指定します。 既定では **[GET]** が選択されています。

5. 次に、 **[トークン エンドポイントの URL]** 、 **[クライアント認証方法]** 、 **[アクセス トークンの送信方法]** 、および **[既定のスコープ]** を指定します。

    ![[OAuth2 サービスの追加] 画面を示すスクリーンショット。](./media/api-management-howto-oauth2/oauth-03.png)

    Azure Active Directory OAuth 2.0 サーバーでは、 **[Token endpoint URL (トークン エンドポイント URL)]** の形式が以下のようになります。ここで、`<TenantID>` の形式は `yourapp.onmicrosoft.com` です。

    `https://login.microsoftonline.com/<TenantID>/oauth2/token`

    既定の設定は、 **[Client authentication methods (クライアント認証方式)]** が **[Basic (基本)]** 、 **[Access token sending method (アクセス トークン送信方式)]** が **[Authorization header (承認ヘッダー)]** です。 これらの値は、 **[既定のスコープ (Default scope)]** と共に、フォームのこのセクションで構成されます。

6. **[クライアントの資格情報]** セクションには **[クライアント ID]** と **[クライアント シークレット]** が含まれます。これらは OAuth 2.0 サーバーの作成と構成のプロセスで取得されます。 **[クライアント ID]** と **[クライアント シークレット]** が指定された後に、 **[認証コード]** の **redirect_uri** が生成されます。 この URI は、OAuth 2.0 サーバー構成で応答 URL を構成するために使用されます。

    新しい開発者ポータルでは、URI サフィックスは次の形式です。

    - 承認コード付与フローの場合は `/signin-oauth/code/callback/{authServerName}`
    - 暗黙的な許可のフローの場合は `/signin-oauth/implicit/callback`

    ![新しい OAuth2 サービス用のクライアント資格情報を追加する場所を示すスクリーンショット。](./media/api-management-howto-oauth2/oauth-04.png)

    **[承認許可の種類]** を **[リソース所有者のパスワード]** に設定した場合は、 **[リソース所有者のパスワード資格情報]** セクションを使用してそれらの資格情報を指定します。それ以外の場合は、空白のままにすることができます。

    フォームを完了したら、 **[作成]** をクリックして API Management OAuth 2.0 認証サーバーの構成を保存します。 サーバーの構成が保存された後、次のセクションで説明されているように、この構成を使用するように API を構成できます。

## <a name="configure-an-api-to-use-oauth-20-user-authorization"></a><a name="step2"> </a>OAuth 2.0 ユーザー承認を使用するように API を構成する

1. 左側の **[API Management]** メニューの **[API]** をクリックします。

    ![OAuth 2.0 API](./media/api-management-howto-oauth2/oauth-05.png)

2. 目的の API の名前をクリックし、 **[設定]** をクリックします。 **[セキュリティ]** セクションまでスクロールし、 **[OAuth 2.0]** チェック ボックスをオンにします。

    ![OAuth 2.0 の設定](./media/api-management-howto-oauth2/oauth-06.png)

3. 必要な **[Authorization server (認証サーバー)]** をドロップダウン リストで選択して、 **[保存]** をクリックします。

    ![選択した承認サーバーと [保存] ボタンが強調表示されているスクリーンショット。](./media/api-management-howto-oauth2/oauth-07.png)

## <a name="legacy-developer-portal---test-the-oauth-20-user-authorization"></a><a name="step3"> </a>従来の開発者ポータル - OAuth 2.0 ユーザー承認をテストする

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

OAuth 2.0 認証サーバーを構成して、そのサーバーを使用するように API を構成した後、開発者ポータルに移動して API を呼び出すことにより、そのサーバーをテストできます。 Azure API Management インスタンスの **[概要]** ページで、上部のメニューの **[Developer portal (legacy)]\(開発者ポータル (レガシ)\)** をクリックします。

上部のメニューで **[API]** をクリックし、 **[Echo API]** を選択します。

![[Echo API]][api-management-apis-echo-api]

> [!NOTE]
> アカウントに対して構成されている (またはアカウントから見える) API が 1 つしかない場合、[API] をクリックすると、その API の操作に直接誘導されます。

**[GET Resource]** 操作を選択し、 **[コンソールを開く]** をクリックして、ドロップダウンで **[認証コード]** を選択します。

![コンソールを開く][api-management-open-console]

**[認証コード]** が選択されると、OAuth 2.0 プロバイダーのサインイン フォームがあるポップアップ ウィンドウが表示されます。 この例では、サインイン フォームは Azure Active Directory によって提供されています。

> [!NOTE]
> ポップアップが無効になっている場合は、それを有効にするように伝えるプロンプトがブラウザーによって出されます。 ポップアップを有効にした後に、再び **[認証コード]** を選択すると、サインイン フォームが表示されます。

![サインイン][api-management-oauth2-signin]

サインインした後、 **[要求ヘッダー]** には、要求を認証するための `Authorization : Bearer` ヘッダーが取り込まれます。

![要求ヘッダー トークン][api-management-request-header-token]

これで、残りのパラメーター用に必要な値を構成して、要求を送信できます。

## <a name="next-steps"></a>次のステップ

OAuth 2.0 と API Management の詳細については、次のビデオとこの [記事](api-management-howto-protect-backend-with-aad.md)をご覧ください。

[api-management-oauth2-signin]: ./media/api-management-howto-oauth2/api-management-oauth2-signin.png
[api-management-request-header-token]: ./media/api-management-howto-oauth2/api-management-request-header-token.png
[api-management-open-console]: ./media/api-management-howto-oauth2/api-management-open-console.png
[api-management-apis-echo-api]: ./media/api-management-howto-oauth2/api-management-apis-echo-api.png

[How to add operations to an API]: ./mock-api-responses.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: ./api-management-policies.md
[Caching policies]: ./api-management-policies.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[https://oauth.net/2/]: https://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps
