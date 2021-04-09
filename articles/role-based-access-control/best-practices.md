---
title: Azure RBAC のベスト プラクティス
description: Azure のロールベースのアクセス制御 (Azure RBAC) を使用するためのベスト プラクティス。
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: conceptual
ms.workload: identity
ms.date: 12/16/2020
ms.author: rolyon
ms.openlocfilehash: d58398c42cdc6faed758e5dba3431e0841fc0b03
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100555565"
---
# <a name="best-practices-for-azure-rbac"></a>Azure RBAC のベスト プラクティス

この記事では、Azure のロールベースのアクセス制御 (Azure RBAC) を使用するためのベスト プラクティスについていくつか説明します。 これらのベスト プラクティスは、Azure RBAC に関して Microsoft が蓄積してきたノウハウと、ユーザーの皆様の経験に基づいています。

## <a name="only-grant-the-access-users-need"></a>ユーザーが必要なアクセスだけを許可する

Azure RBAC を使用して、チーム内で職務を分離し、職務に必要なアクセス許可のみをユーザーに付与します。 すべてのユーザーに Azure サブスクリプションまたはリソースで無制限のアクセス許可を付与するのではなく、特定のスコープで特定の操作のみを許可することができます。

アクセス制御戦略を計画する場合のベスト プラクティスは、ユーザーの作業を実行できる最低限の特権をユーザーに付与することです。 当初、より広範なスコープでより広範なロールを割り当てる方が便利に思われたとしても、避けてください。 カスタム ロールを作成する場合は、ユーザーが必要とするアクセス許可のみが含まれます。 ロールとスコープを制限することで、セキュリティ プリンシパルが侵害された場合にリスクにさらされるリソースを制限することができます。

次の図は、Azure RBAC を使用するための推奨パターンを示しています。

![Azure RBAC と最小限の特権](./media/best-practices/rbac-least-privilege.png)

ロールの割り当て方法について詳しくは、「[Azure portal を使用して Azure ロールを割り当てる](role-assignments-portal.md)」をご覧ください。

## <a name="limit-the-number-of-subscription-owners"></a>サブスクリプションの所有者の数を制限する

侵害された所有者による侵害の可能性を減らすため、サブスクリプション所有者は最大 3 人までにします。 この推奨事項は Azure Security Center で監視できます。 他の ID と Security Center でのアクセスの推奨事項については、「[セキュリティの推奨事項 - リファレンス ガイド](../security-center/recommendations-reference.md)」を参照してください。

## <a name="use-azure-ad-privileged-identity-management"></a>Azure AD Privileged Identity Management を使用する

悪意のあるサイバー攻撃から特権アカウントを保護するために、Azure Active Directory Privileged Identity Management (PIM) を使用して特権の露出時間を短縮し、レポートとアラートによって特権使用の可視性を向上できます。 PIM は、Azure AD と Azure のリソースへの Just-In-Time の特権アクセスを提供することで、特権アカウントを保護するのに役立ちます。 アクセスは、期限を決めて、その期限が過ぎると権限が自動的に取り消されるようにすることができます。 

詳しくは、「[Azure AD Privileged Identity Management とは](../active-directory/privileged-identity-management/pim-configure.md)」をご覧ください。

## <a name="assign-roles-to-groups-not-users"></a>ユーザーではなくグループにロールを割り当てる

ロール割り当てをより管理しやすくするために、ロールをユーザーに直接割り当てないようにします。 代わりに、グループにロールを割り当ててください。 ロールの割り当て数には[サブスクリプションあたり 2,000 までという制限](troubleshooting.md#azure-role-assignments-limit)がありますが、ロールをユーザーでなくグループに割り当てることで、この割り当て数を最小限に抑えることもできます。 

## <a name="next-steps"></a>次のステップ

- [Azure RBAC のトラブルシューティング](troubleshooting.md)
