---
title: Privileged Identity Management での Azure AD ロールの検出と分析情報 (プレビュー) (以前のセキュリティ ウィザード) - Azure Active Directory
description: 検出と分析情報 (以前のセキュリティ ウィザード) を使用すると、永続的な Azure AD ロールの割り当てを Privileged Identity Management を使用した Just-In-Time 割り当てに変換できます。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 09/01/2020
ms.author: curtand
ms.custom: pim ; H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4662e9fb537a93cb87c554e319256e2eca40d2e7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92372431"
---
# <a name="discovery-and-insights-preview-for-azure-ad-roles-formerly-security-wizard"></a>Azure AD ロールの検出と分析情報 (プレビュー) (以前のセキュリティ ウィザード)

Azure Active Directory (Azure AD) 組織内で Privileged Identity Management (PIM) を開始する場合は、 **[Discovery and insights (preview)]\(検出と分析情報 (プレビュー)\)** ページを使用して作業を開始できます。 この機能によって、組織内の特権ロールに割り当てられているユーザーと、PIM を使用して永続的なロールの割り当てを Just-In-Time 割り当てにすばやく変更する方法が示されます。 **[Discovery and insights (preview)]\(検出と分析情報 (プレビュー)\)** で、永続的な特権ロールの割り当てを表示したり、変更を加えたりすることができます。 これは分析ツールであり、アクション ツールです。

## <a name="discovery-and-insights-preview"></a>検出と分析情報 (プレビュー)

組織で Privileged Identity Management の使用を開始する前に、すべてのロールの割り当てが永続になります。 ユーザーは特権を必要としない場合でも、常に割り当てられたロールに含まれます。 以前のセキュリティウィザードに代わる [Discovery and insights (preview)]\(検出と分析情報 (プレビュー)\) には、特権ロールの一覧と、それらのロールに現在存在しているユーザーの数が表示されます。 1 人または複数のロール割り当て済みユーザーが不明な場合に、その詳細を確認するには、ロールの割り当ての一覧を表示できます。

:heavy_check_mark:Microsoft は、グローバル管理者ロールに永続的に割り当てられる、緊急用アカウントを 2 つ保持することを **お勧めします**。 「[Azure AD で緊急アクセス用管理者アカウントを管理する](../roles/security-emergency-access.md)」で説明されているように、これらのアカウントが、サインインする通常の管理者アカウントと同じ多要素認証メカニズムを必要としないようにしてください。

また、ユーザーに Microsoft アカウント (つまり、Skype、Outlook.com などの Microsoft サービスへのサインインに使用するアカウント) がある場合は、ロールの割り当ても永続的に維持してください。 ロールの割り当てをアクティブ化するために、Microsoft アカウントを持つユーザーに対して多要素認証が必要な場合、このユーザーはロックアウトされます。

## <a name="open-discovery-and-insights-preview"></a>[Discovery and insights (preview)]\(検出と分析情報 (プレビュー)\) を開く

1. [Azure portal](https://portal.azure.com/) にサインインします。

1. **[Azure AD Privileged Identity Management]** を開きます。

1. **[Azure AD ロール]** を選択して、 **[Discovery and insights (preview)]\(検出と分析情報 (プレビュー)\)** を選択します。 ページを開くと、検出プロセスが開始され、関連するロールの割り当てが検索されます。

    ![3 つのオプションを示している [Azure AD ロール] の検出と分析情報ページ](./media/pim-security-wizard/new-preview-link.png)

1. **[Reduce global administrators]\(グローバル管理者を減らす\)** を選択します。

    ![[グローバル管理者の削減] 操作が選択されている [検出と分析情報 (プレビュー)] を示すスクリーンショット。](./media/pim-security-wizard/new-preview-page.png)

1. グローバル管理者ロールの割り当ての一覧を確認します。

    ![グローバル管理者を減らす - すべてのグローバル管理者を表示している [ロール] ウィンドウ](./media/pim-security-wizard/new-global-administrator-list.png)

1. **[次へ]** を選択し、対象とするユーザーまたはグループを選択してから、 **[対象化]** または **[割り当ての削除]** を選択します。

    ![ロールの対象にするメンバーを選択するためのオプションがある [メンバーを対象的に変換] ページ](./media/pim-security-wizard/new-global-administrator-buttons.png)

1. すべてのグローバル管理者に対して、自身のアクセス権をレビューするように要求することもできます。

    ![アクセス レビュー セクションを示す [グローバル管理者] ページ](./media/pim-security-wizard/new-global-administrator-access-review.png)

1. これらの変更のいずれかを選択すると、Azure の通知が表示されます。

1. 次に、 **[Eliminate standing access]\(継続的なアクセスを排除する\)** または **[Review service principals]\(サービス プリンシパルを確認する\)** を選択して、他の特権ロールとサービス プリンシパルのロールの割り当てで上記の手順を繰り返します。 サービス プリンシパルのロールの割り当てでは、ロールの割り当てのみを削除できます。

    ![継続的なアクセスを排除し、サービス プリンシパルを確認するための追加の分析情報オプション ](./media/pim-security-wizard/new-preview-page-service-principals.png)

## <a name="next-steps"></a>次のステップ

- [Privileged Identity Management で Azure AD ロールを割り当てる](pim-how-to-add-role-to-user.md)
- [Privileged Identity Management を管理するために、他の管理者にアクセス権を付与する](pim-how-to-give-access-to-pim.md)
