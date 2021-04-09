---
title: Azure AD ロールをユーザーに割り当てる - Azure Active Directory | Microsoft Docs
description: Azure Active Directory を使って、ユーザーに管理者と管理者以外のロールを割り当てる方法について説明します。
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 08/31/2020
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: fe0c7eec5c023fcba3152ad6329d318210a0c2ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92370935"
---
# <a name="assign-administrator-and-non-administrator-roles-to-users-with-azure-active-directory"></a>Azure Active Directory を使ってユーザーに管理者と管理者以外のロールを割り当てる

Azure Active Directory (Azure AD) で、Azure AD リソースを管理するためのアクセス許可がユーザーの 1 人に必要な場合、必要なアクセス許可を提供するロールにユーザーを割り当てる必要があります。 どのロールが Azure リソースを管理し、どのロールが Azure AD リソースを管理するかについては、「[従来のサブスクリプション管理者ロール、Azure ロール、および Azure AD ロール](../../role-based-access-control/rbac-and-directory-admin-roles.md)」を参照してください。

使用可能な Azure AD ロールについて詳しくは、[Azure Active Directory での管理者ロールの割り当て](../roles/permissions-reference.md)に関するページを参照してください。 ユーザーを追加するには、[Azure Active Directory への新しいユーザーの追加](add-users-azure-active-directory.md)に関するページを参照してください。

## <a name="assign-roles"></a>ロールを割り当てる

ユーザーに Azure AD ロールを割り当てる一般的な方法は、ユーザーの **[Assigned roles]\(割り当てられているロール\)** ページです。 Privileged Identity Management (PIM) を使用して、ユーザーの資格を構成し、ジャストインタイムでロールに設定することもできます。 PIM の使用方法については、「[Privileged Identity Management のドキュメント](../privileged-identity-management/index.yml)」をご覧ください。

> [!Note]
> Azure AD Premium P2 ライセンス プランを所有しており、既に PIM を使用している場合、すべてのロール管理タスクは [Privileged Identity Management エクスペリエンス](../roles/manage-roles-portal.md)で実行されます。 この機能では現在、一度に 1 つだけのロールに割り当てることができます。 現在のところ、複数のロールを選択し、一度にユーザーに割り当てることはできません。
>
> ![既に PIM を使用していて Premium P2 ライセンスを所有しているユーザーの Azure AD ロールは PIM で管理される](./media/active-directory-users-assign-role-azure-portal/pim-manages-roles-for-p2.png)

## <a name="assign-a-role-to-a-user"></a>ユーザーにロールを割り当てる

1. ディレクトリのグローバル管理者アカウントを使用して [Azure portal](https://portal.azure.com/) にアクセスしてサインインします。

2. **Azure Active Directory** を検索して選択します。

      ![Azure portal で、Azure Active Directory を検索する](media/active-directory-users-assign-role-azure-portal/search-azure-active-directory.png)

3. **[ユーザー]** を選択します。

4. ロールの割り当てを取得するユーザーを検索して選択します。 たとえば、_Alain Charon_ を選びます。

      ![[すべてのユーザー] ページ - ユーザーの選択](media/active-directory-users-assign-role-azure-portal/directory-role-select-user.png)

5. **[Alain Charon - プロファイル]** ページで、 **[Assigned roles]\(割り当てられているロール\)** を選択します。

    **[Alain Charon - Administrative roles]\(Alain Charon - 管理者ロール\)** ページが表示されます。

6. **[Add assignments]\(割り当ての追加\)** を選択し、Alain に割り当てるロールを選択して (たとえば _[アプリケーション管理者]_ )、 **[選択]** を選択します。

    ![[Assigned roles] (割り当てられているロール) ページ - 選択されたロールを表示中](media/active-directory-users-assign-role-azure-portal/directory-role-select-role.png)

    アプリケーション管理者ロールが Alain Charon に割り当てられて、 **[Alain Charon - Administrative roles]\(Alain Charon - 管理者ロール\)** ページに表示されます。

## <a name="remove-a-role-assignment"></a>ロールの割り当てを削除する

ユーザーからロールの割り当てを削除する必要がある場合も、 **[Alain Charon - Administrative roles]\(Alain Charon - 管理者ロール\)** ページから行うことができます。

### <a name="to-remove-a-role-assignment-from-a-user"></a>ユーザーからロールの割り当てを削除するには

1. **[Azure Active Directory]** 、 **[ユーザー]** の順に選択し、ロールの割り当てを削除するユーザーを検索して選択します。 たとえば、_Alain Charon_ を選びます。

2. **[Assigned roles]\(割り当てられているロール\)** を選択し、 **[アプリケーション管理者]** を選択して、 **[Remove assignment]\(割り当ての削除\)** を選択します。

    ![選択されたロールと削除オプションが表示された [Assigned roles] (割り当てられているロール) ページ](media/active-directory-users-assign-role-azure-portal/directory-role-remove-role.png)

    アプリケーション管理者ロールが Alain Charon から削除されて、 **[Alain Charon - Administrative roles]\(Alain Charon - 管理者ロール\)** ページに表示されなくなります。

## <a name="next-steps"></a>次のステップ

- [ユーザーを追加または削除する](add-users-azure-active-directory.md)

- [プロファイル情報を追加または変更する](active-directory-users-profile-azure-portal.md)

- [別のディレクトリからのゲスト ユーザーの追加](../external-identities/what-is-b2b.md)

[Azure Active Directory のユーザー管理に関するドキュメント](../enterprise-users/index.yml)で、その他のその他のユーザー管理タスクについて確認できます。