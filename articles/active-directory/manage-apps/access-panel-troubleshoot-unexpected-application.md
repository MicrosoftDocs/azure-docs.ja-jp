---
title: アクセス パネルにアプリケーションが表示される方法 | Microsoft Docs
description: アプリケーションがアクセス パネルに表示される理由のトラブルシューティングを行う
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: celested
ms.reviewr: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: fccf671edbc121501a17975be303453a798837e4
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58122201"
---
# <a name="how-applications-appear-on-the-access-panel"></a>アクセス パネルにアプリケーションが表示される方法

Web ベースのポータルであるアクセス パネルを使用すると、Azure Active Directory (Azure AD) の職場または学校アカウントを持つユーザーは、Azure AD 管理者によってアクセスを許可されたクラウドベースのアプリケーションを表示および起動することができます。 これらのアプリケーションは、Azure AD ポータルでユーザーに代わって構成されます。 管理者は、アプリケーションをユーザーに直接、またはユーザーが属するグループにプロビジョニングできます。その結果、ユーザーのアクセス パネルにアプリケーションが表示されます。

## <a name="general-issues-to-check-first"></a>最初にチェックすべき一般的な問題

-   ユーザーまたはユーザーがメンバーとなっているグループからアプリケーションが削除された場合は、数分後にユーザーのアクセス パネルにもう一度サインインおよびサインアウトしてみて、アプリケーションが削除されているかどうかを確認してください。

-   ユーザーまたはユーザーがメンバーとなっているグループからライセンスが削除された場合は、グループのサイズと複雑さに応じて、変更が反映されるまでに長時間かかることがあります。 しばらく待ってからアクセス パネルにサインインしてください。

## <a name="problems-related-to-assigning-applications-to-users"></a>ユーザーへのアプリケーションの割り当てに関連する問題

アプリケーションが以前に割り当てられたため、ユーザーのアクセス パネルにアプリケーションが表示されることがあります。 確認方法をいくつか次に示します。

