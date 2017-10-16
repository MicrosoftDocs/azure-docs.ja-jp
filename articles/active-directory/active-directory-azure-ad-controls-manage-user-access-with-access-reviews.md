---
title: "Azure AD のアクセス レビューでユーザー アクセスを管理する | Microsoft Docs"
description: "Azure Active Directory のアクセス レビューを使用して、グループのメンバーシップやアプリケーションへの割り当てとしてのユーザー アクセスを管理する方法について説明します。"
services: active-directory
documentationcenter: 
author: markwahl-msft
manager: femila
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/19/2017
ms.author: billmath
ms.openlocfilehash: 0459eb5cc71939202c8491f6b2714e28bd8e202d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="manage-user-access-with-azure-ad-access-reviews"></a>Azure AD のアクセス レビューでユーザー アクセスを管理する

Azure Active Directory では、アクセス レビューに参加してユーザーのアクセスを再確認 (または "証明") するようユーザー自身や意思決定者に求めることで、ユーザーが適切なアクセス権を持っていいることを簡単に確認することができます。  レビュー担当者は、Azure AD からの提案に基づいて、各ユーザーの継続的なアクセスのニーズを評価することができます。 アクセス レビューが完了したら、変更を加え、アクセス権が不要になったユーザーからは削除することができます。

> [!NOTE]
> すべての種類のユーザーのアクセス権を確認する必要はなく、ゲスト ユーザーのアクセス権のみを確認する場合は、「[managing guest user access with access reviews (アクセス レビューでゲスト ユーザーのアクセス権を管理する)](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md)」を参照してください。  また、グローバル管理者などの管理者ロールのユーザーのメンバーシップを確認する場合は、「[Azure AD Privileged Identity Management でアクセス レビューを開始する方法](active-directory-privileged-identity-management-how-to-start-security-review.md)」を参照してください。 
>
>

## <a name="prerequisites"></a>前提条件 

アクセス レビューは、EMS E5 に含まれる Azure Active Directory の Premium P2 エディションでご利用いただけます。 詳細については、「[Azure Active Directory のエディション](active-directory-editions.md)」をご覧ください。  アクセス レビューを使用してレビューの作成、アクセスの確認、レビューの適用を行うには、この機能を使用する各ユーザーにライセンスが必要です。


## <a name="creating-and-performing-an-access-review"></a>アクセス レビューの作成と実行

1 人または複数のユーザーをアクセス レビューのレビュー担当者にできます。  

1. 1 人以上のメンバーを持つ Azure Active Directory のグループを選択するか、または 1 人以上のメンバーが割り当てられている、Azure Active Directory に接続済みのアプリケーションを選択します。 
2. 各ユーザーが自分自身のアクセスを確認するか、1 人または複数のユーザーがすべてのユーザーのアクセスを確認するかを決定します。
3. レビュー担当者のアクセス パネルにアクセス レビューが表示されるようにします。  グローバル管理者として[アクセス レビュー ページ](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/)に移動します。 
4. アクセス レビューを開始します。 詳しくは、「[how to create an access review (アクセス レビューを作成する方法)](active-directory-azure-ad-controls-create-access-review.md)」をご覧ください。
5. レビュー担当者に確認を依頼します。 既定では、レビュー担当者はそれぞれ、Azure AD からアクセス パネルへのリンクが記載されたメールを受信し、そこで[アクセス レビューを実行](active-directory-azure-ad-controls-perform-access-review.md)します。
6. レビュー担当者がまだ入力していない場合は、Azure AD にそのレビュー担当者へ通知を送信させることができます。  既定では、終了日まであと半分になった時点で Azure AD はまだ応答していないレビュー担当者に自動的に通知を送信します。
7. レビュー担当者が結果を入力したら、アクセス レビューを停止して変更を適用します。 詳しくは、「[アクセス レビューを完了する方法](active-directory-azure-ad-controls-complete-access-review.md)」をご覧ください。


## <a name="next-steps"></a>次のステップ

- [グループのメンバーまたはアプリケーションへのアクセスのアクセス レビューを作成する](active-directory-azure-ad-controls-create-access-review.md)




