---
title: "チュートリアル: Azure Active Directory と ScreenSteps の統合 | Microsoft Docs"
description: "Azure Active Directory で ScreenSteps を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 4563fe94-a88f-4895-a07f-79df44889cf9
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/03/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: dd50971e16da2cec046dd6dbe2f94dd58d01fb09
ms.openlocfilehash: c2f0e5b1348a25f4cb4c92b2a837fd22207e5dad
ms.lasthandoff: 01/05/2017


---
# <a name="tutorial-azure-active-directory-integration-with-screensteps"></a>チュートリアル: Azure Active Directory と ScreenSteps の統合
このチュートリアルの目的は、Azure と ScreenSteps の統合を示すことです。  
このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* 有効な Azure サブスクリプション
* ScreenSteps テナント

このチュートリアルを終了すると、ScreenSteps に割り当てた Azure AD ユーザーは、ScreenSteps 企業サイト (サービス プロバイダーが開始したサインオン) で、または「 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」の説明に従って、アプリケーションにシングル サインオンできるようになります。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

1. ScreenSteps のアプリケーション統合の有効化
2. シングル サインオンの構成
3. ユーザー プロビジョニングの構成
4. ユーザーの割り当て

![シナリオ](./media/active-directory-saas-screensteps-tutorial/IC778516.png "Scenario")

## <a name="enabling-the-application-integration-for-screensteps"></a>ScreenSteps のアプリケーション統合の有効化
このセクションでは、ScreenSteps のアプリケーション統合を有効にする方法を説明します。

### <a name="to-enable-the-application-integration-for-screensteps-perform-the-following-steps"></a>ScreenSteps のアプリケーション統合を有効にするには、次の手順に従います。
1. Azure クラシック ポータルの左側のナビゲーション ウィンドウで、 **[Active Directory]**をクリックします。
   
    ![Active Directory](./media/active-directory-saas-screensteps-tutorial/IC700993.png "Active Directory")

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![アプリケーション](./media/active-directory-saas-screensteps-tutorial/IC700994.png "Applications")

4. ページの下部にある **[追加]** をクリックします。
   
    ![アプリケーションの追加](./media/active-directory-saas-screensteps-tutorial/IC749321.png "Add application")

5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-screensteps-tutorial/IC749322.png "Add an application from gallerry")

6. **検索ボックス**に、「**ScreenSteps**」と入力します。
   
    ![アプリケーション ギャラリー](./media/active-directory-saas-screensteps-tutorial/IC778517.png "Application Gallery")

7. 結果ウィンドウで **[ScreenSteps]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
    ![ScreenSteps](./media/active-directory-saas-screensteps-tutorial/IC778518.png "ScreenSteps")
   
## <a name="configuring-single-sign-on"></a>シングル サインオンの構成

このセクションでは、SAML プロトコルに基づくフェデレーションを使用して、ScreenSteps で Azure AD のユーザー アカウントを使用してユーザーを認証できるようにする方法を説明します。

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>シングル サインオンを構成するには、次の手順を実行します。
1. Azure クラシック ポータルの **ScreenSteps** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。
   
    ![シングル サインオンの構成](./media/active-directory-saas-screensteps-tutorial/IC778519.png "Configure single sign-on")

