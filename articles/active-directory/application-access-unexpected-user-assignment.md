---
title: "アプリケーションにユーザーを割り当てる方法 | Microsoft Docs"
description: "ユーザーがテナントでアプリケーションに割り当てられる方法について理解します。"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 916238ba402a2555bac620d7f08e02799d981ae0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-assign-users-to-applications"></a>ユーザーをアプリケーションに割り当てる方法

この記事では、ユーザーがテナントでアプリケーションに割り当てられる方法について説明します。

## <a name="how-do-users-get-assigned-to-an-application-in-azure-ad"></a>ユーザーが Azure AD でアプリケーションに割り当てられる方法

ユーザーがアプリケーションにアクセスするには、まず何らかの方法でそのユーザーを割り当てる必要があります。 割り当ては管理者またはビジネス デリゲートによって行われますが、場合によってはユーザー自身で実行できることもあります。 次に、ユーザーをアプリケーションに割り当てることができる方法について説明します。

1.  管理者が、アプリケーションに直接[ユーザーを割り当てる](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)。

2.  管理者が、アプリケーションにユーザーがメンバーとなっている[グループを割り当てる](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)。次のグループが含まれます。

  * オンプレミスから同期されたグループ

  * クラウドで作成された静的なセキュリティ グループ

  * クラウドで作成された[動的なセキュリティ グループ](https://docs.microsoft.com/azure/active-directory/active-directory-groups-dynamic-membership-azure-portal)

  * クラウドで作成された Office 365 グループ

  * [すべてのユーザー](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-dedicated-groups) グループ

3.  管理者が [[アプリケーションのセルフ サービス アクセス]](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) を有効にして、[[アプリケーション アクセス パネル]](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) の **[アプリの追加]** 機能を**ビジネス承認なし**で使用するアプリケーションの追加をユーザーに許可します。

4.  管理者が [[アプリケーションのセルフ サービス アクセス]](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) を有効にして、[[アプリケーション アクセス パネル]](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) の **[アプリの追加]** 機能を使用するアプリケーションの追加をユーザーに許可しますが、**選択された一連のビジネス承認者からの事前の承認があった**場合に限ります。

5.  管理者が [[セルフサービスによるグループ管理]](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management) を有効にして、アプリケーションが**ビジネス承認なし**で割り当てられているグループにユーザーが参加することを許可します。

6.  管理者が [[セルフサービスによるグループ管理]](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management) を有効にして、アプリケーションが割り当てられているグループにユーザーが参加することを許可しますが、**選択された一連のビジネス承認者からの事前の承認があった**場合に限ります。

7.  管理者は、[Microsoft Office 365](http://products.office.com/) などのファースト パーティ アプリケーションのライセンスを直接ユーザーに割り当てます。

8.  管理者は、[Microsoft Office 365](http://products.office.com/) などのファースト パーティ アプリケーションのライセンスを、ユーザーがメンバーとなっているグループに割り当てます。

9.  [管理者がすべてのユーザーによるアプリケーションの使用に同意](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview#understanding-user-and-admin-consent)したら、ユーザーはアプリケーションにサインインします。

10. ユーザーはアプリケーションにサインインすることで、[アプリケーションに同意](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview#understanding-user-and-admin-consent)します。

## <a name="next-steps"></a>次のステップ
[Azure Active Directory でのアプリケーションの管理](active-directory-enable-sso-scenario.md)
