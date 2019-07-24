---
title: 管理者ロールのアクセス許可の表示と割り当てを行う - Azure Active Directory | Microsoft Docs
description: ポータルで、Azure AD 管理者ロールのメンバーを表示して管理できるようになりました。 ロールの割り当てを頻繁に管理するユーザーの場合。
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 20842c8c644a351b59322569661abf82bb524449
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2019
ms.locfileid: "56187153"
---
# <a name="view-and-assign-administrator-roles-in-azure-active-directory"></a>Azure Active Directory で管理者ロールを表示して割り当てる

Azure Active Directory ポータルで、管理者ロールのすべてのメンバーを表示して管理できるようになりました。 ロールの割り当てを頻繁に管理する場合は、このエクスペリエンスが望ましいでしょう。 "これらのロールで何ができるか" については、Azure AD 管理者ロールごとのアクセス許可の詳細な一覧を参照してください。

## <a name="view-all-roles"></a>すべてのロールを表示する

Azure Active Directory で、**[ロールと管理者]** を選択して、利用可能なすべてのロールの一覧を表示します。 

各行の右側にある省略記号をクリックすると、ロールの詳細な説明が表示されます。

![Azure AD ポータルでのロールの一覧](./media/directory-manage-roles-portal/role-list.png)

## <a name="view-my-roles"></a>自分のロールを表示する

自分のアクセス許可も簡単に表示することができます。 **[ロールと管理者]** ページの **[ロール]** を選択すると、現在自分に割り当てられているロールが表示されます。

## <a name="view-assignments-for-a-role"></a>ロールの割り当てを表示する

ロールに割り当てられているユーザーを表示するには、ロールをクリックします。 追加の管理機能の **[Manage in PIM]\(PIM で管理\)** を選択できます。 特権ロール管理者は、割り当てを "永続的" (ロールで常にアクティブ) から "有資格" (昇格された場合にのみ、ロールに存在) に変更することができます。 PIM がない場合でも、**[Manage in PIM]\(PIM で管理\)** を選択して、試用版にサインアップできます。 Privileged Identity Management には、[Azure AD Premium P2 ライセンス プラン](../privileged-identity-management/subscription-requirements.md)が必要です。

![管理者ロールのメンバーの一覧](./media/directory-manage-roles-portal/member-list.png)

全体管理者または特権ロール管理者の場合は、簡単にメンバーを追加または削除したり、一覧をフィルター処理したり、メンバーを選択してそのユーザーが割り当てられているアクティブ ロールを表示したりすることができます。

## <a name="view-a-users-role-permissions"></a>ユーザーのロールのアクセス許可を表示する

ロールのメンバーを表示しているときに、**[説明]** を選択すると、ロールの割り当てによって付与されているアクセス許可の完全な一覧が表示されます。 このページには、ディレクトリ ロールの管理について説明している関連ドキュメントへのリンクが含まれています。

![管理者ロールのアクセス許可の一覧](./media/directory-manage-roles-portal/role-description.png)


## <a name="next-steps"></a>次の手順

* [Azure AD 管理ロール フォーラム](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)でご意見をお寄せください。
* ロールと管理者ロールの割り当ての詳細については、[管理者ロールの割り当て](directory-assign-admin-roles.md)に関するページを参照してください。
* 既定のユーザー アクセス許可については、[既定のゲストとメンバー ユーザーのアクセス許可の比較](../fundamentals/users-default-permissions.md)を参照してください。
