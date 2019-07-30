---
title: アクセス レビューを使用したユーザー アクセスの管理 - Azure Active Directory | Microsoft Docs
description: Azure Active Directory のアクセス レビューを使用して、グループのメンバーシップやアプリケーションへの割り当てとしてのユーザー アクセスを管理する方法について説明します
services: active-directory
documentationcenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 06/21/2018
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 70b6d925772548e6703b57c265ebacf6b7f3f4c9
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/25/2019
ms.locfileid: "68499638"
---
# <a name="manage-user-access-with-azure-ad-access-reviews"></a>Azure AD のアクセス レビューでユーザー アクセスを管理する

Azure Active Directory (Azure AD) を使用すると、ユーザーに適切なアクセス権を確実かつ容易に設定できます。 ユーザー自身または意思決定者に対し、アクセス レビューに参加してユーザーのアクセス権を再確認 (証明) するよう求めることができます。 レビュー担当者は、Azure AD からの提案に基づいて、各ユーザーの継続的なアクセスのニーズを評価できます。 アクセス レビューが完了したら、変更を加え、不要になったアクセス権をユーザーから削除できます。

> [!NOTE]
> すべての種類のユーザーのアクセス権を確認する必要はなく、ゲスト ユーザーのアクセス権のみを確認する場合は、[アクセス レビューによるゲスト ユーザー アクセスの管理](manage-guest-access-with-access-reviews.md)に関するページをご覧ください。 グローバル管理者などの管理者ロールのユーザーのメンバーシップを確認する場合は、[Azure AD Privileged Identity Management でアクセス レビューを開始する方法](../privileged-identity-management/pim-how-to-start-security-review.md)に関するページをご覧ください。

## <a name="prerequisites"></a>前提条件

- Azure AD Premium P2

詳細については、[ライセンスが必要なユーザー](access-reviews-overview.md#which-users-must-have-licenses)に関する記事をご覧ください。

## <a name="create-and-perform-an-access-review"></a>アクセス レビューの作成と実行

1 人または複数のユーザーをアクセス レビューのレビュー担当者にできます。  

1. Azure AD でメンバーがいるグループを選択します。 または、Azure AD に接続され、1 人以上のユーザーが割り当てられているアプリケーションを選択します。 

2. 各ユーザーが自分自身のアクセスを確認するか、1 人以上のユーザーがすべてのユーザーのアクセスを確認するかを決定します。

3. グローバル管理者またはユーザー管理者として [Identity Governance ページ](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/)に移動します。

4. アクセス レビューを作成します。 詳細については、[グループまたはアプリケーションのアクセス レビューの作成](create-access-review.md)に関するページをご覧ください。

5. アクセス レビューの開始時には、レビュー担当者に入力するよう依頼してください。 既定では、それぞれが、アクセス パネルへのリンクが記載されたメールを Azure AD から受け取り、そこで[グループまたはアプリケーションへのアクセスをレビュー](perform-access-review.md)します。

6. レビュー担当者がまだ入力していない場合は、そのレビュー担当者に通知を送信するよう Azure AD を設定できます。 既定では、終了日まであと半分になった時点で、Azure AD はまだ応答していないレビュー担当者に自動的に通知が送信されます。

7. レビュー担当者が入力したら、アクセス レビューを停止し、変更を適用します。 詳細については、[グループまたはアプリケーションのアクセス レビューの完了](complete-access-review.md)に関するページをご覧ください。


## <a name="next-steps"></a>次の手順

[グループまたはアプリケーションのアクセス レビューを作成する](create-access-review.md)




