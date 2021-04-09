---
title: Azure Active Directory でユーザーをアプリに割り当てる方法を理解する
description: ID 管理のために Azure Active Directory を使用するアプリにユーザーを割り当てる方法について説明します。
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: reference
ms.date: 01/07/2021
ms.author: kenwith
ms.openlocfilehash: 161df0446c9478ca0f2b135c1e426f3786b164fc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99257442"
---
# <a name="understand-how-users-are-assigned-to-apps-in-azure-active-directory"></a>Azure Active Directory でユーザーをアプリに割り当てる方法を理解する
この記事では、ユーザーがテナントでアプリケーションに割り当てられる方法について説明します。

## <a name="how-do-users-get-assigned-to-an-application-in-azure-ad"></a>ユーザーが Azure AD でアプリケーションに割り当てられる方法
ユーザーがアプリケーションにアクセスするには、まず何らかの方法でそのユーザーを割り当てる必要があります。 割り当ては管理者またはビジネス デリゲートによって行われますが、場合によってはユーザー自身で実行できることもあります。 次に、ユーザーをアプリケーションに割り当てることができる方法について説明します。

*  管理者が、アプリケーションに直接[ユーザーを割り当てる](./assign-user-or-group-access-portal.md)。
*  管理者が、アプリケーションにユーザーがメンバーとなっている[グループを割り当てる](./assign-user-or-group-access-portal.md)。次のグループが含まれます。
    * オンプレミスから同期されたグループ
    * クラウドで作成された静的なセキュリティ グループ
    * クラウドで作成された[動的なセキュリティ グループ](../enterprise-users/groups-dynamic-membership.md)
    * クラウドで作成された Microsoft 365 グループ
    * [すべてのユーザー](../fundamentals/active-directory-groups-create-azure-portal.md) グループ
*  管理者が [[アプリケーションのセルフ サービス アクセス]](./manage-self-service-access.md) を有効にして、**ビジネス承認なし** でユーザーが [マイ アプリ](../user-help/my-apps-portal-end-user-access.md)の **[アプリの追加]** 機能を使用してアプリケーションを追加することを許可します
*  管理者が [[アプリケーションのセルフ サービス アクセス]](./manage-self-service-access.md) を有効にして、ユーザーが [マイ アプリ](../user-help/my-apps-portal-end-user-access.md)の **[アプリの追加]** 機能を使用してアプリケーションを追加することを許可しますが、**選択された一連のビジネス承認者からの事前の承認があった** 場合に限ります
*  管理者が [[セルフサービスによるグループ管理]](../enterprise-users/groups-self-service-management.md) を有効にして、アプリケーションが **ビジネス承認なし** で割り当てられているグループにユーザーが参加することを許可します。
*  管理者が [[セルフサービスによるグループ管理]](../enterprise-users/groups-self-service-management.md) を有効にして、アプリケーションが割り当てられているグループにユーザーが参加することを許可しますが、**選択された一連のビジネス承認者からの事前の承認があった** 場合に限ります。
*  管理者は、[Microsoft 365](https://products.office.com/) などのファースト パーティ アプリケーションのライセンスを直接ユーザーに割り当てます。
*  管理者は、[Microsoft 365](https://products.office.com/) などのファースト パーティ アプリケーションのライセンスを、ユーザーがメンバーとなっているグループに割り当てます。
*  [管理者がすべてのユーザーによるアプリケーションの使用に同意](../develop/howto-convert-app-to-be-multi-tenant.md)したら、ユーザーはアプリケーションにサインインします。
* ユーザーはアプリケーションにサインインすることで、[アプリケーションに同意](../develop/howto-convert-app-to-be-multi-tenant.md)します。

## <a name="next-steps"></a>次のステップ
* [アプリケーション管理のクイックスタート シリーズ](view-applications-portal.md)
* [アプリケーション管理とは](what-is-application-management.md)
* [シングル サインオンとは](what-is-single-sign-on.md)