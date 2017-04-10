---
title: "チュートリアル: Azure Active Directory と Kintone の統合 | Microsoft Docs"
description: "Azure Active Directory で Kintone を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: c2b947dc-e1a8-4f5f-b40e-2c5180648e4f
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/15/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 400793331aa2d56358a83a51ce64c67f59bbf3b7
ms.openlocfilehash: d6ce5ac0207ac94a4eb8e6dccedd36c7cb446d70
ms.lasthandoff: 02/16/2017


---
# <a name="tutorial-azure-active-directory-integration-with-kintone"></a>チュートリアル: Azure Active Directory と Kintone の統合
このチュートリアルの目的は、Azure と Kintone の統合を紹介することです。  
このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* 有効な Azure サブスクリプション
* Kintone でのシングル サインオンが有効なサブスクリプション

このチュートリアルを完了すると、Kintone に割り当てた Azure AD ユーザーは、Kintone 企業サイト (サービス プロバイダーが開始したサインオン) で、または「 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」の説明に従って、アプリケーションにシングル サインオンできるようになります。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

1. Kintone のアプリケーション統合の有効化
2. シングル サインオンの構成
3. ユーザー プロビジョニングの構成
4. ユーザーの割り当て

![シナリオ](./media/active-directory-saas-kintone-tutorial/IC785859.png "Scenario")

## <a name="enabling-the-application-integration-for-kintone"></a>Kintone のアプリケーション統合の有効化
このセクションでは、Kintone のアプリケーション統合を有効にする方法について説明します。

### <a name="to-enable-the-application-integration-for-kintone-perform-the-following-steps"></a>Kintone のアプリケーション統合を有効にするには、次の手順に従います。
1. Azure クラシック ポータルの左側のナビゲーション ウィンドウで、 **[Active Directory]**をクリックします。
   
    ![Active Directory](./media/active-directory-saas-kintone-tutorial/IC700993.png "Active Directory")

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![アプリケーション](./media/active-directory-saas-kintone-tutorial/IC700994.png "Applications")

4. ページの下部にある **[追加]** をクリックします。
   
    ![アプリケーションの追加](./media/active-directory-saas-kintone-tutorial/IC749321.png "Add application")

5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-kintone-tutorial/IC749322.png "Add an application from gallerry")

6. **検索ボックス**に、「**Kintone**」と入力します。
   
    ![アプリケーション ギャラリー](./media/active-directory-saas-kintone-tutorial/IC785867.png "Application Gallery")

7. 結果ウィンドウで **[Kintone]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
    ![Kintone](./media/active-directory-saas-kintone-tutorial/IC785871.png "Kintone")
   
## <a name="configuring-single-sign-on"></a>シングル サインオンの構成

このセクションでは、SAML プロトコルに基づくフェデレーションを使用して、ユーザーが Azure AD のアカウントで Kintone に対する認証を行えるようにする方法を説明します。

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>シングル サインオンを構成するには、次の手順を実行します。
1. Azure クラシック ポータルの **Kintone** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。
   
    ![シングル サインオンの構成](./media/active-directory-saas-kintone-tutorial/IC785872.png "Configure Single Sign-On")

2. **Kintone にサインオンする方法について尋ねる**ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
    ![シングル サインオンの構成](./media/active-directory-saas-kintone-tutorial/IC785873.png "Configure Single Sign-On")

3. **[アプリ URL の構成]** ページの **[Kintone サインオン URL]** ボックスに、"*https://company.kintone.com*" というパターンの URL を入力して、**[次へ]** をクリックします。
   
    ![アプリケーション URL の構成](./media/active-directory-saas-kintone-tutorial/IC785875.png "Configure App URL")

4. **[Kintone でのシングル サインオンの構成]** ページで、**[証明書のダウンロード]** をクリックして証明書をダウンロードし、証明書ファイルをコンピューターに保存します。
   
    ![シングル サインオンの構成](./media/active-directory-saas-kintone-tutorial/IC785878.png "Configure Single Sign-On")

5. 別の Web ブラウザーのウィンドウで、 **Kintone** の企業サイトに管理者としてログインします。

