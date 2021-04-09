---
title: Azure API Management でグループを使用して開発者アカウントを管理する
titleSuffix: Azure API Management
description: Azure API Management でグループを使用して開発者アカウントを管理する方法について説明します。 グループを作成し、それらを製品や開発者に関連付けます。
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/13/2018
ms.author: apimpm
ms.openlocfilehash: ea674981036b4be292329a4b30b43180ed26d642
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92092785"
---
# <a name="how-to-create-and-use-groups-to-manage-developer-accounts-in-azure-api-management"></a>Azure API Management でグループを作成および使用して開発者アカウントを管理する方法

API Management では、開発者に成果物の表示を許可するかどうかが、グループを使用して管理されます。 グループに対して成果物の表示が許可されると、そのグループに属する開発者は、グループに関連付けられた成果物を表示してサブスクライブできるようになります。 

API Management には、次に示すシステム グループが用意されています。これらのシステム グループを変更することはできません。

* **管理者** - Azure サブスクリプション管理者はこのグループのメンバーになります。 管理者は、API Management サービス インスタンスを管理します。開発者が使用する API とその操作、成果物は、管理者が作成します。
* **開発者** - 認証された開発者ポータル ユーザーはこのグループに属します。 開発者は、API の利用者です。管理者によって作成された API を使用してアプリケーションを構築します。 開発者は、開発者ポータルへのアクセスが認められており、API の操作を呼び出すアプリケーションを構築します。
* **ゲスト** - API Management インスタンスの開発者ポータルに訪れる開発者ポータル ユーザーのうち、認証を受けていないもの (利用予定者など) がこのグループに該当します。 特定の読み取り専用アクセスを許可することができます (API の閲覧はできるが、呼び出すことはできないなど)。

管理者は、これらのシステム グループに加えてカスタム グループを作成できるほか、[関連付けられている Azure Active Directory テナントの外部グループを活用する][leverage external groups in associated Azure Active Directory tenants]こともできます。 カスタム グループと外部グループをシステム グループと共に使用することにより、開発者には API 成果物の可視性とアクセスが提供されます。 たとえば、特定のパートナー企業に所属する開発者向けにカスタム グループを 1 つ作成し、関連する API のみが含まれている成果物の API に対してアクセスを許可することができます。 ユーザーは 複数のグループのメンバーになることができます。

このガイドでは、API Management インスタンスの管理者が新しいグループを追加して成果物および開発者に関連付ける方法について説明します。

発行者ポータルでグループを作成および管理するだけでなく、API Management REST API [グループ](/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-group-entity) エンティティを使用してグループを作成および管理することができます。

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="prerequisites"></a>前提条件

「[Create an Azure API Management instance (Azure API Management インスタンスを作成する)](get-started-create-service-instance.md)」の記事にあるタスクを完了します。

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-a-group"></a><a name="create-group"> </a>グループを作成する

このセクションでは、API Management アカウントに新しいグループを追加する方法を示します。

1. 画面の左にある **[グループ]** タブを選択します。
2. **[+ 追加]** をクリックします。
3. グループの一意の名前とオプションの説明を入力します。
4. **[作成]** をクリックします。

    ![新しいグループを追加する](./media/api-management-howto-create-groups/groups001.png)

グループが作成されると、それが **[グループ]** リストに追加されます。 <br/>グループの **[名前]** または **[説明]** を編集するには、そのグループの名前と **[設定]** をクリックします。<br/>グループを削除するには、そのグループの名前をクリックし、**[削除]** を押します。

グループが作成されます。このグループは、成果物および開発者と関連付けることができます。

## <a name="associate-a-group-with-a-product"></a><a name="associate-group-product"> </a>グループと成果物を関連付ける

1. 左にある **[成果物]** タブを選択します。
2. 目的の成果物の名前をクリックします。
3. **[アクセス制御]** を押します。
4. **[+ グループの追加]** をクリックします。

    ![[グループの追加] ボタンが強調表示されているスクリーンショット。](./media/api-management-howto-create-groups/groups002.png)
5. 追加するグループを選択します。

    ![選択したグループが示され、[選択] ボタンが強調表示されているスクリーンショット。](./media/api-management-howto-create-groups/groups003.png)

    成果物からグループを削除するには、**[削除]** をクリックします。

    ![グループを削除する](./media/api-management-howto-create-groups/groups004.png)

成果物をグループに関連付けると、そのグループに属する開発者は、成果物を表示してサブスクライブすることができます。

> [!NOTE]
> Azure Active Directory グループを追加するには、「 [Azure API Management で Azure Active Directory を使用して開発者アカウントを認証する方法](api-management-howto-aad.md)」をご覧ください。

## <a name="associate-groups-with-developers"></a><a name="associate-group-developer"> </a>グループと開発者を関連付ける

このセクションでは、グループをメンバーに関連付ける方法を示します。

1. 画面の左にある **[グループ]** タブを選択します。
2. **[メンバー]** を選択します。

    ![メンバーの追加](./media/api-management-howto-create-groups/groups005.png)
3. **[+ 追加]** を押し、メンバーを選択します。

    ![[追加] ボタン、選択したユーザー、および [選択] ボタンが強調表示されているスクリーンショット。](./media/api-management-howto-create-groups/groups006.png)
4. **[選択]** を選択します。

開発者とグループの間に関連付けを追加すると、 **[ユーザー]** タブにその関連付けが表示されるようになります。

## <a name="next-steps"></a><a name="next-steps"> </a>次の手順

* 開発者をグループに関連付けると、開発者は、グループに関連付けられた成果物を表示してサブスクライブすることができます。 詳細については、「[Azure API Management で成果物を作成して発行する方法][How create and publish a product in Azure API Management]」をご覧ください。
* 発行者ポータルでグループを作成および管理するだけでなく、API Management REST API [グループ](/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-group-entity) エンティティを使用してグループを作成および管理することができます。

[Create a group]: #create-group
[Associate a group with a product]: #associate-group-product
[Associate groups with developers]: #associate-group-developer
[Next steps]: #next-steps

[How create and publish a product in Azure API Management]: api-management-howto-add-products.md

[Get started with Azure API Management]: get-started-create-service-instance.md
[Create an API Management service instance]: get-started-create-service-instance.md
[leverage external groups in associated Azure Active Directory tenants]: api-management-howto-aad.md
