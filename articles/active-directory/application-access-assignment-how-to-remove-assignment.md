---
title: "アプリケーションへのユーザー アクセスの削除方法 | Microsoft Docs"
description: "アプリケーションへのユーザー アクセスの削除方法について理解します。"
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
ms.translationtype: Human Translation
ms.sourcegitcommit: cc9e81de9bf8a3312da834502fa6ca25e2b5834a
ms.openlocfilehash: 8d4f2cec35a8edfec9b8830a077b8aa65ca0c229
ms.contentlocale: ja-jp
ms.lasthandoff: 04/11/2017

---

# <a name="how-to-remove-a-users-access-to-an-application"></a>アプリケーションへのユーザー アクセスの削除方法

この記事では、アプリケーションへのユーザーのアクセスを削除する方法について説明します。

## <a name="i-want-to-remove-a-specific-users-or-groups-assignment-to-an-application"></a>特定のユーザーまたはグループの、アプリケーションへの割り当てを削除する必要がある

アプリケーションへのユーザーまたはグループの割り当てを削除するには、「[Azure Active Directory でエンタープライズ アプリケーションからユーザーまたはグループの割り当てを削除する](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-remove-assignment-azure-portal)」の記事に示されている手順に従います。

.## すべてのユーザーに対するアプリケーションへのアクセスをすべて無効にする必要がある

アプリケーションへのすべてのユーザーのサインインを無効にするには、「[Azure Active Directory でエンタープライズ アプリケーションのユーザー サインインを無効にする](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-disable-app-azure-portal)」の記事に示されている手順に従います。

## <a name="i-want-to-delete-an-application-entirely"></a>アプリケーションを完全に削除する必要がある

**アプリケーションを削除する**には、以下の手順に従います。

1.  [**Azure Portal**](https://portal.azure.com/) を開き、**グローバル管理者**または**共同管理者**としてサインインします。

2.  左側のメイン ナビゲーション メニューの下部にある **[その他のサービス]** をクリックして **[Azure Active Directory 拡張機能]** を開きます。

3.  フィルター検索ボックスに**「Azure Active Directory」**と入力し、**[Azure Active Directory]** 項目を選択します。

4.  Azure Active Directory の左側にあるナビゲーション メニューから **[エンタープライズ アプリケーション]** をクリックします。

5.  **[すべてのアプリケーション]** をクリックして、すべてのアプリケーションの一覧を表示します。

   * ここに表示したいアプリケーションが表示されない場合は、**[すべてのアプリケーション リスト]** の上部にある **[フィルター]** コントロールを使用して、**[表示]** オプションを **[すべてのアプリケーション]** に設定します。

6.  削除するアプリケーションを選択します。

7.  アプリケーションが読み込まれたら、上部アプリケーションの **[概要]** ブレードから **[削除]** アイコンをクリックします。

## <a name="i-want-to-disable-all-future-user-consent-operations-to-any-application"></a>すべてのアプリケーションに対して今後のユーザーの同意操作をすべて無効にする必要がある

ディレクトリ全体でユーザーの同意を無効にすると、エンドユーザーはすべてのアプリケーションに同意できなくなります。 管理者は、依然としてユーザに代わって同意できます。 アプリケーションの同意と、これを行う理由または行わない理由の詳細については、「[ユーザーおよび管理者の同意について](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview#understanding-user-and-admin-consent)」をご覧ください。

**ディレクトリ全体で今後のユーザーの同意動作をすべて無効にする**には、以下の手順に従います。

1.  [**Azure Portal**](https://portal.azure.com/) を開き、**グローバル管理者**としてサインインします。

2.  左側のメイン ナビゲーション メニューの下部にある **[その他のサービス]** をクリックして **[Azure Active Directory 拡張機能]** を開きます。

3.  フィルター検索ボックスに**「Azure Active Directory」**と入力し、**[Azure Active Directory]** 項目を選択します。

4.  ナビゲーション メニューで **[ユーザーとグループ]** をクリックします。

5.  **[ユーザー設定]** をクリックします。

6.  **[ユーザーはアプリが自分のデータにアクセスすることを許可できる]** トグルを **[いいえ]** に設定し、**[保存]** ボタンをクリックして、今後のすべてのユーザーの同意操作を無効にします。


# <a name="next-steps"></a>次のステップ
[アプリへのアクセスの管理](active-directory-managing-access-to-apps.md)

