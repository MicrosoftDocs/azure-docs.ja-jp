---
title: Azure AD ロールをユーザーに割り当てる - Azure Active Directory
description: Azure AD ロールを割り当てることにより、Azure Active Directory のユーザーにアクセス権を付与する方法について説明します。
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 03/07/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 36ced586db1b4e417e623431c137c43dac8ba56f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103466646"
---
# <a name="assign-azure-ad-roles-to-users"></a>Azure AD ロールをユーザーに割り当てる

Azure AD 管理者センターで、管理者ロールのすべてのメンバーを表示して管理できるようになりました。 ロールの割り当てを頻繁に管理する場合は、このエクスペリエンスが望ましいでしょう。 この記事では、Azure AD 管理センターを使用して Azure AD ロールを割り当てる方法について説明します。

## <a name="assign-a-role"></a>ロールの割り当て

1. グローバル管理者または特権ロール管理者のアクセス許可を使用して、[Azure AD 管理センター](https://aad.portal.azure.com)にサインインします。

1. **[Azure Active Directory]** を選択します。

1. **[ロールと管理者]** を選択して、利用可能なすべてのロールの一覧を表示します。

    ![[ロールと管理者] ページのスクリーンショット](./media/manage-roles-portal/roles-and-administrators.png)

1. ロールを選択して、その割り当てを表示します。

    必要なロールを見つけるために、ロール カテゴリに基づいてロールのサブセットを Azure AD に表示することができます。 選択した種類のロールだけが表示される **[種類]** フィルターを確認してください。

1. **[割り当ての追加]** を選択し、このロールに割り当てるユーザーを選択します。

    次の図と異なるものが表示された場合は、「[Privileged Identity Management (PIM)](#privileged-identity-management-pim)」の注記を参照して、PIM を使用しているかどうかを確認してください。

    ![管理者ロールのアクセス許可の一覧](./media/manage-roles-portal/add-assignments.png)

1. **[追加]** を選択してロールを割り当てます。

## <a name="privileged-identity-management-pim"></a>Privileged Identity Management (PIM)

[Azure AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) を使用して追加の管理機能を追加するには、 **[PIM で管理]** を選択します。 特権ロール管理者は、割り当てを "永続的" (ロールで常にアクティブ) から "有資格" (昇格された場合にのみ、ロールに存在) に変更することができます。 Privileged Identity Management を備えていない場合でも、 **[PIM で管理]** を選択して、引き続き試用版にサインアップできます。 Privileged Identity Management には、[Azure AD Premium P2 ライセンス プラン](../privileged-identity-management/subscription-requirements.md)が必要です。

![[ユーザー管理者 - 割り当て] ページで選択された "PIM で管理" アクションを示すスクリーンショット](./media/manage-roles-portal/member-list-pim.png)

全体管理者または特権ロール管理者の場合は、簡単にメンバーを追加または削除したり、一覧をフィルター処理したり、メンバーを選択してそのユーザーが割り当てられているアクティブ ロールを表示したりすることができます。

> [!Note]
> Azure AD Premium P2 ライセンスがあり、Privileged Identity Management ユーザーに既に使用している場合、ロール管理タスクはすべて、Azure AD ではなく、Privileged Identity Management で実行されます。
>
> ![既に PIM を使用していて Premium P2 ライセンスを所有しているユーザーの PIM で管理される Azure AD ロール](./media/manage-roles-portal/pim-manages-roles-for-p2.png)

## <a name="next-steps"></a>次のステップ

* [Azure AD 管理ロール フォーラム](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)でご意見をお寄せください。
* ロールの詳細については、「[Azure の組み込みロール](permissions-reference.md)」を参照してください。
* 既定のユーザー アクセス許可については、[既定のゲストとメンバー ユーザーのアクセス許可の比較](../fundamentals/users-default-permissions.md)を参照してください。
