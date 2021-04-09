---
title: PIM で Azure リソース ロールのアクセスをレビューする - Azure AD | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM) で Azure リソース ロールのアクセスをレビューする方法を説明します。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8091890d174ae68f012a6ec24685a0e705100f8b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "84743679"
---
# <a name="review-access-to-azure-resource-roles-in-privileged-identity-management"></a>Privileged Identity Management で Azure リソース ロールのアクセスをレビューする

Privileged Identity Management (PIM) のアクセス レビューは、Azure Active Directory (Azure AD) で特権ロールへのアクセスをセキュリティで保護する目的で役立ちます。 この記事では、Azure AD アクセス レビューで特権ロールの割り当てをレビューする手順を紹介します。

自分に管理者ロールが割り当てられているとき、場合によっては、あるロールに対するニーズを確認する目的で、管理者によるアクセス レビューを完了することが要求されます。 確認要求はリンクが含まれる電子メールとして届くか、[Azure portal](https://portal.azure.com) で確認できます。

特権ロール管理者としてアクセス レビューに関心がある場合は、 [アクセス レビューを開始する方法](pim-resource-roles-start-access-review.md)に関するページで詳細を確認できます。

## <a name="approve-or-deny-access"></a>アクセスの承認または拒否

このロールを引き続き使用するかどうかに基づき、アクセスを承認するか拒否できます。 ロールにとどまる場合は **[承認]** を選択します。また、アクセスが不要な場合は **[拒否]** を選択します。 状態は、レビュー担当者が結果を適用した場合にのみ変更されます。

アクセス レビューを検索して完了するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) にサインインします。
1. **[Azure Active Directory]** を選択し、**[Privileged Identity Management]** を開きます。
1. **[アクセスのレビュー]** を選択します。

   ![[アクセスのレビュー] ブレードが選択された Privileged Identity Management アプリケーションのスクリーンショット](media/pim-resource-roles-perform-access-review/rbac-access-review-complete.png)

1. 完了するレビューを選択します。
1. **[承認]** または **[拒否]** を選択します。 **[理由の指定]** ボックスに、必要であれば、業務上の正当な理由を入力します。

   ![[レビューの詳細] ページのスクリーンショット](media/pim-resource-roles-perform-access-review/rbac-access-review-choice.png)

## <a name="next-steps"></a>次のステップ

- [Privileged Identity Management で Azure AD ロールのアクセス レビューを実行する](pim-how-to-perform-security-review.md)
