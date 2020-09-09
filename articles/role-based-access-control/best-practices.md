---
title: Azure RBAC のベスト プラクティス
description: Azure のロールベースのアクセス制御 (Azure RBAC) を使用するためのベスト プラクティス。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/17/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 053e86f3493c7a11a3cbbaad0871e45345697878
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "82735336"
---
# <a name="best-practices-for-azure-rbac"></a>Azure RBAC のベスト プラクティス

この記事では、Azure のロールベースのアクセス制御 (Azure RBAC) を使用するためのベスト プラクティスについていくつか説明します。 これらのベスト プラクティスは、Azure RBAC に関して Microsoft が蓄積してきたノウハウと、ユーザーの皆様の経験に基づいています。

## <a name="only-grant-the-access-users-need"></a>ユーザーが必要なアクセスだけを許可する

Azure RBAC を使用して、チーム内で職務を分離し、職務に必要なアクセス許可のみをユーザーに付与します。 すべてのユーザーに Azure サブスクリプションまたはリソースで無制限のアクセス許可を付与するのではなく、特定のスコープで特定の操作のみを許可することができます。

アクセス制御戦略を計画する場合のベスト プラクティスは、ユーザーの作業を実行できる最低限の特権をユーザーに付与することです。 次の図は、Azure RBAC を使用するための推奨パターンを示しています。

![Azure RBAC と最小限の特権](./media/best-practices/rbac-least-privilege.png)

ロールの割り当てを追加する方法の詳細については、[Azure portal を使用した Azure でのロールの割り当ての追加または削除](role-assignments-portal.md)に関する記事を参照してください。

## <a name="limit-the-number-of-subscription-owners"></a>サブスクリプションの所有者の数を制限する

侵害された所有者による侵害の可能性を減らすため、サブスクリプション所有者は最大 3 人までにします。 この推奨事項は Azure Security Center で監視できます。 他の ID と Security Center でのアクセスの推奨事項については、「[セキュリティの推奨事項 - リファレンス ガイド](../security-center/recommendations-reference.md)」を参照してください。

## <a name="use-azure-ad-privileged-identity-management"></a>Azure AD Privileged Identity Management を使用する

悪意のあるサイバー攻撃から特権アカウントを保護するために、Azure Active Directory Privileged Identity Management (PIM) を使用して特権の露出時間を短縮し、レポートとアラートによって特権使用の可視性を向上できます。 PIM は、Azure AD と Azure のリソースへの Just-In-Time の特権アクセスを提供することで、特権アカウントを保護するのに役立ちます。 アクセスは、期限を決めて、その期限が過ぎると権限が自動的に取り消されるようにすることができます。 

詳しくは、「[Azure AD Privileged Identity Management とは](../active-directory/privileged-identity-management/pim-configure.md)」をご覧ください。

## <a name="next-steps"></a>次のステップ

- [Azure RBAC のトラブルシューティング](troubleshooting.md)