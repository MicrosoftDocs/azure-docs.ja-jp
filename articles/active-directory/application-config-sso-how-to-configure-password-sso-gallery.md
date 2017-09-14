---
title: "Azure AD ギャラリー アプリケーションのパスワード シングル サインオンを構成する方法 | Microsoft Docs"
description: "Azure AD アプリケーション ギャラリーに既に表示されている場合に、セキュリティで保護されたパスワード ベースのシングル サインオン用にアプリケーションを構成する方法"
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
ms.openlocfilehash: d4678afa4d7a9000761d3a60a54539fc1730f76a
ms.contentlocale: ja-jp
ms.lasthandoff: 04/11/2017

---

# <a name="how-to-configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Azure AD ギャラリー アプリケーションのパスワード シングル サインオンを構成する方法

[Azure AD アプリケーション ギャラリー](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#get-started-with-the-azure-ad-application-gallery)からアプリケーションを追加する場合、そのアプリケーションへのユーザーのサインイン方法を選ぶことができます。 [Azure Portal](https://portal.azure.com/) のエンタープライズ アプリケーションで **[シングル サインオン]** ナビゲーション項目を選択して、この選択肢をいつでも構成できます。

使用できるシングル サインオン方法の 1 つは、[[パスワードベースのシングル サインオン]](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work) オプションです。 これは、Azure AD へのアプリケーションの統合を迅速に開始する優れた方法で、以下のことを実現します。

-   Azure AD と統合したアプリケーションのユーザー名とパスワードを安全に保存し、再生して、**ユーザーのシングル サインオン**を有効にする

-   サインインするためにユーザー名とパスワード以外のフィールドも必要とするアプリケーションに対して**複数のサインイン フィールドを必要とするアプリケーションをサポートする**

-   ユーザーが資格情報を入力するときに[アプリケーション アクセス パネル](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に表示されるユーザー名とパスワードの入力フィールドの**ラベルをカスタマイズ**する

-   **ユーザー**が既存のアプリケーション アカウントに対する自分のユーザー名とパスワードを[アプリケーション アクセス パネル](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に手動で入力して指定できるようにする

-   **ビジネス グループのメンバー**が、[[アプリケーションのセルフサービス アクセス]](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) 機能を使用して、ユーザーに割り当てられているユーザー名とパスワードを指定できるようにする

-   **管理者**が、[アプリケーションにユーザーを割り当てる](#assign-a-user-to-an-application-directly)ときに [資格情報の更新] 機能を使用して、ユーザーに割り当てられたユーザー名とパスワードを指定できるようにする

-   **管理者**が、[アプリケーションにグループを割り当てる](#assign-an-application-to-a-group-directly)ときに [資格情報の更新] 機能を使用して、グループで使用される共有ユーザー名とパスワードを指定できるようにする

[Azure AD アプリケーション ギャラリー](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#get-started-with-the-azure-ad-application-gallery)に既に存在するアプリケーションへの [[パスワードベースのシングル サインオン]](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work) を有効にする方法を以下に示します。

## <a name="overview-of-steps-required"></a>必要な手順の概要
Azure AD ギャラリーからアプリケーションを構成するには、以下の手順を実行する必要があります。

-   [Azure AD ギャラリーからアプリケーションを追加する](#add-an-application-from-the-azure-ad-gallery)

-   [パスワード シングル サインオンに対応するようにアプリケーションを構成する](#configure-the-application-for-password-single-sign-on)

-   [アプリケーションをユーザーまたはグループに割り当てる](#assign-the-application-to-a-user-or-a-group)

    -   [アプリケーションにユーザーを直接割り当てる](#assign-a-user-to-an-application-directly)

    -   [アプリケーションをグループに直接割り当てる](#assign-an-application-to-a-group-directly)

## <a name="add-an-application-from-the-azure-ad-gallery"></a>Azure AD ギャラリーからアプリケーションを追加する

Azure AD ギャラリーからアプリケーションを追加するには、次の手順に従います。

1.  [Azure Portal](https://portal.azure.com) を開き、**グローバル管理者**または**共同管理者**としてサインインします

2.  左側のメイン ナビゲーション メニューの下部にある **[More services (その他のサービス)]** をクリックして **[Azure Active Directory Extension (Azure Active Directory 拡張機能)]** を開きます。

3.  フィルター検索ボックスに「**Azure Active Directory**」と入力し、**[Azure Active Directory]** アイテムを選択します。

4.  Azure Active Directory の左側にあるナビゲーション メニューで **[エンタープライズ アプリケーション]** をクリックします。

5.  **[エンタープライズ アプリケーション]** ブレードの右上隅にある **[追加]** をクリックします

6.  **[ギャラリーから追加する]** セクションの **[名前を入力してください]** テキスト ボックスで、アプリケーションの名前を入力します

7.  シングル サインオンを構成するアプリケーションを選択します

8.  アプリケーションを追加する前に、**[名前]** テキストボックスから名前を変更できます。

9.  **[追加]** をクリックして、アプリケーションを追加します。

しばらくすると、アプリケーションの構成ブレードが表示されます。

## <a name="configure-the-application-for-password-single-sign-on"></a>パスワード シングル サインオンに対応するようにアプリケーションを構成する

アプリケーションのシングル サインオンを構成するには、次の手順に従います。

1.  [**Azure Portal**](https://portal.azure.com/) を開き、**グローバル管理者**または**共同管理者**としてサインインします。

2.  左側のメイン ナビゲーション メニューの下部にある **[More services (その他のサービス)]** をクリックして **[Azure Active Directory Extension (Azure Active Directory 拡張機能)]** を開きます。

3.  フィルター検索ボックスに「**Azure Active Directory**」と入力し、**[Azure Active Directory]** アイテムを選択します。

4.  Azure Active Directory の左側にあるナビゲーション メニューで **[エンタープライズ アプリケーション]** をクリックします。

5.  **[All Applications (すべてのアプリケーション)]** をクリックしてすべてのアプリケーションの一覧を表示します。

  * ここに表示したいアプリケーションが表示されない場合は、**[All Applications List (すべてのアプリケーション リスト)]** の上部にある **[フィルター]** コントロールを使用して、**[表示]** オプションを **[All Applications (すべてのアプリケーション)]** に設定します。

6.  シングル サインオンを構成するアプリケーションを選択します

7.  アプリケーションを読み込んだら、アプリケーションの左側のナビゲーション メニューから **[シングル サインオン]** をクリックします。

8.  **[パスワード ベースのサインオン]** モードを選択します。

9.  [アプリケーションにユーザーを割り当てます](#assign-a-user-to-an-application-directly)。

10. さらに、ユーザーの行を選び、**[資格情報の更新]** をクリックしてユーザーに代わってユーザー名とパスワードを入力すると、ユーザーに代わって資格情報を提供することもできます。 そうしないと、起動時に自分で資格情報を入力するように求めるプロンプトがユーザーに表示されます。

## <a name="assign-a-user-to-an-application-directly"></a>アプリケーションにユーザーを直接割り当てる

1 人以上のユーザーをアプリケーションに直接割り当てるには、次の手順に従います。

1.  [**Azure Portal**](https://portal.azure.com/) を開き、**グローバル管理者**としてサインインします。

2.  左側のメイン ナビゲーション メニューの下部にある **[More services (その他のサービス)]** をクリックして **[Azure Active Directory Extension (Azure Active Directory 拡張機能)]** を開きます。

3.  フィルター検索ボックスに「**Azure Active Directory**」と入力し、**[Azure Active Directory]** アイテムを選択します。

4.  Azure Active Directory の左側にあるナビゲーション メニューで **[エンタープライズ アプリケーション]** をクリックします。

5.  **[All Applications (すべてのアプリケーション)]** をクリックしてすべてのアプリケーションの一覧を表示します。

  * ここに表示したいアプリケーションが表示されない場合は、**[All Applications List (すべてのアプリケーション リスト)]** の上部にある **[フィルター]** コントロールを使用して、**[表示]** オプションを **[All Applications (すべてのアプリケーション)]** に設定します。

6.  ユーザーを割り当てるアプリケーションを一覧から選びます。

7.  アプリケーションを読み込んだら、アプリケーションの左側にあるナビゲーション メニューで **[ユーザーとグループ]** をクリックします。

8.  **[ユーザーとグループ]** リストの上部にある **[追加]** をクリックし、**[割り当ての追加]** ブレードを開きます。

9.  **[割り当ての追加]** ブレードの **[ユーザーとグループ]** セレクターをクリックします。

10. 割り当てる対象のユーザーの**フル ネーム**または**電子メール アドレス**を **[名前または電子メール アドレスで検索]** 検索ボックスに入力します。

11. リスト内で**ユーザー**にポインターを合わせ、 **チェック ボックス**を表示します。 ユーザーのプロファイル写真またはロゴの横にあるチェック ボックスをオンにして、ユーザーを**[選択済み]** リストに追加します。

12. **省略可能:** **複数のユーザーを追加**する場合は、別の**フル ネーム**または**電子メール アドレス**を **[名前または電子メール アドレスで検索]** 検索ボックスに入力し、チェック ボックスをオンにして **[選択済み]** リストにこのユーザーを追加します。

13. ユーザーの選択が完了したら、**[選択]** ボタンをクリックして、アプリケーションに割り当てるユーザーとグループの一覧に追加します。

14. **省略可能:** **[割り当ての追加]** ブレードで **[ロールの選択]** セレクターをクリックし、選択したユーザーに割り当てるロールを選択します。

15. **[割り当て]** をクリックして、選択したユーザーにアプリケーションを割り当てます。

## <a name="assign-an-application-to-a-group-directly"></a>アプリケーションをグループに直接割り当てる

1 つ以上のグループをアプリケーションに直接割り当てるには、次の手順に従います。

1.  [**Azure Portal**](https://portal.azure.com/) を開き、**グローバル管理者**としてサインインします。

2.  左側のメイン ナビゲーション メニューの下部にある **[More services (その他のサービス)]** をクリックして **[Azure Active Directory Extension (Azure Active Directory 拡張機能)]** を開きます。

3.  フィルター検索ボックスに「**Azure Active Directory**」と入力し、**[Azure Active Directory]** アイテムを選択します。

4.  Azure Active Directory の左側にあるナビゲーション メニューで **[エンタープライズ アプリケーション]** をクリックします。

5.  **[All Applications (すべてのアプリケーション)]** をクリックしてすべてのアプリケーションの一覧を表示します。

  * ここに表示したいアプリケーションが表示されない場合は、**[All Applications List (すべてのアプリケーション リスト)]** の上部にある **[フィルター]** コントロールを使用して、**[表示]** オプションを **[All Applications (すべてのアプリケーション)]** に設定します。

6.  ユーザーを割り当てるアプリケーションを一覧から選びます。

7.  アプリケーションを読み込んだら、アプリケーションの左側にあるナビゲーション メニューで **[ユーザーとグループ]** をクリックします。

8.  **[ユーザーとグループ]** リストの上部にある **[追加]** をクリックし、**[割り当ての追加]** ブレードを開きます。

9.  **[割り当ての追加]** ブレードの **[ユーザーとグループ]** セレクターをクリックします。

10. **[名前または電子メール アドレスで検索]** 検索ボックスに割り当てるグループの**完全なグループ名**を入力します。

11. リスト内で**グループ**にポインターを合わせ、**チェック ボックス**を表示します。 グループのプロファイル写真またはロゴの横にあるチェック ボックスをオンにして、ユーザーを **[選択済み]** リストに追加します。

12. **省略可能:** **複数のグループを追加**する場合、別の**完全なグループ名**を **[名前または電子メール アドレスで検索]** 検索ボックスに入力し、チェックボックスをオンにして、**[選択済み]** リストにこのグループを追加します。

13. グループの選択が完了したら、**[選択済み]** ボタンをクリックして、アプリケーションに割り当てるユーザーとグループの一覧に追加します。

14. **省略可能:** **[割り当ての追加]** ブレードで **[ロールの選択]** セレクターをクリックして、選択したグループに割り当てるロールを選択します。

15. **[割り当て]** をクリックして、選択したグループにアプリケーションを割り当てます。

しばらくすると、選択したユーザーがアクセス パネルでこれらのアプリケーションを起動できるようになります。

## <a name="next-steps"></a>次のステップ
[アプリケーション プロキシを使用してアプリにシングル サインオンを提供](active-directory-application-proxy-sso-using-kcd.md)

