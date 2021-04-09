---
title: クイックスタート - グループとメンバーを表示する - Azure AD
description: 組織のグループとそれらに割り当てられているメンバーを検索して表示する方法に関する手順。
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: quickstart
ms.date: 09/24/2018
ms.author: ajburnle
ms.custom: it-pro, seodec18, seo-update-azuread-jan
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: ddedd753eb98a8656006c9dddc412e44330e1a21
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "95996730"
---
<!--As a brand-new Azure AD administrator, I need to view my organization’s groups along with the assigned members, so I can manage permissions to apps and services for people in my organization-->

# <a name="quickstart-view-your-organizations-groups-and-members-in-azure-active-directory"></a>クイック スタート: Azure Active Directory で組織のグループとメンバーを表示する
Azure portal を使用して、組織の既存のグループとグループ メンバーを表示できます。 グループは、制限付きの可能性があるアプリおよびサービスに対して全員が同じアクセスとアクセス許可を必要とするユーザー (メンバー) を管理するために使用されます。

このクイック スタートでは、組織の既存のグループすべてを表示し、割り当てられているメンバーを表示します。

Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/)を作成してください。 

## <a name="prerequisites"></a>前提条件
開始する前に、次のことを行う必要があります。

- Azure Active Directory テナントを作成する。 詳細については、[Azure Active Directory ポータルにアクセスし、新しいテナントを作成](active-directory-access-create-new-tenant.md)することに関するページを参照してください。

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインする
ディレクトリの全体管理者アカウントを使用して、[Azure portal](https://portal.azure.com/) にサインインする必要があります。

## <a name="create-a-new-group"></a>新しいグループを作成する 
_MDM policy - West_ という名前の新しいグループを作成します。 グループの作成に関する詳細については、「[基本的なグループを作成し、メンバーを追加する方法](active-directory-groups-create-azure-portal.md)」を参照してください。

1. **[Azure Active Directory]**、**[グループ]**、**[新しいグループ]** と選択します。

2. **[グループ]** ページの入力を完了します。
    
    - **[グループの種類]:** **[セキュリティ]** を選択します
    
    - **[グループ名:]** 「_MDM policy - West_」と入力します
    
    - **[メンバーシップの種類]** : **[割り当て済み]** を選択します。

3. **［作成］** を選択します

## <a name="create-a-new-user"></a>新しいユーザーを作成する
_Alain Charon_ という名前の新しいユーザーを作成します。 ユーザーは、グループ メンバーとして追加される前に存在している必要があります。 まず [カスタム ドメイン名] タブをチェックして、ユーザーの作成先となる検証済みのドメイン名を入手します。 ユーザーの作成に関する詳細については、[ユーザーを追加または削除する方法](add-users-azure-active-directory.md)に関するページを参照してください。

1. **[Azure Active Directory]**、**[ユーザー]**、**[新しいユーザー]** と選択します。

2. **[ユーザー]** ページの入力を完了します。

    - **[名前]:** 「_Alain Charon_」と入力します。

    - **[ユーザー名]:** 「*alain\@contoso.com*」と入力します。

3. 提供される自動生成されたパスワードを **[パスワード]** ボックスにコピーし、**[作成]** を選択します。

## <a name="add-a-group-member"></a>グループ メンバーを追加する
これでグループとユーザーを用意したので、_Alain Charon_ をメンバーとして _MDM policy - West_ グループに追加できます。 グループ メンバーの追加に関する詳細については、[グループ メンバーを追加または削除する方法](active-directory-groups-members-azure-portal.md)に関するページを参照してください。

1. **[Azure Active Directory]**  >  **[グループ]** を選択します。

2. **[グループ - すべてのグループ]** ページから、**[MDM policy - West]** グループを検索して選択します。

3. **[MDM policy - West の概要]** ページで、**[管理]** 領域から **[メンバー]** を選択します。

4. **[メンバーの追加]** を選択してから、**[Alain Charon]** を検索して選択します。

5. **[選択]** を選択します。

## <a name="view-all-groups"></a>すべてのグループを表示する
Azure portal の **[グループ - すべてのグループ]** ページでは、組織のすべてのグループを参照できます。

- **[Azure Active Directory]**  >  **[グループ]** を選択します。

    **[グループ - すべてのグループ]** ページが開き、アクティブなグループがすべて表示されます。

    ![既存のグループをすべて表示している [グループ - すべてのグループ] ページ](media/active-directory-groups-view-azure-portal/groups-all-groups-blade-with-all-groups.png)

## <a name="search-for-the-group"></a>グループを検索する
**[グループ - すべてのグループ]** ページを検索し、**[MDM policy - West]** グループを見つけます。

1. **[グループ - すべてのグループ]** ページで、**[検索]** ボックスに「_MDM_」と入力します。

    _[MDM policy - West]_ グループを含む検索結果が **[検索]** ボックスの下に表示されます。

    ![検索ボックスがいっぱいの [グループ - すべてのグループ] ページ](media/active-directory-groups-view-azure-portal/search-for-specific-group.png)

3. **[MDM policy – West]** グループを選択します。

4. **[MDM policy – West の概要]** ページで、そのグループのメンバー数を含め、グループの情報を参照します。

    ![メンバー情報を含む [MDM policy – West の概要] ページ](media/active-directory-groups-view-azure-portal/group-overview-blade.png)

## <a name="view-group-members"></a>グループ メンバーを表示する
これでグループが見つかったので、割り当てられているすべてのメンバーを表示できます。

- **[管理]** 領域から **[メンバー]** を選択し、_Alain Charon_ を含め、その特定のグループに割り当てられているメンバー名の完全な一覧を確認します。

    ![[MDM policy – West] グループに割り当てられているメンバーの一覧](media/active-directory-groups-view-azure-portal/groups-all-members.png)

## <a name="clean-up-resources"></a>リソースをクリーンアップする
このグループは、このドキュメントの **使用方法のガイド** のセクションで提供されている使用方法のプロセスのいくつかで使用されています。 しかし、このグループを使用しないのであれば、次の手順を使用して、割り当てられているメンバーを削除できます。

1. **[グループ - すべてのグループ]** ページで **[MDM policy - West]** グループを検索します。

2.  **[MDM policy – West]** グループを選択します。

    **[MDM policy – West の概要]** ページが表示されます。

3. **[削除]** を選択します。

    グループと、それに関連付けられているメンバーが削除されます。

    ![[削除] リンクが強調表示されている [MDM policy – West の概要] ページ](media/active-directory-groups-view-azure-portal/group-overview-blade-delete.png)

    >[!Important]
    >これにより、ユーザー Alain Charon は削除されず、削除されたグループ内の彼のメンバーシップだけが削除されました。

## <a name="next-steps"></a>次のステップ
サブスクリプションを Azure AD ディレクトリに関連付ける方法を説明している次の記事に進みます。

> [!div class="nextstepaction"]
> [Azure サブスクリプションを関連付ける](active-directory-how-subscriptions-associated-directory.md)
