---
title: 管理者ロールのアクセス許可の表示と割り当てを行う - Azure AD | Microsoft Docs
description: ポータルで、Azure AD 管理者ロールのメンバーを表示して管理できるようになりました。 ロールの割り当てを頻繁に管理するユーザーの場合。
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: how-to
ms.date: 06/15/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: fea05ada52ba4281acb3451769446d7f0bad790d
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/30/2020
ms.locfileid: "87448734"
---
# <a name="view-and-assign-administrator-roles-in-azure-active-directory"></a>Azure Active Directory で管理者ロールを表示して割り当てる

Azure Active Directory ポータルで、管理者ロールのすべてのメンバーを表示して管理できるようになりました。 ロールの割り当てを頻繁に管理する場合は、このエクスペリエンスが望ましいでしょう。 "これらのロールで何ができるか" については、Azure AD 管理者ロールごとのアクセス許可の詳細な一覧を参照してください。

## <a name="view-all-roles"></a>すべてのロールを表示する

1. [Azure portal](https://portal.azure.com) にサインインし、 **[Azure Active Directory]** を選択します。

1. **[ロールと管理者]** を選択して、利用可能なすべてのロールの一覧を表示します。

1. 各行の右側にある省略記号を選択して、ロールのアクセス許可を表示します。 ロールに割り当てられているユーザーを表示するには、ロールを選択します。 次の図と異なるものが表示された場合は、「[特権ロールの割り当てを表示する](#view-assignments-for-privileged-roles)」の注記を参照して、Privileged Identity Management (PIM) を使用しているかどうかを確認してください。

    ![Azure AD ポータルでのロールの一覧](./media/directory-manage-roles-portal/view-roles-in-azure-active-directory.png)

## <a name="view-my-roles"></a>自分のロールを表示する

自分のアクセス許可も簡単に表示することができます。 **[ロールと管理者]** ページの **[ロール]** を選択すると、現在自分に割り当てられているロールが表示されます。

## <a name="view-assignments-for-privileged-roles"></a>特権ロールの割り当てを表示する

追加の管理機能の **[Manage in PIM]\(PIM で管理\)** を選択できます。 特権ロール管理者は、割り当てを "永続的" (ロールで常にアクティブ) から "有資格" (昇格された場合にのみ、ロールに存在) に変更することができます。 Privileged Identity Management を備えていない場合でも、 **[PIM で管理]** を選択して、引き続き試用版にサインアップできます。 Privileged Identity Management には、[Azure AD Premium P2 ライセンス プラン](../privileged-identity-management/subscription-requirements.md)が必要です。

![管理者ロールのメンバーの一覧](./media/directory-manage-roles-portal/member-list.png)

全体管理者または特権ロール管理者の場合は、簡単にメンバーを追加または削除したり、一覧をフィルター処理したり、メンバーを選択してそのユーザーが割り当てられているアクティブ ロールを表示したりすることができます。

> [!Note]
> Azure AD Premium P2 ライセンスがあり、Privileged Identity Management ユーザーに既に使用している場合、ロール管理タスクはすべて、Azure AD ではなく、Privileged Identity Management で実行されます。
>
> ![既に PIM を使用していて Premium P2 ライセンスを所有しているユーザーの PIM で管理される Azure AD ロール](./media/directory-manage-roles-portal/pim-manages-roles-for-p2.png)

## <a name="view-a-users-role-permissions"></a>ユーザーのロールのアクセス許可を表示する

ロールのメンバーを表示しているときに、 **[説明]** を選択すると、ロールの割り当てによって付与されているアクセス許可の完全な一覧が表示されます。 このページには、ディレクトリ ロールの管理について説明している関連ドキュメントへのリンクが含まれています。

![管理者ロールのアクセス許可の一覧](./media/directory-manage-roles-portal/role-description.png)

## <a name="download-role-assignments"></a>ロールの割り当てのダウンロード

特定のロールのすべての割り当てをダウンロードするには、 **[ロールと管理者]** ページでロールを選択し、 **[ロールの割り当てのダウンロード]** を選択します。 そのロールのすべてのスコープでの割り当てを一覧表示する CSV ファイルがダウンロードされます。

![ロールのすべての割り当てをダウンロードする](./media/directory-manage-roles-portal/download-role-assignments.png)

## <a name="assign-a-role"></a>ロールの割り当て

1. 全体管理者または特権ロール管理者のアクセス許可で [Azure portal](https://portal.azure.com) にサインインし、 **[Azure Active Directory]** を選択します。

1. **[ロールと管理者]** を選択して、利用可能なすべてのロールの一覧を表示します。

1. ロールを選択して、その割り当てを表示します。

    ![管理者ロールのアクセス許可の一覧](./media/directory-manage-roles-portal/member-list.png)

1. **[割り当ての追加]** を選択し、割り当てるロールを選択します。 追加の管理機能の **[Manage in PIM]\(PIM で管理\)** を選択できます。 次の図とは別のものが表示される場合は、「[特権ロールの割り当てを表示する](#view-assignments-for-privileged-roles)」の注記を参照して、PIM を使用しているかどうかを確認してください。

    ![管理者ロールのアクセス許可の一覧](./media/directory-manage-roles-portal/directory-role-select-role.png)

## <a name="next-steps"></a>次のステップ

* [Azure AD 管理ロール フォーラム](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)でご意見をお寄せください。
* ロールと管理者ロールの割り当ての詳細については、[管理者ロールの割り当て](directory-assign-admin-roles.md)に関するページを参照してください。
* 既定のユーザー アクセス許可については、[既定のゲストとメンバー ユーザーのアクセス許可の比較](../fundamentals/users-default-permissions.md)を参照してください。
