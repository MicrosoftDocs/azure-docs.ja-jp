---
title: グループを使用して SaaS アプリへのアクセスを管理する - Azure AD | Microsoft Docs
description: Azure Active Directory と連携する SaaS アプリケーションへのアクセス権を、Azure Active Directory でグループを使用して割り当てる方法について説明します。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: df36cd334dbb455cf1717bf18fc6c8337d0ee6d2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96547884"
---
# <a name="using-a-group-to-manage-access-to-saas-applications"></a>SaaS アプリケーションへのアクセスをグループで管理する

Azure Active Directory (Azure AD) の Azure AD Premium ライセンス プランでは、グループを使用して Azure AD に統合されている SaaS アプリケーションへのアクセス権を割り当てることができます。 たとえばマーケティング部門を対象に 5 つの SaaS アプリケーションを使用するためのアクセス権を割り当てる場合、同部門のユーザーから成るグループを作成したうえで、マーケティングで必要となる 5 つの SaaS アプリケーションにそのグループを割り当てます。 このようにマーケティング部門のメンバーシップを一元的に管理することで時間を節約することが可能です。 この場合アプリケーションに対するユーザーの割り当ては、そのユーザーがマーケティング グループのメンバーとして追加されたときに行われます。同様に、マーケティング グループからユーザーが削除されると、アプリケーションからもその割り当てが削除されます。 この機能は、Azure AD アプリケーション ギャラリー内から追加できる多数のアプリケーションで利用することができます。

> [!IMPORTANT]
> この機能は、Azure AD Premium の試用版の使用を開始するか、Azure AD Premium ライセンス プランを購入した後にのみ使用できます。
> グループ ベースの割り当てがサポートされるのはセキュリティ グループのみです。
> 現在、アプリケーションに対するグループ ベースの割り当てでは入れ子になったグループ メンバーシップはサポートされていません。

## <a name="to-assign-access-for-a-user-or-group-to-a-saas-application"></a>SaaS アプリケーションに対するアクセス権をユーザーまたはグループに割り当てるには

1. [Azure AD 管理センター](https://aad.portal.azure.com)で、**[エンタープライズ アプリケーション]** を選択します。
2. アプリケーション ギャラリーから追加したアプリケーションをクリックして開きます。
3. **[ユーザーとグループ]**、**[ユーザーの追加]** の順に選択します。
4. **[割り当ての追加]** で **[ユーザーとグループ]** を選択し、**[ユーザーとグループ]** 選択リストを開きます。
6. 必要な数のグループまたはユーザーを選択して **[選択]** をクリックまたはタップし、**[割り当ての追加]** リストに追加します。 ユーザーに対するロールの割り当ても、この段階で行います。
7. **[割り当て]** を選択し、選択したエンタープライズ アプリケーションにユーザーまたはグループを割り当てます。

## <a name="next-steps"></a>次のステップ
次の記事は、Azure Active Directory に関する追加情報を示します。

* [Azure Active Directory グループによるリソースへのアクセス管理](../fundamentals/active-directory-manage-groups.md)
* [Azure Active Directory のアプリケーション管理](../manage-apps/what-is-application-management.md)
* [グループの設定を構成するための Azure Active Directory コマンドレット](../enterprise-users/groups-settings-cmdlets.md)
* [Azure Active Directory とは](../fundamentals/active-directory-whatis.md)
* [オンプレミス ID と Azure Active Directory の統合](../hybrid/whatis-hybrid-identity.md)