-   [ユーザーがアプリケーションに割り当てられているかどうかを確認する](#check-if-a-user-is-assigned-to-the-application)

-   [ユーザーがアプリケーションに関連するライセンスを付与されているかどうかを確認する](#check-if-a-user-is-under-a-license-related-to-the-application)


### <a name="check-if-a-user-is-assigned-to-the-application"></a>ユーザーがアプリケーションに割り当てられているかどうかを確認する

ユーザーがアプリケーションに割り当てられているかどうかを確認するには、次の手順に従います。

1. [**Azure Portal**](https://portal.azure.com/) を開き、**グローバル管理者**としてサインインします。

2. 左側のメイン ナビゲーション メニューの上部にある **[すべてのサービス]** をクリックして **[Azure Active Directory 拡張機能]** を開きます。

3. フィルター検索ボックスに「**Azure Active Directory**」と入力し、**[Azure Active Directory]** 項目を選択します。

4. Azure Active Directory の左側のナビゲーション メニューから **[エンタープライズ アプリケーション]** をクリックします。

5. **[すべてのアプリケーション]** をクリックして、すべてのアプリケーションの一覧を表示します。

6. 対象のアプリケーションの名前を**検索**します。

7. **[ユーザーとグループ]** をクリックします。

8. ユーザーがアプリケーションに割り当てられているかどうかを確認します。

   * アプリケーションからユーザーを削除する場合は、ユーザーの**行をクリック**し、**[削除]** を選択します。

### <a name="check-if-a-user-is-under-a-license-related-to-the-application"></a>ユーザーがアプリケーションに関連するライセンスを付与されているかどうかを確認する

ユーザーに割り当てられているライセンスを確認するには、次の手順に従います。

1. [**Azure Portal**](https://portal.azure.com/) を開き、**グローバル管理者**としてサインインします。

2. 左側のメイン ナビゲーション メニューの上部にある **[すべてのサービス]** をクリックして **[Azure Active Directory 拡張機能]** を開きます。

3. フィルター検索ボックスに「**Azure Active Directory**」と入力し、**[Azure Active Directory]** 項目を選択します。

4. ナビゲーション メニューで **[ユーザーとグループ]** をクリックします。

5. **[すべてのユーザー]** をクリックします。

6. 対象のユーザーを**検索**し、**行をクリック**して選択します。

7. **[ライセンス]** をクリックして、ユーザーが現在割り当てられているライセンスを確認します。

   * ユーザーが Office ライセンスに割り当てられている場合、これによりファースト パーティーの Office アプリケーションがユーザーのアクセス パネルに表示されます。

## <a name="problems-related-to-assigning-applications-to-groups"></a>グループへのアプリケーションの割り当てに関連する問題

アプリケーションを割り当てられたグループにユーザーが属しているために、そのユーザーのアクセス パネルにアプリケーションが表示されることがあります。 確認方法をいくつか次に示します。

-   [ユーザーのグループ メンバーシップを確認する](#check-a-users-group-memberships)

-   [ユーザーがライセンスに割り当てられているグループのメンバーかどうかを確認する](#check-if-a-user-is-a-member-of-a-group-assigned-to-a-license)

### <a name="check-a-users-group-memberships"></a>ユーザーのグループ メンバーシップを確認する

グループのメンバーシップを確認するには、次の手順に従います。

1. [**Azure Portal**](https://portal.azure.com/) を開き、**グローバル管理者**としてサインインします。

2. 左側のメイン ナビゲーション メニューの上部にある **[すべてのサービス]** をクリックして **[Azure Active Directory 拡張機能]** を開きます。

3. フィルター検索ボックスに「**Azure Active Directory**」と入力し、**[Azure Active Directory]** 項目を選択します。

4. ナビゲーション メニューで **[ユーザーとグループ]** をクリックします。

5. **[すべてのユーザー]** をクリックします。

6. 対象のユーザーを**検索**し、**行をクリック**して選択します。

7. **[グループ]** をクリックします。

8. ユーザーがアプリケーションに割り当てられているグループに属しているかどうかを確認します。

   * グループからユーザーを削除する場合は、グループの**行をクリック**し、[削除] を選択します。

### <a name="check-if-a-user-is-a-member-of-a-group-assigned-to-a-license"></a>ユーザーがライセンスに割り当てられているグループのメンバーかどうかを確認する

1. [**Azure Portal**](https://portal.azure.com/) を開き、**グローバル管理者**としてサインインします。

2. 左側のメイン ナビゲーション メニューの上部にある **[すべてのサービス]** をクリックして **[Azure Active Directory 拡張機能]** を開きます。

3. フィルター検索ボックスに「**Azure Active Directory**」と入力し、**[Azure Active Directory]** 項目を選択します。

4. ナビゲーション メニューで **[ユーザーとグループ]** をクリックします。

5. **[すべてのユーザー]** をクリックします。

6. 対象のユーザーを**検索**し、**行をクリック**して選択します。

7. **[グループ]** をクリックします。

8. 特定のグループの行をクリックします。

9. **[ライセンス]** をクリックして、グループが割り当てられているライセンスを確認します。

   * グループが Office ライセンスに割り当てられている場合、これにより特定のファースト パーティーの Office アプリケーションがユーザーのアクセス パネルに表示されることがあります。


## <a name="if-these-troubleshooting-steps-do-not-the-resolve-the-issue"></a>これらのトラブルシューティング手順で問題が解決しない場合

使用可能な場合は、次の情報を含むサポート チケットを開きます。

-   関連エラー ID

-   UPN (ユーザーの電子メール アドレス)

-   テナント ID

-   ブラウザーの種類

-   タイム ゾーンとエラーが発生した時刻/タイムフレーム

-   Fiddler のトレース

## <a name="next-steps"></a>次の手順
[Azure Active Directory でのアプリケーションの管理](what-is-application-management.md)