2. **[ユーザーの ScreenSteps へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
    ![シングル サインオンの構成](./media/active-directory-saas-screensteps-tutorial/IC778520.png "Configure single sign-on")

3. **[アプリケーション URL の構成]** ページで、**[ScreenSteps サインイン URL]** ボックスに、"*https://\<テナント名\>.ScreenSteps.com*" のパターンを使用して URL を入力し、**[次へ]** をクリックします。
   
    ![アプリケーション URL の構成](./media/active-directory-saas-screensteps-tutorial/IC778521.png "Configure app URL")

4. **[ScreenSteps でのシングル サインオンの構成]** ページで、**[証明書のダウンロード]** をクリックして証明書をダウンロードし、証明書ファイルをコンピューターに保存します。
   
    ![シングル サインオンの構成](./media/active-directory-saas-screensteps-tutorial/IC778522.png "Configure single sign-on")

5. 別の Web ブラウザー ウィンドウで、ScreenSteps 企業サイトに管理者としてログインします。

6. **[Account Management]**をクリックします。
   
    ![Account management](./media/active-directory-saas-screensteps-tutorial/IC778523.png "Account management")

7. **[Remote Authentication]**をクリックします。
   
    ![Remote authentication](./media/active-directory-saas-screensteps-tutorial/IC778524.png "Remote authentication")

8. **[Create authentication endpoint]**をクリックします。
   
    ![Remote authentication](./media/active-directory-saas-screensteps-tutorial/IC778525.png "Remote authentication")

9. **[Create an Authentication Endpoint]** セクションで、次の手順に従います。
   
    ![Create an authentication endpoint](./media/active-directory-saas-screensteps-tutorial/IC778526.png "Create an authentication endpoint")
   
    1. **[Title]** テキスト ボックスに、タイトルを入力します。
    2. **[Mode]** 一覧から **[SAML]** を選択します。
    3. **[作成]**をクリックします。

10. **[Remote Authentication Endpoint]** セクションで、次の手順に従います。
    
    ![Remote authentication endpoint](./media/active-directory-saas-screensteps-tutorial/IC778527.png "Remote authentication endpoint")
    
    1. Azure クラシック ポータルの **[ScreenSteps でのシングル サインオンの構成]** ページで、**[リモート ログイン URL]** の値をコピーし、**[Remote Login URL]** ボックスに貼り付けます。

    2. Azure クラシック ポータルの **[ScreenSteps でのシングル サインオンの構成]** ページで、**[リモート ログアウト URL]** の値をコピーし、**[Remote Logout URL]** ボックスに貼り付けます。

    3. **[Choose a file]**をクリックし、ダウンロードした証明書をアップロードします。

    4. **[Update]**をクリックします。

11. Azure クラシック ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。
    
    ![シングル サインオンの構成](./media/active-directory-saas-screensteps-tutorial/IC778542.png "Configure single sign-on")
    
## <a name="configuring-user-provisioning"></a>ユーザー プロビジョニングの構成

Azure AD ユーザーが **ScreenSteps** にログインできるようにするには、ユーザーを **ScreenSteps** にプロビジョニングする必要があります。  
**ScreenSteps**の場合、プロビジョニングは手動で行います。

### <a name="to-provision-a-user-account-to-screensteps-perform-the-following-steps"></a>ユーザー アカウントを ScreenSteps にプロビジョニングするには、次の手順に従います。
1. **ScreenSteps** テナントにログインします。

2. **[Account Management]**をクリックします。
   
    ![Account management](./media/active-directory-saas-screensteps-tutorial/IC778523.png "Account management")

3. **[ユーザー]**をクリックします。
   
    ![ユーザー](./media/active-directory-saas-screensteps-tutorial/IC778544.png "Users")

4. **[Create a user]**をクリックします。
   
    ![All Users](./media/active-directory-saas-screensteps-tutorial/IC778545.png "All Users")

5. **[User Role]** の一覧からユーザーのロールを選択します。

6. [User Role] セクションで、プロビジョニングする有効な AAD アカウントの**姓**、**名**、**電子メール**、**ログイン**、**パスワード**、**確認用パスワード**を該当するボックスに入力します。
   
    ![New user](./media/active-directory-saas-screensteps-tutorial/IC778546.png "New user")

7. [Groups] セクションで、**[Authentication Group (SAML)]** を選択し、**[Create User]** をクリックします。
   
    ![Groups](./media/active-directory-saas-screensteps-tutorial/IC778547.png "Groups")

> [!NOTE]
> ScreenSteps から提供されている他の ScreenSteps ユーザー アカウント作成ツールまたは  API を使用して、AAD ユーザー アカウントをプロビジョニングできます。
> 
> 

## <a name="assigning-users"></a>ユーザーの割り当て
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

### <a name="to-assign-users-to-screensteps-perform-the-following-steps"></a>ユーザーを ScreenSteps に割り当てるには、次の手順に従います。
1. Azure クラシック ポータルで、テスト アカウントを作成します。

2. **ScreenSteps** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。
   
    ![ユーザーの割り当て](./media/active-directory-saas-screensteps-tutorial/IC778548.png "Assign users")

3. テスト ユーザーを選択して、**[割り当て]** をクリックし、**[はい]** をクリックして割り当てを確定します。
   

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。 アクセス パネルの詳細については、 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)を参照してください。


