---
title: Azure Maps による認証 | Microsoft Docs
description: Azure Maps サービスを使用するための認証
author: walsehgal
ms.author: v-musehg
ms.date: 02/12/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 9dfe4024607e106565984d6d49de94d793bf7a8f
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/28/2019
ms.locfileid: "57010414"
---
# <a name="authentication-with-azure-maps"></a>Azure Maps による認証

Azure Maps には、要求の認証方法が 2 つあります。共有キーと Azure Active Directory (Azure AD) です。 この記事では、実装の参考になるように、これらの認証方法について説明します。

## <a name="shared-key-authentication"></a>共有キー認証

共有キー認証は、Azure Maps への要求ごとに Azure Maps アカウントによって生成されたキーを渡します。  Azure Maps アカウントの作成時に 2 つのキーが生成されます。 Azure Maps サービスへの要求ごとに、サブスクリプション キーをパラメーターとして URL に追加する必要があります。

> [!Tip]
> キーを定期的に再生成することをお勧めします。 キーは 2 つ提供されるため、1 つのキーを使用して接続を保持したまま、もう 1 つのキーを再生成することが可能です。 キーを再生成したら、新しいキーを使用するように、アカウントにアクセスするすべてのアプリケーションを更新する必要があります。

キーを表示する方法については、「[認証の詳細を表示する](https://aka.ms/amauthdetails)」をご覧ください。

## <a name="authentication-with-azure-active-directory-preview"></a>Azure Active Directory による認証 (プレビュー)

Azure Maps では、Azure Maps サービスに対する要求の認証用に [Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) の統合を提供するようになりました。 Azure AD では、[ロールベースのアクセス制御 (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) などの ID ベースの認証を提供して、ユーザー レベルまたはアプリケーション レベルのアクセス権を Azure Maps リソースに付与しています。 以降のセクションでは、Azure AD と Azure Maps の統合の概念とコンポーネントをわかりやすく説明します。

## <a name="authentication-with-oauth-access-tokens"></a>OAuth アクセス トークンによる認証

Azure Maps では、Azure Maps アカウントを含む Azure サブスクリプションに関連付けられている Azure AD テナントの **OAuth 2.0** アクセス トークンを受け付けます。 Azure Maps は、次のトークンを受け付けます。

* Azure AD ユーザー。 
* ユーザーによって委任されたアクセス許可を使用するパートナー アプリケーション。
* Azure リソースのマネージド ID。

Azure Maps では、Azure Maps アカウントごとに*一意の識別子 (クライアント ID)* を生成します。 このクライアント ID と追加のパラメーターを組み合わせる場合は、次の値を指定して Azure AD からのトークンを要求できます。

```
https://login.microsoftonline.com
```
Azure AD を構成して Azure Maps のトークンを要求する方法について詳しくは、「[Azure Maps での認証の管理](https://review.docs.microsoft.com/azure/azure-maps/how-to-manage-authentication)」をご覧ください。

Azure AD からのトークンの要求について詳しくは、「[認証とは](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios)」をご覧ください。

## <a name="request-azure-map-resources-with-oauth-tokens"></a>OAuth トークンを使用して Azure Map リソースを要求する

Azure AD からトークンを受信したら、次の 2 つの必須の要求ヘッダー セットを使用して要求を Azure Maps に送信できます。

| 要求ヘッダー    |    値    |
|:------------------|:------------|
| x-ms-client-id    | 30d7cc….9f55|
| Authorization     | Bearer eyJ0e….HNIVN |

> [!Note]
> `x-ms-client-id` は、Azure Maps 認証ページに表示される Azure Maps アカウント ベースの GUID です。

OAuth トークンを使用する Azure Maps ルート要求の例を次に示します。

```
GET /route/directions/json?api-version=1.0&query=52.50931,13.42936:52.50274,13.43872 
Host: atlas.microsoft.com 
x-ms-client-id: 30d7cc….9f55 
Authorization: Bearer eyJ0e….HNIVN 
```

クライアント ID を表示する方法については、「[認証の詳細を表示する](https://aka.ms/amauthdetails)」をご覧ください。

## <a name="control-access-with-rbac"></a>RBAC を使用してアクセスを制御する

Azure AD では、RBAC を使用してセキュリティ保護されたリソースへのアクセスを制御できます。 Azure Maps アカウントを作成し、Azure AD テナント内に Azure Maps Azure AD アプリケーションを登録した後は、Azure Maps アカウント ポータル ページ上でユーザー、アプリケーション、または Azure リソースに対して RBAC をセットアップすることが可能です。

Azure Maps では、Azure リソースのマネージド ID を使用して個々の Azure AD ユーザー、アプリケーション、および Azure サービスの読み取りアクセス制御をサポートしています。

![Azure Maps データ閲覧者 (プレビュー)](./media/azure-maps-authentication/concept.png)

RBAC 設定を確認する方法については、[Azure Maps RBAC を構成する方法](https://aka.ms/amrbac)に関するページをご覧ください。

## <a name="managed-identities-for-azure-resources-and-azure-maps"></a>Azure リソースと Azure Maps のマネージド ID

[Azure リソースのマネージド ID](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) では、Azure Maps サービスへのアクセスを認可できる自動マネージド ID を使用して、Azure サービス (Azure App Service、Azure Functions、Virtual Machines など) を提供します。  

## <a name="next-steps"></a>次の手順

* Azure AD と Azure Maps を使用してアプリケーションを認証する方法について詳しく確認するには、「[Azure Maps での認証の管理](https://review.docs.microsoft.com/azure/azure-maps/how-to-manage-authentication)」をご覧ください。

* Azure Maps マップ コントロールおよび Azure AD の認証について詳しく確認するには、「[Azure Maps のマップ コントロールを使用する](https://aka.ms/amaadmc)」をご覧ください。