6. **[設定]**をクリックします。
   
    ![設定](./media/active-directory-saas-kintone-tutorial/IC785879.png "Settings")

7. **[ユーザーとシステム管理]** をクリックします。
   
    ![Users & System Administration](./media/active-directory-saas-kintone-tutorial/IC785880.png "Users & System Administration")

8. **[システム管理] \> [セキュリティ]** で、**[ログイン]** をクリックします。
   
    ![Login](./media/active-directory-saas-kintone-tutorial/IC785881.png "Login")

9. **[SAML 認証を有効にする]**を選択します。
   
    ![SAML Authentication](./media/active-directory-saas-kintone-tutorial/IC785882.png "SAML Authentication")

10. [SAML 承認] セクションで、次の手順に従います。
    
    ![SAML Authentication](./media/active-directory-saas-kintone-tutorial/IC785883.png "SAML Authentication")
    
    1. Azure クラシック ポータルで、**[Kintone でのシングル サインオンの構成]** ダイアログ ページの **[リモート ログイン URL]** の値をコピーし、**[Login URL (ログイン URL)]** ボックスに貼り付けます。
   
    2. Azure クラシック ポータルで、**[Kintone でのシングル サインオンの構成]** ダイアログ ページの **[リモート ログアウト URL]** の値をコピーし、**[Logout URL (ログアウト URL)]** ボックスに貼り付けます。
    
    3. **[参照]** をクリックして、ダウンロードした証明書をアップロードします。
    
    4. [ **Save**] をクリックします。

11. Azure クラシック ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。
    
    ![シングル サインオンの構成](./media/active-directory-saas-kintone-tutorial/IC785884.png "Configure Single Sign-On")
    
## <a name="configuring-user-provisioning"></a>ユーザー プロビジョニングの構成

Azure AD ユーザーが Kintone にログインできるようにするには、そのユーザーを Kintone にプロビジョニングする必要があります。  
Kintone の場合、プロビジョニングは手動で行います。

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>ユーザー アカウントをプロビジョニングするには、次の手順に従います。
1. **Kintone** の企業サイトに管理者としてログインします。

2. **[設定]**をクリックします。
   
    ![設定](./media/active-directory-saas-kintone-tutorial/IC785879.png "Settings")

3. **[ユーザーとシステム管理]** をクリックします。
   
    ![User & System Administration](./media/active-directory-saas-kintone-tutorial/IC785880.png "User & System Administration")

4. **[ユーザー管理]** で、**[Departments & Users (部門とユーザー)]** をクリックします。
   
    ![Department & Users](./media/active-directory-saas-kintone-tutorial/IC785888.png "Department & Users")

5. **[新しいユーザー]**をクリックします。
   
    ![New Users](./media/active-directory-saas-kintone-tutorial/IC785889.png "New Users")

6. **[新しいユーザー]** セクションで、次の手順に従います。
   
    ![New Users](./media/active-directory-saas-kintone-tutorial/IC785890.png "New Users")
   
    1. プロビジョニングする有効な AAD アカウントの **[表示名]**、**[ログイン名]**、**[新しいパスワード]**、**[確認パスワード]**、**[メール アドレス]**、その他の詳細を該当するボックスに入力します。
 
    2. **[Save]**をクリックします。

> [!NOTE]
> 他の Kintone ユーザー アカウント作成ツールや Kintone から提供されている API を使用して、AAD ユーザー アカウントをプロビジョニングできます。
> 
> 

## <a name="assigning-users"></a>ユーザーの割り当て
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

### <a name="to-assign-users-to-kintone-perform-the-following-steps"></a>ユーザーを Kintone に割り当てるには、次の手順に従います。
1. Azure クラシック ポータルで、テスト アカウントを作成します。

2. **Kintone** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。
   
    ![ユーザーの割り当て](./media/active-directory-saas-kintone-tutorial/IC785891.png "Assign Users")

3. テスト ユーザーを選択して、**[割り当て]** をクリックし、**[はい]** をクリックして割り当てを確定します。
   
    ![はい](./media/active-directory-saas-kintone-tutorial/IC767830.png "Yes")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。 アクセス パネルの詳細については、 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)を参照してください。


