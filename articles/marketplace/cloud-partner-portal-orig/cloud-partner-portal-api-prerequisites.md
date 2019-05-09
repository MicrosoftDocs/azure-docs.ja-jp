---
title: API 前提条件 | Microsoft Docs
description: クラウド パートナー ポータル API を使用するための前提条件
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 0d743d89c344ce9cb6eec116ed9c3e0cbe56f86e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57878649"
---
<a name="api-prerequisites"></a>API 前提条件
================

クラウド パートナー ポータル API を使用するには、プログラム アセットが 2 つ必要になります。サービス プリンシパルと Azure Active Directory (Azure AD) アクセス トークンです。


<a name="create-a-service-principal-in-your-azure-active-directory-tenant"></a>Azure Active Directory テナントでサービス プリンシパルを作成する
----------------------------------------------------------------

最初に、Azure AD テナントでサービス プリンシパルを作成する必要があります。 このテナントには、クラウド パートナー ポータルで独自のアクセス許可セットが割り当てられます。 コードでは、個人の資格情報を使用する代わりに、このテナントとして使用して API が呼び出されます。  サービス プリンシパル作成の説明については、[リソースにアクセスできる Azure Active Directory アプリケーションとサービス プリンシパルをポータルで作成する](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal)方法に関するページを参照してください。


<a name="add-the-service-principal-to-your-account"></a>サービス プリンシパルをアカウントに追加する
-----------------------------------------

テナントにサービス プリンシパルを作成したので、それをユーザーとしてクラウド パートナー ポータル アカウントに追加できます。 ユーザーと同様に、サービス プリンシパルには、所有者またはポータルの共同作成者を指定できます。

次の手順でサービス プリンシパルを追加します。

1. クラウド パートナー ポータルにサインオンします。 
2. 左のメニュー バーで **[ユーザー]** をクリックし、**[ユーザーの追加]** を選択します。

   ![ユーザーをポータルに追加する](./media/cloud-partner-portal-api-prerequisites/add-user.jpg)

3. **[種類]** ドロップダウンから **[サービス プリンシパル]** を選択し、次の詳細を追加します。

-   サービス プリンシパルの**フレンドリ名**。たとえば、`spAccount`。
-   **アプリケーション ID**。 この ID を見つけるには、[[Azure portal]](https://portal.azure.com) に移動し、**[Azure Active Directory]** をクリックし、**[アプリの登録]** を選択して自分のアプリをクリックします。
-   自分の Azure AD テナントの**テナント ID**。これは**ディレクトリ ID** とも呼ばれています。 この ID は、[Azure portal](https://portal.azure.com) の Azure Active Directory ページの **[プロパティ]** にあります。
-   サービス プリンシパル オブジェクトの**オブジェクト ID**。 この ID は Azure portal から取得できます。 **[Azure Active Directory]** に移動し、**[アプリの登録]** を選択し、自分のアプリをクリックし、**[ローカル ディレクトリでのマネージド アプリケーション]** の下にあるアプリ名をクリックします。 次に、**[プロパティ]** ページに移動し、オブジェクト ID を見つけます。 アプリに存在する最初のオブジェクト ID を取得せずに、マネージド アプリケーションのオブジェクト ID を取得してください。
-   アカウントに関連付けられている**ロール**。RBAC に使用されます。

     ![マネージド アプリをポータルに追加する](./media/cloud-partner-portal-api-prerequisites/managedapp.png)

1. **[追加]** をクリックし、サービス プリンシパルをアカウントに追加します。

   ![サービス プリンシパルを追加する](./media/cloud-partner-portal-api-prerequisites/add-service-principal.jpg)


<a name="get-an-azure-ad-access-token"></a>Azure AD アクセス トークンを取得する
----------------------------

クラウド パートナー ポータル API では認証の間、次のアセットとプロトコルが使用されます。

- リソースへのアクセスを要求するための JSON Web Token (JWT) ベアラー トークン
- ID を確認するための [OpenID Connect](https://openid.net/connect/) (OIDC) プロトコル
- ID 機関としての [Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/active-directory-whatis)

JWT トークンをプログラムで取得するための主要な方法が 2 つあります。

- .NET 用 Microsoft 認証ライブラリ ([MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)) を使用します。  これはよりベルが高く、.NET 開発者向けに推奨される手法です。 
- Azure AD OAuth **トークン** エンドポイントに **HTTP POST** 要求を行います。これは次の形式になります。

``` HTTP
POST https://login.microsoftonline.com/<tenant-id>/oauth2/token
    client_id: <application-id>
    client_secret:<application-secret>
    grant_type: client_credentials
    resource: https://cloudpartner.azure.com
```

これで、API 要求の認証ヘッダーの一部としてこのトークンを渡すことができます。

``` HTTP
GET https://cloudpartner.azure.com/api/offerTypes?api-version=2016-08-01-preview 
    Accept: application/json
    Authorization: Bearer <access-token>
```
> [!NOTE]
> このリファレンスのすべての API に対して、認証ヘッダーは常に渡されると想定されます。そのため、明示的には言及されません。

要求で認証エラーが発生した場合、[認証エラーのトラブルシューティング](./cloud-partner-portal-api-troubleshooting-authentication-errors.md)に関するページを参照してください。
