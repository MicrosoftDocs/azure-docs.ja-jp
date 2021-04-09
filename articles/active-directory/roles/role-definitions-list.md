---
title: Azure AD ロールの定義を一覧表示する - Azure AD
description: Azure の組み込みロールとカスタム ロールの両方を一覧表示する方法について説明します。
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
ms.openlocfilehash: d37b2988d32c854e4184adee998341ebadcee053
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "103467765"
---
# <a name="list-azure-ad-role-definitions"></a>Azure AD ロールの定義を一覧表示する

ロールの定義は、読み取り、書き込み、削除などの実行できるアクセス許可のコレクションです。 通常は単に "ロール" と呼ばれます。 Azure Active Directory には、60 を超える組み込みロールがあります。また、独自のカスタム ロールを作成することもできます。 "これらのロールで何ができるか" については、ロールごとのアクセス許可の詳細な一覧を参照してください。

この記事では、Azure AD 組み込みロールとカスタムロールの一覧を、そのアクセス許可と共に一覧表示する方法について説明します。

## <a name="list-all-roles"></a>すべてのロールを一覧表示する

1. [Azure AD 管理センター](https://aad.portal.azure.com)にサインインして､ **[Azure Active Directory]** を選択します｡

1. **[ロールと管理者]** を選択して、利用可能なすべてのロールの一覧を表示します。

    ![Azure AD ポータルでのロールの一覧](./media/role-definitions-list/view-roles-in-azure-active-directory.png)

1. 右側にある省略記号を選択し、 **[説明]** をクリックすると、ロールのアクセス許可の完全な一覧が表示されます。

    このページには、ロールの管理について説明している関連ドキュメントへのリンクが含まれています。

    ![[全体管理者 - 説明] ページを示すスクリーンショット。](./media/role-definitions-list/role-description.png)

## <a name="next-steps"></a>次のステップ

* [Azure AD 管理ロール フォーラム](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)でご意見をお寄せください。
* ロールと管理者ロールの割り当ての詳細については、[管理者ロールの割り当て](permissions-reference.md)に関するページを参照してください。
* 既定のユーザー アクセス許可については、[既定のゲストとメンバー ユーザーのアクセス許可の比較](../fundamentals/users-default-permissions.md)を参照してください。
